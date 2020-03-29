---
title: Översikt över enhetshantering med Azure IoT Hub | Microsoft-dokument
description: Översikt över enhetshantering i Azure IoT Hu – livscykel för företagsenheter och enhetshanteringsmönster som omstart, fabriksåterställning, uppdatering av den inbyggda programvaran, konfiguration, enhetstvillingar, frågor, jobb.
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: bdc55af23568b5785a831e81f352400c728c902e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60400986"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Översikt över enhetshantering med IoT Hub

Azure IoT Hub innehåller funktionerna och en modell för utökningsbarhet som gör det möjligt enhets- och backend-utvecklare att bygga robusta lösningar för enhetshantering. Enheter är allt från begränsade sensorer och enskilda mikrostyrenheter för ett särskilt ändamål till kraftfulla gateways som dirigerar kommunikationen för grupper av enheter.  Dessutom varierar användningsfall och krav för IoT-operatörer avsevärt mellan olika branscher.  Trots variationen ger enhetshantering med IoT Hub funktioner, mönster och kodbibliotek för att serva en mängd olika enheter och slutanvändare.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

En viktig del av att skapa en lyckad IoT-lösning för företaget är att tillhandahålla en strategi för hur operatörer kontinuerligt hanterar sin samling med enheter. IoT-operatörer behöver enkla och tillförlitliga verktyg och program som låter dem fokusera på mer strategiska aspekter av sina arbetsuppgifter. Den här artikeln innehåller:

* En kort översikt över metoden för enhetshantering i Azure IoT Hub.
* En beskrivning av gemensamma principer för hantering av enheten.
* En beskrivning av enhetens livscykel.
* En översikt över vanliga mönster för hantering av enheten.

## <a name="device-management-principles"></a>Principer för enhetshantering

IoT medför en unik uppsättning utmaningar för enhetshantering, och en lösning i företagsklass måste ta hänsyn till följande principer:

![Bild över principer för enhetshantering](media/iot-hub-device-management-overview/image4.png)

* **Skala och automatisering**: IoT-lösningar kräver enkla verktyg som kan automatisera rutiner och som låter en mycket liten personalstyrka hantera miljontals enheter. I det dagliga arbetet förväntas operatörer fjärrhantera enhetsåtgärder gruppvis och endast få aviseringar när problem som kräver direkta åtgärder uppstår.

* **Insyn och kompatibilitet**: Enhetsmiljön är ovanligt mångfaldig. Hanteringsverktygen måste skräddarsys för att hantera en mängd olika enhetsklasser, plattformar och protokoll. Operatörer måste ha stöd för många typer av enheter, från de mest begränsade inbäddade enkelprocesschippen till kraftfulla och helt funktionella datorer.

* **Kontextmedvetenhet**: IoT-miljöer är dynamiska och föränderliga. Tjänstens tillförlitlighet är avgörande. Enhetshanteringsåtgärder måste ha följande faktorer i åtanke så att driftavbrotten inte påverkar viktiga företagsrutiner eller skapar farliga förutsättningar:

    * SLA-underhållsfönster
    * Nätverks- och krafttillstånd
    * Användningsförutsättningar
    * Enhetens geografiska plats

* **Stöd för många roller**: stöd för IoT-åtgärdernas unika arbetsflöden och processer är avgörande. Driftpersonalen måste arbeta harmoniskt med de givna begränsningarna för interna IT-avdelningar.  De måste också hitta hållbara sätt för att exponera information om enhetsåtgärder i realtid för chefer och andra företagsledarroller. 

## <a name="device-lifecycle"></a>Enhetens livscykel
Det finns en uppsättning av allmänna enhetshanteringsfaser som är gemensamma för alla företags IoT-projekt. Livscykeln för enheter i Azure IoT består av fem faser:

![De fem Azure IoT-livscyklerna är: planera, etablera, konfigurera, övervaka, dra tillbaka](./media/iot-hub-device-management-overview/image5.png)

I var och en av dessa fem faser finns det flera enhetsoperatorskrav som måste uppfyllas för att tillhandahålla en komplett lösning:

* **Planera**: Operatörer kan skapa ett schema med enhetens metadata. Schemat kan användas till att snabbt och korrekt skicka frågor till eller fokusera på en enhetsgrupp för massåtgärder. Du kan använda enhetstvillingen för att lagra dessa enhetsmetadata i form av taggar och egenskaper.
  
    *Ytterligare läsning:* 
    * [Kom igång med enhetstvillingar](iot-hub-node-node-twin-getstarted.md)
    * [Förstå enhetstvillingar](iot-hub-devguide-device-twins.md)
    * [Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)
    * [Metodtips för enhetskonfiguration i en IoT-lösning](iot-hub-configuration-best-practices.md)

