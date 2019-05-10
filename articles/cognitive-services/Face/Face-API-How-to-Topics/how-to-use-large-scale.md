---
title: 'Exempel: Använda den storskaliga funktionen – Ansikts-API'
titleSuffix: Azure Cognitive Services
description: Använda den storskaliga funktionen i Ansikts-API.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: 35ab2d36a5d6c9977398fdbc16ba22eb1d9656a4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65229846"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>Exempel: Känna igen storskaliga funktioner

Den här guiden är en avancerad artikel om hur du skala upp från befintliga **PersonGroup** och **FaceList** till **LargePersonGroup** och **LargeFaceList**respektive. Den här guiden visar migreringsprocessen och förutsätter en grundläggande kunskaper om **PersonGroup**, **FaceList**, [träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) åtgärden och ansiktsigenkänning funktioner. Se den [står inför igenkänning av](../concepts/face-recognition.md) konceptuella guide om hur du lär dig mer om dessa.

LargePersonGroup och LargeFaceList kallas gemensamt för storskaliga åtgärder. LargePersonGroup kan innehålla upp till 1 000 000 personer, var och en med högst 248 ansikten och LargeFaceList kan innehålla upp till 1 000 000 ansikten. Storskaliga åtgärder är liknande konventionella PersonGroup och FaceList men har några viktiga skillnader på grund av den nya arkitekturen. 

Exemplen är skrivna i C#- med Ansikts-API-klientbiblioteket.

> [!NOTE]
> Om du vill aktivera ansiktssökningsprestanda för identifiering och FindSimilar i stor skala, måste du införa en träningsåtgärd för att förbearbeta LargeFaceList och LargePersonGroup. Träningstiden varierar från sekunder till ungefär en halvtimme beroende på den faktiska kapaciteten. Under träningsperioden är det fortfarande möjligt att utföra identifiering och FindSimilar om en lyckad träning har utförts innan. Nackdelen är dock att de nyligen tillagda personerna/ansiktena inte visas i resultatet tills en ny postmigrering till storskaliga träning har slutförts.

## <a name="step-1-initialize-the-client-object"></a>Steg 1: Initiera klientobjektet

