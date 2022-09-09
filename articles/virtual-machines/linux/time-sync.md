---
title: Time sync for Linux VMs in Azure 
description: Time sync for Linux virtual machines.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/30/2021
ms.author: cynthn
---

# Time sync for Linux VMs in Azure

Time sync is important for security and event correlation. Sometimes it is used for distributed transactions implementation. Time accuracy between multiple computer systems is achieved through synchronization. Synchronization can be affected by multiple things, including reboots and network traffic between the time source and the computer fetching the time. 

Azure is backed by infrastructure running Windows Server 2016. Windows Server 2016 has improved algorithms used to correct time and condition the local clock to synchronize with UTC.  The Windows Server 2016 Accurate Time feature greatly improved how the VMICTimeSync service that governs VMs with the host for accurate time. Improvements include more accurate initial time on VM start or VM restore and interrupt latency correction. 

> [!NOTE]
> For a quick overview of Windows Time service, take a look at this [high-level overview video](https://aka.ms/WS2016TimeVideo).
>
> For more information, see [Accurate time for Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## Overview

Accuracy for a computer clock is gauged on how close the computer clock is to the Coordinated Universal Time (UTC) time standard. UTC is defined by a multinational sample of precise atomic clocks that can only be off by one second in 300 years. But, reading UTC directly requires specialized hardware. Instead, time servers are synced to UTC and are accessed from other computers to provide scalability and robustness. Every computer has time synchronization service running that knows what time servers to use and periodically checks if computer clock needs to be corrected and adjusts time if needed. 

Azure hosts are synchronized to internal Microsoft time servers that take their time from Microsoft-owned Stratum 1 devices, with GPS antennas. Virtual machines in Azure can either depend on their host to pass the accurate time (*host time*) on to the VM or the VM can directly get time from a time server, or a combination of both. 

On stand-alone hardware, the Linux OS only reads the host hardware clock on boot. After that, the clock is maintained using the interrupt timer in the Linux kernel. In this configuration, the clock will drift over time. In newer Linux distributions on Azure, VMs can use the VMICTimeSync provider, included in the Linux integration services (LIS), to query for clock updates from the host more frequently.

Virtual machine interactions with the host can also affect the clock. During [memory preserving maintenance](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot), VMs are paused for up to 30 seconds. For example, before maintenance begins the VM clock shows 10:00:00 AM and lasts 28 seconds. After the VM resumes, the clock on the VM would still show 10:00:00 AM, which would be 28 seconds off. To correct for this, the VMICTimeSync service monitors what is happening on the host and updates the time-of-day clock in Linux VMs to compensate.

Without time synchronization working, the clock on the VM would accumulate errors. When there is only one VM, the effect might not be significant unless the workload requires highly accurate timekeeping. But in most cases, we have multiple, interconnected VMs that use time to track transactions and the time needs to be consistent throughout the entire deployment. When time between VMs is different, you could see the following effects:

- Authentication will fail. Security protocols like Kerberos or certificate-dependent technology rely on time being consistent across the systems.
- It's very hard to figure out what have happened in a system if logs (or other data) don't agree on time. The same event would look like it occurred at different times, making correlation difficult.
- If clock is off, the billing could be calculated incorrectly.


## Configuration options

Time sync requires that a time sync service be running in the Linux VM, plus a source of accurate time information against which to synchronize.
Typically ntpd or chronyd is used as the time sync service, though there are other open source time sync services that can be used as well.
The source of accurate time information can be the Azure host or an external time service that is accessed over the public internet.
By itself, the VMICTimeSync service does not provide ongoing time sync between the Azure host and a Linux VM except after pauses for host maintenance as described above. 

Historically, most Azure Marketplace images with Linux have been configured in one of two ways:
- No time sync service is running by default
- ntpd is running as the time sync service, and synchronizing against an external NTP time source that is accessed over the network. For example, Ubuntu 18.04 LTS Marketplace images use **ntp.ubuntu.com**.

To confirm ntpd is synchronizing correctly, run the `ntpq -p` command.

Starting in early calendar 2021, the most current Azure Marketplace images with Linux are being changed to use chronyd as the time sync service,
and chronyd is configured to synchronize against the Azure host rather than an external NTP time source. The Azure host time is usually the best time source to synchronize
against, as it is maintained very accurately and reliably, and is accessible without the variable network delays inherent in accessing an external NTP time source
over the public internet.

The VMICTimeSync is used in parallel and provides two functions:
- Immediately updates the Linux VM time-of-day clock after a host maintenance event
- Instantiates an IEEE 1588 Precision Time Protocol (PTP) hardware clock source as a /dev/ptp device that provides the accurate time-of-day from the Azure host.  Chronyd can be configured to synchronize against this time source (which is the default configuration in the newest Linux images). Linux distributions with kernel version 4.11 or later (or version 3.10.0-693 or later for RHEL/CentOS 7) support the /dev/ptp device.  For earlier kernel versions that do not support /dev/ptp for Azure host time, only synchronization against an external time source is possible.

Of course, the default configuration can be changed. An older image that is configured to use ntpd and an external time source can be changed to use chronyd and the /dev/ptp device for Azure host time.  Similarly, an image using Azure host time via a /dev/ptp device can be configured to use an external NTP time source if required by your application or workload.


## Tools and resources

There are some basic commands for checking your time synchronization configuration. Documentation for Linux distribution will have more details on the best way to configure time synchronization for that distribution.

### Integration services

Check to see if the integration service (hv_utils) is loaded.

```bash
lsmod | grep hv_utils
```
You should see something similar to this:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

### Check for PTP Clock Source

With newer versions of Linux, a Precision Time Protocol (PTP) clock source corresponding to the Azure host is available as part of the VMICTimeSync provider.
On older versions of Red Hat Enterprise Linux or CentOS 7.x the [Linux Integration Services](https://github.com/LIS/lis-next) can be downloaded and used to
install the updated driver. When the PTP clock source is available, the Linux device will be of the form /dev/ptp*x*. 

See which PTP clock sources are available.

```bash
ls /sys/class/ptp
```

In this example, the value returned is *ptp0*, so we use that to check the clock name. To verify the device, check the clock name.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

This should return `hyperv`, meaning the Azure host.

In Linux VMs with Accelerated Networking enabled, you may see multiple PTP devices listed because the Mellanox mlx5 driver also creates a /dev/ptp device.
Because the initialization order can be different each time Linux boots, the PTP device corresponding to the Azure host might be /dev/ptp0 or it might be /dev/ptp1, which makes
it difficult to configure chronyd with the correct clock source. To solve this problem, the most recent Linux images have a udev rule that creates the
symlink /dev/ptp_hyperv to whichever /dev/ptp entry corresponds to the Azure host. Chrony should be configured to use this symlink instead of /dev/ptp0 or /dev/ptp1.

### chrony

On Ubuntu 19.10 and later versions, Red Hat Enterprise Linux, and CentOS 8.x, [chrony](https://chrony.tuxfamily.org/) is configured to use a PTP source clock. Instead of chrony, older Linux releases use the Network Time Protocol daemon (ntpd), which doesn't support PTP sources. To enable PTP in those releases, chrony must be manually installed and configured (in chrony.conf) by using the following statement:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```
Per above, if the /dev/ptp_hyperv symlink is available, use it instead of /dev/ptp0 to avoid any confusion with the /dev/ptp device created by the Mellanox mlx5 driver.

For more information about Ubuntu and NTP, see [Time Synchronization](https://ubuntu.com/server/docs/network-ntp).

For more information about Red Hat and NTP, see [Configure NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

For more information about chrony, see [Using chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony).

If both chrony and VMICTimeSync sources are enabled simultaneously, you can mark one as **prefer**, which sets the other source as a backup. Because NTP services do not update the clock for large skews except after a long period, the VMICTimeSync will recover the clock from paused VM events far more quickly than NTP-based tools alone.

By default, chronyd accelerates or slows the system clock to fix any time drift. If the drift becomes too big, chrony fails to fix the drift. To overcome this, the `makestep` parameter in **/etc/chrony.conf** can be changed to force a time sync if the drift exceeds the threshold specified.

 ```bash
makestep 1.0 -1
```

Here, chrony will force a time update if the drift is greater than 1 second. To apply the changes restart the chronyd service:

```bash
systemctl restart chronyd
```

### systemd 

On SUSE and Ubuntu releases before 19.10, time sync is configured using [systemd](https://www.freedesktop.org/wiki/Software/systemd/). For more information about Ubuntu, see [Time Synchronization](https://help.ubuntu.com/lts/serverguide/NTP.html). For more information about SUSE, see Section 4.5.8 in [SUSE Linux Enterprise Server 12 SP3 Release Notes](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## Next steps

For more information, see [Accurate time for Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).


