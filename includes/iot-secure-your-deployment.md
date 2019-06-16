---
title: ta med fil
description: ta med fil
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e5acb8e0f8805da7f14bbce58b4bfd2acdc24f23
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66166328"
---
# <a name="secure-your-internet-of-things-iot-deployment"></a>Skydda distributionen av Internet of Things (IoT)

Den här artikeln innehåller nästa detaljnivå för att skydda Azure IoT-baserade Internet of Things (IoT)-infrastruktur. Det länkar till nivån implementeringsdetaljer för att konfigurera och distribuera varje komponent. Det ger också jämförelser och val mellan olika konkurrerande metoder.

Skydda Azure-IoT-distributionen kan delas in i följande tre områden:

* **Enhetssäkerhet**: Skydda IoT-enhet när den har distribuerats i naturen.

* **Anslutningssäkerhet**: Att se till att alla data som överförs mellan IoT-enheter och IoT Hub är konfidentiell och manipuleringssäker.

* **Cloud Security**: Att skapa ett sätt att skydda data medan den går genom och lagras i molnet.

![Tre säkerhetsområden](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Säker enhetsetablering och autentisering

IoT-Lösningsacceleratorer säker IoT-enheter med hjälp av följande två metoder:

* Genom att tillhandahålla en unik identitet nyckel (säkerhetstoken) för varje enhet som kan användas av enheten för att kommunicera med IoT Hub.

* Med hjälp av en på enhet [X.509-certifikat](https://www.itu.int/rec/T-REC-X.509-201210-S) och den privata nyckeln som ett sätt att autentisera enheten till IoT Hub. Den här autentiseringsmetoden säkerställer att den privata nyckeln på enheten inte är känd utanför enheten när som helst, vilket ger en högre säkerhetsnivå.

Token säkerhetsmetod ger autentisering för varje anrop som görs av enheten till IoT Hub genom att associera den symmetriska nyckeln för varje anrop. X.509-baserad autentisering möjliggör autentisering av en IoT-enheter på det fysiska skiktet som en del av TLS anslutningen upprättas. Security-tokenbaserad-metoden kan användas utan X.509-autentisering, vilket är ett mindre säkert mönster. Valet mellan de två metoderna beror främst hur säker autentisering enheten måste vara och tillgänglighet för säker lagring på enheten (för att lagra den privata nyckeln på ett säkert sätt).

## <a name="iot-hub-security-tokens"></a>Säkerhetstoken i IoT Hub

IoT Hub använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklar i nätverket. Dessutom begränsas säkerhetstoken i giltighetstid och omfång. Azure IoT SDK: er generera automatiskt token utan någon specialkonfiguration. Vissa scenarier kräver dock användaren att skapa och använda säkerhetstoken direkt. De här scenarierna är direkt användning av MQTT-, AMQP- eller HTTP-ytor eller implementering av mönstret token service.

Mer information om hur säkerhetstoken och dess användning kan hittas i följande artiklar:

* [Token säkerhetsstruktur](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Med hjälp av SAS-token som en enhet](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Varje IoT-hubben har en [identitetsregistret](../articles/iot-hub/iot-hub-devguide-identity-registry.md) som kan användas för att skapa resurser för varje enhet i tjänsten, till exempel en kö som innehåller relä meddelanden från molnet till enheten, så att åtkomst till enheten-slutpunkter. IoT Hub-identitetsregistret tillhandahåller säker lagring av enhetsidentiteter och säkerhetsnycklar för en lösning. En eller flera enhetsidentiteter kan läggas till en lista över tillåtna eller en Blockeringslista aktivera fullständig kontroll över enhetsåtkomsten. I följande artiklar ger mer information om hur identitetsregistret och åtgärder som stöds.

[IoT-hubb har stöd för protokoll, till exempel MQTT, AMQP och HTTP-](../articles//iot-hub/iot-hub-devguide-security.md). Var och en av dessa protokoll använder säkerhetstoken från IoT-enhet till IoT Hub på olika sätt:

* AMQP: SASL OFORMATERAD och AMQP anspråksbaserade (`{policyName}@sas.root.{iothubName}` med IoT hub på servernivå tokens. `{deviceId}` med enheten-omfattande token).

* MQTT: Anslut paket använder `{deviceId}` som den `{ClientId}`, `{IoThubhostname}/{deviceId}` i den **användarnamn** fält och en SAS-token i den **lösenord** fält.

* HTTP: Det är giltig token i auktoriseringshuvudet för begäran.

IoT Hub-identitetsregistret kan användas för att konfigurera autentiseringsuppgifter för varje enhet och åtkomstkontroll. Men om en IoT-lösning har redan en betydande investering i en [anpassad enhet identitet registret och/eller autentisering schema](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), den kan integreras i en befintlig infrastruktur med IoT Hub genom att skapa en token tjänst.

### <a name="x509-certificate-based-device-authentication"></a>X.509-certifikatbaserad autentisering

Användning av en [enhetsbaserad X.509-certifikat](../articles/iot-hub/iot-hub-devguide-security.md) och dess associerade privata och offentliga nyckelpar tillåter ytterligare autentisering på det fysiska skiktet. Den privata nyckeln lagras på ett säkert sätt i enheten och är inte synliga utanför enheten. X.509-certifikat innehåller information om enheten, t.ex enhets-ID, och andra organisationens data. En signatur för certifikatet som genereras med hjälp av den privata nyckeln.

Övergripande enheten etablering flöde:

* Associera en identifierare för att en fysisk enhet – enhetens identitet och/eller X.509-certifikat som är kopplade till enheten under enhet inom tillverkning eller idriftsättning.

* Skapa en motsvarande identity-transaktion i IoT Hub-enhetsidentitet och en tillhörande enhetsinformation i IoT Hub-identitetsregistret.

* På ett säkert sätt lagra X.509-tumavtrycket i IoT Hub-identitetsregistret.

### <a name="root-certificate-on-device"></a>Rotcertifikatet på enheter

När du upprättar en säker TLS-anslutning med IoT Hub autentiserar IoT-enhetens IoT Hub med ett rotcertifikat som är en del av enhets-SDK. För C-klient SDK certifikatet finns i mappen ”\\c\\certifikat” under roten för lagringsplatsen. Även om dessa rotcertifikat är långlivade, de fortfarande kan upphöra att gälla eller återkallas. Om det finns inget sätt för att uppdatera certifikatet på enheten, kan enheten inte kan därefter ansluta till IoT Hub (eller någon annan molntjänst). Att ha ett sätt att uppdatera rotcertifikatet när IoT-enheter har distribuerats effektivt minimerar risken.

## <a name="securing-the-connection"></a>Skyddar anslutningen

Internet-anslutning mellan IoT-enheter och IoT Hub är skyddad med Transport Layer Security (TLS)-standarden. Azure IoT stöder [TLS 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 och TLS 1.0 i angiven ordning. Stöd för TLS 1.0 tillhandahålls endast för bakåtkompatibilitet. Använd om möjligt TLS 1.2 eftersom det ger bäst skydd.

## <a name="securing-the-cloud"></a>Skydda molnet

Azure IoT Hub kan definitionen av [åtkomst till kontrollprinciper](../articles/iot-hub/iot-hub-devguide-security.md) för varje säkerhetsnyckel. Den använder följande uppsättning behörigheter för att bevilja åtkomst till var och en av IoT Hub-slutpunkter. Användarbehörigheter begränsar åtkomsten till en IoT-hubb som baseras på funktionen.

* **RegistryRead**. Ger läsåtkomst till identitetsregistret. Mer information finns i [identitetsregistret](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Ger Läs- och skrivåtkomst till identitetsregistret. Mer information finns i [identitetsregistret](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Beviljar åtkomst till tjänsten webbservergrupper kommunikation och övervaka slutpunkter i molnet. Det ger till exempel behörighet till backend-molntjänster för att ta emot meddelanden från enheten till molnet, skicka meddelanden från moln till enhet och hämta motsvarande leverans bekräftelser.

* **DeviceConnect**. Beviljar åtkomst till enheten-slutpunkter. Till exempel ger den behörighet att skicka meddelanden från enheten till molnet och ta emot meddelanden från molnet till enheten. Den här behörigheten används av enheter.

Det finns två sätt att hämta **DeviceConnect** behörigheter med IoT Hub med [säkerhetstoken](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): med hjälp av en enhet identity-nyckel eller en delad åtkomstnyckel. Dessutom är det viktigt att Observera att alla funktioner från enheter exponeras avsiktligt på slutpunkter med prefixet `/devices/{deviceId}`.

[Tjänstkomponenter kan bara generera säkerhetstoken](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) med delade åtkomstprinciper som beviljar behörighet.

Azure IoT Hub och andra tjänster som kan vara en del av lösningen kan du hanteringen av användare som använder Azure Active Directory.

Data som matas in i Azure IoT Hub kan användas av en mängd olika tjänster, till exempel Azure Stream Analytics och Azure blob storage. Dessa tjänster kan åtkomst. Läs mer om dessa tjänster och de tillgängliga alternativen:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): En skalbar och fullständigt indexerade databastjänst för halvstrukturerade data som hanterar metadata för de enheter som du etablerar, till exempel attribut, konfiguration och säkerhetsegenskaper för. Azure Cosmos DB erbjuder hög prestanda och hög genomströmning bearbetning, schemaoberoende indexering av data och ett omfattande SQL-gränssnitt.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Strömningsbearbetning i molnet som hjälper dig att snabbt utveckla och distribuera en analyslösning med låg kostnad för att hämta information i realtid från enheter, sensorer, infrastruktur och program. Data från den här fullständigt hanterade tjänsten kan skalas till alla volymer samtidigt som du får hög genomströmning, Låg fördröjning och återhämtning.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): En plattform för att skapa avancerade webb- och mobilappar som ansluter till data överallt; i molnet eller lokalt. Skapa spännande mobilappar för iOS, Android och Windows. Integrera med programvara som en tjänst (SaaS) och företagsprogram med out-of the box anslutningen till massor av molnbaserade tjänster och företagsprogram. Koda på önskat språk och IDE (.NET, Node.js, PHP, Python eller Java) att skapa webbappar och API: er snabbare än någonsin.

* [Logikappar](https://azure.microsoft.com/services/app-service/logic/): Logic Apps-funktionen i Azure App Service hjälper dig att integrera din IoT-lösning till dina befintliga line-of-business-system och automatisera arbetsflödesprocesser. Logic Apps kan utvecklare utforma arbetsflöden som startar från en utlösare och sedan köra en serie steg, regler och åtgärder som använder kraftfulla kopplingar för att integrera med dina affärsprocesser. Logic Apps ger ut nyckelfärdig anslutning till ett enormt ekosystem med SaaS, molnbaserade och lokala program.

* [Azure Blob storage](https://azure.microsoft.com/services/storage/): Pålitlig, ekonomisk molnlagring för de data som enheterna skickar till molnet.

## <a name="conclusion"></a>Sammanfattning

Den här artikeln innehåller översikt över implementering information för att utforma och distribuera en IoT-infrastruktur med hjälp av Azure IoT. Konfigurera varje komponent säker är nyckeln i skydd av den totala IoT-infrastrukturen. Designalternativ som är tillgängliga i Azure IoT tillhandahåller en viss nivå av flexibilitet och valmöjligheter; varje alternativ kan dock ha säkerhetsriskerna. Du rekommenderas att var och en av dessa alternativ att utvärderas genom en risk/kostnaden för utvärdering.
