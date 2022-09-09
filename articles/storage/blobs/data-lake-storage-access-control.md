---
title: Overview of access control in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Understand how access control works in Azure Data Lake Storage Gen2. Azure role-based access control (Azure RBAC) and POSIX-like ACLs are supported.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: normesta
ms.reviewer: jamesbak
---

# Access control in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implements an access control model that supports both Azure role-based access control (Azure RBAC) and POSIX-like access control lists (ACLs). This article summarizes the basics of the access control model for Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac"></a>

## Azure role-based access control

Azure RBAC uses role assignments to effectively apply sets of permissions to *security principals*. A *security principal* is an object that represents a user, group, service principal, or managed identity that is defined in Azure Active Directory (AD) that is requesting access to Azure resources.

Typically, those Azure resources are constrained to top-level resources (For example: Azure Storage accounts). In the case of Azure Storage, and consequently Azure Data Lake Storage Gen2, this mechanism has been extended to the container (file system) resource.

To learn how to assign roles to security principals in the scope of your storage account, see [Use the Azure portal to assign an Azure role for access to blob and queue data](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> A guest user can't create a role assignment.

### The impact of role assignments on file and directory level access control lists

While using Azure role assignments is a powerful mechanism to control access permissions, it is a very coarsely grained mechanism relative to ACLs. The smallest granularity for Azure RBAC is at the container level and this will be evaluated at a higher priority than ACLs. Therefore, if you assign a role to a security principal in the scope of a container, that security principal has the authorization level associated with that role for ALL directories and files in that container, regardless of ACL assignments.

When a security principal is granted Azure RBAC data permissions through a [built-in role](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), or through a custom role, these permissions are evaluated first upon authorization of a request. If the requested operation is authorized by the security principal's Azure role assignments then authorization is immediately resolved and no additional ACL checks are performed. Alternatively, if the security principal does not have an Azure role assignment, or the request's operation does not match the assigned permission, then ACL checks are performed to determine if the security principal is authorized to perform the requested operation.

> [!NOTE]
> If the security principal has been assigned the Storage Blob Data Owner built-in role assignment, then the security principal is considered a *super-user* and is granted full access to all mutating operations, including setting the owner of a directory or file as well as ACLs for directories and files for which they are not the owner. Super-user access is the only authorized manner to change the owner of a resource.

## Shared Key and Shared Access Signature (SAS) authentication

Azure Data Lake Storage Gen2 supports Shared Key and SAS methods for authentication. A characteristic of these authentication methods is that no identity is associated with the caller and therefore security principal permission-based authorization cannot be performed.

In the case of Shared Key, the caller effectively gains 'super-user' access, meaning full access to all operations on all resources, including setting owner and changing ACLs.

SAS tokens include allowed permissions as part of the token. The permissions included in the SAS token are effectively applied to all authorization decisions, but no additional ACL checks are performed.

## Access control lists on files and directories

You can associate a security principal with an access level for files and directories. These associations are captured in an *access control list (ACL)*. Each file and directory in your storage account has an access control list.

> [!NOTE]
> ACLs apply only to security principals in the same tenant. 

If you assigned a role to a security principal at the storage account-level, you can use access control lists to grant that security principal elevated access to specific files and directories.

You can't use access control lists to provide a level of access that is lower than a level granted by a role assignment. For example, if you assign the [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) role to a security principal, then you can't use access control lists to prevent that security principal from writing to a directory.


### Set file and directory level permissions by using access control lists

To set file and directory level permissions, see any of the following articles:

| Environment | Article |
|--------|-----------|
|Azure Storage Explorer |[Use Azure Storage Explorer to manage directories, files, and ACLs in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[Use .NET to manage directories, files, and ACLs in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Use Java to manage directories, files, and ACLs in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
|Python|[Use Python to manage directories, files, and ACLs in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
|PowerShell|[Use PowerShell to manage directories, files, and ACLs in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md)|
|Azure CLI|[Use Azure CLI to manage directories, files, and ACLs in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md)|
|REST API |[Path - Update](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> If the security principal is a *service* principal, it's important to use the object ID of the service principal and not the object ID of the related app registration. To get the object ID of the service principal open the Azure CLI, and then use this command: `az ad sp show --id <Your App ID> --query objectId`. make sure to replace the `<Your App ID>` placeholder with the App ID of your app registration.

### Types of access control lists

There are two kinds of access control lists: *access ACLs* and *default ACLs*.

Access ACLs control access to an object. Files and directories both have access ACLs.

Default ACLs are templates of ACLs associated with a directory that determine the access ACLs for any child items that are created under that directory. Files do not have default ACLs.

Both access ACLs and default ACLs have the same structure.

> [!NOTE]
> Changing the default ACL on a parent does not affect the access ACL or default ACL of child items that already exist.

### Levels of permission

The permissions on a container object are **Read**, **Write**, and **Execute**, and they can be used on files and directories as shown in the following table:

|            |    File     |   Directory |
|------------|-------------|----------|
| **Read (R)** | Can read the contents of a file | Requires **Read** and **Execute** to list the contents of the directory |
| **Write (W)** | Can write or append to a file | Requires **Write** and **Execute** to create child items in a directory |
| **Execute (X)** | Does not mean anything in the context of Data Lake Storage Gen2 | Required to traverse the child items of a directory |

> [!NOTE]
> If you are granting permissions by using only ACLs (no Azure RBAC), then to grant a security principal read or write access to a file, you'll need to give the security principal **Execute** permissions to the container, and to each folder in the hierarchy of folders that lead to the file.

#### Short forms for permissions

**RWX** is used to indicate **Read + Write + Execute**. A more condensed numeric form exists in which **Read=4**, **Write=2**, and **Execute=1**, the sum of which represents the permissions. Following are some examples.

| Numeric form | Short form |      What it means     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Read + Write + Execute |
| 5            | `R-X`        | Read + Execute         |
| 4            | `R--`        | Read                   |
| 0            | `---`        | No permissions         |

#### Permissions inheritance

In the POSIX-style model that's used by Data Lake Storage Gen2, permissions for an item are stored on the item itself. In other words, permissions for an item cannot be inherited from the parent items if the permissions are set after the child item has already been created. Permissions are only inherited if default permissions have been set on the parent items before the child items have been created.

### Common scenarios related to permissions

The following table lists some common scenarios to help you understand which permissions are needed to perform certain operations on a storage account.

|    Operation             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Write permissions on the file are not required to delete it, so long as the previous two conditions are true.

### Users and identities

Every file and directory has distinct permissions for these identities:

- The owning user
- The owning group
- Named users
- Named groups
- Named service principals
- Named managed identities
- All other users

The identities of users and groups are Azure Active Directory (Azure AD) identities. So unless otherwise noted, a *user*, in the context of Data Lake Storage Gen2, can refer to an Azure AD user, service principal, managed identity, or security group.

#### The owning user

The user who created the item is automatically the owning user of the item. An owning user can:

* Change the permissions of a file that is owned.
* Change the owning group of a file that is owned, as long as the owning user is also a member of the target group.

> [!NOTE]
> The owning user *cannot* change the owning user of a file or directory. Only super-users can change the owning user of a file or directory.

#### The owning group

In the POSIX ACLs, every user is associated with a *primary group*. For example, user "Alice" might belong to the "finance" group. Alice might also belong to multiple groups, but one group is always designated as their primary group. In POSIX, when Alice creates a file, the owning group of that file is set to her primary group, which in this case is "finance." The owning group otherwise behaves similarly to assigned permissions for other users/groups.

##### Assigning the owning group for a new file or directory

* **Case 1**: The root directory "/". This directory is created when a Data Lake Storage Gen2 container is created. In this case, the owning group is set to the user who created the container if it was done using OAuth. If the container is created using Shared Key, an Account SAS, or a Service SAS, then the owner and owning group are set to **$superuser**.
* **Case 2** (Every other case): When a new item is created, the owning group is copied from the parent directory.

##### Changing the owning group

The owning group can be changed by:
* Any super-users.
* The owning user, if the owning user is also a member of the target group.

> [!NOTE]
> The owning group cannot change the ACLs of a file or directory.  While the owning group is set to the user who created the account in the case of the root directory, **Case 1** above, a single user account isn't valid for providing permissions via the owning group. You can assign this permission to a valid user group if applicable.

### Access check algorithm

The following pseudocode represents the access check algorithm for storage accounts.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
mask = get_mask( path )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    if ((desired_perms & entry.permissions & mask) == desired_perms)
        return True 
        
# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### The mask

As illustrated in the Access Check Algorithm, the mask limits access for named users, the owning group, and named groups.  

> [!NOTE]
> For a new Data Lake Storage Gen2 container, the mask for the access ACL of the root directory ("/") defaults to 750 for directories and 640 for files. Files do not receive the X bit as it is irrelevant to files in a store-only system.
>
> The mask may be specified on a per-call basis. This allows different consuming systems, such as clusters, to have different effective masks for their file operations. If a mask is specified on a given request, it completely overrides the default mask.

#### The sticky bit

The sticky bit is a more advanced feature of a POSIX container. In the context of Data Lake Storage Gen2, it is unlikely that the sticky bit will be needed. In summary, if the sticky bit is enabled on a directory,  a child item can only be deleted or renamed by the child item's owning user.

The sticky bit isn't shown in the Azure portal.

### Default permissions on new files and directories

When a new file or directory is created under an existing directory, the default ACL on the parent directory determines:

- A child directory's default ACL and access ACL.
- A child file's access ACL (files do not have a default ACL).

#### umask

When creating a file or directory, umask is used to modify how the default ACLs are set on the child item. umask is a 9-bit value on parent directories that contains an RWX value for **owning user**, **owning group**, and **other**.

The umask for Azure Data Lake Storage Gen2 a constant value that is set to 007. This value translates to:

| umask component     | Numeric form | Short form | Meaning |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | For owning user, copy the parent's default ACL to the child's access ACL | 
| umask.owning_group  |    0         |   `---`      | For owning group, copy the parent's default ACL to the child's access ACL | 
| umask.other         |    7         |   `RWX`      | For other, remove all permissions on the child's access ACL |

The umask value used by Azure Data Lake Storage Gen2 effectively means that the value for **other** is never transmitted by default on new children, unless a default ACL is defined on the parent directory. In that case, the umask is effectively ignored and the permissions defined by the default ACL are applied to the child item. 

The following pseudocode shows how the umask is applied when creating the ACLs for a child item.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## Common questions about ACLs in Data Lake Storage Gen2

### Do I have to enable support for ACLs?

No. Access control via ACLs is enabled for a storage account as long as the Hierarchical Namespace (HNS) feature is turned ON.

If HNS is turned OFF, the Azure RBAC authorization rules still apply.

### What is the best way to apply ACLs?

Always use Azure AD security groups as the assigned principal in ACLs. Resist the opportunity to directly assign individual users or service principals. Using this structure will allow you to add and remove users or service principals without the need to reapply ACLs to an entire directory structure. Instead, you simply need to add or remove them from the appropriate Azure AD security group. Keep in mind that ACLs are not inherited and so reapplying ACLs requires updating the ACL on every file and subdirectory. 

### Which permissions are required to recursively delete a directory and its contents?

- The caller has 'super-user' permissions,

Or

- The parent directory must have Write + Execute permissions.
- The directory to be deleted, and every directory within it, requires Read + Write + Execute permissions.

> [!NOTE]
> You do not need Write permissions to delete files in directories. Also, the root directory "/" can never be deleted.

### Who is the owner of a file or directory?

The creator of a file or directory becomes the owner. In the case of the root directory, this is the identity of the user who created the container.

### Which group is set as the owning group of a file or directory at creation?

The owning group is copied from the owning group of the parent directory under which the new file or directory is created.

### I am the owning user of a file but I don't have the RWX permissions I need. What do I do?

The owning user can change the permissions of the file to give themselves any RWX permissions they need.

### Why do I sometimes see GUIDs in ACLs?

A GUID is shown if the entry represents a user and that user doesn't exist in Azure AD anymore. Usually this happens when the user has left the company or if their account has been deleted in Azure AD. Additionally, service principals and security groups do not have a User Principal Name (UPN) to identify them and so they are represented by their OID attribute (a guid).

### How do I set ACLs correctly for a service principal?

When you define ACLs for service principals, it's important to use the Object ID (OID) of the *service principal* for the app registration that you created. It's important to note that registered apps have a separate service principal in the specific Azure AD tenant. Registered apps have an OID that's visible in the Azure portal, but the *service principal* has another (different) OID.

To get the OID for the service principal that corresponds to an app registration, you can use the `az ad sp show` command. Specify the Application ID as the parameter. Here's an example on obtaining the OID for the service principal that corresponds to an app registration with App ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Run the following command in the Azure CLI:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID will be displayed.

When you have the correct OID for the service principal, go to the Storage Explorer **Manage Access** page to add the OID and assign appropriate permissions for the OID. Make sure you select **Save**.

### Does Data Lake Storage Gen2 support inheritance of ACLs?

Azure role assignments do inherit. Assignments flow from subscription, resource group, and storage account resources down to the container resource.

ACLs do not inherit. However, default ACLs can be used to set ACLs for child subdirectories and files created under the parent directory. 

### Where can I learn more about POSIX access control model?

* [POSIX Access Control Lists on Linux](https://www.linux.com/news/posix-acls-linux)
* [HDFS permission guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX FAQ](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL on Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL using access control lists on Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## See also

* [Overview of Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
