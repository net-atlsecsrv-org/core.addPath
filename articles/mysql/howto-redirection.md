---
title: Connect with redirection - Azure Database for MySQL
description: This article describes how you can configure you application to connect to Azure Database for MySQL with redirection.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
---

# Connect to Azure Database for MySQL with redirection

This topic explains how to connect an application your Azure Database for MySQL server with redirection mode. Redirection aims to reduce network latency between client applications and MySQL servers by allowing applications to connect directly to backend server nodes.

## Before you begin
Sign in to the [Azure portal](https://portal.azure.com). Create an Azure Database for MySQL server with engine version 5.6, 5.7, or 8.0. For details, refer to [How to create Azure Database for MySQL server from Portal](quickstart-create-mysql-server-database-using-azure-portal.md) or [How to create Azure Database for MySQL server using CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Redirection is currently only supported when **SSL is enabled** on your Azure Database for MySQL server. For details on how to configure SSL, see [Using SSL with Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure).

## PHP

Support for redirection in PHP applications is available through the [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) extension, developed by Microsoft. 

The mysqlnd_azure extension is available to add to PHP applications through PECL and it is highly recommended to install and configure the extension through the officially published [PECL package](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Support for redirection in the PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) extension is currently in preview.

### Redirection logic

>[!IMPORTANT]
> Redirection logic/behavior beginning version 1.1.0 was updated and **it is recommended to use version 1.1.0+**.

The redirection behavior is determined by the value of `mysqlnd_azure.enableRedirect`. The table below outlines the behavior of redirection based on the value of this parameter beginning in **version 1.1.0+**.

If you are using an older version of the mysqlnd_azure extension (version 1.0.0-1.0.3), the redirection behavior is determined by the value of `mysqlnd_azure.enabled`. The valid values are `off` (acts similarly as the behavior outlined in the table below) and `on` (acts like `preferred` in the table below).  

|**mysqlnd_azure.enableRedirect value**| **Behavior**|
|----------------------------------------|-------------|
|`off` or `0`|Redirection will not be used. |
|`on` or `1`|- If SSL is not enabled on the Azure Database for MySQL server, no connection will be made. The following error will be returned: *"mysqlnd_azure.enableRedirect is on, but SSL option is not set in connection string. Redirection is only possible with SSL."*<br>- If SSL is enabled on the MySQL server, but redirection is not supported on the server, the first connection is aborted and the following error is returned: *"Connection aborted because redirection is not enabled on the MySQL server or the network package doesn't meet redirection protocol."*<br>- If the MySQL server supports redirection, but the redirected connection failed for any reason, also abort the first proxy connection. Return the error of the redirected connection.|
|`preferred` or `2`<br> (default value)|- mysqlnd_azure will use redirection if possible.<br>- If the connection does not use SSL, the server does not support redirection, or the redirected connection fails to connect for any non-fatal reason while the proxy connection is still a valid one, it will fall back to the first proxy connection.|

The subsequent sections of the document will outline how to install the `mysqlnd_azure` extension using PECL and set the value of this parameter.

### Ubuntu Linux

#### Prerequisites 
- PHP versions 7.2.15+ and 7.3.2+
- PHP PEAR 
- php-mysql
- Azure Database for MySQL server with SSL enabled

1. Install [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) with [PECL](https://pecl.php.net/package/mysqlnd_azure). It is recommended to use version 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Locate the extension directory (`extension_dir`) by running the below:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Change directories to the returned folder and ensure `mysqlnd_azure.so` is located in this folder. 

4. Locate the folder for .ini files by running the below: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Change directories to this returned folder. 

6. Create a new .ini file for `mysqlnd_azure`. Make sure the alphabet order of the name is after that of mysqnld, since the modules are loaded according to the name order of the ini files. For example, if `mysqlnd` .ini is named `10-mysqlnd.ini`, name the mysqlnd ini as `20-mysqlnd-azure.ini`.

7. Within the new .ini file, add the following lines to enable redirection.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### Windows

#### Prerequisites 
- PHP versions 7.2.15+ and 7.3.2+
- php-mysql
- Azure Database for MySQL server with SSL enabled

1. Determine if you are running a x64 or x86 version of PHP by running the following command:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Download the corresponding x64 or x86 version of the [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL from [PECL](https://pecl.php.net/package/mysqlnd_azure) that matches your version of PHP. It is recommended to use version 1.1.0+.

3. Extract the zip file and find the DLL named `php_mysqlnd_azure.dll`.

4. Locate the extension directory (`extension_dir`) by running the below command:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copy the `php_mysqlnd_azure.dll` file into the directory returned in step 4. 

6. Locate the PHP folder containing the `php.ini` file using the following command:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modify the `php.ini` file and add the following extra lines to enable redirection. 

    Under the Dynamic Extensions section: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Under the Module Settings section:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### Confirm redirection

You can also confirm redirection is configured with the below sample PHP code. Create a PHP file called `mysqlConnect.php` and paste the below code. Update the server name, username, and password with your own. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## Next steps
For more information about connection strings, see [Connection Strings](howto-connection-string.md).