När du använder klientbiblioteket för ansikts-API så skickas prenumerationsnyckeln och prenumerationens slutpunkt till konstruktorn för klassen FaceServiceClient. Exempel:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Prenumerationsnyckeln med motsvarande slutpunkt kan hämtas från Marketplace-sidan på Azure-portalen.
Se [Prenumerationer](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Steg 2: Kod migrering

Det här avsnittet fokuserar endast på migrering av PersonGroup/FaceList implementeringen till LargePersonGroup/LargeFaceList. Även om LargePersonGroup/LargeFaceList skiljer sig från PersonGroup/FaceList i utformningen och interna implementeringen liknar de API-gränssnitt för bakåtkompatibilitet.

Datamigrering stöds inte, du måste återskapa LargePersonGroup/LargeFaceList istället.

### <a name="migrate-persongroup-to-largepersongroup"></a>Migrera PersonGroup till LargePersonGroup

Migrering från PersonGroup till LargePersonGroup är enkelt eftersom de har exakt samma gruppnivå-åtgärder.

För PersonGroup/Person-relaterad implementering, behöver du bara ändra sökvägarna för API:et eller SDK-klassen/modulen till LargePersonGroup och LargePersonGroup Person.

Du måste lägga till alla ansikten och personer från PersonGroup till den nya LargePersonGroup. Se [hur du lägger till ansikten](how-to-add-faces.md) referens.

### <a name="migrate-facelist-to-largefacelist"></a>Migrera FaceList till LargeFaceList

| FaceList API:er | LargeFaceList API:er |
|:---:|:---:|
| Skapa | Skapa |
| Radera | Radera |
| Hämta  | Hämta  |
| Visa lista | Visa lista |
| Uppdatera | Uppdatera |
| - | Träna |
| - | Hämta träningsstatus |

Föregående tabell är en jämförelse av åtgärder på listnivå mellan FaceList och LargeFaceList. Som du ser så har LargeFaceList nya åtgärder, träna och hämta träningsstatus jämfört med FaceList. Att få LargeFaceList tränat är en förutsättning för [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)-åtgärden medan ingen träning krävs för FaceList. Följande kodfragment är en hjälpfunktion för att vänta på träningen av en LargeFaceList.

```CSharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Tidigare var en typisk användning av FaceList med att lägga till ansikten och FindSimilar skulle se ut så här:

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

När du migrerar den till LargeFaceList, blir den följande:

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Som det visas ovan är delarna datahantering och FindSimilar nästan desamma. Det enda undantaget är att en ny träningsåtgärd för förbearbetning måste slutföras i LargeFaceList innan FindSimilar fungerar.

## <a name="step-3-train-suggestions"></a>Steg 3: Träna förslag

Även om träningsåtgärden påskyndar [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) och [identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), så blir träningstiden lidande, speciellt i stor skala. Den uppskattade träningstiden i olika skalor visas i följande tabell:

| Skala (ansikten eller personer) | Beräknad träningstid |
|:---:|:---:|
| 1,000 | 1 – 2 sek |
| 10 000 | 5 – 10 sek |
| 100,000 | 1 – 2 min |
| 1,000,000 | 10 – 30 min |

För att bättre utnyttja storskalefunktionen så rekommenderas det att ta hänsyn till några strategier.

## <a name="step-31-customize-time-interval"></a>Steg 3.1: Anpassa tidsintervall

Som det visas i `TrainLargeFaceList()`, finns det en `timeIntervalInMilliseconds` att fördröja den oändliga träningsstatusens kontrollprocess. För LargeFaceList med flera ansikten, minskar anropsantalet och -kostnaden med hjälp av ett större intervall. Tidsintervallet bör anpassas enligt den förväntade kapaciteten för LargeFaceList.

Samma strategin gäller även för LargePersonGroup. Till exempel när en LargePersonGroup utbildning med 1 000 000 personer i `timeIntervalInMilliseconds` kan vara 60 000 (1-minutersintervall).

## <a name="step-32-small-scale-buffer"></a>Steg 3.2 småskalig buffert

Personer/ansikten i LargePersonGroup/LargeFaceList är enbart sökbara efter träning. I ett dynamiskt scenario läggs nya personer/ansikten hela tiden till och måste vara omedelbart sökbara, men träning kan ta längre tid än önsvärt. För att lösa problemet, kan du använda en extra småskalig LargePersonGroup/LargeFaceList som en buffert enbart för de nytillagda posterna. Den här bufferten tar kortare tid att träna på grund av en mycket mindre storlek och omedelbar sökning på den här tillfälliga bufferten borde fungera. Använd den här bufferten i kombination med träning på den huvudsakliga LargePersonGroup/LargeFaceList genom att köra huvudträningen på ett lägre intervall, till exempel mitt på natten och dagligen.

Ett exempelarbetsflöde:
1. Skapa en huvudsaklig LargePersonGroup/LargeFaceList (huvudsamling) och en buffert LargePersonGroup/LargeFaceList (buffertsamling). Buffertsamlingen är endast för nyligen tillagda personer/ansikten.
1. Lägg till nya personer/ansikten i både huvudsamlingen och buffertsamlingen.
1. Träna endast buffertsamlingen med ett kort tidsintervall för att se till att de nyligen tillagda posterna börjar gälla.
1. Anropa identifiering/FindSimilar mot både huvudsamlingen och buffertsamlingen och sammanfoga resultaten.
1. När buffertsamlingens storlek ökar till ett tröskelvärde eller när systemet är inaktivt, skapa en ny buffertsamling och utlös träning på huvudsamlingen.
1. Ta bort den gamla buffertsamlingen efter att träningen på huvudsamlingen slutförts.

## <a name="step-33-standalone-training"></a>Steg 3.3 fristående utbildning

Om en relativt lång svarstid är acceptabelt, behöver du inte utlösa träningsåtgärden direkt efter att nya data lagts till. Istället kan träningsåtgärden delas upp från den huvudsakliga logiken och utlösas regelbundet. Den här strategin är lämplig för dynamiska scenarier med acceptabel svarstid och kan tillämpas på statiska scenarier för att ytterligare minska träningsfrekvensen.

Anta att det finns en `TrainLargePersonGroup`-funktion som liknar `TrainLargeFaceList`. En typisk implementering av fristående utbildning på LargePersonGroup genom att aktivera den [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) klassen i `System.Timers` skulle bli:

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Mer information om datahantering och identifieringsrelaterade implementeringar finns i [Så här lägger du till ansikten](how-to-add-faces.md) och [Så här identifierar du ansikten i bilden](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig hur du migrerar den befintliga PersonGroup/FaceList koden (inte data) till LargePersonGroup/LargeFaceList:

- LargePersonGroup och LargeFaceList fungerar på liknande sätt som PersonGroup/FaceList, förutom att träningsåtgärd krävs av LargeFaceList.
- Vidta rätt träningsstrategi för dynamisk datauppdatering för storskaliga datauppsättningar.

## <a name="next-steps"></a>Nästa steg

Följ en instruktionsguide för att lära dig att lägga till ansikten i en PersonGroup eller köra identifiering igen på en PersonGroup.

- [Så här lägger du till ansikten](how-to-add-faces.md)
- [Så här identifierar du ansikten i en bild](HowtoIdentifyFacesinImage.md)