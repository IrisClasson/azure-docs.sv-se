---
title: Migrera BizTalk Server EDI-lösningar till BizTalk Services | Microsoft Docs
description: Lär dig hur du kan migrera dina Microsoft BizTalk Server EDI-lösningar till Microsoft Azure BizTalk Services (MABS)
services: biztalk-services
ms.service: biztalk-services
author: jonfancey
ms.author: jonfan
manager: jeconnoc
ms.topic: article
ms.date: 07/31/2018
ms.reviewer: jonfan, LADocs
ms.suite: integration
ms.openlocfilehash: e6f0b11c99cbe8778b51024c418ffba70da61a77
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917387"
---
# <a name="migrate-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrera BizTalk Server EDI-lösningar till BizTalk Services: Teknisk Guide

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Författare: Tim Wieman och Nitin Mehrotra

Granskare: Karthik Bharthy

Skrivits med:  Microsoft Azure BizTalk Services – februari 2014-versionen.

## <a name="introduction"></a>Introduktion
Electronic Data Interchange (EDI) är en av de vanligaste medel genom vilka företag exchange-data elektroniskt, kallas även för Business-to-Business eller B2B-transaktioner. BizTalk Server har stöd för över tio eftersom versionen av första BizTalk Server EDI. Microsoft fortsätter med BizTalk Services stöd för EDI-lösningar på Microsoft Azure-plattformen. B2B-transaktioner är huvudsakligen externa för en organisation och därför är det enklare att implementera om den har implementerats på en molnplattform. Microsoft Azure tillhandahåller den här funktionen via BizTalk Services.

Även om vissa kunder tittar på BizTalk Services ”grönt fält” plattform för nya EDI-lösningar, har många kunder aktuella BizTalk Server EDI-lösningar som kan de vilja migrera till Azure. Eftersom BizTalk Services EDI har byggts är baserat på samma nyckelentiteterna som BizTalk Server EDI-arkitektur (trading partner, entiteter, avtal), det möjligt att migrera BizTalk Server EDI-artefakter till BizTalk Services.

Det här dokumentet beskriver några av skillnaderna ingår i migrera BizTalk Server EDI-artefakter till BizTalk Services. Det här dokumentet förutsätter kunskaper om BizTalk Server EDI-bearbetning och Trading Partner-avtal. Läs mer på BizTalk Server EDI [Trading Partner Management med hjälp av BizTalk Server](/biztalk/core/trading-partner-management-using-biztalk-server).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Vilken version av BizTalk Server EDI-artefakter kan migreras till BizTalk Services?
BizTalk Server EDI-modulen förbättrats avsevärt för BizTalk Server 2010, när den har remodeled för att inkludera partner, profiler och avtal. BizTalk Services använder samma modell för att organisera affärspartner och business-avdelningar inom dessa affärspartner. Därför är migrerar EDI artefakter från BizTalk Server 2010 och senare versioner till BizTalk Services, en mycket mer okomplicerat process. Om du vill migrera EDI-artefakter som associeras med versioner före BizTalk Server 2010 måste du först uppgradera till BizTalk Server 2010 och sedan migrera artefakterna EDI till BizTalk Services.

## <a name="scenariosmessage-flow"></a>Scenarier/Message flöde
Som med BizTalk Server är EDI-bearbetning i BizTalk Services uppbyggd kring en Trading Partner Management (TPM)-lösning. TPM-lösningen har följande viktiga komponenter:

* Handelspartner, som representerar organisationen i en B2B-transaktion.
* Profiler som representerar avdelningar inom en handelspartner.
* Trading partner (eller avtal), som representerar företagsavtal mellan två partner-profiler.

Följande bild visar likheter, samt skillnaderna mellan en BizTalk Server EDI-lösning och BizTalk Services EDI-lösning:

![][EDImessageflow]

Viktiga skillnader och likheter mellan ett EDI-lösningsflödet i BizTalk Server och BizTalk Services är:

