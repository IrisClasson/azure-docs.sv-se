---
title: Så här kör du Integration Runtime med egen värd i Windows-behållare
description: Läs om hur du kör egen värd Integration Runtime i Windows-behållare.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927583"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Så här kör du Integration Runtime med egen värd i Windows-behållare

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du kör Integration Runtime med egen värd i Windows-behållare.
Azure Data Factory levererar det officiella Windows-behållarobjektet som stöder egen värd Integration Runtime. Du kan hämta Docker build-källkoden och kombinera processen för att skapa och köra den i din egen kontinuerliga leverans pipeline. 

## <a name="prerequisites"></a>Förutsättningar 
- [Krav för Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker version 2,3 och senare 
- Egen värd Integration Runtime version 4.11.7512.1 och senare 
## <a name="get-started"></a>Kom igång 
1.  Installera Docker och aktivera Windows-behållare 
2.  Ladda ned källkoden från https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Hämta den senaste versionen av SHIR i mappen SHIR 
4.  Öppna mappen i gränssnittet: 
```console
cd "yourFolderPath"
```

5.  Bygg Windows Docker-avbildningen: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Kör Docker-behållare: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY är obligatoriskt för det här kommandot. NODE_NAME, ENABLE_HA och HA_PORT är valfria. Om du inte anger värdet använder kommandot standardvärden. Standardvärdet för ENABLE_HA är falskt och HA_PORT är 8060.

## <a name="container-health-check"></a>Hälso kontroll för behållare 
Efter 120 sekunders start period körs hälso kontrollen regelbundet var 30: e sekund. Den kommer att tillhandahålla IR-hälsostatus till container motor. 

## <a name="limitations"></a>Begränsningar
För närvarande har vi inte stöd för följande funktioner när du kör egen värd Integration Runtime i Windows-behållaren:
- HTTP-proxy 
- Krypterad nod-Node-kommunikation med TLS/SSL-certifikat 
- Skapa och importera säkerhets kopia 
- Daemon-tjänst 
- Automatisk uppdatering 

### <a name="next-steps"></a>Nästa steg
- Granska [integration runtime-koncept i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).
- Lär dig hur du [skapar en integration runtime med egen värd i Azure Portal](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).


