---
title: Använd skapare för att skapa inliggande kartor
description: Använd Azure Maps Creator för att skapa inomhus Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7ea1995b6d1232b3e4c6371313e5b3d45bdbb756
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075404"
---
# <a name="use-creator-to-create-indoor-maps"></a>Använd skapare för att skapa inliggande kartor

I den här kursen får du lära dig hur du skapar inlednings kartor. I den här självstudien får du lära dig hur du använder API: et för att:

> [!div class="checklist"]
> * Ladda upp ditt Drawing-paket för Inhämtnings kartor
> * Konvertera ditt Drawing-paket till kart data
> * Skapa en data uppsättning från dina kart data
> * Skapa en TILESET från data i din data uppsättning
> * Fråga WFS-API (Azure Maps Web Feature service) om du vill veta mer om dina kart funktioner
> * Skapa en funktions stateset med hjälp av kart funktionerna och data i din data uppsättning
> * Uppdatera din funktions stateset

## <a name="prerequisites"></a>Förutsättningar

Skapa inomhus Maps:

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.
3. [Skapa en skapare resurs](how-to-manage-creator.md)
4. Ladda ned [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

>[!IMPORTANT]
> API-URL: er i det här dokumentet kan behöva justeras enligt platsen för din skapare-resurs. Mer information finns i [åtkomst till Creator Services](how-to-manage-creator.md#access-to-creator-services).

## <a name="upload-a-drawing-package"></a>Ladda upp ett ritnings paket

Använd [API: et för data överföring](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) för att ladda upp ritnings paketet till Azure Maps resurser.

API för data uppladdning är en tids krävande transaktion som implementerar det mönster som definierats här. När åtgärden har slutförts använder vi `udid` för att komma åt det överförda paketet för att konvertera det. Följ stegen nedan för att hämta `udid` .

1. Öppna Postman-appen. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **samling**.  Namnge samlingen och välj knappen **skapa** .

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj metoden **post** http på fliken Builder och ange följande URL för att överföra ritnings paketet till Azure Maps-tjänsten. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Ange ett värde för nyckeln på fliken **sidhuvud** `Content-Type` . Ritnings paketet är en zippad mapp, så Använd `application/octet-stream` värdet. På fliken **brödtext** väljer du **binär**. Klicka på **Välj fil** och välj ett ritnings paket.

     ![data hantering](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Klicka på knappen blå **sändning** och vänta tills begäran har bearbetats. När begäran har slutförts går du till fliken **sidhuvud** i svaret. Kopiera värdet för **plats** nyckeln, som är `status URL` .

6. Om du vill kontrol lera status för API-anropet skapar du en **Get** http-begäran på `status URL` . Du måste lägga till din primära prenumerations nyckel till URL: en för autentisering. **Get** -begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. När **Get** http-begäran har slutförts returneras en `resourceLocation` . `resourceLocation`Innehåller det unika `udid` för det överförda innehållet. Du kan också använda `resourceLocation` URL: en för att hämta metadata från den här resursen i nästa steg.

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. Om du vill hämta metadata för innehåll skapar **du en http** -begäran på den `resourceLocation` URL som hämtades i steg 7. Se till att lägga till din primära prenumerations nyckel till URL: en för autentisering. **Get** -begäran bör likna följande URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. När **Get** http-begäran har slutförts innehåller svars texten som `udid` anges i i `resourceLocation` steg 7, platsen för att komma åt/Ladda ned innehållet i framtiden och några andra metadata om innehållet som skapat/uppdaterat datum, storlek och så vidare. Ett exempel på det övergripande svaret är:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Konvertera ett ritnings paket

 Nu när ritnings paketet har överförts använder vi `udid` för det överförda paketet för att konvertera paketet till kartdata. Konverterings-API: et använder en tids krävande transaktion som implementerar det mönster som definierats [här](creator-long-running-operation.md). När åtgärden har slutförts använder vi `conversionId` för att komma åt konverterade data. Följ stegen nedan för att hämta `conversionId` .

1. Välj **Nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn på begäran** och välj en samling. Klicka på **Spara**.

2. Välj metoden **post** http på fliken Builder och ange följande URL för att konvertera det överförda ritnings paketet till kart data. Använd `udid` för det överförda paketet.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```
    >[!IMPORTANT]
    > API-URL: er i det här dokumentet kan behöva justeras enligt platsen för din skapare-resurs. Mer information finns i [åtkomst till Creator Services](how-to-manage-creator.md#access-to-creator-services).

3. Klicka på knappen **Skicka** och vänta tills begäran har bearbetats. När begäran har slutförts går du till fliken **sidhuvud** i svaret och letar efter **plats** nyckeln. Kopiera värdet för **plats** nyckeln, som är `status URL` för konverterings förfrågan.

4. Starta en ny **Get** http-metod på fliken Builder. Lägg till din Azure Maps primära prenumerations nyckel i `status URL` . Gör en **Get** -begäran i `status URL` från föregående steg. Om konverterings processen ännu inte har slutförts kan du se något som följande JSON-svar:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. När begäran har slutförts visas ett status meddelande om lyckad status i svars texten.  Kopiera `conversionId` från `resourceLocation` URL: en för det konverterade paketet. `conversionId`Används av andra API för att komma åt de konverterade kart data.

    ```json
   {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Postman-programmet stöder inte internt HTTP tids krävande begär Anden. Därför kan du se en lång fördröjning när du gör en **Get** -begäran på status-URL: en.  Vänta i 30 sekunder och försök att klicka på knappen **Skicka** igen tills svaret visar att åtgärden lyckades eller misslyckades.

Exempel ritnings paketet ska konverteras utan fel eller varningar. Men om du får fel eller varningar från ditt eget ritnings paket får du en länk till [ritnings fel visualiseraren](drawing-error-visualizer.md)med JSON-svaret. Med fel visualiserare för ritning kan du granska information om fel och varningar. För att få rekommendationer om hur du löser konverterings fel och varningar, se [fel och varningar för ritnings konvertering](drawing-conversion-error-codes.md).

```json
{
    "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Skapa en datauppsättning

Data uppsättningen är en samling kart funktioner, till exempel byggnader, nivåer och rum. Om du vill skapa en data uppsättning använder du [data uppsättningen Create API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview). Skapa-API: et för data uppsättningen tar `conversionId` för det konverterade ritnings paketet och returnerar en `datasetId` av de skapade data uppsättningarna. Stegen nedan visar hur du skapar en data uppsättning.

1. I Postman-programmet väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn på begäran** och välj en samling. Klicka på **Spara**

2. Skapa en ny data uppsättning genom att göra en **post** -begäran till [data uppsättningen skapa API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) . Lägg till både din prenumerations nyckel och `conversionId` med den som `conversionId` hämtades under konverterings processen i steg 5 innan du skickar in begäran.  Begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Hämta `statusURL` i svars **huvudets** **plats** nyckel.

4. Gör en **Get** -begäran på `statusURL` för att hämta `datasetId` . Lägg till din Azure Maps primära prenumerations nyckel för autentisering. Begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. När **Get** http-begäran har slutförts, kommer svars huvudet att innehålla `datasetId` för den skapade data uppsättningen. Kopiera `datasetId` . Du måste använda `datasetId` för att skapa en TILESET.

    ```json
    {
        "operationId": "a93570cb-3e4f-4e45-a2b1-360df174180a",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Skapa en TILESET

En TILESET är en uppsättning vektor paneler som återges på kartan. Tilesets skapas från befintliga data uppsättningar. En TILESET är dock oberoende från den data uppsättning som den har blivit källaad från. Om data uppsättningen tas bort, kommer TILESET fortfarande att finnas kvar. Följ stegen nedan om du vill skapa en TILESET:

1. I Postman-programmet väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn på begäran** och välj en samling. Klicka på **Spara**

2. Gör en **post** -begäran på fliken Builder. URL: en för begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Gör en **Get** -begäran `statusURL` för TILESET. Lägg till din Azure Maps primära prenumerations nyckel för autentisering. Begäran bör se ut som följande URL:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key=<Azure-Maps-Primary-Subscription-key>
    ```

4. När **Get** http-begäran har slutförts innehåller svars huvudet `tilesetId` för den skapade TILESET. Kopiera `tilesetId` .

    ```json
    {
        "operationId": "a93570cb-3e4f-4e45-a2b1-360df174180a",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Fråga data uppsättningar med WFS-API

 Data uppsättningar kan frågas med [WFS-API](https://docs.microsoft.com/rest/api/maps/wfs). Med WFS-API: et kan du fråga efter funktions samlingar, en speciell samling eller en speciell funktion med ett funktions **-ID**. Funktions **-ID: t** identifierar en unik funktion i data uppsättningen. Den används till exempel för att identifiera vilket funktions tillstånd som ska uppdateras i en specifik stateset.

1. I Postman-programmet väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn på begäran** och välj en samling. Klicka på **Spara**

2. Gör en **Get** -begäran för att visa en lista över samlingarna i din data uppsättning. Ersätt `<dataset-id>` med `datasetId` . Använd Azure Maps primär nyckel istället för plats hållaren. Begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. Svars texten levereras i formatet för det interjson-formatet och kommer att innehålla alla samlingar i data uppsättningen. För enkelhetens skull visar exemplet bara `unit` samlingen. Om du vill se ett exempel som innehåller alla samlingar, se [WFS beskriver API: er för samlingar](https://docs.microsoft.com/rest/api/maps/wfs/collectiondescriptionpreview). Om du vill veta mer om en samling kan du klicka på någon av URL: erna inuti `link` elementet.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Gör en **Get** -begäran för `unit` funktions samlingarna.  Ersätt `{datasetId}` med `datasetId` . Använd Azure Maps primär nyckel istället för plats hållaren. Svars texten innehåller alla funktioner i `unit` samlingen. Begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Kopiera funktionen `id` för en enhets funktion som har stil egenskaper som kan ändras dynamiskt.  Eftersom enhetens besittnings status och temperatur kan uppdateras dynamiskt, kommer vi att använda den här funktionen `id` i nästa avsnitt. I följande exempel `id` är funktionen "UNIT26". Vi kommer att se stil egenskaperna för den här funktionen som tillstånd och vi använder funktionen för att skapa en stateset.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Skapa en funktions stateset

1. I Postman-programmet väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn på begäran** och välj en samling. Klicka på **Spara**

2. Gör en **post** -begäran till [create Stateset-API: et](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview). Använd `datasetId` för data uppsättningen som innehåller det tillstånd som du vill ändra. Begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Ange som i **rubrikerna** för **post** -begäran `Content-Type` `application/json` . Ange formaten nedan i **bröd texten**för att återspegla ändringar i och- `occupied` `temperature` *tillstånden*. När du är klar klickar du på **Skicka**.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Kopiera `statesetId` från svars texten.

5. Skapa en **post** -begäran för att uppdatera statusen: skicka statesetId och funktionen `ID` med din Azure Maps prenumerations nyckel. Begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Ange som i **rubrikerna** för **post** -begäran `Content-Type` `application/json` . Kopiera och klistra in JSON i exemplet nedan i **bröd texten** i **post** -begäran.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > Uppdateringen kommer bara att sparas om den tid som är bokförd är efter tidsstämpeln för föregående begäran. Vi kan skicka alla namn som vi har konfigurerat tidigare när de skapas.

7. Vid en lyckad uppdatering får du en `200 OK` HTTP-statuskod. Om du har en [dynamisk formatering som har implementerats](indoor-map-dynamic-styling.md) för en inomhus karta visas uppdateringen i den angivna tids stämplingen.

Med [funktionen Get](https://docs.microsoft.com/rest/api/maps/featurestate/getstatespreview) States API kan du hämta tillståndet för en funktion med hjälp av dess funktion `ID` . Du kan också ta bort stateset och dess resurser genom att använda [funktions tillstånd ta bort API](https://docs.microsoft.com/rest/api/maps/featurestate/deletestatesetpreview).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ladda upp ditt Drawing-paket för Inhämtnings kartor
> * Konvertera ditt Drawing-paket till kart data
> * Skapa en data uppsättning från dina kart data
> * Skapa en TILESET från data i din data uppsättning
> * Fråga tjänsten Azure Maps WFS om du vill veta mer om dina kart funktioner
> * Skapa en funktions stateset med hjälp av kart funktionerna och data i din data uppsättning
> * Uppdatera din funktions stateset

Nu är du utrustad med de kunskaper du behöver för att gå vidare till nästa guider:

> [!div class="nextstepaction"]
> [Använda modulen inomhus Maps](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Implementera dynamisk formatering för inomhus Maps](indoor-map-dynamic-styling.md)

Läs mer om de olika Azure Maps tjänsterna som beskrivs i den här artikeln:

> [!div class="nextstepaction"]
> [Data uppladdning](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Data konvertering](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Datamängd](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Funktions tillstånds uppsättning](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS-tjänst](creator-indoor-maps.md#web-feature-service-api)