* Precis som BizTalk Server använder en EDIReceive pipeline för att få ett EDI-meddelande och en EDISend pipeline för att skicka ett EDI-meddelande, använder BizTalk Services en EDI får brygga att ta emot och skicka EDI bridge för att skicka ut EDI-meddelanden. I BizTalk Server, pipelines är kopplade till ett avtal med hjälp av skicka eller ta emot portar. I BizTalk Services, avtalet själva anger skicka eller ta emot brygga.
* I BizTalk Server, när EDIReceive pipelinen bearbetar EDI-meddelande dumpas meddelandet till en SQL Server-databas. Pipelinen EdiSend sedan hämtar meddelandet från SQL Server-databasen, bearbetar dessa och skickas till handelspartner.
  
    I BizTalk Services, när EDI fått brygga behandlar EDI-meddelande, dirigeras meddelandet till en extern process. Extern process kan köras på Microsoft Azure eller lokalt. Extern process bör dirigera meddelandet till EDI skicka bryggan; Skicka bryggan hämtar inte meddelandet sin natur. När behandlingen av meddelandet dirigerar bryggan EDI skicka meddelandet till handelspartner.

BizTalk Services får du en enkel att använda konfigurationsupplevelse för att snabbt skapa och distribuera en B2B-avtal mellan handelspartner utan att konfigurera alla Microsoft Azure Compute-instanser (Web- eller Worker-roller), valfri Microsoft Azure SQL-databas eller någon Microsoft Azure storage-konton. Mer komplicerade scenarier kräver integreras i arbetsflöden eller annan bearbetning som tjänst ”runt kanterna” i en Trading Partner avtal, det vill säga före eller efter Trading Partner avtal EDI bridge-bearbetning. I detalj inträffar av händelser följande under ett EDI-meddelandebehandling i BizTalk Services.

1. Ett EDI-meddelande tas emot från handelspartner, Fabrikam.  För att ta emot EDI-meddelanden från handelspartner BizTalk Services har stöd för transport som FTP-, SFTP, AS2 och HTTP/S.
2. Handel partner avtal receive side bearbetningen Disassemblerar EDI-meddelande till XML-format.  Du kan dirigera nedmonterade EDI-meddelanden (i XML-format) till Service Bus-slutpunkter som en Service Bus Relay-slutpunkt, Service Bus-ämne, Service Bus-kö eller en brygga med BizTalk Services.
3. Nedmonterade XML-meddelanden kan sedan tas emot från slutpunkten för ytterligare anpassad bearbetning.  De här slutpunkterna kunde bearbetas av en komponent som en lokal eller en Microsoft Azure Compute-instans som bearbetar meddelandet i en Windows-arbetsflödet (WF) eller Windows Communication Foundation (WCF) tjänst, till exempel.
4. ”Sändningssidan bearbetningen” av handelspartner avtalet sedan monterar XML-meddelande till EDI-format och skickar det till handelspartner, Contoso.  För att skicka EDI-meddelanden för handelspartners, stöder BizTalk Services protokoll som de som används för att ta emot EDI-meddelanden.

Ytterligare det här dokumentet ger konceptuell vägledning på migrera några av de olika BizTalk Server EDI-artefakterna till BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Skicka och ta emot portar till handelspartner
I BizTalk Server ställer du in mottagningsplatser och ta emot portar för att ta emot EDI/XML-meddelanden från affärspartner och du har konfigurerat skicka portar för att skicka EDI/XML-meddelanden till handelspartner. Du binder sedan upp dessa portar till ett handelspartneravtal med hjälp av BizTalk Server Administration-konsolen. I BizTalk Services, de platser där du får meddelanden från handelspartner där du skickar meddelanden till handelspartner är konfigurerade som en del av handelspartneravtal (som en del av Transport inställningar) i BizTalk Services-portalen.  Så har du inte verkligen begreppet ”skicka portar” och ”ta emot platser” sig självt, i BizTalk Services. Mer information finns i [skapa avtal](/previous-versions/azure/hh689908(v=azure.100)).

