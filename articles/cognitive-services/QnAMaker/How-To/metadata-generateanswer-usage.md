---
title: Metadata med GenerateAnswer API – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker kan du lägga till metadata i form av nyckel/värde-par för din fråga/svar-uppsättningar. Den här informationen kan användas för att filtrera resultat på användarförfrågningar och för att lagra ytterligare information som kan användas i Uppföljnings konversationer.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.openlocfilehash: 2454e07e4fc4600f846acc7afbcc19cc0b677450
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792242"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>Hämta ett knowledge svar med GenerateAnswer API och metadata

Använd GenerateAnswer API för att få förväntade svaret på en användares fråga. När du publicerar en kunskapsbas visas den här informationen för att använda detta API på på publiceringssidan. Du kan också konfigurera API för att filtrera svar baserat på metadatataggar och testa kunskapsbas från slutpunkten med test-frågesträngparametern.

QnA Maker kan du lägga till metadata i form av nyckel / värdepar i din fråga/svar-uppsättningar. Den här informationen kan användas för att filtrera resultat på användarförfrågningar och för att lagra ytterligare information som kan användas i Uppföljnings konversationer. Mer information finns i [kunskapsbas](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>Lagra frågor och svar med en entitet för frågor och svar

Först är det viktigt att förstå hur QnA Maker lagrar fråga/svar-data. Följande bild visar en frågor och svar om entitet:

![Frågor och svar om entiteten](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Varje entitet för frågor och svar har ett unikt och beständiga-ID. ID: T kan användas för att göra uppdateringar för en viss affärsenhet i frågor och svar.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Få svar förutsägelser med GenerateAnswer-API

Du kan använda GenerateAnswer-API: er i din robot eller ditt program för att fråga din kunskapsbas med en fråga för användare att få bästa möjliga matchning från fråga/svar-uppsättningar.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicera för att hämta GenerateAnswer slutpunkten 

När du publicerar din kunskapsbas, antingen från den [QnA Maker portal](https://www.qnamaker.ai), eller med hjälp av den [API](https://go.microsoft.com/fwlink/?linkid=2092179), du kan hämta information om din GenerateAnswer slutpunkt.

Hämta information om din slutpunkt:
1. Logga in på [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. I **min kunskapsbaser**, klicka på **Visa kod** för kunskapsbasen.
    ![Min kunskapsbaser](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Får tillgång till dina GenerateAnswer slutpunkt.

    ![information om slutpunkten](../media/qnamaker-how-to-metadata-usage/view-code.png)

Du kan också få din slutpunktsinformation från den **inställningar** fliken i kunskapsbasen.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer begäran-konfiguration

Du kan anropa GenerateAnswer med en HTTP POST-begäran. Exempel som visar hur du anropar GenerateAnswer, finns det [snabbstarter](../quickstarts/csharp.md).

Den **URL för begäran** har följande format: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|Egenskapen för HTTP-begäran|Namn|Type|Syfte|
|--|--|--|--|
|URL-parameter för väg|Kunskapsbas-ID|string|GUID för kunskapsbasen.|
|URL-parameter för väg|QnAMaker slutpunktsvärd|string|Värdnamnet för den slutpunkt som distribuerats i din Azure-prenumeration. Detta är tillgängligt på sidan inställningar när du har publicerat i knowledge base. |
|Sidhuvud|Innehållstyp|string|Medietyp i texten som skickas till API: et. Standardvärdet är: ''|
|Sidhuvud|Auktorisering|string|Din slutpunktsnyckeln (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Publicera brödtext|JSON-objekt|JSON|Fråga med inställningar|


JSON-texten har flera inställningar:

|Brödtext JSON-egenskap|Krävs|Type|Syfte|
|--|--|--|--|
|`question`|obligatorisk|string|En användare-fråga som ska skickas till din kunskapsbas.|
|`top`|valfri|heltal|Antal översta resultat ska ingå i utdata. Standardvärdet är 1.|
|`userId`|valfri|string|Ett unikt ID för att identifiera användaren. Detta ID kommer att läggas till i chattloggarna.|
|`scoreThreshold`|valfri|heltal|Endast svar med förtroendepoäng ovanför denna tröskel returneras. Standardvärdet är 0.|
|`isTest`|valfri|boolesk|Om värdet är true, returnerar resultat från `testkb` Search-index i stället för publicerade index.|
|`strictFilters`|valfri|string|Om anges talar du om QnA Maker att returnera endast de svar som har angivna metadata. Använd `none` som visar svaret ska ha inga filter för filmetadata. |

Det ser ut som ett exempel på JSON-texten:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer responsegenskaperna

Ett lyckat svar returneras statusen 200 och ett JSON-svar. 

|Svar-egenskap (sorterat efter bedömning)|Syfte|
|--|--|
|poäng|En rangordning mellan 0 och 100.|
|ID|Ett unikt ID för svaret.|
|Frågor|Frågor som anges av användaren.|
|Svar|Svaret på frågan.|
|source|Namnet på källan svaret har extraherats, eller sparats i knowledge base.|
|metadata|Metadata som associeras med svaret.|
|metadata.name|Namn på metadata. (string, maximal längd: 100 krävs)|
|metadata.value: Metadata-värde. (string, maximal längd: 100 krävs)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>Med metadata kan du filtrera svar efter anpassade metadatataggar

Att lägga till metadata kan du filtrera svar efter dessa metadatataggar. Lägga till metadatakolumnen från den **Visningsalternativ** menyn. Lägga till metadata i kunskapsbasen genom att klicka på metadata **+** ikon för att lägga till ett metadata-par. Den här par består av en nyckel och ett värde.

![lägga till metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrera resultaten med strictFilters för metadatataggarna

Överväg att användaren frågan ”när fungerar den här hotell Stäng”? Om avsikten är angiven för restaurang ”Paradise”.

Eftersom det behövs endast för restaurang ”Paradise” resultat, kan du ange ett filter i GenerateAnswer anropet om metadata som ”restaurang-Name”, enligt följande.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Använda frågor och svar resultat för att hålla konversationen kontext

Svaret på GenerateAnswer innehåller motsvarande metadatainformation för matchade fråga/svar-uppsättningen. Den här informationen kan användas i klientprogrammet för att lagra kontexten för den tidigare konversationen för användning i senare konversationer. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Publiceringssidan innehåller även information om du vill generera ett svar med [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) och [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](./create-knowledge-base.md)
