---
title: Persist files in PowerShell in Azure Cloud Shell (Preview) | Microsoft Docs
description: Walkthrough of how Azure Cloud Shell persists files.
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager

ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## How PowerShell in Azure Cloud Shell (Preview) works
PowerShell in Cloud Shell (Preview) persists files through the following method: 
* Mounting your specified Azure file share as `clouddrive` in your `$Home` directory for direct file-share interaction.

## List Cloud Drive Azure file shares
The `Get-CloudDrive` command retrieves the Azure file share information currently mounted by the Cloud Drive in the Cloud Shell. <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## Unmount Cloud Drive
You can unmount an Azure file share that's mounted to Cloud Shell at any time. If the Azure file share has been removed, you will be prompted to create and mount a new Azure file share at the next session.

The `Dismount-CloudDrive` command unmounts an Azure file share from the current storage account. Dismounting the Cloud Drive terminates the current session. The user will be prompted to create and mount a new Azure file share during the next session.
![Running Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## Next steps
[Quickstart for PowerShell](quickstart-powershell.md) <br>

[Learn about Azure Files](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>

[Learn about storage tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