## <a name="pipelines-bridges"></a>Pipelines (bryggor)
Pipeliner är i BizTalk Server EDI meddelande bearbetning entiteter som kan även inkludera anpassad logik för specifika bearbetningsfunktioner som krävs av programmet. För BizTalk Services skulle motsvarande ett EDI-brygga. Men i BizTalk Services, för tillfället EDI-bryggor ”stängs”.  Det vill säga du kan inte lägga till dina egna anpassade aktiviteter till ett EDI-brygga. Valfri anpassad bearbetning måste göras utanför EDI-brygga i ditt program, antingen innan eller efter meddelandet anger bryggan konfigurerat som en del av handel partneravtalet. EAI-bryggor har du möjlighet att göra anpassad bearbetning. Om du vill anpassad bearbetning, kan du använda EAI-bryggor innan eller efter meddelandet bearbetas av EDI-brygga. Mer information finns i [hur du inkludera anpassad kod i bryggor](/previous-versions/azure/dn232389(v=azure.100)).

Du kan infoga en Publicera/prenumerera-flöde med anpassad kod och/eller med hjälp av Service Bus-meddelandefunktionen köer och ämnen innan handelspartneravtal tar emot meddelandet eller när avtalet kan bearbeta meddelandet och skickar den till en Service Bus-slutpunkt.

Se **scenarier/Message Flow** i den här artikeln för meddelandemönstret för flödet.

## <a name="agreements"></a>Avtal
Om du är bekant med BizTalk Server 2010 Trading Partner avtalen används för EDI-bearbetning kan sedan BizTalk Services handelspartneravtal Se bekant ut. De flesta avtal inställningarna är samma och Använd samma terminologi. I vissa fall kan avtal inställningarna är mycket enklare jämfört med samma inställningar i BizTalk Server. Microsoft Azure BizTalk Services stöder X12 och EDIFACT AS2 transport.

