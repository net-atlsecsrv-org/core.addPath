---
title: Troubleshoot common errors - Azure Database for MySQL
description: Learn how to troubleshoot common migration errors encounters by users new to the Azure Database for MySQL service
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
---

# Common errors

Azure Database for MySQL is a fully managed service powered by the community version of MySQL. The MySQL experience in a managed service environment may differ from running MySQL in your own environment. In this article, you will see some of the common errors users may encounter while migrating to or developing on Azure Database for MySQL service for the first time.

## Errors due to lack of SUPER privilege and DBA role

The SUPER privilege and DBA role are not supported on the service. As a result, you may encounter some common errors listed below:

#### ERROR 1419: You do not have the SUPER privilege and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)

The above error may occur while creating a function, trigger as below or importing a schema. The DDL statements like CREATE FUNCTION or CREATE TRIGGER are written to the binary log, so the secondary replica can execute them. The replica SQL thread has full privileges, which can be exploited to elevate privileges. To guard against this danger for servers that have binary logging enabled, MySQL engine requires stored function creators must have the SUPER privilege, in addition to the usual CREATE ROUTINE privilege that is required. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Resolution**:  To resolve the error, set log_bin_trust_function_creators to 1 from [server parameters](howto-server-parameters.md) blade in portal, execute the DDL statements or import the schema to create the desired objects and revert back the log_bin_trust_function_creators parameter to its previous value after creation.

#### ERROR 1227 (42000) at line 101: Access denied; you need (at least one of) the SUPER privilege(s) for this operation. Operation failed with exitcode 1

The above error may occur while importing a dump file or creating procedure that contains [definers](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Resolution**:  To resolve this error, the admin user can grant privileges to create or execute procedures by running GRANT command as in the following examples:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Alternatively, you can replace the definers with the name of the admin user that is running the import process as shown below.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### ERROR 1227 (42000) at line 295: Access denied; you need (at least one of) the SUPER or SET_USER_ID privilege(s) for this operation

The above error may occur while executing CREATE VIEW with DEFINER statements as part of importing a dump file or running a script. Azure Database for MySQL does not allow SUPER privileges or SET_USER_ID privilege to any user. 

**Resolution**: 
* Use the definer user to execute CREATE VIEW if possible. It is likely that there are many views with different definers having different permissions so this may not be feasible.  OR
* Edit the dump file or CREATE VIEW script and remove the DEFINER= statement from the dump file OR 
* Edit the dump file or CREATE VIEW script and replace the definer values with user with admin permissions who is performing the import or execute the script file.

> [!Tip] 
> Use sed or perl to modify a dump file or SQL script to replace the DEFINER= statement

## Common connection errors for server admin login

When an Azure Database for MySQL server is created, a server admin login is provided by the end user during the server creation. The server admin login allows you to create new databases, add new users and grant permissions. If the server admin login is deleted, its permissions are revoked or its password is changed, you may start to see connections errors in your application while connections. Following are some of the common errors

#### ERROR 1045 (28000): Access denied for user 'username'@'IP address' (using password: YES)

The above error occurs if:

* The username does not exists
* The user username was deleted
* its password is changed or reset.

**Resolution**: 
* Validate if "username" exists as a valid user in the server or is accidentally deleted. You can execute the following query by logging into the Azure Database for MySQL user:
  ```sql
  select user from mysql.user;
  ```
* If you cannot log in to the MySQL to execute the above query itself, we recommend you to [reset the admin password using Azure portal](howto-create-manage-server-portal.md). The reset password option from Azure portal will help recreate the user, reset the password and restore the admin permissions, which will allow you to log in using server admin and perform further operations.

## Next steps
If you did not find the answer you are looking for, consider following:

- Post your question on [Microsoft Q&A question page](/answers/topics/azure-database-mysql.html) or [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Send an email to the Azure Database for MySQL Team [@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). This email address is not a technical support alias.
- Contact Azure Support, [file a ticket from the Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). To fix an issue with your account, file a [support request](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) in the Azure portal.
- To provide feedback or to request new features, create an entry via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
