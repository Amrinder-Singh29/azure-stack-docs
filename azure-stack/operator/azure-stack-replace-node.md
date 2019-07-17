---
title: Replace a scale unit node on an Azure Stack integrated system | Microsoft Docs
description: Learn how to replace a physical scale unit node on an Azure Stack integrated system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''

ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/06/2018

---

# Replace a scale unit node on an Azure Stack integrated system

*Applies to: Azure Stack integrated systems*

This article describes the general process to replace a physical computer (also referred to as a scale unit node) on an Azure Stack integrated system. Actual scale unit node replacement steps will vary based on your original equipment manufacturer (OEM) hardware vendor. See your vendor's field replaceable unit (FRU) documentation for detailed steps that are specific to your system.

> [!CAUTION]  
> Firmware leveling is critical for the success of the operation described in this article. Missing this step can lead to system instability, performance decrease, security threads or prevent Azure Stack automation to deploy the operating system. Always consult your hardware partner's documentation when replacing hardware to ensure applied firmware is matching the OEM Version displayed in the [Azure Stack administrator portal](azure-stack-updates.md).

| Hardware Partner | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb<br><br>https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html |
| Dell EMC | All | https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G<br><br>https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G |
| Fujitsu | JAPAN | https://eservice.fujitsu.com/supportdesk-web/ |
|  | EMEA | https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del |
|  |  | https://support.ts.fujitsu.com/IndexMySupport.asp |
| HPE | All | http://www.hpe.com/info/MASupdates |
| Lenovo |  | https://datacentersupport.lenovo.com/us/en/solutions/ht505122 |
| Wortmann | All |  |

The following flow diagram shows the general FRU process to replace an entire scale unit node.

![Flow chart for replace node process](media/azure-stack-replace-node/replacenodeflow.png)

*This action may not be required based on the physical condition of the hardware.

> [!Note]  
> If the shutdown operation does fail, it is recommended to use the drain operation followed by the stop operation. For more details see available node operations  

## Review alert information

If a scale unit node is down, you'll receive the following critical alerts:

- Node not connected to network controller
- Node inaccessible for virtual machine placement
- Scale unit node is offline

![List of alerts for scale unit down](media/azure-stack-replace-node/nodedownalerts.png)

If you open the **Scale unit node is offline** alert, the alert description contains the scale unit node that's inaccessible. You may also receive additional alerts in the OEM-specific monitoring solution that's running on the hardware lifecycle host.

![Details of node offline alert](media/azure-stack-replace-node/nodeoffline.png)

## Scale unit node replacement process

The following steps are provided as a high-level overview of the scale unit node replacement process. See your OEM hardware vendor's FRU documentation for detailed steps that are specific to your system. Do not follow these steps without referring to your OEM-provided documentation.

1. Use the **Shutdown** action to gracefully shutdown the scale unit node. This action may not be required based on the physical condition of the hardware. 

2. In the unlikely case the shutdown action fails, use the [Drain](azure-stack-node-actions.md#drain) action to put the scale unit node into maintenance mode. This action may not be required based on the physical condition of the hardware.

   > [!NOTE]  
   > In any case, only one node can be disabled and powered off at the same time without breaking the S2D (Storage Spaces Direct).

3. After the scale unit node is in maintenance mode, use the [Stop](azure-stack-node-actions.md#stop) action. This action may not be required based on the physical condition of the hardware.

   > [!NOTE]  
   > In the unlikely case that the Power off action doesn't work, use the baseboard management controller (BMC) web interface instead.

4. Replace the physical computer. Typically, this is done by your OEM hardware vendor.
5. Use the [Repair](azure-stack-node-actions.md#repair) action to add the new physical computer to the scale unit.
6. Use the privileged endpoint to [check the status of virtual disk repair](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). With new data drives, a full storage repair job can take multiple hours depending on system load and consumed space.
7. After the repair action has finished, validate that all active alerts have been automatically closed.

## Next steps

- For information about replacing a physical disk while the system is powered on, see [Replace a disk](azure-stack-replace-disk.md). 
- For information about replacing a hardware component that requires the system to be powered off, see [Replace a hardware component](azure-stack-replace-component.md).
