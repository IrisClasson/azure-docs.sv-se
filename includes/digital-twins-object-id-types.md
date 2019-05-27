---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162142"
---
Den `objectIdType` (eller **objekt-ID-typ**) refererar till typ av identitet som har tilldelats en roll. Förutom den `DeviceId` och `UserDefinedFunctionId` typer, identifierare objekttyper motsvarar egenskaperna för Azure Active Directory-objekt.

I följande tabell innehåller stöds objekttyper identifierare i Azure Digital Twins:

| Type | Beskrivning |
| --- | --- |
| UserId | Tilldelar en roll till en användare. |
| DeviceId | Tilldelar en roll till en enhet. |
| Domännamn | Tilldelar en roll till ett domännamn. Varje användare med det angivna domännamnet har rättigheter motsvarande roll. |
| TenantId | Tilldelar en roll till en klient. Varje användare som tillhör den angivna Azure AD-klient-ID har behörighet för rollen motsvarande. |
| ServicePrincipalId | Tilldelar en roll till ett objekt-ID. |
| UserDefinedFunctionId | Tilldelar en roll till en användardefinierad funktion (UDF). |