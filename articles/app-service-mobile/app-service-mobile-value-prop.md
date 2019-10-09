---
title: Om Mobile Apps i Azure App Service
description: Läs om fördelarna med App Service i dina företagsmobilappar.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 48f7066f2db96165daa104dcf32fe8e2e2cd55cf
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025223"
---
# <a name="getting-started"> </a>Om Mobile Apps i Azure App Service

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

Azure App Service är ett helt hanterat [PaaS](https://azure.microsoft.com/overview/what-is-paas/)-erbjudande (plattform som en tjänst) för professionella utvecklare. Tjänsten ger en omfattande uppsättning funktioner för webb-, mobil- och integrationsscenarier. 

Funktionen Mobile Apps i Azure App Service ger företagsutvecklare och systemintegratörer en plattform för utveckling av mobilappar med global tillgänglighet och hög skalbarhet.

![Visuell översikt över Mobile Apps-funktioner](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Varför Mobile Apps?
Med Mobile Apps-funktionen kan du:

* **Skapa plattformsspecifika och plattformsoberoende appar**: Oavsett om du bygger appar särskilt för iOS, Android eller Windows eller plattformsoberoende Xamarin- eller Cordova-appar (PhoneGap) kan du använda App Service med plattformsspecifika SDK:er.
* **Koppla ihop dina affärssystem**: Med Mobile Apps kan du lägga till företagsövergripande inloggning på några minuter och ansluta till resurser som lagras lokalt eller i molnet.
* **Bygga appar som kan användas offline med datasynkronisering**: Du kan öka personalens produktivitet genom att bygga appar som fungerar offline och synkronisera data i bakgrunden med Mobile Apps när det finns anslutning till någon källa med företagsdata eller Saas-API:er (programvara som en tjänst).
* **Skicka push-meddelanden till miljontals användare på några sekunder**: Du kan engagera kunderna genom att skicka direkta push-meddelanden anpassade efter deras behov vid precis rätt tidpunkt – oavsett vilken enhet de använder.

## <a name="mobile-apps-features"></a>Funktioner i Mobile Apps
Följande funktioner är viktiga för molnkompatibel mobilutveckling:

* **Autentisering och auktorisering**: Stöd för identitetsprovidrar, till exempel Azure Active Directory för företagsautentisering, och även leverantörer via sociala nätverk som Facebook, Google, Twitter eller Microsoft-konton. Mobile Apps erbjuder en OAuth 2.0-tjänst för varje provider. Du kan även integrera SDK:n för identitetsprovidern för leverantörsspecifika funktioner.

    Läs mer om [autentiseringsfunktionerna].

* **Dataåtkomst**: Mobile Apps erbjuder en mobilvänlig OData v3-datakälla som är kopplad till Azure SQL Database eller en lokal SQL-server. Eftersom här tjänsten kan byggas på Entity Framework kan du lätt kan integrera med andra NoSQL- och SQL-dataleverantörer, till exempel [Azure Table Storage], MongoDB, [Azure Cosmos DB] och SaaS-API-leverantörer som Office 365 och Salesforce.com.

* **Offlinesynkronisering**: Klient-SDK:erna gör det enkelt att bygga robusta mobilappar med kort svarstid som fungerar med en offlinedatamängd. Du kan synkronisera datamängden automatiskt med data på serverdelen, inklusive konfliktlösningsstöd.

  Läs mer om [datafunktionerna].

* **Push-meddelanden**: Klient-SDK:erna integreras sömlöst med registreringsfunktionerna i Azure Notification Hubs, vilket innebär att du kan skicka push-meddelanden till miljontals mottagare samtidigt.

  Läs mer om [push-meddelandefunktionerna].

* **Klient-SDK:er**: Det finns en komplett uppsättning klient-SDK:er för plattformsspecifik utveckling ([iOS], [Android] och [Windows]), plattformsoberoende utveckling ([Xamarin.iOS och Xamarin.Android], [Xamarin.Forms]) samt hybridprogramutveckling ([Apache Cordova]). Alla klient-SDK:er fås med MIT-licens och har öppen källkod.

## <a name="azure-app-service-features"></a>Funktioner i Azure App Service
Följande plattformsfunktioner är användbara i produktionsmiljöer för mobilappar:

* **Automatisk skalning**: Med App Service kan du snabbt skala upp eller ut för att hantera inkommande kundbelastning. Du kan manuellt välja antal och storleken på virtuella datorer eller ställa in automatisk skalning av mobilappsservern utifrån belastning eller schema.

  Läs mer om [automatisk skalning].

* **Mellanlagringsmiljöer**: Med App Service kan du köra flera olika versioner av webbplatsen så att du kan genomföra A- och B-testning, testa i produktionsmiljö som en del i en större DevOps-plan och mellanlagra en ny serverdel direkt på plats.

  Läs mer om [mellanlagringsmiljöer].

* **Kontinuerlig distribution**: App Service kan integreras med vanliga _källkontrollhanteringssystem_ (SCM), så att du enkelt kan distribuera en ny version av en serverdel.

  Läs mer om [distributionsalternativ](../app-service/deploy-local-git.md).

* **Virtuella nätverk**: App Service kan ansluta till lokala resurser genom virtuella nätverk, Azure ExpressRoute eller hybridanslutningar.

  Läs mer om [hybridanslutningar], [virtuella nätverk] och [ExpressRoute].

* **Isolerade och dedikerade miljöer**: Du kan köra App Service i en helt isolerad och dedikerad miljö för säker körning av Azure App Service-appar. Miljön är perfekt för programarbetsbelastningar som kräver storskalighet, isolering eller säker nätverksåtkomst.

  Läs mer om [App Service-miljöer].

## <a name="next-steps"></a>Nästa steg

Kom igång med Mobile Apps i Azure App Service genom att slutföra den här [självstudiekursen]. I kursen lär du dig grunderna i hur du skapar en valfri mobil serverdelstjänst och klient. Kursen omfattar också integrering av autentisering, offlinesynkronisering och push-meddelanden. Du kan slutföra kursen flera gånger, en gång för varje klientprogram.

Mer information om Mobile Apps finns i vår [utbildningsväg].
Mer information om Azure App Service-plattformen finns på [Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[självstudiekursen]: app-service-mobile-ios-get-started.md
[Azure Table Storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[autentiseringsfunktionerna]: ./app-service-mobile-auth.md
[datafunktionerna]: ./app-service-mobile-offline-data-sync.md
[push-meddelandefunktionerna]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS och Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatisk skalning]: ../app-service/manage-scale-up.md
[mellanlagringsmiljöer]: ../app-service/deploy-staging-slots.md
[hybridanslutningar]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuella nätverk]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[App Service-miljöer]: ../app-service/environment/intro.md
[utbildningsväg]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Azure App Service]: ../app-service/overview.md
