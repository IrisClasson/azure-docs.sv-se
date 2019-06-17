---
title: Integration Runtime i Azure Data Factory | Microsoft Docs
description: Mer information om Integration Runtime i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 7c86577abe1e8e158299e3a6aee2cff7f3568241
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427151"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime i Azure Data Factory
Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att ge olika nätverksmiljöer integrationsfunktioner:

- **Data Flow**: Köra en [dataflöde](concepts-data-flow-overview.md) i hanterade Azure-beräkningsmiljö.  
- **Dataförflyttning**: Kopiera data mellan datalager i offentliga nätverk och datalager i privat nätverk (lokalt eller virtuellt privat nätverk). Den ger stöd åt inbyggda anslutningsappar, konvertering av format, kolumnmappning och bättre och skalbar dataöverföring.
- **Aktivitetssändning**:  Skick och övervaka transformeringsaktiviteter som är som körs på en rad olika Beräkningstjänster som Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server och mer.
- **SSIS paketkörning**: Internt köra SQL Server Integration Services (SSIS)-paket i en hanterad Azure-beräkningsmiljö.

I Data Factory definierar en aktivitet åtgärden som ska utföras. En länkad tjänst definierar ett datalager som mål eller en beräkningstjänst. Integration Runtime utgör bryggan mellan aktiviteten och länkade tjänster.  Det refereras till av den länkade tjänsten eller aktivitet och tillhandahåller beräkningsmiljön där aktiviteten körs på eller skickas från. På så sätt kan aktiviteten utföras i regionen som är den närmaste möjliga till måldatalagret eller beräkningstjänsten på det bästa sättet samtidigt som den uppfyller säkerhets- och efterlevnadsbehoven.

## <a name="integration-runtime-types"></a>Integration Runtime
Data Factory erbjuder tre typer av Integration Runtime och du bör välja den typ som fungerar bäst med dataintegreringstjänsterna och de nätverksmiljöbehov du har.  Dessa tre typer är:

- Azure
- Egen värd
- Azure-SSIS

I följande tabell beskrivs funktioner och nätverksstöd för varje Integration Runtime-typ:

IR-typ | Offentligt nätverk | Privat nätverk
------- | -------------- | ---------------
Azure | Data Flow<br/>Dataförflyttning<br/>Aktivitetssändning | &nbsp;
Egen värd | Dataförflyttning<br/>Aktivitetssändning | Dataförflyttning<br/>Aktivitetssändning
Azure-SSIS | Körning av SSIS-paket | Körning av SSIS-paket

Följande diagram visar hur olika IR-körningar kan användas i kombination för att ge omfattande dataintegrationsfunktioner och nätverksstöd:

