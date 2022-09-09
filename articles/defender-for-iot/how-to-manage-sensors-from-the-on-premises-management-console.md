---
title: Manage sensors from the on-premises management console 
description: Learn how to manage sensors from the management console, including updating sensor versions, pushing system settings to sensors, and enabling and disabling engines on sensors.
ms.date: 12/07/2020
ms.topic: how-to
---

# Manage sensors from the management console

This article describes how to manage sensors from the management console, including:

- Push system settings to sensors

- Enable and disable engines on sensors

- Update sensor versions

## Push configurations

You can define various system settings and automatically apply them to sensors that are connected to the management console. This saves time and helps ensure streamlined settings across your enterprise sensors.

You can define the following sensor system settings from the management console:

- Mail server

- SNMP MIB monitoring

- Active Directory

- DNS settings

- Subnets

- Port aliases

To apply system settings:

1. On the console's left pane, select **System Settings**.

2. On the **Configure Sensors** pane, select one of the options.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="The system setting options for a sensor.":::

   The following example describes how to define mail server parameters for your enterprise sensors.

3. Select **Mail Server**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Select your mail server from the System Settings screen.":::

4. Select a sensor on the left.

5. Set the mail server parameters and select **Duplicate**. Each item in the sensor tree appears with a check box next to it.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Ensure the check boxes are selected for your sensors.":::

6. In the sensor tree, select the items to which you want to apply the configuration.

7. Select **Save**.

## Update versions

You can update several sensors simultaneously from the on-premises management console.

To update several sensors:

1. Go to the [Azure portal](https://portal.azure.com/).

2. Go to Azure Defender for IoT.

3. Go to the **Updates** page.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Screenshot of the Updates dashboard view.":::

4. Select **Download** from the **Sensors** section and save the file.

5. Sign in to the management console and select **System Settings**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Screenshot of the Administration menu to select System Settings.":::

6. Mark the sensors that you want to update in the **Sensor Engine Configuration** section, and then select **Automatic Updates**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Two sensors showing learning mode and automatic updates.":::

7. Select **Save Changes**.

8. On the **Sensors Version upgrade** pane, select :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false":::.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Sensor version upgrade screen to display files.":::

9. An **Upload File** dialog box opens. Upload the file that you downloaded from the **Updates** page.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Select the Browse button to upload your file.":::

10. During the update process, the update status of each sensor appears in the **Site Management** window.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Observe the progress of your update.":::

## Update threat intelligence packages 

The data package for threat intelligence is provided with each new Defender for IoT version, or if needed between releases. The package contains signatures (including malware signatures), CVEs, and other security content. 

You can manually upload this file from the Defender for IoT portal's **Updates** page and automatically update it to sensors. 

To update the threat intelligence data: 

1. Go to the Defender for IoT **Updates** page. 

2. Download and save the file.

3. Sign in to the management console. 

4. On the side menu, select **System Settings**. 

5. Select the sensors that should receive the update in the **Sensor Engine Configuration** section.  

6. In the **Select Threat Intelligence Data** section, select the plus sign (**+**). 

7. Upload the package that you downloaded from the Defender for IoT **Updates** page.

## Understand sensor disconnection events

The **Site Manager** window displays disconnection information if sensors disconnect from their assigned on-premises management console. The following sensor disconnection information is available:

- "The on-premises management console cannot process data received from the sensor."

- "Times drift detected. The on-premises management console has been disconnected from sensor."

- "Sensor not communicating with on-premises management console. Check network connectivity or certificate validation."

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Screenshot of the zone 1 view.":::

You can send alerts to third parties with information about disconnected sensors. For more information, see [Forward sensor failure alerts](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## Enable or disable sensors

Sensors are protected by five Defender for IoT engines. You can enable or disable the engines for connected sensors.

| Engine | Description | Example scenario |
|--|--|--|
| Protocol violation engine | A protocol violation occurs when the packet structure or field values don't comply with the protocol specification. | "Illegal MODBUS Operation (Function Code Zero)" alert. This alert indicates that a primary device sent a request with function code 0 to a secondary device. This is not allowed according to the protocol specification, and the secondary device might not handle the input correctly. |
| Policy violation engine | A policy violation occurs with a deviation from baseline behavior defined in the learned or configured policy. | "Unauthorized HTTP User Agent" alert. This alert indicates that an application that was not learned or approved by the policy is used as an HTTP client on a device. This might be a new web browser or application on that device. |
| Malware engine | The malware engine detects malicious network activity. | "Suspicion of Malicious Activity (Stuxnet)" alert. This alert indicates that the sensor found suspicious network activity known to be related to the Stuxnet malware, which is an advanced persistent threat aimed at industrial control and SCADA networks. |
| Anomaly engine | The malware engine detects an anomaly in network behavior. | "Periodic Behavior in Communication Channel." This is a component that inspects network connections and finds periodic or cyclic behavior of data transmission, which is common in industrial networks. |
| Operational engine | This engine detects operational incidents or malfunctioning entities. | `Device is Suspected to be Disconnected (Unresponsive)` alert. This alert triggered when a device is not responding to any requests for a predefined period. It might indicate a device shutdown, disconnection, or malfunction.
|

To enable or disable engines for connected sensors:

1. In the console's left pane, select **System Settings**.

2. In the **Sensor Engine Configuration** section, select **Enable** or **Disable** for the engines.
 	 	 
3. Select **SAVE CHANGES**.

   A red exclamation mark appears if there's a mismatch of enabled engines on one of your enterprise sensors. The engine might have been disabled directly from the sensor.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Mismatch of enabled engines."::: 

## Define sensor backup schedules

You can schedule sensor backups and perform on-demand sensor backups from the on-premises management console. This helps protect against hard drive failures and data loss.

- What is backed up: Configurations and data.

- What isn't backed up: PCAP files and logs. You can manually back up and restore PCAPs and logs.

By default, sensors are automatically backed up at 3:00 AM daily. The backup schedule feature for sensors lets you collect these backups and store them on the on-premises management console or on an external backup server. Copying files from sensors to the on-premises management console happens over an encrypted channel.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="A view of the sensor backup screen.":::

When the default sensor backup location is changed, the on-premises management console automatically retrieves the files from the new location on the sensor or an external location, provided that the console has permission to access the location. 

When the sensors are not registered with the on-premises management console, the **Sensor Backup Schedule** dialog box indicates that no sensors are managed.  

The restore process is the same regardless of where the files are stored.

### Backup storage for sensors

You can use the on-premises management console to maintain up to nine backups for each managed sensor, provided that the backed-up files don't exceed the maximum backup space that's allocated. 

The available space is calculated based on the management console model you're working with: 

- **Production model**: Default storage is 40 GB; limit is 100 GB. 

- **Medium model**: Default storage is 20 GB; limit is 50 GB. 

- **Laptop model**: Default storage is 10 GB; limit is 25 GB. 

- **Thin model**: Default storage is 2 GB; limit is 4 GB. 

- **Rugged model**: Default storage is 10 GB; limit is 25 GB. 

The default allocation is displayed in the **Sensor Backup Schedule** dialog box. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="The Edit Mail Server Configuration screen.":::

There is no storage limit when you're backing up to an external server. You must, however, define an upper allocation limit in the **Sensor Backup Schedule** > **Custom Path** field. The following numbers and characters are supported: `/, a-z, A-Z, 0-9, and _`. 

Here's information about exceeding allocation storage limits:

- If you exceed the allocated storage space, the sensor is not backed up. 

- If you're backing up more than one sensor, the management console tries to retrieve sensor files for the managed sensors.  

- If the retrieval from one sensor exceeds the limit, the management console tries to retrieve backup information from the next sensor. 

When you exceed the retained number of backups defined, the oldest backed-up file is deleted to accommodate the new one.

Sensor backup files are automatically named in the following format: `<sensor name>-backup-version-<version>-<date>.tar`. For example: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

To back up sensors:

1. Select **Schedule Sensor Backup** from the **System Settings** window. Sensors that your on-premises management console manages appear in the **Sensor Backup Schedule** dialog box.  

2. Enable the **Collect Backups** toggle.  

3. Select a calendar interval, date, and time zone. The time format is based on a 24-hour clock. For example, enter 6:00 PM as **18:00**. 

4. In the **Backup Storage Allocation** field, enter the storage that you want to allocate for your backups. You're notified if you exceed the maximum space.

5. In the **Retain Last** field, indicate the number of backups per sensor you want to retain. When the limit is exceeded, the oldest backup is deleted.  

6. Choose a backup location:  

   - To back up to the on-premises management console, disable the **Custom Path** toggle. The default location is `/var/cyberx/sensor-backups`.  

   - To back up to an external server, enable the **Custom Path** toggle and enter a location. The following numbers and characters are supported: `/, a-z, A-Z, 0-9, and, _`. 

7. Select **Save**. 

To back up immediately: 

- Select **Back Up Now**. The on-premises management console creates and collects sensor backup files. 

### Receiving backup notifications for sensors 

The **Sensor Backup Schedule** dialog box and the backup log automatically list information about backup successes and failures.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="View your sensors and where they are located and all relevant information.":::

Failures might occur because:    

- No backup file is found. 

- A file was found but can't be retrieved.  

- There's a network connection failure. 

- There's not enough room allocated to the on-premises management console to complete the backup.  

You can send an email notification, syslog updates, and system notifications when a failure occurs. To do this, create a forwarding rule in **System Notifications**. 

### Restoring sensors 

You can restore backups from the on-premises management console and by using the CLI.  

To restore from the console: 

- Select **Restore Image** from the **Sensor System** setting window.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Restore a backup of your image.":::

  The console then displays restore failures.  

To restore by using the CLI: 

- Sign in to an administrative account and enter `$ sudo cyberx-xsense-system-restore`. 

### Save a sensor backup to an external SMB server

To set up an SMB server so you can save a sensor backup to an external drive: 

1. Create a shared folder in the external SMB server. 

2. Get the folder path, username, and password required to access the SMB server. 

3. In Defender for IoT, make a directory for the backups: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Edit fstab:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Edit or create credentials to share. These are the credentials for the SMB server: 

   `sudo nano /etc/samba/user` 

6. Add:  

   `username=<user name>` 

   `password=<password>` 

7. Mount the directory: 

   `sudo mount -a` 

8. Configure a backup directory to the shared folder on the Defender for IoT sensor:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Set `Backup.shared_location` to `<backup_folder_name_on_cyberx_server>`.

## See also

[Manage individual sensors](how-to-manage-individual-sensors.md)
