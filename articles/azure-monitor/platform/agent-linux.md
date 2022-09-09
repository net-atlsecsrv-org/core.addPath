---
title: Install Log Analytics agent on Linux computers
description: This article describes how to connect Linux computers hosted in other clouds or on-premises to Azure Monitor with the Log Analytics agent for Linux.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020

---

# Install Log Analytics agent on Linux computers
This article provides details on installing the Log Analytics agent on Linux computers using the following methods:

* [Install the agent for Linux using a wrapper-script](#install-the-agent-using-wrapper-script) hosted on GitHub. This is the recommended method to install and upgrade the agent when the computer has connectivity with the Internet, directly or through a proxy server.
* [Manually download and install](#install-the-agent-manually) the agent. This is required when the Linux computer does not have access to the Internet and will be communicating with Azure Monitor or Azure Automation through the [Log Analytics gateway](gateway.md). 

>[!IMPORTANT]
> The installation methods described in this article are typically used for virtual machines on-premises or in other clouds. See [Installation options](log-analytics-agent.md#installation-options) for more efficient options you can use for Azure virtual machines.



## Supported operating systems

See [Overview of Azure Monitor agents](agents-overview.md#supported-operating-systems) for a list of Linux distributions supported by the Log Analytics agent.

>[!NOTE]
>OpenSSL 1.1.0 is only supported on x86_x64 platforms (64-bit) and OpenSSL earlier than 1.x is not supported on any platform.
>
Starting with versions released after August 2018, we are making the following changes to our support model:  

* Only the server versions are supported, not client.  
* Focus support on any of the [Azure Linux Endorsed distros](../../virtual-machines/linux/endorsed-distros.md). Note that there may be some delay between a new distro/version being Azure Linux Endorsed and it being supported for the Log Analytics Linux agent.
* All minor releases are supported for each major version listed.
* Versions that have passed their manufacturer's end-of-support date are not supported.  
* New versions of AMI are not supported.  
* Only versions that run SSL 1.x by default are supported.

>[!NOTE]
>If you are using a distro or version that is not currently supported and doesn't align to our support model, we recommend that you fork this repo, acknowledging that Microsoft support will not provide assistance with forked agent versions.

### Python 2 requirement

 The Log Analytics agent requires Python 2. If your virtual machine is using a distro that doesn't include Python 2 by default then you must install it. The following sample commands will install Python 2 on different distros.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

The python2 executable must be aliased to *python*. Following is one method that you can use to set this alias:

1. Run the following command to remove any existing aliases.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Run the following command to create the alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## Supported Linux hardening
The OMS Agent has limited customization support for Linux. 

The following are currently supported: 
- FIPs

The following are planned but not yet supported:
- CIS
- SELINUX

Other hardening and customization methods are not supported nor planned for OMS Agent.  

## Agent prerequisites

The following table highlights the packages required for [supported Linux distros](#supported-operating-systems) that the agent will be installed on.

|Required package |Description |Minimum version |
|-----------------|------------|----------------|
|Glibc |    GNU C Library | 2.5-12 
|Openssl    | OpenSSL Libraries | 1.0.x or 1.1.x |
|Curl | cURL web client | 7.15.5 |
|Python | | 2.6+ or 3.3+
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Either rsyslog or syslog-ng are required to collect syslog messages. The default syslog daemon on version 5 of Red Hat Enterprise Linux, CentOS, and Oracle Linux version (sysklog) is not supported for syslog event collection. To collect syslog data from this version of these distributions, the rsyslog daemon should be installed and configured to replace sysklog.

## Network requirements
See [Log Analytics agent overview](log-analytics-agent.md#network-requirements) for the network requirements for the Linux agent.

## Agent install package

The Log Analytics agent for Linux is composed of multiple packages. The release file contains the following packages, which are available by running the shell bundle with the `--extract` parameter:

**Package** | **Version** | **Description**
----------- | ----------- | --------------
omsagent | 1.13.9 | The Log Analytics Agent for Linux
omsconfig | 1.1.1 | Configuration agent for the Log Analytics agent
omi | 1.6.4 | Open Management Infrastructure (OMI) -- a lightweight CIM Server. *Note that OMI requires root access to run a cron job necessary for the functioning of the service*
scx | 1.6.4 | OMI CIM Providers for operating system performance metrics
apache-cimprov | 1.0.1 | Apache HTTP Server performance monitoring provider for OMI. Only installed if Apache HTTP Server is detected.
mysql-cimprov | 1.0.1 | MySQL Server performance monitoring provider for OMI. Only installed if MySQL/MariaDB server is detected.
docker-cimprov | 1.0.0 | Docker provider for OMI. Only installed if Docker is detected.

### Agent installation details

After installing the Log Analytics agent for Linux packages, the following additional system-wide configuration changes are applied. These artifacts are removed when the omsagent package is uninstalled.

* A non-privileged user named: `omsagent` is created. The daemon runs under this credential. 
* A sudoers *include* file is created in `/etc/sudoers.d/omsagent`. This authorizes `omsagent` to restart the syslog and omsagent daemons. If sudo *include* directives are not supported in the installed version of sudo, these entries will be written to `/etc/sudoers`.
* The syslog configuration is modified to forward a subset of events to the agent. For more information, see [Configure Syslog data collection](data-sources-syslog.md).

On a monitored Linux computer, the agent is listed as `omsagent`. `omsconfig` is the Log Analytics agent for Linux configuration agent that looks for new portal side configuration every 5 minutes. The new and updated configuration is applied to the agent configuration files located at `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

## Install the agent using wrapper script

The following steps configure setup of the agent for Log Analytics in Azure and Azure Government cloud using the wrapper script for Linux computers that can communicate directly or through a proxy server to download the agent hosted on GitHub and install the agent.  

If your Linux computer needs to communicate through a proxy server to Log Analytics, this configuration can be specified on the command line by including `-p [protocol://][user:password@]proxyhost[:port]`. The *protocol* property accepts `http` or `https`, and the *proxyhost* property accepts a fully qualified domain name or IP address of the proxy server. 

For example: `https://proxy01.contoso.com:30443`

If authentication is required in either case, you need to specify the username and password. For example: `https://user01:password@proxy01.contoso.com:30443`

1. To configure the Linux computer to connect to a Log Analytics workspace, run the following command providing the workspace ID and primary key. The following command downloads the agent, validates its checksum, and installs it.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    The following command includes the `-p` proxy parameter and example syntax when authentication is required by your proxy server:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. To configure the Linux computer to connect to Log Analytics workspace in Azure Government cloud, run the following command providing the workspace ID and primary key copied earlier. The following command downloads the agent, validates its checksum, and installs it. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    The following command includes the `-p` proxy parameter and example syntax when authentication is required by your proxy server:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Restart the agent by running the following command: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## Install the agent manually

The Log Analytics agent for Linux is provided in a self-extracting and installable shell script bundle. This bundle contains Debian and RPM packages for each of the agent components and can be installed directly or extracted to retrieve the individual packages. One bundle is provided for x64 and one for x86 architectures. 

> [!NOTE]
> For Azure VMs, we recommend you install the agent on them using the [Azure Log Analytics VM extension](../../virtual-machines/extensions/oms-linux.md) for Linux. 

1. [Download](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) and transfer the appropriate bundle (x64 or x86) to your Linux VM or physical computer, using scp/sftp.

2. Install the bundle by using the `--install` argument. To onboard to a Log Analytics workspace during installation, provide the `-w <WorkspaceID>` and `-s <workspaceKey>` parameters copied earlier.

    >[!NOTE]
    >You need to use the `--upgrade` argument if any dependent packages such as omi, scx, omsconfig or their older versions are installed, as would be the case if the system Center Operations Manager agent for Linux is already installed. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. To configure the Linux agent to install and connect to a Log Analytics workspace through a Log Analytics gateway, run the following command providing the proxy, workspace ID, and workspace key parameters. This configuration can be specified on the command line by including `-p [protocol://][user:password@]proxyhost[:port]`. The *proxyhost* property accepts a fully qualified domain name or IP address of the Log Analytics gateway server.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    If authentication is required, you need to specify the username and password. For example: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. To configure the Linux computer to connect to a Log Analytics workspace in Azure Government cloud, run the following command providing the workspace ID and primary key copied earlier.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

If you want to install the agent packages and configure it to report to a specific Log Analytics workspace at a later time, run the following command:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

If you want to extract the agent packages from the bundle without installing the agent, run the following command:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## Upgrade from a previous release

Upgrading from a previous version, starting with version 1.0.0-47, is supported in each release. Perform the installation with the `--upgrade` parameter to upgrade all components of the agent to the latest version.

## Cache information
Data from the Log Analytics agent for Linux is cached on the local machine at *%STATE_DIR_WS%/out_oms_common*.buffer* before it's sent to Azure Monitor. Custom log data is buffered in *%STATE_DIR_WS%/out_oms_blob*.buffer*. The path may be different for some [solutions and data types](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=).

The agent attempts to upload every 20 seconds. If it fails, it will wait an exponentially increasing length of time until it succeeds: 30 seconds before the second attempt, 60 seconds before the third, 120 seconds ... and so on up to a maximum of 16 minutes between retries until it successfully connects again. The agent will retry up to 6 times for a given chunk of data before discarding and moving to the next one. This continues until the agent can successfully upload again. This means that data may be buffered up to approximately 30 minutes before being discarded.

The default cache size is 10 MB but can be modified in the [omsagent.conf file](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf).


## Next steps

- Review [Managing and maintaining the Log Analytics agent for Windows and Linux](agent-manage.md) to learn about how to reconfigure, upgrade, or remove the agent from the virtual machine.

- Review [Troubleshooting the Linux agent](agent-linux-troubleshoot.md) if you encounter issues while installing or managing the agent.
