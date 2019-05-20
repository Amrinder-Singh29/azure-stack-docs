---
title: Deploy templates with the command line in Azure Stack | Microsoft Docs
description: Learn how to use the cross-platform command-line interface (CLI) to deploy templates to Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''

ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019

---
# Deploy templates in Azure Stack using the command line

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

Use the Azure Command-Line Interface (CLI) to deploy Azure Resource Manager templates in Azure Stack. Azure Resource Manager templates deploy and provision resources for your app in a single, coordinated operation.

## Before you begin

- [Install and connect](azure-stack-version-profiles-azurecli2.md) to Azure Stack with Azure CLI.
- Download the files *azuredeploy.json* and *azuredeploy.parameters.json* from the [create storage account example template](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## Deploy template

Navigate to the folder into which these files were downloaded, and run the following command to deploy the template:

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

This command deploys the template to the resource group **cliRG** in the Azure Stack POC default location.

## Validate template deployment

To see this resource group and storage account, use the following CLI commands:

```azurecli
az group list

az storage account list
```

## Next steps

Learn more about [deploying templates by using PowerShell](azure-stack-deploy-template-powershell.md).
