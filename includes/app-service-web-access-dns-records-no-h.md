---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 66d3397fae24ee2546dae4eb5d7c9d188f9ede99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944141"
---
> [!NOTE]
> Du kan använda Azure DNS för att konfigurera ett anpassat DNS-namn för Azure App Service. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Logga in på webbplatsen till din domänleverantör.

Sök upp sidan för hantering av DNS-poster. Leverantören för varje domän har sitt eget DNS-postgränssnitt, så läs leverantörens dokumentation. Leta efter områden på webbplatsen med namnet **Domännamn**, **DNS**, eller **Namnserverhantering**. 

Du kan ofta hitta sidan med DNS-poster genom att visa din kontoinformation och sedan söka efter en länk som heter exempelvis **Mina domäner**. Gå till sidan och sök upp efter en länk som heter något i stil med **Zonfil**, **DNS-poster** eller **Avancerad konfiguration**.

Skärmbilden nedan är ett exempel på en sida med DNS-poster:

![Exempelsida för DNS-poster](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

På exempelskärmbilden väljer du **Lägg till** för att skapa en post. Vissa providrar har olika länkar för att lägga till olika posttyper. Se leverantörens dokumentation.

> [!NOTE]
> För vissa leverantörer, till exempel GoDaddy, börjar ändringar i DNS-posterna inte att gälla förrän du väljer en separat **Spara ändringar**-länk. 
