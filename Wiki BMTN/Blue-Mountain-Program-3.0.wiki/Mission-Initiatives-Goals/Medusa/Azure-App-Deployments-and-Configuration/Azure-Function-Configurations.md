# Getting Started
There are two types of runtime stack environments in the deployed function apps, Node.js and PowerShell Core.

---
## Identity
System assigned identity should be enabled for both apps.

## Storage Account - Access Control (IAM)
With the previously enabled system assigned identities, add them via Access Control (IAM) on the storage account that is used for document storage/processing by the Azure Functions.  The following role assignments should be added:

| Name | Role |
|--|--|
| _PowerShell Core App System Assigned Identity_ | Storage Account Contributor |
| _PowerShell Core App System Assigned Identity_  | Storage Blob Data Contributor |
| _Node.js App System Assigned Identity_ | Storage Account Contributor |

## SQL Server Managed Identity Access

1. Start SQL Server Management Studio (SSMS)
2. Connect to the SQL Server used to store metadata
1. In the Object Explorer, expand the Databases folder.
2. Right-click on a user database and click New query.
3. In the query window, enter the following line, and click Execute in the toolbar:

```sql
CREATE USER [name of Node.js function app] FROM EXTERNAL PROVIDER
GO
```

The command should complete successfully, creating the contained user for the function app's system-assigned identity.

4. Clear the query window, enter the following line, and click Execute in the toolbar:
```sql
ALTER ROLE db_datareader ADD MEMBER [name of Node.js function app]
GO
```
The command should complete successfully, granting the contained user the ability to read the entire database.

> **Reference**
> [Tutorial: Use a Windows VM system-assigned managed identity to access Azure SQL](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)
## PowerShell Core App Configuration 

### Application Settings

| Name | Value |
|--|--|
| AzureWebJobsStorage | _Storage Account Connection String for Blob Trigger_ |

### Connection Strings

| Name | Value |
|--|--|
| StorageConnectionString | _Storage Account Connection String for PDF Storage_ |
---

## Node.js App Configuration

### Application Settings

| Name | Value |
|--|--|
| AzureWebJobsStorage | _Storage Account Connection String for Blob Trigger_ |
| SQL_SERVER | _SQL Server name for stored metadata_ |
| SQL_DATABASE | _SQL Database name for stored metadata_ |
| STORAGE_ACCOUNT | _Display name of storage account_ |
