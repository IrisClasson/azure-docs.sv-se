---
title: Support – Azure dedikerad HSM | Microsoft Docs
description: Supportalternativ och ansvarsområden för Azure dedikerad HSM i olika scenarier
services: dedicated-hsm
author: johndaw
manager: barbkess
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: 8341e9057b07fbb72957eddc342ae875e4591993
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60912281"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure dedikerad HSM-support

Tjänsten Azure dedikerad HSM ger en fysisk enhet för användning efter kund fullständig administrativ kontroll och hantering av ansvar. Enheten som gjorts tillgängliga är en [Gemalto SafeNet Luna 7 HSM modellen A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft kommer inte ha administrativ åtkomst när etablerats av en kund, utöver fysiska serieport bifogad fil som en övervakning roll.  Utan åtkomst till, kan Microsoft har inga pågående programvara på underhåll eller system administration ansvarsområden. Därför kan ansvarar kunder för vanliga driftaktiviteter.
Kunderna ansvarar helt för program som använder HSM: erna och ska fungera med Gemalto för support eller consulting-baserade hjälp. På grund av omfattningen av kunden ägarskapet för operativa hygien är det inte möjligt för Microsoft att erbjuda alla slags garanti för hög tillgänglighet för den här tjänsten. Det är kundens ansvar att se till att programmen är korrekt konfigurerade för att uppnå hög tillgänglighet. Microsoft kommer att övervaka och underhålla enhetens hälsotillstånd och nätverksanslutningen.

## <a name="getting-support"></a>Få support

Kundsupport för dedikerad HSM är ett gemensamt projekt mellan Microsoft och Gemalto. Problem med maskinvara eller sökvägen nätverksproblem kan hanteras av Microsoft och något att göra med den faktiska HSM, till exempel konfiguration, programvara, inbyggd programvara och programutveckling, kommer att lösas med Gemalto. Den här supportmodell innebär snabbaste sättet det mest effektiva stöd. Om du tvekar med ett visst problem, begär support med Microsoft och vi ser till att du dirigeras korrekt. Microsoft kommer arbetet i alla supportscenarier och sträva efter den bästa upplevelsen för stöd för våra kunder.

## <a name="gemalto-support"></a>Gemalto support

Kunder som använder dedikerade HSM-tjänst som är kvalificerade för support från Gemalto enligt deras Plus-supportavtalet. Detta kräver bara en registreringsprocess med hjälp av Gemalto support portal. Ett kund-ID och instruktioner ges för detta som en del av inledande samarbetet med Microsoft att få åtkomst till dedikerad HSM-tjänst. Mekanism för att få support från Gemalto är via sina [support-kundportalen](https://supportportal.gemalto.com/csm/).
En viktig aspekt att notera är att Gemalto ger alla programvaran och dokumentationen som krävs för att använda HSM (till exempel klientprogrammet för åtkomst och SDK: er) via hämtning på kundportal för support.

### <a name="software-components"></a>Programkomponenter

Olika programvarukomponenter som används i konfigurationen av HSM-enheter:

* -Klientprogramvaran
* SDK
* Verktyg

### <a name="guidance"></a>Riktlinjer

Gemalto gör tillgänglig vägledning för administration och konfiguration via den [support-kundportalen](https://supportportal.gemalto.com/csm/). När du har loggat in med ett giltigt kund-ID, är dokumenten tillgängliga för hämtning. Gemalto innehåller också ett antal integreringsguiderna för att hjälpa kunder med olika scenarier och programvara integreringar. Mer information finns i den [Gemalto partner-webbplats för Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Support

Alla programproblem nivån eller en fråga i förhållande till med hjälp av HSM: erna som en del av dedikerad HSM-tjänst bör åtgärdas till Gemalto stöd direkt. Alla programkomponenter som anges ovan och eventuella anpassade HSM-konfiguration som är efter etablering, kommer att lösas med Gemalto. Mer information finns i den [Gemalto kundportalen support](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Konsulttjänster

Kontakta representanten för ditt konto Gemalto för alla hjälp i design, utveckling och distribution av anpassade program som använder HSM.

## <a name="microsoft-support"></a>Microsoft Support

Microsoft garanterar fysiska HSM-enheter är tillgänglig och ett drifttillstånd för exklusiv användning av en enda kund. Kunderna ansvarar för konfiguration, administration och hantering av enheten. Microsofts ansvar omfattar:

* Se till att enheten har ström och kylning
* Underhålla en driftstatus för HSM (till exempel webbsupport scenario)
* Enheten är tillgänglig över nätverket.

Problem som till exempel följande ska rapporteras till Microsoft:

* Komponentfel
* Fullständig enhetsfel
* Problem med nätverksåtkomst
* Problem med etablering och borttagning.

Microsoft har fysiska serieport åtkomst till enheten via en övervakning roll (det vill säga inte administrativa roll) som ger grundläggande hälsotillstånds telemetri.  Detta gör att Microsoft ska kunna ge Proaktiva meddelanden om problem till kunden, såvida inte kunden väljer att begränsa den här behörigheten. 

### <a name="provisioning-and-decommissioning"></a>Etablering och inaktivering av

När en kund har en godkänd registrering för dedikerad HSM-tjänst, kommer de att kunna skapa HSM-resurser (för närvarande via PowerShell eller kommandoradsgränssnittet och inte Azure-portalen). Resursen genomgår en allokeringsprocessen som mappar en fysisk enhet i en specifik region till en kunds fördefinierade virtuellt nätverk (VNet). När det är synliga på ett virtuellt nätverk, kan kunden få åtkomst till enheten och konfigurera den ytterligare enligt kraven. Kunder komma åt dedikerade HSM: er med Gemalto klientprogrammet och verktyg. Process för att skapa resursen stöds av Microsoft. Anpassad konfiguration bearbeta och även efteråt stöds av Gemalto. (se Gemalto stöder ovan). När en kund har slutförts med hjälp av en HSM, den måste återställas (eller zeroized) att se till att inget persistence av data. Processen med att återställa enheten tar bort alla anpassad konfiguration och data. Microsoft tar bort enheten och returnerar den till poolen i tillståndet enkla. Det innebär att när enheten returneras till poolen finns det några tecken på tidigare kundaktivitet. 

### <a name="hardware-issues"></a>Maskinvaruproblem

HSM-enheten har redundant och replaceable strömförsörjning och fläkt enheter.  Fläkt enhet borttagningen kommer dock fortfarande få förvanskningstålig händelsen. När ett fel uppstår kan använder Microsoft den lämpligaste processen för att åtgärda komponent på problemet på ett sätt som gör att minimal avbrottstid och lägsta risken för angrepp på våra kunder tjänstens tillgänglighet.
Alla allvarligare fel på enheten resulterar i enheten håller på att ersättas av ett nytt från poolen. Kunden innehåller bara den nya enheten i befintliga HA-par för det att synkronisera och återgå till fullständig Användningsstatus. Misslyckade enheten har sina data med enheter tas bort och förstörs på platsen på datacentret. Endast chassi återgår till Gemalto för återvinning.


### <a name="networking-issues"></a>Problem med nätverk

Om kunderna får nätverksproblem åtkomst till HSM-enhet, bör de kontakta Microsoft support. Ett enkelt test för nätverk åtkomst är att använda SSH för att ansluta till HSM-enheten. Om detta misslyckas kan du kontakta Microsoft support.

## <a name="service-level-expectations-for-support"></a>Service på förväntningar för support

Servicenivåer för Microsoft-supporten finns i den [Azure-supportplan](https://azure.microsoft.com/support/plans/).
Gemalto för tjänsten supportnivåer finns i den [Gemalto stöder Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Nästa steg

Du rekommenderas att nyckelbegrepp som hög tillgänglighet och säkerhet är väl förstådda innan enhetsetablering och programmets design eller distribution.

* [Distributionsarkitektur för](deployment-architecture.md)
* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)

