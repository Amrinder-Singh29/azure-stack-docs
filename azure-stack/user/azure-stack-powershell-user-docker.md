---
title: Use Docker to run PowerShell in Azure Stack | Microsoft Docs
description: Use Docker to run PowerShell in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''

ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019

---
# Use Docker to run PowerShell in Azure Stack

In this article, you use Docker to create Windows-based containers on which to run the version of PowerShell that's required for working with the various interfaces. In Docker, you must use Windows-based containers.

## Docker prerequisites

1. Install [Docker](https://docs.docker.com/install/).

1. In a command-line program, such as Powershell or Bash, enter:

    ```bash
        Docker -version
    ```

1. You need to run Docker by using Windows containers that require Windows 10. When you run Docker, switch to Windows containers.

1. Run Docker from a machine that's joined to the same domain as Azure Stack. If you are using the Azure Stack Development Kit (ASDK), you need to install [the VPN on your remote machine](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## Set up a service principal for using PowerShell

To use PowerShell to access resources in Azure Stack, you need a service principal in your Azure Active Directory (Azure AD) tenant. You delegate permissions with user role-based access control (RBAC).

1. To set up your service principal, follow the instructions in [Give applications access to Azure Stack resources by creating service principals](azure-stack-create-service-principals.md).

2. Note the application ID, the secret, and your tenant ID for later use.

## Docker - Azure Stack API profiles module

The Dockerfile opens the Microsoft image *microsoft/windowsservercore*, which has Windows PowerShell 5.1 installed. The file then loads NuGet and the Azure Stack PowerShell modules, and downloads the tools from Azure Stack Tools.

1. [Download the azure-stack-powershell repository](https://github.com/mattbriggs/azure-stack-powershell) as a ZIP file, or clone the repository.

2. Open the repository folder from your terminal.

3. Open a command-line interface in your repository, and then enter the following command:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. When the image has been built, start an interactive container by entering:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. The shell is ready for your cmdlets.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Connect to your Azure Stack instance by using the service principal. You are now using a PowerShell prompt in Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell returns your account object:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Test your connectivity by creating a resource group in Azure Stack.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## Next steps

-  Read an overview of [Azure Stack PowerShell in Azure Stack](azure-stack-powershell-overview.md).
- Read about [API profiles for PowerShell](azure-stack-version-profiles.md) in Azure Stack.
- Install [Azure Stack Powershell](../operator/azure-stack-powershell-install.md).
- Read about creating [Azure Resource Manager templates](azure-stack-develop-templates.md) for cloud consistency.
