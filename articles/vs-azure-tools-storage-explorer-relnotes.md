---
title: Viktig information om Microsoft Azure Lagringsutforskaren
description: Viktig information för Microsoft Azure Lagringsutforskaren
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: c6416e36d7d2723577ca0d2009fb7bbad24f3b6a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154704"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Viktig information om Microsoft Azure Lagringsutforskaren

Den här artikeln innehåller viktig information om Azure Storage Explorer 1.4.3 versionen, samt viktig information för tidigare versioner.

[Microsoft Azure Lagringsutforskaren](./vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux.

## <a name="version-180"></a>Version 1.8.0
5/1/2019

### <a name="download-azure-storage-explorer-180"></a>Hämta Azure Storage Explorer 1.8.0
- [Azure Storage Explorer 1.8.0 för Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.8.0 för Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.8.0 för Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Ny

* Den integrerade AzCopy-versionen har blivit uppdaterad till version 10.1.0.
* CTRL / Cmd + R kan nu användas för att uppdatera fokus redigeraren. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API-version har ändrats till 2017-04-17.
* Dialogrutan Hantera åtkomst för ADLS Gen2 kommer nu se masken synkroniserade på ett sätt liknande till andra verktyg för behörigheter av POSIX. Användargränssnittet kommer också att varna dig om en ändring görs som gör att behörigheterna för en användare eller grupp att överskrida gränserna för masken. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Flagga för att beräkna och ange MD5-hash har nu aktiverats för AzCopy uppladdningar. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Förhandsversionsfunktioner

* Enheten kod flow inloggningen finns nu att förhandsgranska. Aktivera det genom att gå till ”förhandsversion” → ”Använd kod Flow enhetsinloggning”. Vi rekommenderar att alla användare som har haft problem med tomma inloggning-fönster för den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning.
* Lagringsutforskaren integrerat med AzCopy är nu tillgänglig för förhandsgranskning. Aktivera det genom att gå till ”förhandsversion” → ”Använd AzCopy för förbättrad Blob ladda upp och ladda ned”. BLOB-överföringar har slutförts med AzCopy ska vara snabbare och bättre.

### <a name="fixes"></a>Korrigeringar

* Dialogrutan åtkomstprinciper kommer inte längre ange ett förfallodatum på åtkomst-principer för lagring som inte har ett utgångsdatum. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Vissa ändringar har gjorts att generera SAS-dialog Kontrollera lagrad åtkomstprinciper används korrekt när du genererar en SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* När försök att överföra en icke - 512 byte justerade filen till en sida Blob Storage Explorer nu att exponera ett mer relevant fel. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Kopiera en Blob-behållare där man använder ett visningsnamn misslyckas. Nu används det faktiska namnet på Blob-behållaren. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Skulle inte att utföra vissa åtgärder på ett ADLS-Gen2-mappen som hade unicode-tecken i namnet. Alla åtgärder ska nu fungera. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Kända problem

* När du utför en icke - AzCopy Blob-nedladdning, verifieras inte MD5 för stora filer. Detta beror på en bugg i Storage-SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Lagringsutforskaren vissa management layer-behörigheter för att komma åt dina lagringsresurser. Se den [felsökningsguide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) för mer information.
* Försök att få åtkomst till ADLS Gen2 Blobar när datorn är bakom en proxyserver misslyckas.
* Kopplar från en resurs som är anslutna via SAS-URI, t.ex en blob-behållare kan orsaka ett fel som förhindrar andra bilagor från visas korrekt. Undvik problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om du gör detta inte avblockera du kommentera problemet.
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron. Om du vill undvika det här problemet vid överföring till eller hämta det från en blobbehållare kan använda du funktionen experimentella AzCopy.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Azure Stack har inte stöd för följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack kan resurser resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Om du kör Lagringsutforskaren i Linux måste vissa beroenden installeras först. Kontrollera Storage Explorer [felsökningsguide](https://docs.microsoft.com/en-us/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies) för mer information.

## <a name="previous-releases"></a>Tidigare versioner

* [Version 1.7.0](#version-170)
* [Version 1.6.2](#version-162)
* [Version 1.6.1](#version-161)
* [Version 1.6.0](#version-160)
* [Version 1.5.0](#version-150)
* [Version 1.4.4](#version-144)
* [Version 1.4.3](#version-143)
* [Version 1.4.2](#version-142)
* [Version 1.4.1](#version-141)
* [Version 1.3.0](#version-130)
* [Version 1.2.0 eller senare](#version-120)
* [Version 1.1.0](#version-110)
* [Version 1.0.0](#version-100)
* [Version 0.9.6](#version-096)
* [Version 0.9.5](#version-095)
* [Version 0.9.4 och 0.9.3](#version-094-and-093)
* [Version 0.9.2](#version-092)
* [Version 0.9.1 till och och 0.9.0](#version-091-and-090)
* [Version 0.8.16](#version-0816)
* [Version 0.8.14](#version-0814)
* [Version 0.8.13](#version-0813)
* [Version 0.8.12 och 0.8.11 och 0.8.10](#version-0812-and-0811-and-0810)
* [Version 0.8.9 och 0.8.8](#version-089-and-088)
* [Version 0.8.7](#version-087)
* [Version 0.8.6](#version-086)
* [Version 0.8.5](#version-085)
* [Version 0.8.4](#version-084)
* [Version 0.8.3](#version-083)
* [Version 0.8.2](#version-082)
* [Version 0.8.0](#version-080)
* [Version 0.7.20160509.0](#version-07201605090)
* [Version 0.7.20160325.0](#version-07201603250)
* [Version 0.7.20160129.1](#version-07201601291)
* [Version 0.7.20160105.0](#version-07201601050)
* [Version 0.7.20151116.0](#version-07201511160)

## <a name="version-170"></a>Version 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Hämta Azure Storage Explorer 1.7.0
- [Azure Storage Explorer 1.7.0 för Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.7.0 för Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 för Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Ny

* Du kan nu ändra ägare och ägande grupp vid hantering av åtkomst för en ADLS Gen2 behållare, fil eller mapp.
* På Windows är uppdatering av Storage Explorer från inom produkten nu en inkrementell installation. Detta resulterar i en snabbare uppdatering-upplevelse. Om du föredrar en ren installation så du kan ladda ned den [installer](https://azure.microsoft.com/features/storage-explorer/) själv och sedan installera manuellt. #1089

### <a name="preview-features"></a>Förhandsversionsfunktioner

* Enheten kod flow inloggningen finns nu att förhandsgranska. Aktivera det genom att gå till ”förhandsversion” → ”Använd kod Flow enhetsinloggning”. Vi rekommenderar att alla användare som har haft problem med tomma inloggning-fönster för den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning. #938
* Lagringsutforskaren integrerat med AzCopy är nu tillgänglig för förhandsgranskning. Aktivera det genom att gå till ”förhandsversion” → ”Använd AzCopy för förbättrad Blob ladda upp och ladda ned”. BLOB-överföringar har slutförts med AzCopy ska vara snabbare och bättre.

### <a name="fixes"></a>Korrigeringar

* Du kan nu välja blobtyp som du vill ladda upp som när AzCopy är aktiverat. #1111
* Tidigare, om du har aktiverat serverstatiska webbplatser för ett ADLS Gen2 Storage-konto och appliceras med namnet och nyckeln, Lagringsutforskaren skulle inte har upptäckt att hierarkiskt namnområde har aktiverats. Problemet har åtgärdats. #1081
* I blob-redigeraren bröts sortera efter status eller kvarhållningsdagar återstående. Problemet har åtgärdats. #1106
* Efter 1.5.0, Storage Explorer inte längre väntade på att servern sida kopior som ska finish innan den rapporterar lyckade under en Byt namn på eller kopiera och klistra in. Problemet har åtgärdats. #976
* När du använder funktionen experimentella AzCopy, var kommandot som kopieras när du klickar på ”Kopiera kommandot till Urklipp” inte alltid att köra flödet på egen hand. Nu kan kopieras alla kommandon som behövs för att köra överföringen manuellt. #1079
* Tidigare var ADLS Gen2 blobar inte tillgänglig om du är bakom en proxyserver. Det berodde på en bugg i ett nytt nätverk bibliotek som används av Storage SDK. Ett försök att åtgärda problemet har gjorts i 1.7.0, men vissa personer kan fortsätta att problem uppstå. En fullständig korrigering släpps i en kommande uppdatering. #1090
* I 1.7.0, spara fildialogruta nu korrekt ihåg den senaste plats som du sparade en fil till. #16
* På egenskapspanelen som SKU-nivån av ett lagringskonto visades som kontots typ. Problemet har åtgärdats. #654
* Ibland inte var möjligt att avbryta lånet för en blob, även om du har angett namnet på bloben korrekt. Problemet har åtgärdats. #1070

### <a name="known-issues"></a>Kända problem

* När du använder RBAC kräver Lagringsutforskaren vissa management layer-behörigheter för att komma åt dina lagringsresurser. Se den [felsökningsguide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) för mer information.
* Försök att få åtkomst till ADLS Gen2 Blobar när datorn är bakom en proxyserver misslyckas.
* Kopplar från en resurs som är anslutna via SAS-URI, t.ex en blob-behållare kan orsaka ett fel som förhindrar andra bilagor från visas korrekt. Undvik problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Kopplar från en resurs som är anslutna via SAS-URI, t.ex en blob-behållare kan orsaka ett fel som förhindrar andra bilagor från visas korrekt. Undvik problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om du gör detta inte avblockera du kommentera problemet.
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron. Om du vill undvika det här problemet vid överföring till eller hämta det från en blobbehållare kan använda du funktionen experimentella AzCopy.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Azure Stack har inte stöd för följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack kan resurser resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Version 1.6.2
1/9/2019

### <a name="hotfixes"></a>Snabbkorrigeringar
* 1.6.1 lades entiteter som läggs till ADLS Gen2 ACL: er av ObjectId som användare kan inte alltid som grupper. Endast grupper läggs nu som grupper och enheter, till exempel företagsprogram andService huvudkonton läggs till som användare. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Om ett ADLS Gen2 Storage-konto har inga behållare och anslöts med namnet och nyckeln, skulle Lagringsutforskaren inte identifierar att Lagringskontot har ADLS Gen2. Problemet har åtgärdats. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* I 1.6.0 eller senare, skulle konflikter under kopiera och klistra in inte fråga efter en lösning. I stället misslyckas bara den borttagna kopian. Nu på den första konflikten blir du ombedd hur du vill ska lösas. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Alla verifiering av ObjectIds i dialogrutan Hantera åtkomst har inaktiverats på grund av API-begränsningar. Verifieringen utförs nu endast för användare UPN-namn. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* I dialogrutan Hantera ADLS Gen2 åtkomst kan behörigheter för en grupp inte ändras. Problemet har åtgärdats. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Ladda upp stöd till ADLS Gen2 redigeraren har lagts till dra och släpp. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* URL-egenskapen i egenskapsdialogrutan för ADLS Gen2 filer och mappar saknades ibland en '/'. Problemet har åtgärdats. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Om får den aktuella behörigheten för en behållare för ADLS Gen2 fil eller mapp misslyckas och felet är nu propertly som visas i aktivitetsloggen. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Den tillfälliga sökvägen som skapats för att öppna filer har kortats ned för att minska risken för att skapa en sökväg som är längre än MAX_PATH på Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogrutan Anslut nu visas korrekt när inga användare är inloggad och inga resurser har bifogats. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Spara egenskaperna för icke - HNS Blobar och filer skulle i 1.6.0 eller senare koda värdet för varje egenskap. Detta resulterade i onödiga kodning av värden som innehöll endast ASCII-tecken. Värden kommer nu endast att kodas om de innehåller icke-ASCII-tecken. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Ladda upp en mapp till en icke - HNS blobbehållare misslyckas om en SAS har använts och SAS har inte behörighet att läsa. Problemet har åtgärdats. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Avbryter en AzCopy-överföring fungerade inte. Problemet har åtgärdats. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy misslyckas vid försök att ladda ned en mapp från en ADLS Gen2 Blob-behållare om mappen hade blanksteg i namnet. Problemet har åtgärdats. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* CosmosDB-redigeraren har delats i 1.6.0 eller senare. Det har lösts. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att få åtkomst till din Blob-data via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Om du har loggat in och Storage Explorer kan inte hämta nycklarna för ditt lagringskonto, används en OAuth-token för autentisering när du interagerar med dina data.
* Lagringsutforskaren stöder nu ADLS Gen2 Storage-konton. När Storage Explorer upptäcker det hierarkiskt namnområdet har aktiverats för ett lagringskonto, kommer du att se ”(ADLS Gen2 förhandsgranskning)” bredvid namnet på ditt lagringskonto. Lagringsutforskaren kan identifiera huruvida hierarkiskt namnområde är aktiverad när du är inloggad, eller om du har anslutit ditt Lagringskonto med namnet och nyckeln. Du kan använda Storage Explorer för ADLS Gen2 Storage-konton:
  * Skapa och ta bort behållare
  * Hantera egenskaper för behållare och behörigheter (vänster)
  * Visa och navigera data i behållare
  * Skapa nya mappar
  * Ladda upp, hämta, byta namn på och ta bort filer och mappar
  * Hantera egenskaper för filer och mappar och behörigheter (till höger).
    
    Andra vanliga Blob-funktioner, till exempel mjuk borttagning och ögonblicksbilder är inte tillgänglig för tillfället. Hantera behörigheter är också bara tillgängligt när du loggat in. När du arbetar i ett ADLS Gen2 Storage-konto, kommer Lagringsutforskaren dessutom använda AzCopy för alla överföringar och hämtning och med namnet och nyckeln autentiseringsuppgifter för alla åtgärder om det är tillgängligt som standard.
* Efter stark Användarfeedback användas lease break återigen att avbryta lån för flera blobar på samma gång.

### <a name="known-issues"></a>Kända problem

* När du laddar ned från ett ADLS Gen2 Storage-konto om något av de filer som överförs redan finns kommer AzCopy ibland krascha. Detta kommer att åtgärdas i en kommande snabbkorrigering.
* Kopplar från en resurs som är anslutna via SAS-URI, t.ex en blob-behållare kan orsaka ett fel som förhindrar andra bilagor från visas korrekt. Undvik problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om du gör detta inte avblockera du kommentera problemet.
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron. Om du vill undvika det här problemet vid överföring till eller hämta det från en blobbehållare kan använda du funktionen experimentella AzCopy.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Azure Stack har inte stöd för följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack kan resurser resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Version 1.6.1
12/18/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Alla verifiering av ObjectIds i dialogrutan Hantera åtkomst har inaktiverats på grund av API-begränsningar. Verifieringen utförs nu endast för användare UPN-namn. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* I dialogrutan Hantera ADLS Gen2 åtkomst kan behörigheter för en grupp inte ändras. Problemet har åtgärdats. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Ladda upp stöd till ADLS Gen2 redigeraren har lagts till dra och släpp. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* URL-egenskapen i egenskapsdialogrutan för ADLS Gen2 filer och mappar saknades ibland en '/'. Problemet har åtgärdats. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Om får den aktuella behörigheten för en behållare för ADLS Gen2 fil eller mapp misslyckas och felet är nu propertly som visas i aktivitetsloggen. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Den tillfälliga sökvägen som skapats för att öppna filer har kortats ned för att minska risken för att skapa en sökväg som är längre än MAX_PATH på Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialogrutan Anslut nu visas korrekt när inga användare är inloggad och inga resurser har bifogats. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* Spara egenskaperna för icke - HNS Blobar och filer skulle i 1.6.0 eller senare koda värdet för varje egenskap. Detta resulterade i onödiga kodning av värden som innehöll endast ASCII-tecken. Värden kommer nu endast att kodas om de innehåller icke-ASCII-tecken. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Ladda upp en mapp till en icke - HNS blobbehållare misslyckas om en SAS har använts och SAS har inte behörighet att läsa. Problemet har åtgärdats. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Avbryter en AzCopy-överföring fungerade inte. Problemet har åtgärdats. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy misslyckas vid försök att ladda ned en mapp från en ADLS Gen2 Blob-behållare om mappen hade blanksteg i namnet. Problemet har åtgärdats. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* CosmosDB-redigeraren har delats i 1.6.0 eller senare. Det har lösts. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att få åtkomst till din Blob-data via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Om du har loggat in och Storage Explorer kan inte hämta nycklarna för ditt lagringskonto, används en OAuth-token för autentisering när du interagerar med dina data.
* Lagringsutforskaren stöder nu ADLS Gen2 Storage-konton. När Storage Explorer upptäcker det hierarkiskt namnområdet har aktiverats för ett lagringskonto, kommer du att se ”(ADLS Gen2 förhandsgranskning)” bredvid namnet på ditt lagringskonto. Lagringsutforskaren kan identifiera huruvida hierarkiskt namnområde är aktiverad när du är inloggad, eller om du har anslutit ditt Lagringskonto med namnet och nyckeln. Du kan använda Storage Explorer för ADLS Gen2 Storage-konton:
  * Skapa och ta bort behållare
  * Hantera egenskaper för behållare och behörigheter (vänster)
  * Visa och navigera data i behållare
  * Skapa nya mappar
  * Ladda upp, hämta, byta namn på och ta bort filer och mappar
  * Hantera egenskaper för filer och mappar och behörigheter (till höger).
    
    Andra vanliga Blob-funktioner, till exempel mjuk borttagning och ögonblicksbilder är inte tillgänglig för tillfället. Hantera behörigheter är också bara tillgängligt när du loggat in. När du arbetar i ett ADLS Gen2 Storage-konto, kommer Lagringsutforskaren dessutom använda AzCopy för alla överföringar och hämtning och med namnet och nyckeln autentiseringsuppgifter för alla åtgärder om det är tillgängligt som standard.
* Efter stark Användarfeedback användas lease break återigen att avbryta lån för flera blobar på samma gång.

### <a name="known-issues"></a>Kända problem

* När du laddar ned från ett ADLS Gen2 Storage-konto om något av de filer som överförs redan finns kommer AzCopy ibland krascha. Detta kommer att åtgärdas i en kommande snabbkorrigering.
* Kopplar från en resurs som är anslutna via SAS-URI, t.ex en blob-behållare kan orsaka ett fel som förhindrar andra bilagor från visas korrekt. Undvik problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om du gör detta inte avblockera du kommentera problemet.
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron. Om du vill undvika det här problemet vid överföring till eller hämta det från en blobbehållare kan använda du funktionen experimentella AzCopy.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Azure Stack har inte stöd för följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack kan resurser resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Version 1.6.0
12/5/2018

### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att få åtkomst till din Blob-data via [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409). Om du har loggat in och Storage Explorer kan inte hämta nycklarna för ditt lagringskonto, används en OAuth-token för autentisering när du interagerar med dina data.
* Lagringsutforskaren stöder nu ADLS Gen2 Storage-konton. När Storage Explorer upptäcker det hierarkiskt namnområdet har aktiverats för ett lagringskonto, kommer du att se ”(ADLS Gen2 förhandsgranskning)” bredvid namnet på ditt lagringskonto. Lagringsutforskaren kan identifiera huruvida hierarkiskt namnområde är aktiverad när du är inloggad, eller om du har anslutit ditt Lagringskonto med namnet och nyckeln. Du kan använda Storage Explorer för ADLS Gen2 Storage-konton:
  * Skapa och ta bort behållare
  * Hantera egenskaper för behållare och behörigheter (vänster)
  * Visa och navigera data i behållare
  * Skapa nya mappar
  * Ladda upp, hämta, byta namn på och ta bort filer och mappar
  * Hantera egenskaper för filer och mappar och behörigheter (till höger).
    
    Andra vanliga Blob-funktioner, till exempel mjuk borttagning och ögonblicksbilder är inte tillgänglig för tillfället. Hantera behörigheter är också bara tillgängligt när du loggat in. När du arbetar i ett ADLS Gen2 Storage-konto, kommer Lagringsutforskaren dessutom använda AzCopy för alla överföringar och hämtning och med namnet och nyckeln autentiseringsuppgifter för alla åtgärder om det är tillgängligt som standard.
* Efter stark Användarfeedback användas lease break återigen att avbryta lån för flera blobar på samma gång.

### <a name="known-issues"></a>Kända problem

* När du laddar ned från ett ADLS Gen2 Storage-konto om något av de filer som överförs redan finns kommer AzCopy ibland krascha. Detta kommer att åtgärdas i en kommande snabbkorrigering.
* Kopplar från en resurs som är anslutna via SAS-URI, t.ex en blob-behållare kan orsaka ett fel som förhindrar andra bilagor från visas korrekt. Undvik problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om du gör detta inte avblockera du kommentera problemet.
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron. Om du vill undvika det här problemet vid överföring till eller hämta det från en blobbehållare kan använda du funktionen experimentella AzCopy.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Azure Stack har inte stöd för följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack kan resurser resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Version 1.5.0
10/29/2018

### <a name="new"></a>Ny

* Du kan nu använda [AzCopy v10 (förhandsversion)](https://github.com/Azure/azure-storage-azcopy) överföra och hämta Blobar. Aktivera den här funktionen går du till menyn ”experimentella” och klicka sedan på ”Använd AzCopy för förbättrad Blob ladda upp och ladda ned”. När aktiverad används AzCopy i följande scenarier:
   * Ladda upp mappar och filer till blob-behållare via verktygsfältet eller dra och släpp.
   * Nedladdning av mappar och filer, antingen via menyn verktygsfältet eller kontext.

* Dessutom när du använder AzCopy:
   * Du kan kopiera AzCopy-kommandot som används för att köra överföringen till Urklipp. Klickar du på ”Kopiera AzCopy-kommandot till Urklipp” i aktivitetsloggen.
   * Behöver du uppdatera blob-redigeraren manuellt när du har överfört.
   * Ladda upp filer för att bifoga blobbar stöds inte, och kommer att överföra vhd-filer som sidblobar och alla andra filer överförs som blockblobar.
   * Fel och konflikter som uppstår under överföring eller hämtning exponeras inte förrän efter en överföring eller hämtningen är klar.

Slutligen kommer stöd för att använda AzCopy med filresurser i framtiden.
* Lagringsutforskaren använder Electron version 2.0.11.
* Avbryter lån kan nu endast utföras på en blob i taget. Du måste dessutom ange namnet på blob vars lån du bryter ned. Den här ändringen har gjorts för att minska sannolikheten för att av misstag större ett lån, särskilt för virtuella datorer. #394
* Om du stöter på problem med användarinloggning någonsin kan försöka du nu återställa autentisering. Gå till ”Hjälp”-menyn och klicka på ”Återställ” för att komma åt den här funktionen. #419

### <a name="fix"></a>Korrigera

* Efter stark Användarfeedback har emulatorn standardnoden återaktiverats. Du kan fortfarande lägga till ytterligare emulatorn anslutningar via dialogrutan Anslut, men om din emulatorn är konfigurerad för att använda standardportarna du kan också använda noden ”emulatorn * standardportarna” under ”lokala och anslutna/Storage konton”. #669
* Storage Explorer kan inte längre du ange värden för blob-metadata som har inledande eller avslutande blanksteg. #760
* Knappen ”Logga In” aktiverades alltid på samma sidor i dialogrutan Anslut. Det är nu inaktiverat när det är lämpligt. #761
* Snabb åtkomst kommer inte längre genererar ett fel i konsolen när inga Snabbåtkomst objekt har lagts till.

### <a name="known-issues"></a>Kända problem

* Kopplar från en resurs som är anslutna via SAS-URI, t.ex en blob-behållare kan orsaka ett fel som förhindrar andra bilagor från visas korrekt. Undvik problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om du gör detta inte avblockera du kommentera problemet.
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron. Om du vill undvika det här problemet vid överföring till eller hämta det från en blobbehållare kan använda du funktionen experimentella AzCopy.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Azure Stack har inte stöd för följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack kan resurser resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Version 1.4.4
10/15/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Azure Resource Management API-versionen har återställts för att låsa upp användare i Azure för amerikanska myndigheter. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Läser in rotationsrutor använder nu CSS-animeringar för att minska mängden GPU som används av Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Ny
* Extern resurs bifogade filer, till exempel för SAS-anslutningarna och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visningsnamnet för den resurs som du bifogar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ansluta till flera lokala emulatorer som använder olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägga till resurser som är anslutna till Snabbåtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Lagringsutforskaren har nu stöd för mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden Blob-behållare för ditt lagringskonto.
   * Visa ej permanent bort blobar i Blob-redigeraren genom att välja ”Active och tas bort blobbar” i listrutan bredvid navigeringsfältet.
   * Ångra borttagning av ej permanent borttagna blobar.

### <a name="fixes"></a>Korrigeringar
* ”Konfigurera CORS-inställningar”-åtgärden är inte längre tillgänglig på Premium Storage-konton eftersom Premium Storage-konton inte har stöd för CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en signatur för delad åtkomst-egenskap för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* ”Ange standard åtkomstnivå”-åtgärden är nu tillgängliga för Blob- och gpv2-/ Storage-konton som har fästs på Snabbåtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland misslyckas Storage Explorer att visa klassiska lagringskonton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage-emulatorn eller Azurite, behöver du ha dem lyssna efter anslutningar på sina standardportarna. I annat fall är Storage Explorer inte kan ansluta till dem.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om detta inte det avblockerar du, kommentera på [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta eftersom vi använder Avbryt filter lösning beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Version 1.4.3
10/11/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Azure Resource Management API-versionen har återställts för att låsa upp användare i Azure för amerikanska myndigheter. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Läser in rotationsrutor använder nu CSS-animeringar för att minska mängden GPU som används av Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Ny
* Extern resurs bifogade filer, till exempel för SAS-anslutningarna och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visningsnamnet för den resurs som du bifogar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ansluta till flera lokala emulatorer som använder olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägga till resurser som är anslutna till Snabbåtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Lagringsutforskaren har nu stöd för mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden Blob-behållare för ditt lagringskonto.
   * Visa ej permanent bort blobar i Blob-redigeraren genom att välja ”Active och tas bort blobbar” i listrutan bredvid navigeringsfältet.
   * Ångra borttagning av ej permanent borttagna blobar.

### <a name="fixes"></a>Korrigeringar
* ”Konfigurera CORS-inställningar”-åtgärden är inte längre tillgänglig på Premium Storage-konton eftersom Premium Storage-konton inte har stöd för CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en signatur för delad åtkomst-egenskap för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* ”Ange standard åtkomstnivå”-åtgärden är nu tillgängliga för Blob- och gpv2-/ Storage-konton som har fästs på Snabbåtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland misslyckas Storage Explorer att visa klassiska lagringskonton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage-emulatorn eller Azurite, behöver du ha dem lyssna efter anslutningar på sina standardportarna. I annat fall är Storage Explorer inte kan ansluta till dem.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om detta inte det avblockerar du, kommentera på [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta eftersom vi använder Avbryt filter lösning beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Version 1.4.2
09/24/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Uppdatera Azure Resource Management API-versionen till 2018-07-01 för att lägga till stöd för nya typer av Azure Storage-konto. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Ny
* Extern resurs bifogade filer, till exempel för SAS-anslutningarna och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visningsnamnet för den resurs som du bifogar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ansluta till flera lokala emulatorer som använder olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägga till resurser som är anslutna till Snabbåtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Lagringsutforskaren har nu stöd för mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden Blob-behållare för ditt lagringskonto.
   * Visa ej permanent bort blobar i Blob-redigeraren genom att välja ”Active och tas bort blobbar” i listrutan bredvid navigeringsfältet.
   * Ångra borttagning av ej permanent borttagna blobar.

### <a name="fixes"></a>Korrigeringar
* ”Konfigurera CORS-inställningar”-åtgärden är inte längre tillgänglig på Premium Storage-konton eftersom Premium Storage-konton inte har stöd för CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en signatur för delad åtkomst-egenskap för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* ”Ange standard åtkomstnivå”-åtgärden är nu tillgängliga för Blob- och gpv2-/ Storage-konton som har fästs på Snabbåtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland misslyckas Storage Explorer att visa klassiska lagringskonton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage-emulatorn eller Azurite, behöver du ha dem lyssna efter anslutningar på sina standardportarna. I annat fall är Storage Explorer inte kan ansluta till dem.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om detta inte det avblockerar du, kommentera på [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta eftersom vi använder Avbryt filter lösning beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Version 1.4.1
08/28/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Storage Explorer gick inte att generera nyckeln används för att kryptera känsliga data vid första start. Detta kan orsaka problem när du använder Snabbåtkomst och bifogande av resurser. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Om ditt konto inte kräver MFA för dess startklientorganisation, men gjorde för vissa andra klienter, Storage Explorer inte till listan över prenumerationer. Nu när du har loggat in med något sådant konto, ber Storage Explorer dig att ange dina autentiseringsuppgifter igen och genomför MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Lagringsutforskaren gick inte att ansluta resurser från Azure Tyskland och Azure för amerikanska myndigheter. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Om du har loggat in till två konton som har samma e-postadress, misslyckas Lagringsutforskaren ibland att visa dina resurser i trädvyn. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* På Windows-datorer långsammare ta välkomstskärmen ibland lång tid att visas. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Dialogrutan Anslut visas även om det fanns anslutna konton eller tjänster. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Ny
* Extern resurs bifogade filer, till exempel för SAS-anslutningarna och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visningsnamnet för den resurs som du bifogar. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ansluta till flera lokala emulatorer som använder olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägga till resurser som är anslutna till Snabbåtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Lagringsutforskaren har nu stöd för mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden Blob-behållare för ditt lagringskonto.
   * Visa ej permanent bort blobar i Blob-redigeraren genom att välja ”Active och tas bort blobbar” i listrutan bredvid navigeringsfältet.
   * Ångra borttagning av ej permanent borttagna blobar.

### <a name="fixes"></a>Korrigeringar
* ”Konfigurera CORS-inställningar”-åtgärden är inte längre tillgänglig på Premium Storage-konton eftersom Premium Storage-konton inte har stöd för CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en signatur för delad åtkomst-egenskap för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* ”Ange standard åtkomstnivå”-åtgärden är nu tillgängliga för Blob- och gpv2-/ Storage-konton som har fästs på Snabbåtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland misslyckas Storage Explorer att visa klassiska lagringskonton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage-emulatorn eller Azurite, behöver du ha dem lyssna efter anslutningar på sina standardportarna. I annat fall är Storage Explorer inte kan ansluta till dem.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om detta inte det avblockerar du, kommentera på [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta eftersom vi använder Avbryt filter lösning beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Version 1.3.0
07/09/2018

### <a name="new"></a>Ny
* Nu har stöd för att komma åt $web-behållare som används av Serverstatiska webbplatser. På så sätt kan du enkelt ladda upp och hantera filer och mappar som används av din webbplats. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* App-panelen på macOS har organiserats. Förändringarna innefattar en Arkiv-menyn, vissa genväg viktiga ändringar och flera nya kommandon under menyn app. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Utfärdare av slutpunkten för att logga in till Azure för amerikanska myndigheter har ändrats till https://login.microsoftonline.us/
* Hjälpmedel: När en skärmläsare är aktiv, fungerar tangentbordsnavigering nu med de tabeller som används för att visa objekt på höger sida. Du kan använda piltangenterna för att navigera rader och kolumner, RETUR för att anropa standardåtgärder, på kontexten menyn för att öppna snabbmenyn för ett objekt, och flytta eller kontroll till flera markeringar. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Korrigeringar
*  På vissa datorer, underordnade processerna tar lång tid att starta. När detta händer visas felet ”underordnade processen misslyckades att starta i tid”. Tiden för en underordnad process att starta har nu ökat från 20 till 90 sekunder. Om du fortfarande har drabbats av det här problemet, kommentera på den länkade GitHub-ärenden. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* När du använder en SAS som inte har läsbehörighet, gick det inte att ladda upp en stor blob. Logiken för överföring har ändrats för att fungera i det här scenariot. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Ange offentlig åtkomstnivå för en behållare som skulle ta bort alla åtkomstprinciper och vice versa. Nu kan bevaras principer och åtkomst för offentlig åtkomst när du ställer in av två. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* ”AccessTierChangeTime” trunkerades i dialogrutan Egenskaper. Problemet har åtgärdats. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* ”Microsoft Azure Storage Explorer –” prefix saknades från dialogrutan Skapa ny katalog. Problemet har åtgärdats. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Hjälpmedel: Dialogrutan Lägg till entitet var svårt att navigera när du använder VoiceOver. Förbättringar har gjorts. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Hjälpmedel: Bakgrundsfärgen för Dölj/Visa knappen för fönstret Akce a Vlastnosti var inkonsekvent med liknande UI-kontroller i högkontrast svart tema. Färgen har ändrats. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Hjälpmedel: Högkontrast svart tema kunde inte fokus formatering för knappen ”X” i dialogrutan Egenskaper visas. Problemet har åtgärdats. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Hjälpmedel: Flikarna åtgärder och egenskaper saknades flera aria-värden som resulterade i en läsare för subpar skärm. Värden som saknas aria har nu lagts till. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Hjälpmedel: Komprimerad trädnoder på vänster sida har inte som den angivna värdet false för aria-expanderas. Problemet har åtgärdats. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Kända problem
* Kopplar från en resurs som är anslutna via SAS-URI, t.ex en blob-behållare kan orsaka ett fel som förhindrar andra bilagor från visas korrekt. Undvik problemet genom att bara uppdatera gruppnoden. Se [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/537) för mer information.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om detta inte det avblockerar du, kommentera på [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta eftersom vi använder Avbryt filter lösning beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Azure Stack har inte stöd för följande funktioner och försök att använda dem när du arbetar med Azure Stack kan resultera i oväntade fel:
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Version 1.2.0
06/12/2018

### <a name="new"></a>Ny
* Om det inte går att läsa in prenumerationer från endast en delmängd av klienterna Lagringsutforskaren, visas har lästs in prenumerationer tillsammans med ett felmeddelande specifikt för klienter som misslyckades. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* På Windows, när en uppdatering är tillgänglig, kan du nu välja att ”uppdatera vid stängning”. När du har stängt Storage Explorer körs installationsprogrammet för uppdateringen om det här alternativet är valt. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Återställ ögonblicksbild har lagts till på snabbmenyn för filen dela redigerare när du visar en ögonblicksbild av filresurs. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Knappen Rensa kön är nu alltid aktiverat. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Stöd för inloggning till ADFS Azure Stack har återaktiverats. Azure Stack version 1804 eller senare krävs. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Korrigeringar
* Om du har visat ögonblicksbilder för en filresurs vars namn har ett prefix på en annan filresurs i samma lagringskonto, skulle ögonblicksbilder för den delade filresursen även anges. Det här problemet har åtgärdats. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* När ansluten via SAS, skulle återställer en fil från en ögonblicksbild av en filresurs resultera i ett fel. Det här problemet har åtgärdats. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* När du visar ögonblicksbilder för en blob aktiverades åtgärden Flytta upp ögonblicksbild när den grundläggande blobben- och en enda ögonblicksbild har valts. Åtgärden är nu endast aktiverad om en enda ögonblicksbild har valts. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Om ett enskilt jobb (till exempel ladda ned en blob) startades och senare misslyckades, skulle det inte automatiskt försök tills du startade ett annat jobb av samma typ. Alla jobb bör nu automatiskt återförsök, oavsett hur många jobb har du i kö.
* Redigerare öppnas för nyligen skapade blob-behållare i GPV2 och Blob Storage-konton har inte en åtkomstnivå kolumnen. Det här problemet har åtgärdats. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* En åtkomstnivå kolumnen visas ibland inte när ett Storage-konto eller blob-behållare har anslutna via SAS. Kolumnen nu visas alltid, men med ett tomt värde om det finns inget åtkomstnivå har angetts. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Ange åtkomstnivån för en nyligen uppladdade blockblob har inaktiverats. Det här problemet har åtgärdats. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Om knappen ”Behåll fliken Öppna” anropades med tangentbordet, skulle tangentbordsfokus gå förlorade. Fokus flyttas nu till fliken behöll öppen. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* För en fråga i Frågeverktyget VoiceOver inte ger en användbar beskrivning av den aktuella operatorn. Nu är det mer beskrivande. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Det gick inte att beskrivande sidbrytning länkarna för olika redigerare. De har ändrats för att vara mer beskrivande. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* I dialogrutan Lägg till entitet VoiceOver inte presenterar vilken kolumn ett inkommande element var en del av. Namnet på den aktuella kolumnen ingår nu i beskrivningen av elementet. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Alternativknappar och kryssrutorna hade inte en synlig kantlinje när fokus. Det här problemet har åtgärdats. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage-emulatorn eller Azurite, behöver du ha dem lyssna efter anslutningar på sina standardportarna. I annat fall är Storage Explorer inte kan ansluta till dem.
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om detta inte det avblockerar du, kommentera på [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta eftersom vi använder Avbryt filter lösning beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Version 1.1.0
05/09/2018

### <a name="new"></a>Ny
* Lagringsutforskaren stöder nu användning av Azurite. Obs: anslutningen till Azurite är hårdkodad att standardslutpunkterna för utveckling.
* Lagringsutforskaren stöder nu åtkomstnivåerna för endast Blob- och GPV2-konton. Mer information om åtkomstnivåerna [här](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).
* En starttid krävs inte längre när du genererar en SAS.

### <a name="fixes"></a>Korrigeringar
* Hämtning av prenumerationer för amerikanska myndigheter konton bröts. Det här problemet har åtgärdats. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Förfallotiden för åtkomstprinciper som korrekt sparades inte. Det här problemet har åtgärdats. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* När du genererar en SAS-URL för ett objekt i en behållare, har inte namnet på objektet som läggs till URL: en. Det här problemet har åtgärdats. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* När du skapar en SAS kan är gälla gånger som är tidigare ibland standardvärdet. Det berodde på att Storage Explorer med hjälp av senast använda start- och tid som standardvärden. Varje gång du öppnar SAS-dialog skapas nu en ny uppsättning standardvärden. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* När du kopierar mellan Lagringskonton, skapas en SAS med 24 timmar. Om kopieringen varat mer än 24 timmar, skulle kopian misslyckas. Vi har gjort SAS till senaste 1 veckan för att minska risken för en kopia som misslyckas på grund av en SAS som har upphört att gälla. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* För vissa aktiviteter skulle att klicka på ”Avbryt” inte alltid fungerar. Det här problemet har åtgärdats. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* För vissa aktiviteter var överföringshastigheten fel. Det här problemet har åtgärdats. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Stavningen av ”föregående” på menyn Visa var fel. Det är nu korrekt stavat. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Sista sidan i Windows installer hade en knappen ”Nästa”. Det har ändrats till en ”Slutför”-knapp. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Flikfokus kunde inte visas för knapparna i dialogrutor när du använder temat HC svart. Den syns nu. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Versaler och gemener i ”Lös automatiskt” för åtgärder i aktivitetsloggen var fel. Nu är det rätt. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* När du tar bort en entitet från en tabell, visas en felikon i dialogrutan där du uppmanas bekräfta. Dialogrutan använder nu en varningsikon. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Kända problem
* Om du använder VS för Mac och någon gång har skapat en anpassad AAD-konfiguration kan kanske du inte att logga in. Undvik problemet genom att ta bort innehållet i ~ /. IdentityService/AadConfigurations. Om detta inte det avblockerar du, kommentera på [problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite ännu inte helt har genomfört alla Storage API: er. Därför bör finnas det oväntade fel eller beteende när du använder Azurite för utvecklingslagring.
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* Ladda upp från OneDrive-mapp fungerar inte på grund av ett fel i NodeJS. Buggen har åtgärdats, men ännu inte har integrerats i Electron.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta eftersom vi använder Avbryt filter lösning beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Version 1.0.0
04/16/2018

### <a name="new"></a>Ny
* Förbättrad autentisering som gör att Lagringsutforskaren kan använda samma kontoarkiv som Visual Studio 2017. Om du vill använda den här funktionen behöver du re-logga in på dina konton och ange dina filtrerade prenumerationer igen.
* För Azure Stack-konton backas upp av AAD hämtar Lagringsutforskaren nu Azure Stack-prenumerationer när 'Target Azure Stack ”är aktiverad. Du behöver inte längre att skapa en anpassad inloggnings-miljö.
* Flera genvägar har lagts till för att aktivera snabbare navigering. Dessa inkluderar växla mellan olika paneler och flytta mellan redigerare. Se menyn Visa mer information.
* Det finns nu Storage Explorer feedback på GitHub. Du kan nå sidan problem genom att klicka på Feedback i nederkant vänstra hörnet eller genom att gå till [ https://github.com/Microsoft/AzureStorageExplorer/issues ](https://github.com/Microsoft/AzureStorageExplorer/issues). Gärna förslag, rapportera problem, ställa frågor eller lämna andra former av feedback.
* Om du använder SSL-certifikat problem och kan inte hitta felaktiga certifikatet nu kan du starta Lagringsutforskaren från kommandoraden med den `--ignore-certificate-errors` flaggan. När startas med den här flaggan ignorerar Lagringsutforskaren SSL-certifikatfel.
* Det finns nu ett ”Hämta” alternativ på snabbmenyn för blob- och objekt.
* Förbättrad tillgänglighet och stöd för Skärmläsaren. Om du förlitar dig om dessa funktioner finns i vår [hjälpmedel dokumentation](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility) för mer information.
* Lagringsutforskaren använder nu Electron 1.8.3

### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
* Lagringsutforskaren har ändrats till en ny autentiseringsbibliotek. Som en del av växeln i biblioteket behöver du re-logga in på dina konton och ange nytt filtrerade prenumerationer
* Den metod som används för att kryptera känsliga data har ändrats. Det kan resultera i några av dina Snabbåtkomst-objekten som ska läggas till igen och/eller några av anslutna resurser behöver kopplas.

### <a name="fixes"></a>Korrigeringar
* Vissa användare bakom proxyservrar skulle ha grupp blob-överföringar eller hämtningar avbryts av ”det går inte att matcha' felmeddelande. Det här problemet har åtgärdats.
* Om inloggning behövdes medan med en direktlänk som att klicka på ”inloggning” visas varpå en dialogruta som är tom. Det här problemet har åtgärdats.
* På Linux, om Storage Explorer kan inte starta på grund av en GPU-processkrasch du nu informeras om kraschen, ett meddelande om att använda den ”--inaktivera gpu' växel och Storage Explorer kommer sedan startar om automatiskt med växeln aktiverat.
* Ogiltig åtkomstprinciper har svårt att identitet i dialogrutan åtkomstprinciper. Ogiltig åtkomstprincip ID: N nu beskrivs i rött för mer synlighet.
* Aktivitetsloggen behöver ibland stora delar av blanksteg mellan de olika delarna i en aktivitet. Det här problemet har åtgärdats.
* I frågeredigeraren tabell om du har lämnat en tidsstämpel-sats i ett ogiltigt tillstånd och sedan försökte ändra en annan satsen skulle redigeraren låsa. Redigeraren återställs nu tidsstämpel-satsen till sitt senaste giltigt tillstånd när en ändring i en annan-satsen har identifierats.
* Om du pausas medan du skriver i en sökfråga i trädvyn sökningen ska börja och fokus skulle vara stals från textrutan. Nu måste du uttryckligen starta sökningen genom att trycka på RETUR-tangenten eller genom att klicka på knappen start sökning.
* Kommandot ”Hämta signatur för delad åtkomst, skulle ibland inaktiveras när Högerklicka på en fil i en filresurs. Det här problemet har åtgärdats.
* Om resursen trädnoden med fokus har filtrerats bort vid sökning, det gick inte fliken i trädet för resurser och Använd piltangenterna för att navigera i resursträdet. Nu kan kommer fokuserade resource trädnoden är dold, den första noden i trädet för resurser automatiskt bestå.
* En extra avgränsare vore ibland visas i verktygsfältet i. Det här problemet har åtgärdats.
* Textrutan brödsmula skulle ibland overflow. Det här problemet har åtgärdats.
* Redigerare för Blob- och filresurs skulle ibland ständigt uppdatera när du laddar upp flera filer samtidigt. Det här problemet har åtgärdats.
* Funktionen 'Mappstatistik' hade inga syfte i vyn hanteringen av ögonblicksbilder av filresurser. Det har nu inaktiverats.
* På Linux visas inte på Arkiv-menyn. Det här problemet har åtgärdats.
* När du laddar upp en mapp till en filresurs som standard laddades bara innehållet i mappen upp. Standardinställningen är nu att ladda upp innehållet i mappen till en matchande mapp i filresursen.
* Sorteringen av knapparna i flera dialogrutor har återförts. Det här problemet har åtgärdats.
* Olika säkerhetsrelaterade korrigeringar.

### <a name="known-issues"></a>Kända problem
* I sällsynta fall kan trädet fokus fastna på Snabbåtkomst. Du kan uppdatera alla som behövdes fokus.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Linux-användare behöver du installera [.NET Core 2.0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x).
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Version 0.9.6
2018-02-28

### <a name="fixes"></a>Korrigeringar
* Ett problem hindrade förväntade blobar/filer listas i redigeraren. Det här problemet har åtgärdats.
* Ett problem orsakade att växla mellan ögonblicksbildvyer visas objekten på fel sätt. Det här problemet har åtgärdats.

### <a name="known-issues"></a>Kända problem
* Storage Explorer har inte stöd för AD FS-konton.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta eftersom vi använder Avbryt filter lösning beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer igen.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Version 0.9.5
2018-02-06

### <a name="new"></a>Ny

* Stöd för ögonblicksbilder av filresurser:
    * Skapa och hantera ögonblicksbilder för den filresurser.
    * Enkelt växla vy mellan ögonblicksbilder av dina filresurser som du utforskar.
    * Återställ tidigare versioner av dina filer.
* Förhandsversion av stöd för Azure Data Lake Store:
    * Ansluta till ADLS-resurser över flera konton.
    * Ansluta till och dela ADLS-resurser med hjälp av ADL-URI: er.
    * Utför grundläggande filen/mappen operations rekursivt.
    * Enskilda mappar Fäst i Snabbåtkomst.
    * Visa mappstatistik.

### <a name="fixes"></a>Korrigeringar
* Prestandaförbättringar för start.
* Rad felkorrigeringar.

### <a name="known-issues"></a>Kända problem
* Storage Explorer har inte stöd för AD FS-konton.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer igen.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Version 0.9.4 och 0.9.3
2018-01-21

### <a name="new"></a>Ny
* Din befintliga Storage Explorer-fönstret ska återanvändas när:
    * Öppna Direktlänkar som genererats i Storage Explorer.
    * Öppna Storage Explorer från portalen.
    * Öppna Storage Explorer från Azure Storage VS Code-tillägg (kommer snart).
* Möjlighet att öppna ett nytt Storage Explorer-fönster i Storage Explorer har lagts till.
    * För Windows finns det ett ”nytt fönster” alternativ under Arkiv-menyn och på snabbmenyn i Aktivitetsfältet.
    * För Mac finns det en ”nytt fönster” alternativet App-menyn.

### <a name="fixes"></a>Korrigeringar
* Fasta ett säkerhetsproblem. Uppgradera till 0.9.4 vid första möjliga tillfälle.
* Gamla aktiviteter har inte korrekt rensas. Detta påverkas prestanda för långvariga jobb. De är nu rensas korrekt.
* Åtgärder som inbegriper stort antal filer och kataloger kan ibland orsaka Storage Explorer för att låsa. Begäranden till Azure för filresurser har nu begränsats för att begränsa system Resursanvändning.

### <a name="known-issues"></a>Kända problem
* Storage Explorer har inte stöd för AD FS-konton.
* Kortkommandon för ”Visa Explorer” och ”visa kontohantering” ska vara Ctrl / Cmd + SKIFT + E och Ctrl / Cmd + SKIFT + A respektive.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer igen.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Version 0.9.2
11/01/2017

### <a name="hotfixes"></a>Snabbkorrigeringar
* Oväntade dataändringar var möjligt när du redigerar Edm.DateTime värden för tabellenheter beroende på den lokala tidszonen. Redigeraren använder nu en oformaterad textruta, vilket ger exakt och konsekvent kontroll över Edm.DateTime värden.
* Ladda upp/ned en grupp av blobar när ansluten med namnet och nyckeln startar inte. Det här problemet har åtgärdats.
* Tidigare Lagringsutforskaren endast ombeds du att autentiseras på nytt ett inaktuella konto om en eller flera av kontots prenumerationer har valts. Nu uppmanar Storage Explorer dig även om konton som är fullständigt filtrerats bort.
* Domän för lagringsslutpunkter för Azure för amerikanska myndigheter var fel. Det har åtgärdats.
* Knappen Använd på panelen hantera konton har ibland svår att klicka på. Det här ska inte längre inträffa.

### <a name="new"></a>Ny
* Förhandsversion av stöd för Azure Cosmos DB:
    * [Online-dokumentation](./cosmos-db/storage-explorer.md)
    * Skapa databaser och samlingar
    * Ändra data
    * Fråga, skapa eller ta bort dokument
    * Uppdatera lagrade procedurer, användardefinierade funktioner och utlösare
    * Använda anslutningssträngar för att ansluta till och hantera dina databaser
* Förbättrad prestanda för uppladdning/nedladdning av många små blobbar.
* Lägga till en åtgärd som ”försök alla” om det finns fel i en blob ladda upp gruppen eller blob download grupp.
* Lagringsutforskaren pausar nu iteration under blob uppladdning/nedladdning om nätverksanslutningen har kopplats från. Du kan sedan återuppta iteration när nätverksanslutningen har återupprättats.
* Lagt till möjligheten att ”Stäng alla”, ”Stäng andra” och ”Stäng” flikarna via snabbmenyn.
* Lagringsutforskaren använder nu inbyggda dialogrutor och interna snabbmenyer.
* Storage Explorer är nu mer tillgänglig. Förbättringarna innefattar:
    * Förbättrat stöd för Skärmläsaren, för NVDA på Windows och för VoiceOver på Mac
    * Förbättrad hög kontrast teman
    * Tabbar och tangentbord tangentbordsfokus korrigeringar

### <a name="fixes"></a>Korrigeringar
* Om du försöker att öppna eller ladda ned en blob med ett ogiltigt namn för Windows-fil, misslyckas åtgärden. Storage Explorer ska nu upptäcka om en blobnamnet är ogiltigt och be om du vill koda eller hoppa över blob. Lagringsutforskaren identifierar även om ett filnamn verkar vara kodad och be dig om vill avkoda innan du laddar upp.
* Under uppladdningen av blob, Redigeraren för blob-behållaren målet skulle ibland inte korrekt att uppdatera. Det här problemet har åtgärdats.
* Stöd för flera typer av anslutningssträngar och SAS-URI: er försämrat. Vi har åtgärdat alla kända problem, men skicka feedback om du får ytterligare problem.
* Meddelanden om uppdateringar bröts för vissa användare i 0.9.0. Det här problemet har åtgärdats och för dem som berörs av felet, kan du manuellt ladda ned den senaste versionen av Storage Explorer [här](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Kända problem
* Storage Explorer har inte stöd för AD FS-konton.
* Kortkommandon för ”Visa Explorer” och ”visa kontohantering” ska vara Ctrl / Cmd + SKIFT + E och Ctrl / Cmd + SKIFT + A respektive.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer igen.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Version 0.9.1 till och och 0.9.0
10/20/2017
### <a name="new"></a>Ny
* Förhandsversion av stöd för Azure Cosmos DB:
    * [Online-dokumentation](./cosmos-db/storage-explorer.md)
    * Skapa databaser och samlingar
    * Ändra data
    * Fråga, skapa eller ta bort dokument
    * Uppdatera lagrade procedurer, användardefinierade funktioner och utlösare
    * Använda anslutningssträngar för att ansluta till och hantera dina databaser
* Förbättrad prestanda för uppladdning/nedladdning av många små blobbar.
* Lägga till en åtgärd som ”försök alla” om det finns fel i en blob ladda upp gruppen eller blob download grupp.
* Lagringsutforskaren pausar nu iteration under blob uppladdning/nedladdning om nätverksanslutningen har kopplats från. Du kan sedan återuppta iteration när nätverksanslutningen har återupprättats.
* Lagt till möjligheten att ”Stäng alla”, ”Stäng andra” och ”Stäng” flikarna via snabbmenyn.
* Lagringsutforskaren använder nu inbyggda dialogrutor och interna snabbmenyer.
* Storage Explorer är nu mer tillgänglig. Förbättringarna innefattar:
    * Förbättrat stöd för Skärmläsaren, för NVDA på Windows och för VoiceOver på Mac
    * Förbättrad hög kontrast teman
    * Tabbar och tangentbord tangentbordsfokus korrigeringar

### <a name="fixes"></a>Korrigeringar
* Om du försöker att öppna eller ladda ned en blob med ett ogiltigt namn för Windows-fil, misslyckas åtgärden. Storage Explorer ska nu upptäcka om en blobnamnet är ogiltigt och be om du vill koda eller hoppa över blob. Lagringsutforskaren identifierar även om ett filnamn verkar vara kodad och be dig om vill avkoda innan du laddar upp.
* Under uppladdningen av blob, Redigeraren för blob-behållaren målet skulle ibland inte korrekt att uppdatera. Det här problemet har åtgärdats.
* Stöd för flera typer av anslutningssträngar och SAS-URI: er försämrat. Vi har åtgärdat alla kända problem, men skicka feedback om du får ytterligare problem.
* Meddelanden om uppdateringar bröts för vissa användare i 0.9.0. Det här problemet har åtgärdats och för dem som berörs av felet, kan du manuellt ladda ned den senaste versionen av Storage Explorer [här](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Kända problem
* Storage Explorer har inte stöd för AD FS-konton.
* Kortkommandon för ”Visa Explorer” och ”visa kontohantering” ska vara Ctrl / Cmd + SKIFT + E och Ctrl / Cmd + SKIFT + A respektive.
* När du riktar in sig på Azure Stack, misslyckas ladda upp filer tilläggsblobbar.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta beskrivs eftersom vi använder Avbryt filter lösning här.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer igen.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Electron-gränssnitt som används av Storage Explorer har problem med vissa GPU (grafikprocessor) maskinvaruacceleration. Om Storage Explorer visning av ett tomt (tom) huvudfönstret måste du starta Lagringsutforskaren från kommandoraden och inaktivera GPU-acceleration genom att lägga till den `--disable-gpu` växla:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Version 0.8.16
8/21/2017

### <a name="new"></a>Ny
* När du öppnar en blob uppmanar Storage Explorer dig att ladda upp den hämta filen om en ändring identifieras
* Förbättrad Azure Stack-inloggningen
* Förbättrad prestanda för att ladda upp/ned många små filer på samma gång


### <a name="fixes"></a>Korrigeringar
* För vissa blobtyper av skulle välja ”Ersätt” under en uppladdning konflikt ibland resultera i att överföringen kommer den startas.
* I version 0.8.15 skulle överföringar ibland av stopp vid 99%.
* När överföringen av filer till en filresurs, om du har valt att ladda upp till en katalog som inte ännu finns, skulle ladda upp misslyckas.
* Lagringsutforskaren felaktigt genereras tidsstämplarna för signaturer för delad åtkomst och skickar frågor till tabellen.


### <a name="known-issues"></a>Kända problem
* Med hjälp av ett namn och anslutningssträng nyckel fungerar inte för närvarande. Det korrigeras i nästa version. Fram till dess kan du använda bifoga med namn och nyckel.
* Om du försöker öppna en fil med ett ogiltigt Windows-filnamn leder nedladdningen av en fil som gick inte att hitta.
* När du klickar på ”Avbryt” för en aktivitet, kan det ta en stund innan aktiviteten att avbryta. Detta är en begränsning på noden för Azure Storage-biblioteket.
* När du har slutfört en blob-överföring, uppdateras fliken som initierat överföringen. Detta innebär en förändring från tidigare beteende och medför även att du för att gå tillbaka till roten för den behållare som du tillhör.
* Om du väljer fel PIN-kod/smartkort-certifikat måste startas om för att få Lagringsutforskaren glömmer detta beslut.
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter för att filtrera prenumerationer igen.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* För användare på Ubuntu 14.04 behöver du kontrollera GCC är uppdaterad – detta kan göras genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Du måste installera GConf – detta kan göras genom att köra följande kommandon och sedan starta om datorn för användare på Ubuntu nr 17.04 från:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Version 0.8.14
06/22/2017

### <a name="new"></a>Ny

* Uppdaterade Electron versionen till 1.7.2 för att kunna dra nytta av flera kritiska säkerhetsuppdateringar
* Du kan nu snabbt komma åt online felsökningsguide för Hjälp-menyn
* Felsöka Lagringsutforskaren [Guide][2]
* [Instruktioner] [ 3] om hur du ansluter till en Azure Stack-prenumeration

### <a name="known-issues"></a>Kända problem

* Knappar i bekräftelsedialogen ta bort mappen inte registrerar dig med musklick i Linux. arbete är cirka att använda RETUR-tangenten
* Om du väljer fel PIN-kod/smartkort-certifikat måste du starta om för att få Lagringsutforskaren Glöm beslutet
* Med fler än 3 grupper av blobar eller filöverföring samtidigt kan orsaka fel
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter igen för att filtrera prenumerationer
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Ubuntu 14.04 installation måste gcc versionen eller uppgraderas – steg för att uppgradera finns nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Version 0.8.13
05/12/2017

#### <a name="new"></a>Ny

* Felsöka Lagringsutforskaren [Guide][2]
* [Instruktioner] [ 3] om hur du ansluter till en Azure Stack-prenumeration

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Ladda upp filen hade en hög risk orsaka en out of minnesfel
* Åtgärdat: Du kan nu logga in med PIN-kod/smartkort
* Åtgärdat: Öppna i portalen nu fungerar med Azure Kina 21Vianet, Azure Germany, Azure US Government och Azure Stack
* Åtgärdat: När du laddar upp en mapp på en blobbehållare, kan ett ”otillåten åtgärd”-fel ibland uppstå
* Åtgärdat: Markera allt har inaktiverats medan du hanterar ögonblicksbilder
* Åtgärdat: Metadata för grundläggande blob kan komma över när du visar egenskaperna för dess ögonblicksbilder

#### <a name="known-issues"></a>Kända problem

* Om du väljer fel PIN-kod/smartkort-certifikat måste du starta om för att få Lagringsutforskaren Glöm beslutet
* Medan zoomar in eller ut, kan zoomnivån tillfälligt återställa till Standardnivå
* Med fler än 3 grupper av blobar eller filöverföring samtidigt kan orsaka fel
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter igen för att filtrera prenumerationer
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Ubuntu 14.04 installation måste gcc versionen eller uppgraderas – steg för att uppgradera finns nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Version 0.8.12 och 0.8.11 och 0.8.10
04/07/2017

#### <a name="new"></a>Ny

* Lagringsutforskaren stängs nu automatiskt när du installerar en uppdatering från meddelanden om uppdateringar
* Plats för snabb åtkomst ger en förbättrad upplevelse för att arbeta med resurserna används ofta
* Redigeraren för Blob-behållare kan du nu se vilken virtuell dator som tillhör en utlånat blob
* Du kan nu minimerar panelen till vänster
* Identifiering nu körs på samma gång nedladdning
* Använda statistik i Blob-behållare, filresursen och tabellen redigerare för att se storleken på din resurs eller markering
* Du kan logga in till Azure Active Directory (AAD) baserat Azure Stack-konton.
* Du kan nu överföra arkivfiler över 32MB till Premium storage-konton
* Förbättrat stöd för hjälpmedel
* Du kan nu lägga till betrodda Base64-kodad X.509 SSL-certifikat genom att gå att redigera -&gt; SSL-certifikat –&gt; Importera certifikat

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: när du har uppdaterat en kontoautentiseringsuppgifter trädvyn kan ibland uppdateras inte automatiskt
* Åtgärdat: Generera en SAS för emulatorn köer och tabeller som skulle resultera i en ogiltig URL
* Fast: premium-lagringskonton kan nu utökas medan en proxy är aktiverat
* Åtgärdat: knappen Använd på kontosidan fungerar inte om du hade 1 eller 0 konton som har valts
* Fast: ladda upp blobar som kräver konfliktlösningar misslyckas - opravený 0.8.11
* Åtgärdat: skicka feedback har brutits i 0.8.11 - opravený 0.8.12

#### <a name="known-issues"></a>Kända problem

* Efter uppgraderingen till 0.8.10, kommer du behöva uppdatera alla dina autentiseringsuppgifter.
* Medan zoomar in eller ut, kan zoomnivån tillfälligt återställa till Standardnivå.
* Med fler än 3 grupper av blobar eller filöverföring samtidigt kan det orsaka fel.
* Inställningspanelen konto kan indikera att du måste ange autentiseringsuppgifter igen för att filtrera prenumerationer.
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte.
* Även om Azure Stack inte för närvarande stöd för filresurser, visas en filresurser nod fortfarande under ett anslutna Azure Stack-lagringskonto.
* Ubuntu 14.04 installation måste gcc versionen eller uppgraderas – steg för att uppgradera finns nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Version 0.8.9 och 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Ny

* Lagringsutforskaren 0.8.9 kommer automatiskt att hämta den senaste versionen för uppdateringar.
* Snabbkorrigering: från en portal skulle genererade SAS-URI att ansluta ett lagringskonto resultera i ett fel.
* Du kan nu skapa, hantera och uppgradera blob-ögonblicksbilder.
* Du kan nu logga in på Azure Kina 21Vianet, Azure Tyskland och Azure US Government-konton.
* Du kan nu ändra zoomnivån. Använda alternativen i menyn Visa att Zooma In, Zooma ut och återställa Zooma.
* Unicode-tecken stöds nu i Användarmetadata för för blobbar och filer.
* Förbättrad användbarhet.
* Nästa version viktig information kan ses från meddelanden om uppdateringar. Du kan också visa aktuell viktig information från Hjälp-menyn.

#### <a name="fixes"></a>Korrigeringar

* Fast: versionsnumret korrekt visas nu i Kontrollpanelen på Windows
* Fast: sökning är inte längre begränsad till 50 000 noder
* Fast: ladda upp till en filresurs som kunde alltid om målmappen inte redan fanns
* Fast: förbättrad stabilitet för lång överföra och hämta filer

#### <a name="known-issues"></a>Kända problem

* Medan zoomar in eller ut, kan zoomnivån tillfälligt återställa till Standardnivå.
* Snabbåtkomst fungerar bara med prenumerationsbaserade objekt. Lokala resurser eller resurser som är anslutna via nyckel eller SAS-token stöds inte i den här versionen.
* Snabbåtkomst kan det ta några sekunder att navigera till målresursen, beroende på hur många resurser som du har.
* Med fler än 3 grupper av blobar eller filöverföring samtidigt kan det orsaka fel.

12/16/2016
### <a name="version-087"></a>Version 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Ny

* Du kan välja hur du löser konflikter i början av en uppdatering, hämtning eller kopiering session i fönstret aktiviteter
* Hovra över en flik för att se den fullständiga sökvägen till lagringsresursen
* När du klickar på en flik synkroniseras den med dess plats i navigeringsfönstret till vänster

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Storage Explorer är nu en betrodd app på Mac
* Åtgärdat: Ubuntu 14.04 stöds igen
* Åtgärdat: Ibland Lägg till konto Användargränssnittet blinkar vid inläsning av prenumerationer
* Åtgärdat: Ibland inte alla lagringsresurser visades i navigeringsfönstret till vänster
* Åtgärdat: Åtgärdsfönstret visas ibland tomma åtgärder
* Åtgärdat: Fönsterstorlek från den senaste stängd sessionen sparas nu
* Åtgärdat: Du kan öppna flera flikar för samma resurs med hjälp av snabbmenyn

#### <a name="known-issues"></a>Kända problem

* Snabbåtkomst fungerar bara med prenumerationsbaserade objekt. Lokala resurser eller resurser som är anslutna via nyckel eller SAS-token stöds inte i den här versionen
* Det kan ta Snabbåtkomst några sekunder att navigera till målresursen, beroende på hur många resurser som du har
* Med fler än 3 grupper av blobar eller filöverföring samtidigt kan orsaka fel
* Sök handtag söka i ungefär 50 000 noder - därefter kan prestanda påverkas eller kan orsaka ett ohanterat undantag
* För första gången med Storage Explorer på macOS, kan du se flera meddelanden som ber om användarens tillåtelse att komma åt nyckelring. Vi rekommenderar att du markerar Tillåt alltid uppmaningen inte visas igen

11/18/2016
### <a name="version-086"></a>Version 0.8.6

#### <a name="new"></a>Ny

* Du kan nu PIN-kod används oftast tjänster snabb åtkomst för enkel navigering
* Du kan nu öppna flera redigerare på olika flikar. Enda Klicka för att öppna en tillfällig flik; Dubbelklicka om du vill öppna en permanent flik. Du kan också klicka på fliken tillfälligt så att de blir en permanent flik
* Vi har gjort märkbar prestanda och stabilitet för överföring och hämtning, särskilt för stora filer på snabb datorer
* Tom ”virtuella” mappar kan nu skapas i blob-behållare
* Nytt har vi lagt omfattad sökning med vår nya förbättrade sökningen, så nu har du två alternativ för att söka:
    * Global Sök - bara ange en sökterm i sökrutan för bibliotekskontrollen
    * Omfångssökning - Klicka på förstoringsglaset bredvid en nod, och sedan lägga till en sökterm i slutet av sökvägen, eller högerklicka och välj ”Sök från hit”
* Vi har lagt till olika teman: Ljus (standard), mörk, hög kontrast svart och Högkontrast vit. Gå till redigera -&gt; teman för att ändra inställningarna teman
* Du kan ändra Blob- och egenskaper
* Vi stöder nu kodade (base64) och kodat Kömeddelanden
* På Linux, ett 64-bitars operativsystem är nu krävs. Den här versionen stöder vi bara 64-bitars Ubuntu 16.04.1 LTS
* Vi har uppdaterat vår logotyp!

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Skärmen låser sig. problem
* Åtgärdat: Förbättrad säkerhet
* Åtgärdat: Ibland duplicerade anslutna konton kan visas
* Åtgärdat: En blob med en odefinierad innehållstyp kan generera ett undantag
* Åtgärdat: Öppna panelen fråga på en tom tabell var inte möjligt
* Åtgärdat: Varierar buggar i Search
* Åtgärdat: Ökat antal resurser som har lästs in från 50 till 100 när du klickar på ”Läs in mer”
* Åtgärdat: På första körningen om ett konto som är inloggad på, välja vi nu alla prenumerationer för kontot som standard

#### <a name="known-issues"></a>Kända problem

* Den här versionen av Storage Explorer kan inte köras i Ubuntu 14.04
* För att öppna flera flikar för samma resurs kontinuerligt Klicka inte på samma resurs. Klicka på en annan resurs och sedan gå tillbaka och klicka sedan på den ursprungliga resursen att öppna den igen i en annan flik
* Snabbåtkomst fungerar bara med prenumerationsbaserade objekt. Lokala resurser eller resurser som är anslutna via nyckel eller SAS-token stöds inte i den här versionen
* Det kan ta Snabbåtkomst några sekunder att navigera till målresursen, beroende på hur många resurser som du har
* Med fler än 3 grupper av blobar eller filöverföring samtidigt kan orsaka fel
* Sök handtag söka i ungefär 50 000 noder - därefter kan prestanda påverkas eller kan orsaka ett ohanterat undantag

10/03/2016
### <a name="version-085"></a>Version 0.8.5

#### <a name="new"></a>Ny

* Portal-genererade SAS-nycklar kan nu använda för att ansluta till Storage-konton och resurser

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: konkurrenstillstånd vid sökning ibland orsakade noder blir inte är expanderbara
* Åtgärdat: ”Använd HTTP” fungerar inte när du ansluter till Storage-konton med kontonamnet och nyckeln
* Åtgärdat: SAS-nycklar (som är särskilt Portal-genererade) returneras en ”avslutande snedstreck”-fel
* Fast: importera tabell problem
    * Ibland har partitionsnyckel och radnyckel återförts
    * Det gick inte att läsa ”null” partitionsnycklar

#### <a name="known-issues"></a>Kända problem

* Sök handtag söka i ungefär 50 000 noder - därefter kan påverkas prestanda
* Azure Stack stöder för närvarande inte filer, så försök att expandera filer visas ett fel

09/12/2016
### <a name="version-084"></a>Version 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Ny

* Generera Direktlänkar till lagringskonton, behållare, köer, tabeller eller filresurser för att dela och stöd för enkel åtkomst till dina resurser – Windows och Mac OS
* Sök efter din blob-behållare, tabeller, köer, filresurser eller storage-konton från sökrutan
* Nu kan du gruppera satser i tabellen Frågeverktyget
* Byt namn på och kopiera och klistra in blob-behållare, filresurser, tabeller, blobbar, blob-mappar, filer och kataloger från i SAS-anslutna konton och behållare
* Byta namn på och kopiering av blob-behållare och filresurser bevara nu egenskaper och metadata

#### <a name="fixes"></a>Korrigeringar

* Fast: Det går inte att redigera tabellentiteter om de innehåller booleska eller binära egenskaper

#### <a name="known-issues"></a>Kända problem

* Sök handtag söka i ungefär 50 000 noder - därefter kan påverkas prestanda

08/03/2016
### <a name="version-083"></a>Version 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Ny

* Byt namn på behållare, tabeller, filresurser
* Förbättrad upplevelse för frågan builder
* Möjlighet att spara och läsa in frågor
* Dirigera länkar till storage-konton eller behållare, köer, tabeller eller filresurser för att dela och enkel åtkomst till dina resurser (Windows endast - macOS stöd kommer snart!)
* Möjlighet att hantera och konfigurera CORS-regler

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Microsoft-Accounts kräver omautentisering var 8 – 12: e timme

#### <a name="known-issues"></a>Kända problem

* Ibland i Användargränssnittet kan vara låsta – maximerar fönstret hjälper dig att lösa problemet
* Mac OS-installationen kan kräver förhöjd behörighet
* Konto inställningspanelen kan indikera att du måste ange autentiseringsuppgifter igen för att filtrera prenumerationer
* Byta namn på filresurser, blob-behållare och tabeller bevaras inte i metadata eller andra egenskaper för behållaren, till exempel filresurskvot, offentlig åtkomstnivå eller åtkomstprinciper
* Ögonblicksbilder bevaras inte när du byter namn på BLOB-objekt (individuellt eller i en omdöpt blobbehållare). Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under en namnbyte
* Kopiera eller byta namn på resurser fungerar inte i SAS-anslutna konton

07/07/2016
### <a name="version-082"></a>Version 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Ny

* Storage-konton är grupperade efter prenumerationer; utvecklingslagring och resurser som är anslutna via nyckel eller SAS visas under noden (lokal och ansluten)
* Logga ut från konton i panelen ”Azure-kontoinställningar”
* Konfigurera proxyinställningar för att aktivera och hantera inloggning
* Skapa och ta bort blob-lån
* Öppna blob-behållare, köer, tabeller och filer med enkelklickning

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Kömeddelanden som infogats med .NET och Java-bibliotek är inte korrekt avkodas från base64
* Fast: $metrics tabeller visas inte för Blob Storage-konton
* Fast: tabellnod fungerar inte för lokal lagring för (utveckling)

#### <a name="known-issues"></a>Kända problem

* Mac OS-installationen kan kräver förhöjd behörighet

06/15/2016
### <a name="version-080"></a>Version 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Ny

* Filen resurs support: visa, ladda upp, ladda ned, kopiera filer och kataloger, SAS URI: er (skapa och Anslut)
* Förbättrad användarupplevelse för att ansluta till Storage med SAS URI: er eller konto nycklar
* Exportera frågeresultat för tabell
* Tabellen kolumnordning och anpassning
* Visa $logs blob-behållare och $metrics tabeller för Lagringskonton med aktiverade mått
* Förbättrad exportera och importera beteende, nu har egenskapsvärdetypen

#### <a name="fixes"></a>Korrigeringar

* Fast: överföra och hämta stora blobbar kan resultera i ofullständig uppladdningar/nedladdningar
* Fast: redigera, lägga till eller importera en entitet med ett numeriskt värde (”1”) konverteras det till dubbla
* Åtgärdat: Det går inte att expandera noden tabellen i den lokala utvecklingsmiljön

#### <a name="known-issues"></a>Kända problem

* $metrics tabeller är inte synliga för Blob Storage-konton
* Kömeddelanden som har lagts till via programmering kan inte visas korrekt om meddelanden är kodad med Base64-kodning

05/17/2016
### <a name="version-07201605090"></a>Version 0.7.20160509.0

#### <a name="new"></a>Ny

* Bättre felhantering för appen kraschar

#### <a name="fixes"></a>Korrigeringar

* En bugg har åtgärdats där informationsfält meddelanden ibland inte visas när inloggningsuppgifter krävdes

#### <a name="known-issues"></a>Kända problem

* Tabeller: Att lägga till, redigera eller importera en entitet som har en egenskap med ett ambiguously numeriska värde, till exempel ”1” eller ”1.0”, och användaren försöker skicka det som en `Edm.String`, värdet kommer tillbaka via klientens API som en Edm.Double

03/31/2016

### <a name="version-07201603250"></a>Version 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Ny

* Tabellen support: visa, fråga, export, import och CRUD-åtgärder för entiteter
* Stöd i kö: visa, lägga till dequeueing meddelanden
* Generera SAS URI: er för Lagringskonton
* Ansluta till Lagringskonton med SAS URI: er
* Meddelanden om uppdateringar för framtida uppdateringar av Storage Explorer
* Uppdaterade utseende

#### <a name="fixes"></a>Korrigeringar

* Förbättringar av prestanda och tillförlitlighet

### <a name="known-issues-amp-mitigations"></a>Kända problem &amp; åtgärder

* Hämtning av stora filer fungerar inte – vi rekommenderar att du använder AzCopy medan vi löser problemet
* Kontoautentiseringsuppgifter att inte hämtas och cachelagras om arbetsmappen går inte att hitta eller inte kan skrivas till
* Om vi lägger till, redigera eller importera en entitet som har en egenskap med ett ambiguously numeriska värde, till exempel ”1” eller ”1.0”, och användaren försöker skicka det som en `Edm.String`, värdet kommer tillbaka via klientens API som en Edm.Double
* När du importerar CSV-filer med flera rader poster kan data komma upp eller skrev ner texten

02/03/2016

### <a name="version-07201601291"></a>Version 0.7.20160129.1

#### <a name="fixes"></a>Korrigeringar

* Förbättrad övergripande prestanda när du laddar upp, ladda ned och kopiera blobbar

01/14/2016

### <a name="version-07201601050"></a>Version 0.7.20160105.0

#### <a name="new"></a>Ny

* Linux-support (paritet funktioner till OSX)
* Lägg till blob-behållare med signaturer för delad åtkomst (SAS)-nyckel
* Lägga till Lagringskonton för Azure Kina 21Vianet
* Lägga till Lagringskonton med anpassade slutpunkter
* Öppna och visa innehållet text- och blobar
* Visa och redigera blobbegenskaper och metadata

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: överföra eller hämta ett stort antal blobar (500 +) kan ibland orsaka appen att ha en vit skärm
* Fast: när du anger blob-behållare offentlig åtkomstnivå, det nya värdet uppdateras inte förrän du har angett fokus på behållaren igen. Dessutom dialogrutan alltid som standard ”ingen offentlig åtkomst” och inte det faktiska aktuella värdet.
* Stöd för bättre övergripande tangentbord/hjälpmedel och gränssnitt
* Spår historik text radbryts när det är långt med tomt utrymme
* SAS-dialog har stöd för verifiering av indata
* Lokal lagring fortsätter att vara tillgängligt även om användarens autentiseringsuppgifter har upphört att gälla
* När du tar bort en öppnad blob-behållare är i blob-Utforskaren på höger sida stängd

#### <a name="known-issues"></a>Kända problem

* Linux-installation måste gcc versionen eller uppgraderas – steg för att uppgradera finns nedan:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Version 0.7.20151116.0

#### <a name="new"></a>Ny

* macOS och Windows-versioner
* Logga in på Visa dina Lagringskonton – använda din organisations konto, Account, 2FA, osv.
* Lokal utvecklingslagring (använda lagringsemulatorn, endast Windows)
* Support för Azure Resource Manager och klassisk resurs
* Skapa och ta bort blobbar, köer och tabeller
* Sök efter specifika blobbar, köer och tabeller
* Utforska innehållet i blob-behållare
* Visa och navigera genom kataloger
* Ladda upp, hämta och ta bort blobbar och mappar
* Visa och redigera blobbegenskaper och metadata
* Generera SAS-nycklar
* Hantera och skapa lagras åtkomst principer (SAP)
* Sök efter blobbar efter prefix
* Dra ' n släppa filer överföra eller hämta

#### <a name="known-issues"></a>Kända problem

* När du anger blob-behållare offentlig åtkomstnivå, uppdateras inte det nya värdet förrän du har angett fokus på behållaren igen
* När du öppnar dialogrutan för att ange offentlig åtkomstnivå, visar det alltid ”ingen offentlig åtkomst” som standard och inte det faktiska aktuella värdet
* Det går inte att byta namn på hämtade BLOB-objekt
* Aktivitetsloggposter kommer ibland ”fastnar” i pågående tillstånd när ett fel inträffar, och felet visas inte
* Ibland kraschar eller stängs helt vit när du försöker överföra eller hämta ett stort antal blobbar
* Ibland avbryter en kopieringsåtgärd fungerar inte
* När du skapar en behållare (blob/queue/table), om du vill ange ett ogiltigt namn och fortsätta att skapa en annan under en annan behållare-typ kan inte du ange fokus på den nya typen
* Det går inte att skapa ny mapp eller Byt namn på mapp




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