* **Etablera**: Etablera säkert nya enheter i IoT Hub där operatörerna kan se enhetens funktioner direkt.  Använd IoT Hub-identitetsregistret för att skapa flexibla enhetsidentiteter och autentiseringsuppgifter och utför åtgärden gruppvis med hjälp av ett jobb. Skapa enheter om du vill rapportera deras kapacitet och villkor via enhetens egenskaper i enhetstvillingen.
  
    *Ytterligare läsning:* 
    * [Hantera enhetsidentiteter](iot-hub-devguide-identity-registry.md)
    * [Masshantering av enhetsidentiteter](iot-hub-bulk-identity-mgmt.md)
    * [Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)
    * [Metodtips för enhetskonfiguration i en IoT-lösning](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning-tjänst](https://azure.microsoft.com/documentation/services/iot-dps)

* **Konfigurera**: underlätta massinläsning av konfigurationsändringar och uppdaterad enhetsprogramvara utan att förlora funktion eller säkerhet. Utför dessa åtgärder för enhetshantering gruppvis genom att använda önskade egenskaper eller med direkta metoder och sändningsjobb.
  
    *Ytterligare läsning:*
    * [Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)
    * [Konfigurera och övervaka IoT-enheter i stor skala](iot-hub-auto-device-config.md)
    * [Metodtips för enhetskonfiguration i en IoT-lösning](iot-hub-configuration-best-practices.md)

* **Övervaka**: Övervaka enhetssamlingens allmänna funktion samt statusen för pågående åtgärder så att operatörerna blir medvetna om problem som kräver åtgärder.  Använd enhetstvillingen för att tillåta att enheterna rapporterar driftsförhållanden och status för uppdateringsåtgärder i realtid. Skapa kraftfulla instrumentpanelsrapporter som lyfter fram de mest omedelbara problemen genom att använda enhetstvillingsfrågor.
  
    *Ytterligare läsning:* 
    * [Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)
    * [IoT Hub-frågespråk för enhetstvillingar, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
    * [Konfigurera och övervaka IoT-enheter i stor skala](iot-hub-auto-device-config.md)
    * [Metodtips för enhetskonfiguration i en IoT-lösning](iot-hub-configuration-best-practices.md)

* **Dra tillbaka**: Byt ut eller inaktivera enheter efter ett fel, uppgraderingscykel eller i slutet av tjänstens livstid.  Använd enhetstvillingen för att underhålla enhetsinformation om den fysiska enheten håller på att ersättas eller arkiveras om den har återkallats. Använd IoT Hub-identitetsregistret för att säkert återkalla enhetsidentiteter och autentiseringsuppgifter.
  
    *Ytterligare läsning:* 
    * [Så här använder du enhetstvillingegenskaper](tutorial-device-twins.md)
    * [Hantera enhetsidentiteter](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Enhetshanteringsmönster

IoT Hub använder följande uppsättning enhetshanteringsmönster. [Självstudiekursen om enhetshantering](iot-hub-node-node-device-management-get-started.md) beskriver i mer detalj hur du utökar dessa mönster så att de passar just ditt scenario och hur du designar nya mönster utifrån dessa basmallar.

* **Omstart**: Backend-appen informerar enheten via en direkt metod som den har initierat en omstart.  Enheten använder de rapporterade egenskaperna för att uppdatera enhetens status för omstart.
  
    ![Bild över omstartsmönster för enhetshantering](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Fabriksåterställning:** Backend-appen informerar enheten med en direkt metod om att den har initierat en fabriksåterställning. Enheten använder de rapporterade egenskaperna för att uppdatera enhetens status för fabriksåterställning.
  
    ![Bilder över fabriksåterställningsmönster för enhetshantering](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Konfiguration**: Backend-appen använder önskade egenskaper för att konfigurera programvara som körs på enheten. Enheten använder de rapporterade egenskaperna för att uppdatera enhetens konfigurationsstatus.
  
    ![Bild över konfigurationsmönster för enhetshantering](./media/iot-hub-device-management-overview/configuration-pattern.png)

* Uppdatering av den **inbyggda programvaran:** Backend-appen använder en automatisk enhetshanteringskonfiguration för att välja vilka enheter som ska ta emot uppdateringen, för att tala om för enheterna var uppdateringen ska hittas och för att övervaka uppdateringsprocessen. Enheten initierar en flerstegsprocess för att hämta, verifiera och tillämpa den inbyggda programvaran och startar sedan om enheten innan den återansluter till IoT Hub-tjänsten. Enheten använder de rapporterade egenskaperna under hela flerstegsprocessen för att uppdatera enhetens förlopp och status.
  
    ![Bild över mönster för uppdatering av inbyggd programvara för enhetshantering](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Rapporteringsstatus och status**: Lösningens back end kör enhetstvillingfrågor, över en uppsättning enheter, för att rapportera om status och förlopp för åtgärder som körs på enheterna.
  
    ![Bild över mönster för uppdatering av rapporteringsprocess och status](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Efterföljande moment

Du kan använda de funktioner, mönster och kodbibliotek som ingår i IoT Hub-enhetshanteringen för att skapa IoT-program som uppfyller företagets IoT-operatörskrav i varje enhets livscykelfas.

Om du vill lära dig mer om enhetshanteringsfunktionerna i IoT Hub går du självstudiekursen [Komma igång med enhetshantering](iot-hub-node-node-device-management-get-started.md).