![Olika typer av Integration Runtime](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
En Azure Integration Runtime kan:

- Köra dataflöden i Azure 
- Köra kopieringsaktivitet mellan molndatalager
- Skicka följande transformeringsaktiviteter i offentligt nätverk: Databricks Notebook / Jar / Python-aktivitet, HDInsight Hive-aktivitet, HDInsight Pig-aktivitet, HDInsight MapReduce-aktivitet, HDInsight Spark-aktivitet, HDInsight-strömningsaktivitet, Machine Learning Batch Execution aktivitet, Machine Learning-resursuppdatering aktiviteter, lagringsprocedur-aktivitet, Data Lake Analytics U-SQL-aktivitet, .NET-anpassad aktivitet, Web-aktivitet, Lookup-aktivitet och GetMetaData-aktivitet.

### <a name="azure-ir-network-environment"></a>Azure IR-nätverksmiljö
Azure Integration Runtime stöder anslutning till datalager och Beräkningstjänster med offentligt tillgängliga slutpunkter. Använd Integration Runtime med egen värd för Azure Virtual Network-miljön.

### <a name="azure-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i Azure IR
Med Azure Integration Runtime får du en helt hanterad, serverlös beräkning i Azure.  Du behöver inte bekymra dig om infrastrukturetablering, programvaruinstallation, uppdatering eller skalbarhet.  Dessutom betalar du bara för den faktiska användningen.

Med Azure Integration Runtime får du interna beräkningsfunktioner för att flytta data mellan molndatalager på ett säkert, pålitligt sätt med höga prestanda.  Du kan ange hur många dataintegreringsenheter som ska användas för kopieringsaktiviteten, och beräkningsstorleken för Azure IR skalas elastiskt upp efter detta utan att du behöver justera storleken på Azure Integration Runtime. 

Aktivitetssändning är en enkel åtgärd för att dirigera aktiviteten till målberäkningstjänsten, så du behöver inte skala upp beräkningsstorleken för det här scenariot.

Information om hur du skapar och konfigurerar Azure IR finns i How to create and configure Azure IR (Så här skapar och konfigurerar du Azure IR) och under ”så här gör du”-guiderna. 

> [!NOTE] 
> Azure Integration runtime har egenskaper som rör dataflöde körning, som definierar den underliggande beräkningsinfrastrukturen som används för att köra dataflöden på. 

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd
En IR med egen värd kan:

- Köra kopieringsaktivitet mellan molndatalager och ett datalager i privat nätverk.
- Skicka följande transformeringsaktiviteter mot beräkningsresurser i lokalt eller Azure Virtual Network: HDInsight Hive-aktivitet (BYOC ta med din egen kluster), HDInsight Pig-aktivitet (BYOC), HDInsight MapReduce-aktivitet (BYOC), HDInsight Spark-aktivitet (BYOC), HDInsight Streaming-aktivitet (BYOC), Machine Learning-batchkörningsaktivitet, Machine Learning Uppdatera resursaktiviteter, lagringsprocedur-aktivitet, Data Lake Analytics U-SQL-aktivitet, .NET-anpassad aktivitet, Lookup-aktivitet och GetMetaData-aktivitet.

> [!NOTE] 
> Använda Integration Runtime med egen värd för att stödja datalager som kräver egen drivrutin, till exempel SAP Hana, MySQL osv.  Mer information finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="self-hosted-ir-network-environment"></a>IR-nätverksmiljö med egen värd
Om du vill utföra dataintegration säkert i en privat nätverk-miljö, som har skymd sikt från offentligt moln-miljön, kan du installera IR med egen värd i en lokal miljö bakom företagsbrandväggen, eller i ett virtuellt privat nätverk.  IR med egen värd utför bara utgående HTTP-baserade anslutningar till öppet internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i IR med egen värd
IR med egen värd måste installeras på en lokal dator eller en virtuell dator i ett privat nätverk. För närvarande stöder vi bara att IR med egen värd körs på ett Windows-operativsystem.  

För hög tillgänglighet och skalbarhet kan du skala ut IR med egen värd genom att associera den logiska instansen med flera lokala datorer i aktiv/aktiv-läge.  Om du vill ha mer information läser du artikeln om hur du skapar och konfigurerar IR med egen värd under ”så här gör du”-guiderna.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime
Om du vill lyfta och skifta befintlig SSIS-arbetsbelastning kan du skapa en Azure-SSIS IR för att köra SSIS-paket internt.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR-nätverksmiljö
Azure-SSIS IR kan etableras i offentliga eller privata nätverk.  Åtkomst till lokala data stöds genom att koppla Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i Azure-SSIS IR
Azure-SSIS IR är ett helt hanterat kluster av virtuella Azure-datorer särskilt avsedda att köras SSIS-paketen. Du kan använda din egen Azure SQL Database eller hanterad instans-server som värd för katalogen med SSIS-projekt /-paket (SSISDB) som ska kopplas till den. Du kan skala upp kraften i beräkningen genom att ange nodstorlek och skala ut den genom att ange antalet noder i klustret. Du kan hantera kostnaden för att köra Azure-SSIS Integration Runtime genom att stoppa och starta den efter önskemål.

Om du vill ha mer information läser du artikeln om hur du skapar och konfigurerar Azure-SSIS IR under ”så här gör du”-guiderna.  När du har skapat den kan du distribuera och hantera dina befintliga SSIS-paket med få eller inga ändringar med välbekanta verktyg, som SQL Server Data Tools (SSDT) och SQL Server Management Studio (SSMS), precis som när du använder SSIS lokalt.

Mer information om Azure-SSIS runtime finns i följande artiklar: 

- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Anvisningar: Skapa en Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökas med självstudien och innehåller instruktioner för hur du använder Azure SQL Database Managed Instance och ansluter IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 

## <a name="integration-runtime-location"></a>Integration Runtime-plats
Data Factory-platsen är där metadata för datafabriken lagras och där utlösningen av pipeline initieras. Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster. Det här beteende realiseras via [globalt tillgängligt IR](https://azure.microsoft.com/global-infrastructure/services/) för att säkerställa dataefterlevnad, effektivitet och minskade kostnader för nätverksegress.

IR-platsen definierar platsen för backend-beräkningen och i stort sett platsen där dataflytt, aktivitetssändning och SSIS-paketkörning utförs. IR-platsen kan skilja sig från platsen som datafabriken tillhör. 

### <a name="azure-ir-location"></a>Azure IR-plats
Du kan ställa in en vissa plats för en Azure IR varmed dataflytter eller aktivitetssändningar sker i den specifika regionen. 

Om du väljer att använda den **löses av Azure IR** som är standard, 

- För kopieringsaktivitet kommer ADF att försöka identifiera ditt mål- och källdatalager för att välja den bästa platsen, antingen i samma region om den är tillgänglig eller den närmaste inom samma geografiska område. Om det inte går att avgöra används datafabriksregionen.

- För sökning/GetMetadata/ta bort aktivitetskörning (även kallat pipelineaktiviteter) transformeringsaktivitet skicka (även kallat externa aktiviteter) och redigera åtgärder (Testa anslutning, bläddra mapplistan och Tabellista förhandsgranska data), ADF använder IR i data factory-region.

- För Data flöda använder ADF IR i data factory-region. 

  > [!TIP] 
  > Ett bra tips är att se till att dataflödet körs i samma region som dina motsvarande datalager (om möjligt). Du kan åstadkomma detta genom löses av Azure IR (om data store platsen är samma som Data Factory-platsen) eller genom att skapa en ny Azure IR-instans i samma region som dina datalager och sedan köra dataflödet på den. 

Du kan övervaka vilken IR plats som börjar gälla under körning av aktiviteten i övervakningsvyn för pipeline-aktivitet i gränssnittet eller en aktivitetsövervakningsnyttolast.

>[!TIP]
>Om du måste efterleva strikta datakrav och måste säkerställa att data inte lämnar ett visst område kan du uttryckligen skapa en Azure IR i den regionen och hänvisa den länkade tjänsten till denna IR med egenskapen ConnectVia. Till exempel om du vill kopiera data från en blob i Storbritannien, Syd, till SQL DW i Storbritannien, Syd, och vill se till att data inte lämnar Storbritannien ska du skapa en Azure-IR i Storbritannien, Syd, och länka båda länkade tjänster till denna IR.

### <a name="self-hosted-ir-location"></a>Namn på IR med egen värd
IR med egen värd registreras logiskt hos Data Factory och du får beräkningen som används för att stödja dess funktioner. Det finns därför ingen uttrycklig platsegenskapen för IR med egen värd. 

När IR med egen värd används för att utföra dataflyttning extraherar den data från källan och skriver dem till målet.

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR-plats
Att välja rätt plats för Azure-SSIS IR är viktigt för att uppnå höga prestanda i dina arbetsflöden för extrahering, transformering och laddning (ETL).

- Platsen för din Azure-SSIS IR måste inte vara samma som platsen för din datafabrik, men det bör vara samma som platsen för din egen Azure SQL Database/hanterad instans-server där SSISDB ska finnas. På så sätt kan din Azure-SSIS Integration Runtime enkelt få åtkomst till SSISDB utan att det medför överdriven trafik mellan olika platser.
- Om du inte har en befintlig Azure SQL Database/hanterad instans-server som värd för SSISDB, men du har lokala datakällor /-mål, bör du skapa en ny Azure SQL Database/hanterad instans-server i ett virtuellt nätverk som är anslutna till samma plats ditt lokala nätverk.  På så sätt kan du skapa din Azure-SSIS IR med den nya Azure SQL Database/hanterad instans-servern och koppla det virtuella nätverket, allt på samma plats, vilket effektivt minimerar dataflyttningar mellan olika platser.
- Om platsen för din befintliga Azure SQL Database/hanterad instans-server där SSISDB finns inte är samma som platsen för ett virtuellt nätverk som är anslutna till ditt lokala nätverk, först skapa din Azure-SSIS IR med en befintlig Azure SQL-databas / Hanterad instans-server och ansluta till ett annat virtuellt nätverk på samma plats och sedan konfigurera ett virtuellt nätverk med virtuell nätverksanslutning mellan olika platser.

I följande diagram visas platsinställningar för Data Factory och dess Integration Runtime-instanser:

![Integration Runtime-plats](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Bestämma vilken IR som ska användas

### <a name="copy-activity"></a>Kopieringsaktivitet

För kopieringsaktiviteten kräver den länkade tjänster-källa och länkade tjänster-mottagare för att definiera dataflödets riktning. Följande logik används till att bestämma vilken Integration Runtime-instans som används för att utföra kopieringen: 

- **Kopiera mellan två molndatakällor**: när både käll- och mottagarnoderna för länkade tjänster använder Azure IR, använder ADF en regional Azure IR om du har angett eller automatiskt bestämt en plats för Azure IR om du väljer lös IR automatiskt (standard), enligt beskrivningen i avsnittet [Plats för integreringskörning](#integration-runtime-location).
- **Kopiera mellan molndatalager och datakälla i privat nätverk**: om den länkade källtjänsten eller den länkade mottagartjänsten pekar på en IR med egen värd körs kopieringsaktiviteten på den Integration Runtime med egen värd.
- **Kopiera mellan två datakällor i privat nätverk**: Båda den länkade käll- och mottagartjänsten måste peka på samma instans av Integration Runtime, och den IR används för att köra kopieringsaktiviteten.

### <a name="lookup-and-getmetadata-activity"></a>Lookup och GetMetadata-aktivitet

Aktiviteterna Lookup och GetMetadata har körts på integreringskörningsmiljön som är associerad med den länkade datalagringstjänsten.

### <a name="transformation-activity"></a>Transformeringsaktivitet

Varje transformeringsaktivitet har en länkad målberäkningstjänst, som pekar på en Integration Runtime. Denna Integration Runtime-instans är där transformeringsaktiviteten skickas från.

### <a name="data-flow-activity"></a>Data flödesaktivitet

Data flödesaktivitet körs på integreringskörningen som är associerade med den. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa Azure integration runtime](create-azure-integration-runtime.md)
- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökas med självstudien och innehåller instruktioner för hur du använder Azure SQL Database Managed Instance och ansluter IR till ett virtuellt nätverk. 
