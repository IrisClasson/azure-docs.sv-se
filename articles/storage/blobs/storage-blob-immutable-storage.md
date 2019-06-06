---
title: Oföränderlig lagring för Azure Storage-Blobbar | Microsoft Docs
description: Azure Storage erbjuder stöd för mask (Skriv en gång, Läs många) för lagring av Blob (objekt) som gör att användarna kan lagra data i ett bevarandeintervallet, icke-ändringsbart tillstånd för ett visst intervall.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: d58c596421cec2e69210dd39a5d4a9708c154b44
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492758"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Store verksamhetskritiska data i Azure Blob storage

Oföränderlig lagring för Azure Blob storage kan du lagra sina verksamhetskritiska dataobjekt i tillståndet mask (Skriv en gång, Läs många). Det här tillståndet tillhandahåller data bevarandeintervallet och icke-ändringsbart för ett intervall som angetts av användaren. BLOB-objekt kan skapas och läsa, men inte ändras eller tas bort under Kvarhållningsintervall. Oföränderlig lagring är aktiverat för generell användning v2 och Blob Storage-konton i alla Azure-regioner.

## <a name="overview"></a>Översikt

Oföränderlig storage hjälper inom organisationen, finansiella institutioner och relaterade branscher – särskilt broker-återförsäljare organisationer – att lagra data på ett säkert sätt. Det kan också utnyttjas i alla scenarier för att skydda viktiga data mot ändras eller tas bort. 

Vanliga program innehåller:

- **Regelefterlevnad**: Oföränderlig lagring för Azure Blob storage hjälper organisationer adress sek 17a-4(f), CFTC 1.31(d), FINRA och andra bestämmelser. Ett tekniskt White Paper av Cohasset associerar information om hur oföränderligt storage adresser dessa regelkrav är hämtningsbara via den [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). Den [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) innehåller detaljerad information om våra efterlevnadscertifieringar.

- **Skydda dokumentet kvarhållning**: Oföränderlig lagring för Azure Blob storage säkerställer att data inte ändras eller tas bort av någon användare, inklusive användare med administrativ behörighet.

- **Bevarande av juridiska skäl**: Oföränderlig lagring för Azure Blob storage kan du lagra känslig information som är kritiska för tvister eller företag som används i ett manipuleringssäker tillstånd för den önskade varaktigheten förrän undantaget tas bort. Den här funktionen är inte begränsad endast till juridiska användningsfall, men kan också betraktas som en händelsebaserad skäl eller ett enterprise-Lås där behovet av att skydda data baserat på händelseutlösare eller företagspolicy krävs.

Oföränderlig storage har stöd för följande:

- **[Stöd för tidsbaserat bevarande Grupprincip](#time-based-retention)** : Användare kan ange principer för att lagra data för ett visst intervall. När en tidsbaserad bevarandeprincip är kan har angetts blobar skapas och läsa, men inte ändras eller tas bort. När kvarhållningsperioden har gått ut, BLOB-objekt tas bort men inte skrivas över.

- **[Bevarande av juridiska skäl stöd för Grupprincip](#legal-holds)** : Användare kan ange bevarande av juridiska skäl att lagra data immutably tills bevarande av juridiska skäl rensas om Kvarhållningsintervall som inte är känd.  När en princip för bevarande av juridiska skäl anges kan blobar skapas och läsa, men inte ändras eller tas bort. Varje bevarande av juridiska skäl är associerad med en användardefinierad alfanumeriska tagg (till exempel ett ärende-ID, händelsenamn, etc.) som används som en ID-sträng. 

- **Stöd för alla blob-nivåerna**: MASK principer är oberoende av Azure Blob storage-nivå och gäller för alla nivåer: frekvent, lågfrekvent och Arkiv. Användarna kan överföra data till den mest kostnaden-optimerade nivån för sina arbetsbelastningar samtidigt som data oföränderlighetsprincip.

- **Behållare på servernivå configuration**: Användare kan konfigurera tidsbaserad bevarandeprinciper och bevarande av juridiska skäl taggar på behållarenivån. Genom att använda enkla behållarenivån inställningar kan användare skapa och låsa tidsbaserade bevarandeprinciper, utöka kvarhållningsintervaller, uppsättning och rensa bevarande av juridiska skäl med mera. Dessa principer gäller för alla blobar i behållaren, både befintliga och nya.

- **Granska loggningsstöd**: Varje behållare innehåller en princip-granskningsloggen. Den visar upp till sju tidsbaserade kvarhållning-kommandon för låst tidsbaserade bevarandeprinciper och innehåller användar-ID, kommandotypen, tidsstämplar och Kvarhållningsintervall. Loggen innehåller användar-ID, kommandotypen, tidsstämplar för bevarande av juridiska skäl och bevarande av juridiska skäl taggar. Den här loggfilen sparas i livslängden för principen, i enlighet med sek 17a-4(f) föreskrifter. Den [Azure-aktivitetsloggen](../../azure-monitor/platform/activity-logs-overview.md) visar en mer omfattande logg över alla aktiviteter för kontroll-plan; vid aktivering av [Azure diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-overview.md) behåller och visar data kontrollplansåtgärder. Det är användarens ansvar att lagra dessa loggar beständigt som kan krävas för regler eller andra ändamål.

## <a name="how-it-works"></a>Hur det fungerar

Oföränderlig lagring för Azure Blob storage stöder två typer av mask eller inte kan ändras principer: tidsbaserat bevarande och bevarande av juridiska skäl. När en tidsbaserad bevarandeprincip eller bevarande av juridiska skäl används på en behållare, flytta alla befintliga blobar till ett oföränderligt mask tillstånd i mindre än 30 sekunder. Alla nya blobbar som överförs till den behållaren flyttas även om den inte kan ändras statusen. När alla blobar har flyttats till tillståndet inte kan ändras, kan ändras principen bekräftas och alla skriver över eller tar bort tillåts inte åtgärder för befintliga och nya objekt i behållaren kan ändras.

Behållare och försöker ta bort också tillåts inte om det finns alla blobar som skyddas av en princip som inte kan ändras. Ta bort behållaråtgärden misslyckas om det finns minst en blob med en låst tidsbaserad bevarandeprincip eller ett juridiskt bevarande. Borttagningen av lagringskontot misslyckas om det finns minst en WORM-container med bevarande av juridiska skäl eller en blob med ett aktivt kvarhållningsintervall. 

### <a name="time-based-retention"></a>Tidsbaserat bevarande

> [!IMPORTANT]
> En tidsbaserad bevarandeprincip måste vara *låst* för blobben som ska finnas i en kompatibel kan ändras (skriva och ta bort skyddade) tillstånd för sek 17a-4(f) och andra föreskrifter. Vi rekommenderar att du låser principen inom en rimlig tid, vanligtvis mindre än 24 timmar. Det ursprungliga tillståndet för en tillämpad tidsbaserad bevarandeprincip är *upplåst*, så att du kan testa funktionen och göra ändringar i principen innan du låser den. Medan den *upplåst* tillstånd skyddar oföränderlighetsprincip, bör inte använda den *upplåst* tillstånd för något annat syfte än kortsiktig funktionen utvärderingsversioner. 

När en tidsbaserad bevarandeprincip används på en behållare, alla blobar i behållaren ska vara kvar i tillståndet inte kan ändras under hela den *effektiva* kvarhållningsperiod. Effektiva kvarhållningsperioden för befintliga blobar är lika med skillnaden mellan ändringstid blob och Kvarhållningsintervall som angetts av användaren.

För nya blobbar är den effektiva kvarhållningsperioden lika med det kvarhållningsintervall som angetts av användaren. Eftersom användare kan utöka Kvarhållningsintervall, använder kan ändras storage de senaste värdet för Kvarhållningsintervall som angetts av användaren för att beräkna effektiv kvarhållningsperioden.

> [!TIP]
> **Exempel:** En användare skapar en tidsbaserad bevarandeprincip med ett Kvarhållningsintervall på fem år.
>
> Befintlig blob i den behållaren _testblob1_, skapades ett år sedan. Effektiva kvarhållningsperioden för _testblob1_ är fyra år.
>
> En ny blob _testblob2_, nu har överförts till behållaren. Effektiva kvarhållningsperioden för den här nya bloben är fem år.

Ett olåst tidsbaserad bevarandeprincip rekommenderas endast för testning av funktionen och en princip måste låsas för att vara kompatibel med sek 17a-4(f) och andra föreskrifter. När en tidsbaserad bevarandeprincip är låst, principen kan inte tas bort och högst 5 ökar till effektiva kvarhållningsperioden är tillåtet. Mer information om hur du anger och låsa tidsbaserade bevarandeprinciper finns i den [komma igång](#getting-started) avsnittet.

### <a name="legal-holds"></a>Bevarande av juridiska skäl

När du ställer in ett bevarande av juridiska skäl Behåll alla befintliga och nya BLOB kan ändras tillståndet tills bevarande av juridiska skäl är avmarkerad. Mer information om hur du definierar och rensa bevarande av juridiska skäl finns i den [komma igång](#getting-started) avsnittet.

En behållare kan ha både ett bevarande av juridiska skäl och en tidsbaserad bevarandeprincip på samma gång. Alla blobar i behållaren förblir kan ändras statusen tills alla bevarande av juridiska skäl rensas, även om deras effektiva kvarhållningsperiod har upphört att gälla. Omvänt kan är en blob i ett tillstånd som inte kan ändras tills effektiva kvarhållningsperioden upphör att gälla, även om alla bevarande av juridiska skäl har rensats.

I följande tabell visas typerna av blobåtgärder som är inaktiverade i olika scenarier som inte kan ändras. Mer information finns i den [Azure Blob Service-API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentation.

|Scenario  |BLOB-tillstånd  |Blobåtgärder inte tillåtet  |
|---------|---------|---------|
|Effektivt kvarhållningsintervall för blobben har ännu inte gått ut och/eller bevarande av juridiska skäl har angetts     |Oåterkallelig: både ta bort- och skrivskyddad         | Placera Blob<sup>1</sup>, placera Block<sup>1</sup>, placera Blockeringslista<sup>1</sup>, ta bort behållare, ta bort Blob ange Blob-Metadata, placera sidan genom att ange egenskaper för Blob, ta ögonblicksbild av Blob, inkrementell kopiering av Blob, Lägga till Block         |
|Effektivt kvarhållningsintervall på blobben har upphört att gälla     |Skrivskyddad endast (ta bort tillåts)         |Placera Blob<sup>1</sup>, placera Block<sup>1</sup>, placera Blockeringslista<sup>1</sup>, ange Blob-Metadata, placera sidan, ange Blob egenskaper, ta ögonblicksbild av Blob, inkrementell kopiering av Blob, lägga till Block         |
|Alla juridiska innehåller avmarkerad och ingen tidsbaserad bevarandeprincip anges för behållaren     |Föränderlig         |Ingen         |
|Ingen mask princip har skapats (tidsbaserat bevarande eller bevarande av juridiska skäl)     |Föränderlig         |Ingen         |

<sup>1</sup> programmet tillåter dessa åtgärder att skapa en ny blob en gång. Alla efterföljande skriva över åtgärder på en befintlig blob-sökväg i en oföränderlig behållare tillåts inte.

## <a name="supported-values"></a>Värden som stöds

### <a name="time-based-retention"></a>Tidsbaserat bevarande
- För ett lagringskonto är det maximala antalet behållare med låsta tidsbaserade oföränderligt principer 1 000.
- Det minsta Kvarhållningsintervall är 1 dag. Det maximala antalet är 146,000 dagar (400 år).
- För en behållare är det maximala antalet redigeringar att utöka en Kvarhållningsintervall för låst tidsbaserade oföränderligt principer 5.
- För en behållare behålls högst 7 tidsbaserat bevarande princip granskningsloggar under av principen.

### <a name="legal-hold"></a>Bevarande av juridiska skäl
- För ett lagringskonto är det maximala antalet behållare med en inställning för bevarande av juridiska skäl 1 000.
- För en behållare är det maximala antalet taggar för bevarande av juridiska skäl 10.
- Den minsta längden på en tagg för bevarande av juridiska skäl är 3 alfanumeriska tecken. Den maximala längden är 23 alfanumeriska tecken.
- För en behållare innehålla högst 10 juridiska principgranskning loggar bevaras under av principen.

## <a name="pricing"></a>Prissättning

Det finns ingen extra kostnad för att använda den här funktionen. Oföränderlig data debiteras på samma sätt som vanliga, föränderliga data. Information om priser på Azure Blob Storage finns i den [Azure Storage-prissidan](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Komma igång
Oföränderlig storage är endast tillgängligt för generell användning v2 och Blob Storage-konton. Dessa konton måste hanteras via [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Information om hur du uppgraderar ett befintligt lagringskonto för generell användning v1 finns i [uppgradera ett lagringskonto](../common/storage-account-upgrade.md).

De senaste versionerna av den [Azure-portalen](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), och [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) stöder inte kan ändras lagring för Azure Blob storage. [Bibliotek för klientstöd](#client-libraries) tillhandahålls också.

### <a name="azure-portal"></a>Azure Portal

1. Skapa en ny container eller välj en befintlig container för lagring av de blobbar som ska behållas i det oföränderliga tillståndet.
 Behållaren måste vara i ett GPv2- eller blob storage-konto.
2. Välj **princip** i inställningarna för behållaren. Välj sedan **+ Lägg till** under **oföränderlig bloblagring**.

    ![Behållarinställningar i portalen](media/storage-blob-immutable-storage/portal-image-1.png)

3. Välj för att aktivera tidsbaserat bevarande **tidsbaserat bevarande** från den nedrullningsbara menyn.

    ![”Tidsbaserat bevarande” som valts under ”principtypen”](media/storage-blob-immutable-storage/portal-image-2.png)

4. Ange Kvarhållningsintervall i dagar (godkända värden 1 146000 dagar).

    ![Kryssrutan ”Uppdatera kvarhållningsperioden till”](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Det ursprungliga tillståndet för principen är upplåst så att du kan testa funktionen och göra ändringar i principen innan du låser den. Låsning principen är nödvändigt för regler som sek 17a – 4 efterlevs.

5. Låsa principen. Högerklicka på de tre punkterna ( **...** ), och följande meny visas med ytterligare åtgärder:

    ![”Låsa principen” på menyn](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Välj **Låsprincip** och bekräfta låset. Principen nu är låst och kan inte tas bort, tillåts endast tillägg av Kvarhållningsintervall som. Tar bort BLOB och åsidosättningar tillåts inte. 

    ![Bekräfta ”låsprincip” på menyn](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Välj för att aktivera bevarande av juridiska skäl **+ Lägg till**. Välj **bevarande av juridiska skäl** från den nedrullningsbara menyn.

    ![”Bevarande av juridiska skäl” under ”principtypen” på menyn](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Skapa ett bevarande av juridiska skäl med en eller flera taggar.

    ![”Taggnamnet”-rutan under principtypen](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Om du vill ta bort ett bevarande av juridiska skäl, tar du bort taggen tillämpade bevarande av juridiska skäl identifierare.

### <a name="azure-cli"></a>Azure CLI

Funktionen ingår i följande kommando grupper: `az storage container immutability-policy` och `az storage container legal-hold`. Kör `-h` på dem för att se kommandona.

### <a name="powershell"></a>PowerShell

Modulen Az.Storage stöder inte kan ändras lagring.  Följ dessa steg om du vill aktivera funktionen:

1. Kontrollera att du har den senaste versionen av installerat PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Ta bort alla tidigare installation av Azure PowerShell.
3. Installera Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

Den [exempel på PowerShell-kod](#sample-powershell-code) senare i den här artikeln visar användning av funktioner.

## <a name="client-libraries"></a>Klientbibliotek

Följande klientbibliotek oföränderligt storage har stöd för Azure Blob storage:

- [Klientbiblioteket för .NET version 7.2.0-preview och senare](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Klientbiblioteket för node.js version 4.0.0 och senare](https://www.npmjs.com/package/azure-arm-storage)
- [Python-klientbiblioteket version 2.0.0 Release Candidate 2 och senare](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java-klientbiblioteket](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Kan ni ge dokumentation av mask efterlevnad?**

Ja. Dokumentet att Microsoft behålls ett ledande oberoende utvärdering av företag som specialiserar sig på poster hanterings- och styrning Cohasset associerar att utvärdera Azure oföränderligt Blob Storage och dess kompatibilitet med särskilda krav i branschen för finansiella tjänster. Cohasset verifierat att Azure kan ändras Blob-lagring, när de används för att behålla tidsbaserade Blobar i tillståndet mask uppfyller relevanta lagringskrav CFTC regeln 1.31(c)-(d) och FINRA regeln 4511 sek regel 17a-4. Microsoft mål den här uppsättningen regler, som de representerar den mest vägledningen globalt för kvarhållning av poster för finansinstitutioner. Cohasset rapporten är tillgänglig i den [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). Om du vill begära en bokstav för bestyrkande från Microsoft om mask efterlevnad, kontakta Azure-supporten.

**Gäller funktionen om du vill endast blockblobar, eller sidan och tilläggsblobbar samt?**

Oföränderlig storage kan användas med en blobtyp, men vi rekommenderar att du använder den huvudsakligen för blockblob-objekt. Till skillnad från blockblob-objekt, sidan BLOB-objekt och lägga till BLOB-objekt måste skapa utanför en mask behållare och kopieras sedan i. När du har inte kopierat dessa blobar till en mask behållare och ytterligare *lägger till* till en tilläggs blob eller ändringar av en sidblobb tillåts.

**Behöver jag skapa ett nytt lagringskonto för att använda den här funktionen?**

Nej, du kan använda lagring som inte kan ändras med befintliga eller nya generell användning v2 eller Blob storage-konton. Den här funktionen är avsedd för användning med blockblobar i GPv2 och Blob Storage-konton. Allmänt syfte v1-lagringskonton stöds inte, men kan enkelt uppgraderas till gpv2. Information om hur du uppgraderar ett befintligt lagringskonto för generell användning v1 finns i [uppgradera ett lagringskonto](../common/storage-account-upgrade.md).

**Kan jag använda ett bevarande av juridiska skäl och en tidsbaserad bevarandeprincip?**

Ja, en behållare har både ett bevarande av juridiska skäl och en tidsbaserad bevarandeprincip på samma gång. Alla blobar i behållaren förblir kan ändras statusen tills alla bevarande av juridiska skäl rensas, även om deras effektiva kvarhållningsperiod har upphört att gälla. Omvänt kan är en blob i ett tillstånd som inte kan ändras tills effektiva kvarhållningsperioden upphör att gälla, även om alla bevarande av juridiska skäl har rensats.

**Bevarande av juridiska skäl principer är endast för talan eller finns det andra scenarier för användning?**

Nej, juridiska håller är bara den allmänna termen som används för en icke tidsbaserad bevarandeprincip. Det måste inte bara att användas för tvister relaterade åtal. Juridiska skäl principer är användbara för att inaktivera överskrivning och tar bort för att skydda viktiga enterprise mask data om kvarhållningsperioden är okänd. Du kan använda den som en princip för att skydda dina verksamhetskritiska mask arbetsbelastningar eller använda den som en mellanlagringsprincip innan en anpassad händelse-utlösare måste du använda en tidsbaserad bevarandeprincip. 

**Kan jag ta bort en *låst* tidsbaserad bevarandeprincip eller bevarande av juridiska skäl?**

Endast upplåst tidsbaserade bevarandeprinciper kan tas bort från en behållare. När en tidsbaserad bevarandeprincip är låst, kan inte tas bort; endast effektiva kvarhållningsperiod tillägg tillåts. Bevarande av juridiska skäl taggar kan tas bort. När alla juridiska taggar har tagits bort, tas bort bevarande av juridiska skäl.

**Vad händer om jag försöker ta bort en container med en *låst* tidsbaserade bevarandeprincip eller bevarande av juridiska skäl?**

Ta bort behållaråtgärden misslyckas om det finns minst en blob med en låst tidsbaserad bevarandeprincip eller ett juridiskt bevarande. Ta bort behållaren åtgärden lyckas bara om inga blob med en aktiv Kvarhållningsintervall finns och att det finns inga bevarande av juridiska skäl. Du måste ta bort blobar innan du kan ta bort behållaren.

**Vad händer om jag försöker ta bort ett lagringskonto med en WORM-container som har en *låst* tidsbaserad bevarandeprincip eller ett bevarande av juridiska skäl?**

Borttagningen av lagringskontot misslyckas om det finns minst en WORM-container med bevarande av juridiska skäl eller en blob med ett aktivt kvarhållningsintervall. Du måste ta bort alla mask behållare innan du kan ta bort lagringskontot. Information om borttagning av behållare, finns i den föregående frågan.

**Kan jag flytta data över olika blob-nivåer (frekvent, lågfrekvent, kall) när blobben är i oförändrat tillstånd?**

Ja, du kan använda kommandot Ange Blobnivå för att flytta data mellan blob-nivåer samtidigt som data i kompatibla oföränderligt tillstånd. Oföränderlig lagring stöds på frekvent, lågfrekvent och arkivnivå på blob.

**Vad händer om jag inte betalat och mitt kvarhållningsintervall inte har gått ut?**

När det gäller utebliven betalning gäller normal datalagringsprinciper som anges i villkoren i ditt avtal med Microsoft.

**Finns det en utvärderingsversion eller en respitperiod för att bara testa funktionen?**

Ja. När en tidsbaserad bevarandeprincip skapas, det är i ett *upplåst* tillstånd. I det här tillståndet kan du göra önskade ändringar Kvarhållningsintervall, till exempel öka eller minska och även ta bort principen. När principen är låst, förblir den låst tills Kvarhållningsintervall som upphör att gälla. Den här låst principen förhindrar borttagning och ändring av Kvarhållningsintervall. Vi rekommenderar starkt att du använder den *upplåst* endast för försöket och låsa principen inom en 24-timmarsperiod. Dessa metoder hjälper dig att uppfylla med sek 17a-4(f) och andra bestämmelser.

**Kan jag använda mjuk borttagning tillsammans med oföränderligt blob principer?**

Ja. [Mjuk borttagning för Azure Blob storage](storage-blob-soft-delete.md) gäller för alla behållare i ett lagringskonto oavsett ett bevarande av juridiska skäl eller tidsbaserad bevarandeprincip. Vi rekommenderar att aktivera mjuk borttagning för ytterligare skydd innan eventuella oföränderligt mask-principer tillämpas och bekräftats. 

**Var finns funktionen?**

Oföränderlig storage är tillgängligt i offentlig Azure-, Kina och Government-regioner. Om inte kan ändras lagring inte är tillgängligt i din region, kontakta supporten och e-post azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Exempel på PowerShell-kod

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följande PowerShell-exempelskript är referens. Det här skriptet skapar ett nytt lagringskonto och en behållare. Den sedan visar hur du kan ange och ta bort bevarande av juridiska skäl, skapa och låsa en tidsbaserad bevarandeprincip (även kallat en oföränderlighetsprincip) och utöka Kvarhållningsintervall.

Konfigurera och testa Azure Storage-konto:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Ange och ta bort bevarande av juridiska skäl:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Skapa eller uppdatera oföränderlighetsprinciper:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Hämta oföränderlighetsprinciper:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Låsa oföränderlighetsprinciper (Lägg till - Force för att stänga Kommandotolken):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Utöka oföränderlighetsprinciper:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Ta bort ett olåst oföränderlighetsprincip (Lägg till - Force för att stänga Kommandotolken):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
