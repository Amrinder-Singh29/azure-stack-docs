---
title: Update the MySQL resource provider in Azure Stack Hub 
description: Learn how to update the Azure Stack Hub MySQL resource provider in Azure Stack Hub. 
author: bryanla
ms.topic: article 
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020

# Intent: As an Azure Stack operator, I want to update the Azure Stack MySQL resource provider.
# Keyword: azure stack update mysql resource provider

---


# Update the MySQL resource provider in Azure Stack Hub

> [!IMPORTANT]
> Before updating the resource provider, review the release notes to learn about new functionality, fixes, and any known issues that could affect your deployment. The release notes also specify the minimum Azure Stack Hub version required for the resource provider.

> [!IMPORTANT]
> Updating the resource provider will NOT update the hosting MySQL Server. 

A new MySQL resource provider adapter might be released when Azure Stack Hub builds are updated. While the existing adapter continues to work, we recommend updating to the latest build as soon as possible.

  |Supported Azure Stack Hub version|MySQL RP version|Windows Server that RP service is running on
  |-----|-----|-----|
  |2102, 2008, 2005|[MySQL RP version 1.1.93.5](https://aka.ms/azshmysqlrp11935)|Microsoft AzureStack Add-on RP Windows Server
  |2005, 2002, 1910|[MySQL RP version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter - Server Core|
  |1908|[MySQL RP version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter - Server Core|
  |     |     |     |

MySQL resource provider update is cumulative. When updating from an old version, you can directly update to the latest version. 

To update the resource provider, you use the **UpdateMySQLProvider.ps1** script. Use your service account with local administrative rights and is an **owner** of the subscription. The update script is included with the download of the resource provider. 

The update process is similar to the process used to [Deploy the resource provider](./azure-stack-mysql-resource-provider-deploy.md). The update script uses the same arguments as the DeployMySqlProvider.ps1 script, and you'll need to provide certificate information.

## Update script processes

The **UpdateMySQLProvider.ps1** script creates a new virtual machine (VM) with the latest OS image, deploy the latest resource provider code, and migrates the settings from the old resource provider to the new resource provider.

>[!NOTE]
>We recommend that you download the latest Windows Server 2016 Core image or Microsoft AzureStack Add-on RP Windows Server image from Marketplace Management. If you need to install an update, you can place a **single** MSU package in the local dependency path. The script will fail if there's more than one MSU file in this location.

After the *UpdateMySQLProvider.ps1* script creates a new VM, the script migrates the following settings from the old resource provider VM:

* database information
* hosting server information
* required DNS record

> [!IMPORTANT]
> We strongly recommend using **Clear-AzureRmContext -Scope CurrentUser** and **Clear-AzureRmContext -Scope Process** to clear the cache before running the deployment or update script.

## Update script parameters 
Specify the following parameters from the command line when you run the **UpdateMySQLProvider.ps1** PowerShell script. If you don't, or if any parameter validation fails, you're prompted to provide the required parameters.

| Parameter Name | Description | Comment or default value | 
| --- | --- | --- | 
| **CloudAdminCredential** | The credential for the cloud admin, necessary for accessing the privileged endpoint. | _Required_ | 
| **AzCredential** | The credentials for the Azure Stack Hub service admin account. Use the same credentials that you used for deploying Azure Stack Hub. The script will fail if the account you use with AzCredential requires multi-factor authentication (MFA). | _Required_ | 
| **VMLocalCredential** |The credentials for the local admin account of the SQL resource provider VM. | _Required_ | 
| **PrivilegedEndpoint** | The IP address or DNS name of the privileged endpoint. |  _Required_ | 
| **AzureEnvironment** | The Azure environment of the service admin account used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are **AzureCloud**, **AzureUSGovernment**, or if using a China Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Your certificate .pfx file must be placed in this directory as well. | _Optional_ (_mandatory_ for multi-node) | 
| **DefaultSSLCertificatePassword** | The password for the .pfx certificate. | _Required_ | 
| **MaxRetryCount** | The number of times you want to retry each operation if there's a failure.| 2 | 
| **RetryDuration** | The timeout interval between retries, in seconds. | 120 | 
| **Uninstall** | Remove the resource provider and all associated resources (see the following notes). | No | 
| **DebugMode** | Prevents automatic cleanup on failure. | No | 
| **AcceptLicense** | Skips the prompt to accept the GPL license.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## Update script example

If you are updating the MySQL resource provider version to 1.1.33.0 or previous versions, you need to install specific versions of AzureRm.BootStrapper and Azure Stack Hub modules in PowerShell. 

If you are updating the MySQL resource provider to version 1.1.47.0 or later, you can skip this step. The deployment script will automatically download and install the necessary PowerShell modules for you to path C:\Program Files\SqlMySqlPsh. 

>[!NOTE]
>If folder C:\Program Files\SqlMySqlPsh already exists with PowerShell module downloaded, it is recommended to clean up this folder before running the update script. This is to make sure the right version of PowerShell module is downloaded and used.

```powershell 
# Run the following scripts when updating to version 1.1.33.0 only.
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> In disconnected scenario, you need to download the required PowerShell modules and register the repository manually as a prerequisite. You can get more information in [Deploy MySQL resource provider](azure-stack-mysql-resource-provider-deploy.md)

The following example shows the *UpdateMySQLProvider.ps1* script that you can run from an elevated PowerShell console. Be sure to change the variable information and passwords as needed:

```powershell 
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force 

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
.$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
-VMLocalCredential $vmLocalAdminCreds `
-CloudAdminCredential $cloudAdminCreds `
-PrivilegedEndpoint $privilegedEndpoint `
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass `
-DependencyFilesLocalPath $tempDir\cert `
-AcceptLicense
```  

When the resource provider update script finishes, close the current PowerShell session.

## Next steps
[Maintain MySQL resource provider](azure-stack-mysql-resource-provider-maintain.md)
