---
title: Skriptexempel för Azure PowerShell – Skapa en schemalagd säkerhetskopiering för en webbapp | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Skapa en schemalagd säkerhetskopiering för en webbapp
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: acb17386a2c2e0b2a89f46c607befb56eadaee48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136581"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>Skapa en schemalagd säkerhetskopiering för en webbapp med PowerShell

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser, och skapar sedan en schemalagd säkerhetskopia för webbappen. 

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure. 

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Skapar ett lagringskonto. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Skapar en Azure-lagringscontainer. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Genererar en SAS-token för en Azure-lagringscontainer. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Skapar en App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp. |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | Redigerar konfigurationen för säkerhetskopiering för webbappen. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Hämtar en lista över säkerhetskopior för en webbapp. |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | Redigerar konfigurationen för säkerhetskopiering för webbappen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
