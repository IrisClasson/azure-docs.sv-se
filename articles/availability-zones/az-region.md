---
title: Regioner som stöder Tillgänglighetszoner i Azure
description: Om du vill skapa hög tillgängliga och elastiska program i Azure kan Tillgänglighetszoner tillhandahålla fysiskt åtskilda platser som du kan använda för att köra dina resurser.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/05/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: cf1fc81ea63db21d2e864c00e1987eec3d376b59
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853168"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regioner som stöder Tillgänglighetszoner i Azure

Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. Mer information om Tillgänglighetszoner finns [i regioner och Tillgänglighetszoner i Azure](az-overview.md).

I det här avsnittet visas de Azure-tjänster och regioner som stöder Tillgänglighetszoner.

Tjänster som är tillgängliga i varje region, tillsammans med kommande översikt över tillgänglighet, finns på [produkter tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="americas"></a>Nord- och Sydamerika

| Tjänst | Central US | East US | USA, östra 2 | USA, västra 2 |
| --- | :---: | :---: | :---: | :---: |
| **Beräkning** |  |  |  |  |
| Virtuella Linux-datorer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtuella Windows-datorer           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service miljöer ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Zon-redundant lagring             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Nätverk** |  |  |  |  |
| Standard-IP-adress                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute-gateway               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databaser** |  |  |  |  |
| Azure-datautforskaren                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_check_mark: (förhands granskning) |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering** |  |  |  |  |
| Service Bus (endast Premium-nivå)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identitet** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="europe"></a>Europa

| Tjänst | Frankrike, centrala | Norra Europa | Storbritannien, södra | Europa, västra |
| --- | :---: | :---: | :---: | :---: |
| **Beräkning** |  |  |  |  |
| Virtuella Linux-datorer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtuella Windows-datorer           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service miljöer ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Zon-redundant lagring             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Nätverk** |  |  |  |  |
| Standard-IP-adress                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute-gateway               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databaser** |  |  |  |  |
| Azure-datautforskaren                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | : heavy_check_mark: (förhands granskning) | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering**  |  |  |  |  |
| Service Bus (endast Premium-nivå)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identitet** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="asia-pacific"></a>Asien och stillahavsområdet

| Tjänst | Japan, östra | Sydostasien | Australien, östra |
| --- | :---: | :---: | :---: |
| **Beräkning** |  |  |  |
| Virtuella Linux-datorer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtuella Windows-datorer           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service miljöer ILB | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: |  |
| **Storage** |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Zon-redundant lagring             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Nätverk** |  |  |  |
| Standard-IP-adress                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute-gateway               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: |  |
| **Databaser** |  |  |  |
| Azure-datautforskaren                | :heavy_check_mark: | :heavy_check_mark: |  |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analys** |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrering** |  |  |  |
| Service Bus (endast Premium-nivå)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: |  |
| **Identitet** |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>Övrigt

Azure erbjuder också Tillgänglighetszoner support i följande regioner:

- US Gov, Virginia
- Sydafrika, norra
- USA, södra centrala
- Kanada, centrala

Om du vill veta mer om Tillgänglighetszoner support i de här fyra regionerna kontaktar du din Microsoft-säljare eller kund representant eller öppnar en teknisk support förfrågan.

## <a name="next-steps"></a>Nästa steg

- [Regioner och tillgänglighetszoner i Azure](az-overview.md)
