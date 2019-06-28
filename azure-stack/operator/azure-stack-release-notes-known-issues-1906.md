---
title: Azure Stack 1906 known issues | Microsoft Docs
description: Learn about known issues in Azure Stack 1906.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''

ms.assetid:  
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/27/2019
---

# Azure Stack 1906 known issues

This article lists known issues in the 1906 release of Azure Stack. The list is updated as new issues are identified.

> [!IMPORTANT]  
> Review this section before applying the update.

## Update process

- Applicable: This issue applies to all supported releases.
- Cause: When attempting to install the 1906 Azure Stack update, the status for the update might fail and change state to **PreparationFailed**. This is caused by the update resource provider (URP) being unable to properly transfer the files from the storage container to an internal infrastructure share for processing. 
- Remediation: Starting with version 1901 (1.1901.0.95), you can work around this issue by clicking **Update now** again (not **Resume**). The URP then cleans up the files from the previous attempt, and restarts the download. If the problem persists, we recommend manually uploading the update package by following the [Import and install updates section](azure-stack-apply-updates.md#import-and-install-updates).
- Occurrence: Common

## Portal

### Administrative subscriptions

- Applicable: This issue applies to all supported releases.
- Cause: The two administrative subscriptions that were introduced with version 1804 should not be used. The subscription types are **Metering** subscription, and **Consumption** subscription.
- Remediation: If you have resources running on these two subscriptions, recreate them in user subscriptions.
- Occurrence: Common

### Subscription resources

- Applicable: This issue applies to all supported releases.
- Cause: Deleting user subscriptions results in orphaned resources.
- Remediation: First delete user resources or the entire resource group, and then delete the user subscriptions.
- Occurrence: Common

### Subscription permissions

- Applicable: This issue applies to all supported releases.
- Cause: You cannot view permissions to your subscription using the Azure Stack portals.
- Remediation: Use [PowerShell to verify permissions](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence: Common

### Storage account settings

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the storage account **Configuration** blade shows an option to change **security transfer type**. The feature is currently not supported in Azure Stack.
- Occurrence: Common

### Upload blob

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, when you try to upload a blob using the **OAuth(preview)** option, the task fails with an error message.
- Remediation: Upload the blob using the SAS option.
- Occurrence: Common

## Networking

### Service endpoints

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network** blade shows an option to use **Service Endpoints**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

### Network interface

- Applicable: This issue applies to all supported releases.
- Cause: A new network interface cannot be added to a VM that is in a **running** state.
- Remediation: Stop the virtual machine before adding/removing a network interface.
- Occurrence: Common

### Virtual Network Gateway

#### Alerts

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Virtual Network Gateway** blade shows an option to use **Alerts**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### Active-Active

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, while creating, and in the resource menu of **Virtual Network Gateway**, you will see an option to enable **Active-Active** configuration. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### VPN troubleshooter

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, the **Connections** blade shows a feature called **VPN Troubleshooter**. This feature is currently not supported in Azure Stack.
- Occurrence: Common

#### Documentation

- Applicable: This issue applies to all supported releases.
- Cause: The documentation links in the overview page of Virtual Network gateway link to Azure-specific documentation instead of Azure Stack. Please use the following links for the Azure Stack documentation:

  - [Gateway SKUs](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Highly Available Connections](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configure BGP on Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute circuits](azure-stack-connect-expressroute.md)
  - [Specify custom IPsec/IKE policies](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### Load balancer

#### Add backend pool

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, if you attempt to add a **Backend Pool** to a **Load Balancer**, the operation fails with the error message **failed to update Load Balancer...**.
- Remediation: Use PowerShell, CLI or a Resource Manager template to associate the backend pool with a load balancer resource.
- Occurrence: Common

#### Create inbound NAT

- Applicable: This issue applies to all supported releases.
- Cause: In the user portal, if you attempt to create an **Inbound NAT Rule** for a **Load Balancer**, the operation fails with the error message **Failed to update Load Balancer...**.
- Remediation: Use PowerShell, CLI or a Resource Manager template to associate the backend pool with a load balancer resource.
- Occurrence: Common

## Compute

### VM boot diagnostics

- Applicable: This issue applies to all supported releases.
- Cause: When creating a new Windows virtual machine (VM), the following error may be displayed:
**Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'**. The error occurs if you enable boot diagnostics on a VM, but delete your boot diagnostics storage account.
- Remediation: Recreate the storage account with the same name you used previously.
- Occurrence: Common

### Virtual machine scale set

#### CentOS

- Applicable: This issue applies to all supported releases.
- Cause: The virtual machine scale set creation experience provides CentOS-based 7.2 as an option for deployment. CentOS 7.2 is not available on Azure Stack.
- Remediation: Select another operating system for your deployment, or use an Azure Resource Manager template specifying another CentOS image that has been downloaded prior to deployment from the marketplace by the operator.
- Occurrence: Common

#### Remove scale set

- Applicable: This issue applies to all supported releases.
- Cause: You cannot remove a scale set from the **Virtual machine scale sets** blade.
- Remediation: Select the scale set that you want to remove, then click the **Delete** button from the **Overview** pane.
- Occurrence: Common

#### Create failures during patch and update on 4-node Azure Stack environments

- Applicable: This issue applies to all supported releases.
- Cause: Creating VMs in an availability set of 3 fault domains and creating a virtual machine scale set instance fails with a **FabricVmPlacementErrorUnsupportedFaultDomainSize** error during the update process on a 4-node Azure Stack environment.
- Remediation: You can create single VMs in an availability set with 2 fault domains successfully. However, scale set instance creation is still not available during the update process on a 4-node Azure Stack.

#### Scale set instance view blade does not load

- Applicable: This issue applies to the 1904, 1905, and 1906 releases.
- Cause: The instance view blade of a virtual machine scale set located on the Azure Stack portal, in **Dashboard > Virtual machine scale sets > AnyScaleSet > Instances > AnyScaleSetInstance** fails to load, and displays a "rainy cloud" image.
- Remediation: There is currently no remediation and we are working on a fix. Until then, please use the CLI command `az vmss get-instance-view` to get the instance view of a scale set.

### Ubuntu SSH access

- Applicable: This issue applies to all supported releases.
- Cause: An Ubuntu 18.04 VM created with SSH authorization enabled does not allow you to use the SSH keys to sign in.
- Remediation: Use VM access for the Linux extension to implement SSH keys after provisioning, or use password-based authentication.
- Occurrence: Common

### Virtual machine scale set reset password does not work

- Applicable: This issue applies to the 1906 release.
- Cause: A new reset password blade appears in the scale set UI, but Azure Stack does not support resetting password on a scale set yet.
- Remediation: None.
- Occurrence: Common

### Rainy cloud on scale set diagnostics

- Applicable: This issue applies to the 1906 release.
- Cause: The virtual machine scale set overview page shows an empty chart. Clicking on the empty chart opens a "rainy cloud" blade. This is the chart for scale set diagnostic information, such as CPU percentage, and is not a feature supported in the current Azure Stack build.
- Remediation: None.
- Occurrence: Common

### Virtual machine diagnostic settings blade

- Applicable: This issue applies to the 1906 release.
- Cause: The virtual machine diagnostic settings blade has a **Sink** tab, which asks for an **Application Insight Account**. This is the result of a new blade and is not yet supported in Azure Stack.
- Remediation: None.
- Occurrence: Common

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## Next steps

- [Review update activity checklist](azure-stack-release-notes-checklist.md)
- [Review list of security updates](azure-stack-release-notes-security-updates-1905.md)
