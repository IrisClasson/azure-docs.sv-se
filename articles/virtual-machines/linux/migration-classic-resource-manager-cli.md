---
title: Migrera virtuella datorer till Resource Manager med Azure CLI | Microsoft Docs
description: Den här artikeln beskriver plattformsunderstödd migrering av resurser från klassisk till Azure Resource Manager med hjälp av Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 34dad39e3784dd0bc73e3be108d6b31d4f479a1e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543278"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrera IaaS-resurser från klassisk till Azure Resource Manager med hjälp av Azure CLI
De här stegen visar hur du använder Azure-kommandoradsgränssnittet (CLI)-kommandon för att migrera infrastruktur som en tjänst (IaaS)-resurser från den klassiska distributionsmodellen Azure Resource Manager-distributionsmodellen. Artikeln kräver den [Azure klassiskt CLI](../../cli-install-nodejs.md). Eftersom Azure CLI kan bara användas för Azure Resource Manager-resurser, kan inte användas för den här migreringen.

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotenta. Om du har andra problem än en funktion som inte stöds eller ett konfigurationsfel rekommenderar vi att du gör om förbereda, avbryta eller utför-åtgärden. Plattformen försöker sedan igen.
> 
> 

<br>
Här är ett flödesschema för att identifiera den ordning som stegen måste utföras en Migreringsprocess

