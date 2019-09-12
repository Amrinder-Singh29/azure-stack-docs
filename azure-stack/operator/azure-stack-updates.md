---
title: Manage updates in Azure Stack | Microsoft Docs
description: Learn to manage updates in Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''

ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent 

---

# Manage updates in Azure Stack overview

*Applies to: Azure Stack integrated systems*

Full and express updates, hotfixes, as well as driver and firmware updates from the original equipment manufacturer (OEM) all help keep Azure Stack up-to-date. This article explains the different types of updates, when to expect their release, and where to find more about the current release.

> [!Note]  
> You cannot apply Azure Stack update packages to the Azure Stack Development Kit (ASDK). The update packages are designed for integrated systems. For information, see [Redeploy the ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## Update package types

There are three types of update packages for integrated systems:

-   **Azure Stack software updates**. Microsoft is responsible for the end-to-end servicing lifecycle for the Microsoft software update packages. These packages can include the latest Windows Server security updates, non-security updates, and Azure Stack feature updates. You download theses update packages directly from Microsoft.

    Each update package has a corresponding type, **Full** or **Express**. 
 
    **Full** update packages update the physical host operating systems in the scale unit and require a larger maintenance window. 

    **Express** update packages are scoped and do not update the underlying physical host operating systems.

-   **Azure Stack hotfixes**. Microsoft provides hotfixes for Azure Stack that address a specific issue that is often preventative or time-sensitive. Each hotfix is released with a corresponding Microsoft Knowledge Base article that details the issue, cause, and resolution. You download and install hotfixes just like the regular full update packages for Azure Stack. Hotfixes are cumulative and can install in minutes.

-   **OEM hardware-vendor-provided updates**. Azure Stack hardware partners are responsible for the end-to-end servicing lifecycle (including guidance) for the hardware-related firmware and driver update packages. In addition, Azure Stack hardware partners own and maintain guidance for all software and hardware on the hardware lifecycle host. The OEM hardware vendor hosts these update packages on their own download site.

## When to update

The three type of updates are released with the following cadence:

-   **Azure Stack software updates**. Microsoft typically releases software update packages each month.

-   **Azure Stack hotfixes**. Hotfixes are time-sensitive releases that can be released at any time.

-   **OEM hardware vendor-provided updates**. OEM hardware vendors release their updates on an as-needed basis.

To continue to receive support, you must keep your Azure Stack environment on a supported Azure Stack software version. For more information, see [Azure Stack Servicing Policy](azure-stack-update-servicing-policy.md).

## Where to get notice of an update

Notice of updates varies on a couple of factors, such as your connection to the Internet and the type of update.

- **Microsoft software updates and hotfixes** 

    An update alert for Microsoft software updates and hotfixes will appear in the Update blade for Azure Stack instances that are connected to the internet.

    If your instance is not connected and you would like to be notified about each hotfix release, subscribe to the [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) or [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) feed.

- **OEM hardware vendor-provided updates**

    OEM updates will depend on your manufacturer. You will need to establish a communication channel with your OEM so that you aware when you have updates from your OEM that need to be applied. For more information about the OEMs and the OEM update process, see [Apply Azure Stack original equipment manufacturer (OEM) updates](azure-stack-update-oem.md).

## Update processes

Once you know you have an update, apply it by using the following steps.

![Azure Stack update process](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Plan for the update**.

    Prepare your Azure Stack to make the update process go as smoothly as possible so that there is a minimal impact on your users. Notifying your users of any possible service outage and then follow the steps to prepare your instance for the update. For more steps to plan for the update, see [Plan for an Azure Stack update](azure-stack-update-plan.md).

2. **Upload and prepare the update package**.

    For internet-connected Azure Stack environments, Azure Stack software updates and hotfixes are automatically imported into the system and prepared for update.

    For internet-disconnected Azure Stack environments and environments with weak or intermittent internet-connectivity, update packages are imported into Azure Stack storage via the Azure Stack administrator portal. For more steps to upload and prepare the update package, see [Upload and prepare an Azure Stack update package](azure-stack-update-prepare-package.md).

    All OEM update packages are manually imported into your environment, regardless of your Azure Stack system’s internet connectivity. For more steps to import and prepare the update package, see [Upload and prepare an Azure Stack update package](azure-stack-update-prepare-package.md).

3. **Apply the update**.

    Apply the update using the **Update** blade in Azure Stack. During the update, monitor the update progress, and troubleshoot. For more information, see [Apply an Azure Stack update](azure-stack-apply-updates.md).

## The update resource provider

Azure Stack includes an update resource provider that handles the application of Microsoft software updates. This provider checks that updates are applied across all physical hosts, Service Fabric applications and runtimes, and all infrastructure virtual machines and their associated services.

As updates install, you can view high-level status as the update process targets the various subsystems in Azure Stack (for example, physical hosts, and infrastructure virtual machines).

## Next steps

- To begin the update process, follow the steps in see [Plan for an Azure Stack update](azure-stack-update-plan.md).
- To learn what versions of Azure Stack are in support, see [Azure Stack Servicing Policy](azure-stack-servicing-policy.md).  
- To learn more about the current and recent updates, see the [Azure Stack release notes](azure-stack-release-notes-security-updates-1907.md).
