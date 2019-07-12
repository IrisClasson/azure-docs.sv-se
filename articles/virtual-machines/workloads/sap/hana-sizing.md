---
title: Ändra storlek på SAP Hana på Azure (stora instanser) | Microsoft Docs
description: Storlek på SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7a81468fd19d3bd5d6eee79bb8a75396e3021f1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707271"
---
# <a name="sizing"></a>Storleksändring

Storlek för stora HANA-instansen är inte skiljer sig från att ändra storlek för HANA i allmänhet. För befintliga och distribuerade system som du vill flytta från andra RDBMS för HANA, SAP tillhandahåller ett antal rapporter som körs på dina befintliga SAP-system. Om databasen flyttas till HANA, kontrollera data som de här rapporterna och beräkna minneskraven för HANA-instans. Läs följande SAP-information för mer information om hur du kör dessa rapporter och hämta sina senaste korrigeringsfiler eller versioner:

- [SAP-kommentar #1793345 - storlek för SAP programsvit på HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Obs! #1872170 - programsvit på HANA och s/4 HANA storlek rapport](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-kommentar #2121330 – vanliga frågor och svar: SAP BW för HANA som ändrar storlek på rapporten](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Obs! #1736976 - storlek rapporten för BW på HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Obs! #2296290 - storlek ny rapport för BW på HANA](https://launchpad.support.sap.com/#/notes/2296290)

SAP snabb Ange storlek är tillgänglig att beräkna minneskrav för implementering av SAP-programvara på HANA för implementeringar av grönt fält.

Minneskraven för HANA öka när datavolymen växer. Tänk på din aktuella minnesanvändningen för att förutse vad den ska vara i framtiden. Baserat på minneskrav, kan sedan du mappa din begäran till någon av HANA-SKU: er för stor instans.

**Nästa steg**
- Se [integrationskrav](hana-onboarding-requirements.md)