![Skärmbild som visar migreringsstegen](../windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Steg 1: Förbereda för migrering
Här följer några metodtips som vi rekommenderar medan du utvärderar migrera IaaS-resurser från klassisk till Resource Manager:

* Läs igenom den [lista över konfigurationer som inte stöds eller funktioner](../windows/migration-classic-resource-manager-overview.md). Om du har virtuella datorer som använder konfigurationer som inte stöds eller funktioner, rekommenderar vi att du väntar funktionen/konfigurationssupport meddelas. Du kan också ta bort funktionen eller flytta från den konfigurationen för att aktivera migrering om det passar dina behov.
* Om du har automatiserat skript som distribuerar din infrastruktur och dina program idag, försök att skapa en liknande test-konfiguration med hjälp av dessa skript för migrering. Du kan också ställa in exempelmiljöer med hjälp av Azure portal.

> [!IMPORTANT]
> Programgatewayer stöds inte för migrering från klassisk till Resource Manager. Ta bort gatewayen innan du kör en Förbered-åtgärden för att flytta nätverket för att migrera ett klassiskt virtuellt nätverk med en Programgateway. När du har slutfört migreringen kan du återansluta gateway i Azure Resource Manager. 
>
>ExpressRoute-gatewayer som ansluter till ExpressRoute-kretsar i en annan prenumeration migreras inte automatiskt. I sådana fall kan du ta bort ExpressRoute-gatewayen, migrera det virtuella nätverket och återskapa gatewayen. Se [migrera ExpressRoute circuits och tillhörande virtuella nätverk från klassiskt till Resource Manager-distributionsmodellen](../../expressroute/expressroute-migration-classic-resource-manager.md) för mer information.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Steg 2: Ställ in prenumerationen och registrera providern
För Migreringsscenarier kan du behöva konfigurera din miljö för både klassiska och Resource Manager. [Installera Azure CLI](../../cli-install-nodejs.md) och [Välj din prenumeration](/cli/azure/authenticate-azure-cli).

Logga in på ditt konto.

    azure login

Välj den Azure-prenumerationen med hjälp av följande kommando.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> Registreringen är en tidssteg men den måste göras en gång innan du försöker migrera. Utan att registrera visas följande felmeddelande 
> 
> *BadRequest: Prenumerationen har inte registrerats för migrering.* 
> 
> 

Registrera med resursprovidern migrering med hjälp av följande kommando. Observera att i vissa fall kan det här kommandot tidsgränsen. Dock kommer registreringen att lyckas.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Vänta fem minuter att slutföra registreringen. Du kan kontrollera statusen för godkännandet med hjälp av följande kommando. Se till att RegistrationState `Registered` innan du fortsätter.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Växla CLI för att den `asm` läge.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Steg 3: Kontrollera att du har tillräckligt med Azure Resource Manager-VM vcpu: er i Azure-regionen för din aktuella distributionen eller virtuella nätverk
Det här steget måste du växla till `arm` läge. Du kan göra detta med följande kommando.

```
azure config mode arm
```

Du kan använda följande CLI-kommando för att kontrollera det aktuella antalet virtuella processorer som du har i Azure Resource Manager. Läs mer om vCPU-kvoter i [gränser och Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

När du har verifierat det här steget, kan du gå tillbaka till `asm` läge.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Steg 4: Alternativ 1 – migrera virtuella datorer i en molntjänst
Hämta listan över molntjänster med hjälp av följande kommando och välj sedan den molntjänst som du vill migrera. Observera att om de virtuella datorerna i Molntjänsten är i ett virtuellt nätverk eller om de har web/worker-roller, visas ett felmeddelande.

    azure service list

Kör följande kommando för att hämta distributionens namn för Molntjänsten från utförlig utdata. I de flesta fall är distributionens namn samma som molntjänstens namn.

    azure service show <serviceName> -vv

Verifiera först om du kan migrera Molntjänsten med hjälp av följande kommandon:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Förbered de virtuella datorerna i Molntjänsten för migrering. Har du två alternativ att välja bland.

Om du vill migrera de virtuella datorerna till ett virtuellt nätverk med plattformen skapats använder du följande kommando.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Om du vill migrera till ett befintligt virtuellt nätverk i Resource Manager-distributionsmodellen använder du följande kommando.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>

När Förberedelseåtgärden är klar, kan du söka igenom utdata migrering tillståndet för de virtuella datorerna och se till att de är i den `Prepared` tillstånd.

    azure vm show <vmName> -vv

Kontrollera konfigurationen för de förberedda resurserna med hjälp av CLI eller Azure-portalen. Om du inte är redo för migrering och du vill gå tillbaka till det gamla tillståndet, använder du följande kommando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Om den förberedda konfigurationen ser bra ut du gå vidare och genomför resurserna med hjälp av följande kommando.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Steg 4: Alternativ 2 – migrera virtuella datorer i ett virtuellt nätverk
Välj det virtuella nätverk som du vill migrera. Observera att om det virtuella nätverket innehåller web/worker-roller eller virtuella datorer med konfigurationer som inte stöds, visas ett felmeddelande för verifiering.

Hämta alla virtuella nätverk i prenumerationen med hjälp av följande kommando.

    azure network vnet list

Utdata ser ut ungefär så här:

![Skärmbild av kommandoraden med hela virtuella nätverksnamnet markerat.](../media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

I exemplet ovan den **virtualNetworkName** är namnet **”grupp classicubuntu16 classicubuntu16”**.

Verifiera först om du kan migrera det virtuella nätverket med hjälp av följande kommando:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Förbered det virtuella nätverket valfri för migrering med hjälp av följande kommando.

    azure network vnet prepare-migration <virtualNetworkName>

Kontrollera konfigurationen för förberedda virtuella datorer med hjälp av CLI eller Azure-portalen. Om du inte är redo för migrering och du vill gå tillbaka till det gamla tillståndet, använder du följande kommando.

    azure network vnet abort-migration <virtualNetworkName>

Om den förberedda konfigurationen ser bra ut du gå vidare och genomför resurserna med hjälp av följande kommando.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Steg 5: Migrera ett lagringskonto
När du är klar migrerar de virtuella datorerna, rekommenderar vi du migrerar storage-konto.

Förbereda storage-konto för migrering med hjälp av följande kommando

    azure storage account prepare-migration <storageAccountName>

Kontrollera konfigurationen för förberedda storage-konto med hjälp av CLI eller Azure-portalen. Om du inte är redo för migrering och du vill gå tillbaka till det gamla tillståndet, använder du följande kommando.

    azure storage account abort-migration <storageAccountName>

Om den förberedda konfigurationen ser bra ut du gå vidare och genomför resurserna med hjälp av följande kommando.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Nästa steg

* [Översikt över plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
