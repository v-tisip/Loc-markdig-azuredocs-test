---
title: Azure PowerShell Samples - Service Fabric | Microsoft Docs
description: Azure PowerShell Samples - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: 
tags: 

ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
---
# Azure PowerShell samples

The following table includes links to PowerShell scripts samples that create and manage Service Fabric clusters, applications, and services.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

|                                                                                                                          |                                                                                                           |
|--------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
|                                             <strong>Create cluster</strong>                                              |                                                                                                           |
|              [Create a cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)               |                                 Creates an Azure Service Fabric cluster.                                  |
|               [Create a test cluster (Azure)](./scripts/service-fabric-powershell-create-test-cluster.md)                |                        Creates a three-node test Service Fabric cluster on Azure.                         |
|                                <strong>Manage cluster, nodes, and infrastructure</strong>                                |                                                                                                           |
|           [Add an application certificate](./scripts/service-fabric-powershell-add-application-certificate.md)           |                     Adds an application X.509 certificate to all nodes in a cluster.                      |
|         [Update the RDP port range on cluster VMs](./scripts/service-fabric-powershell-change-rdp-port-range.md)         |                   Changes the RDP port range on cluster node VMs in a deployed cluster.                   |
| [Update the admin user and password for cluster node VMs](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) |                       Updates the admin username and password for cluster node VMs.                       |
|          [Open a port in the load balancer](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)           |     Open an application port in the Azure load balancer to allow inbound traffic on a specific port.      |
|           [Create an inbound network security group rule](./scripts/service-fabric-powershell-add-nsg-rule.md)           | Create an inbound network security group rule to allow inbound traffic to the cluster on a specific port. |
|                                           <strong>Manage applications</strong>                                           |                                                                                                           |
|                    [Deploy an application](./scripts/service-fabric-powershell-deploy-application.md)                    |                                    Deploy an application to a cluster.                                    |
|                   [Upgrade an application](./scripts/service-fabric-powershell-upgrade-application.md)                   |                                          Upgrade an application.                                          |
|                    [Remove an application](./scripts/service-fabric-powershell-remove-application.md)                    |                                   Remove an application from a cluster.                                   |

