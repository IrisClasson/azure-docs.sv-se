---
title: CLI-exempel
description: Hitta Azure CLI-exempel för några vanliga App Service scenarier. Lär dig hur du automatiserar App Service distributions-eller hanterings uppgifter.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 12/12/2017
ms.custom: mvc
ms.openlocfilehash: 06f03e0005c4f98370bb1f6430fff506f9145d5a
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688514"
---
# <a name="cli-samples-for-azure-app-service"></a>CLI-exempel för Azure App Service

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|-|-|
|**Skapa app**||
| [Skapa en app och distribuera filer med FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och distribuerar en fil till den via FTP. |
| [Skapa en app och distribuera kod från GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och distribuerar kod från en offentlig GitHub-lagringsplats. |
| [Skapa en app med kontinuerlig distribution från GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app med kontinuerlig publicering från en GitHub-lagringsplats som du äger. |
| [Skapa en app och distribuera kod från en lokal Git-lagringsplats](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och konfigurerar kodöverföring från en lokal Git-lagringsplats. |
| [Skapa en app och distribuera kod till en mellanlagringsmiljö](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app med ett distributionsfack för att mellanlagra kodändringar. |
| [Skapa en ASP.NET Core-app i en Docker-container](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app i Linux och läser in en Docker-avbildning från Docker Hub. |
|**Konfigurera app**||
| [Mappa en anpassad domän till en app](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och mappar ett anpassat domännamn till den. |
| [Binda ett anpassat SSL-certifikat till en app](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och binder SSL-certifikatet för ett anpassat domännamn till den. |
|**Skala app**||
| [Skala en app manuellt](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och skalar den över 2 instanser. |
| [Skala en app globalt med en arkitektur för hög tillgänglighet](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar två App Service-appar i två olika geografiska regioner och gör dem tillgängliga via en enskild slutpunkt med Azure Traffic Manager. |
|**Anslut app till resurser**||
| [Anslut en webbapp till en SQL-databas](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och en SQL-databas och lägger sedan till dataanslutningssträngen till appinställningarna. |
| [Anslut en app till ett lagringskonto](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en App Service-app och ett lagringskonto och lägger sedan till lagringsanslutningssträngen till appinställningarna. |
| [Ansluta en app till en Azure Cache for Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och en Azure Cache for Redis och lägger sedan till Redis-anslutningsinformationen till appinställningarna.) |
| [Ansluta en app till Azure Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och en Cosmos DB och lägger sedan till Cosmos DB-anslutningsinformationen till appinställningarna. |
|**Säkerhetskopiera och återställ app**||
| [Säkerhetskopiera en app](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och en engångssäkerhetskopia för den. |
| [Skapa en schemalagd säkerhetskopiering för en app](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app och skapar en schemalagd säkerhetskopia för den. |
| [Återställer en app från en säkerhetskopia](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Återställer en App Service-app från en säkerhetskopia. |
|**Övervaka app**||
| [Övervaka en app med webbserverloggar](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en App Service-app, aktiverar loggning för den och hämtar loggarna till den lokala datorn. |
| | |