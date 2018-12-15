---
title: Azure CLI-exempel – Azure SignalR Service
description: Azure CLI-exempel – Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a39f0abf4f3a8cc30d6f8f83dcad7e9069f348e4
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258788"
---
# <a name="azure-cli-samples"></a>Azure CLI-exempel

Följande tabell inkluderar länkar till bash-skript som skapats för Azure SignalR Service med Azure CLI.

| | |
|-|-|
|**Skapa**||
| [Skapa en ny SignalR Service och resursgrupp](scripts/signalr-cli-create-service.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn.  |
|**Integrera**||
| [Skapa en ny SignalR Service och webbapp som är konfigurerad för att använda SignalR](scripts/signalr-cli-create-with-app-service.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn. Lägger också till en ny webbapp och App Service-plan som värd för din ASP.NET Core-webbapp som använder SignalR-tjänsten. Webbappen konfigureras med en appinställning för att ansluta till den nya SignalR Service-resursen. |
| [Skapa en ny SignalR Service och webbapp som är konfigurerad för att använda SignalR och GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn. Lägger också till en ny Azure-webbapp och värdplan som värd för din ASP.NET Core-webbapp som använder SignalR-tjänsten. Webbappen konfigureras med appinställningar för anslutningssträngen till den nya SignalR Service-resursen, och klienthemligheter för att stödja [GitHub-autentisering](https://developer.github.com/v3/guides/basics-of-authentication/) som det visas i [självstudien om autentisering](signalr-authenticate-oauth.md). Webbappen är också konfigurerad för att använda en lokal distributionskälla för Git-lagringsplatsen. |
| | |