Microsoft Azure BizTalk Services tillhandahåller också en **TPM datamigrering** verktyg för att migrera handelspartners och avtal från BizTalk Server Trading Partner-modulen till BizTalk Services-portalen. Datamigrering för TPM-verktyget är tillgängligt som en del av ett paket, som kan hämtas från den [MABS SDK](https://go.microsoft.com/fwlink/p/?LinkId=235057). Paketet innehåller också ett viktigt-fil som innehåller instruktioner om hur du använder verktyget och grundläggande felsökningsinformation för verktyget.

## <a name="schemas"></a>Scheman
BizTalk Services får EDI-scheman som kan användas i BizTalk Services-lösningar.  Dessutom kan BizTalk Server EDI-scheman också användas med BizTalk Services eftersom rotnoden i EDI-schemat är samma för BizTalk Server, samt BizTalk Services. Därför kommer du att kunna direkt ta din BizTalk Server EDI-scheman och använda dem i EDI-lösningar som du utvecklar med hjälp av BizTalk Services. Du kan också hämta scheman från den [MABS SDK](https://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Maps (transformationer)
Kartor i BizTalk Server kallas transformeringar i BizTalk Services. Migrera mappningar från BizTalk Server till BizTalk Services kan vara något av de mer avancerade aktiviteterna att uppnå (beroende på komplexiteten för kartan). Mappning av verktyget används för BizTalk Services skiljer sig från BizTalk-mappning. Även om mappningen ser ut huvudsakligen densamma, skiljer sig underliggande mappningsformat. Functoids (kallas **kartan Operations** i BizTalk Services) tillgängliga för användarna är också olika.  Du kan inte i praktiken direkt använda en BizTalk-karta i BizTalk Services. Inte alla functoids som är tillgängliga i BizTalk Server är också tillgängliga som kartan funktioner i BizTalk Services.

### <a name="new-transform-operations"></a>Ny omvandlingsåtgärder
När listan över karta omvandlingsåtgärder tillgängliga kan verka ganska skiljer sig från BizTalk Server-mappning, har omvandlar för BizTalk Services nya sätt att utföra samma uppgifter. Till exempel BizTalk Services omvandlar ha **Liståtgärder** tillgängliga. Detta var inte tillgänglig i BizTalk-mappning.  Den **Liståtgärder** gör att du kan skapa och arbeta med en ”lista”, där en lista är en uppsättning objekt (även kallat ”rader”) och varje objekt kan ha flera medlemmar (även kallat ”kolumner”).  Du kan sortera listan, Välj objekt baserat på ett villkor osv.

Ett annat exempel på nya funktioner i BizTalk Services omvandlar är den **Loop Operations**.  Det är svårt att skapa kapslade loopar i BizTalk Server-mappning.  Därmed läggs Loop kartan åtgärder för BizTalk Services omvandlar.

Men ett annat exempel är den **If-Then-Else** uttryck kartan igen.  Gör en if-then-else åtgärd var möjligt i BizTalk-mappning, men det krävs flera functoids att utföra en till synes enkla uppgift.

### <a name="migrating-biztalk-server-maps"></a>Migrera BizTalk Server-kartor
Microsoft Azure BizTalk Services är ett verktyg för att migrera BizTalk Server mappar till BizTalk Services-transformeringar. Den **BTMMigrationTool** är tillgänglig som en del av den **verktyg** paket som medföljer den [BizTalk Services SDK-nedladdning](https://go.microsoft.com/fwlink/p/?LinkId=235057). Mer information om verktyget finns i [konvertera en BizTalk-karta att omvandla en BizTalk Services](/previous-versions/azure/hh949812(v=azure.100)).

Du kan också titta på ett exempel av Sandro Pereira, chef för BizTalk-MVP och om hur du [migrera BizTalk Server maps till BizTalk Services transformeringar](https://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orkestreringar
Om du vill migrera BizTalk Server-dirigering av bearbetning till Microsoft Azure skulle orkestreringarna behöva skrivas eftersom Microsoft Azure inte stöder köra BizTalk Server-orkestreringar.  Du kan skriva om orchestration-funktioner i en tjänst i Windows Workflow Foundation 4.0 (WF4).  Detta är en fullständig omarbetning eftersom det inte finns för närvarande ingen migrering från BizTalk Server-orkestreringar till WF4. Här är några resurser för Windows-arbetsflödet:

* [*Hur du integrerar en WCF-tjänst för arbetsflödet med Service Bus-köer och ämnen* ](https://blogs.msdn.microsoft.com/paolos/2013/04/09/how-to-integrate-a-wcf-workflow-service-with-service-bus-queues-and-topics/) av Paolo Salvatori. 
* [*Skapa appar med Windows Workflow Foundation och Azure* session](https://go.microsoft.com/fwlink/p/?LinkId=237314) från Build 2011-konferensen.
* [*Windows Workflow Foundation Developer Center*](https://docs.microsoft.com/previous-versions/dotnet/articles/ee342461(v=msdn.10)).
* [*Dokumentation om Windows Workflow Foundation 4 (WF4)* ](/dotnet/framework/windows-workflow-foundation/index) på MSDN.

## <a name="other-considerations"></a>Annat att tänka på
Här följer några saker som du måste utföra när du använder BizTalk Services.

### <a name="fallback-agreements"></a>Återställningsplats avtal
BizTalk Server EDI-bearbetning har begreppet ”reserv avtal”.  BizTalk Services har **inte** har ett begrepp som reserv avtal hittills.  BizTalk-dokumentationen avsnitten [The rollen av avtal i EDI-bearbetning](https://go.microsoft.com/fwlink/p/?LinkId=237317) och [reserv avtal egenskaper eller konfigurera globala](/biztalk/core/configuring-global-or-fallback-agreement-properties) information om hur används reserv avtal i BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Routning till flera mål
BizTalk Services-bryggor, i det aktuella tillståndet stöder inte vidarebefordra meddelanden till flera destinationer med ett Publicera-prenumerera modellen. I stället kan du vidarebefordra meddelanden från en brygga med BizTalk Services till ett Service Bus-ämne som sedan kan ha flera prenumerationer att ta emot meddelandet vid mer än en slutpunkt.

## <a name="see-also"></a>Se även
[LOB-lösningar i Azure](https://azure.microsoft.com/solutions/lob-applications)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
