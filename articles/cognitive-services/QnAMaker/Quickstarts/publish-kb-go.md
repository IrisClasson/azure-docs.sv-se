---
title: 'Snabbstart: Publicera kunskaps bas, REST, go-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Den här Go REST-baserade snabbstarten vägleder dig genom publiceringen av din kunskapsbas, vilken överför den senaste versionen av den testade kunskapsbasen till ett dedikerat Azure Search-index som representerar den publicerade kunskapsbasen. Den skapar även en slutpunkt som kan anropas i ditt program eller en chattrobot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: b271de5437ee58c8b01c762c3ee770b2a5d0746b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802886"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Snabbstart: Publicera en kunskapsbas i QnA Maker med Go

Den här REST-baserade snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publicering skickar den senaste versionen av kunskapsbasen till ett dedikerat Azure Search-index och skapar en slutpunkt som kan anropas i ditt program eller en chattrobot.

Den här snabbstarten anropar API:er för QnA Maker:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Publicera) – detta API kräver inte någon information i brödtexten för begäran.

## <a name="prerequisites"></a>Förutsättningar

* [Go 1.10.1](https://golang.org/dl/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta din nyckel och slut punkt (som innehåller resurs namnet) väljer du **snabb start** för resursen i Azure Portal.

* ID för QnA Maker-kunskapsbas (KB) som finns i URL:en i kbid-frågesträngsparametern enligt nedan.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Om du inte har någon kunskapsbas ännu kan du skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas.](create-new-kb-csharp.md)

> [!NOTE] 
> Kompletta lösningsfiler finns på [**Azure-Samples/cognitive-services-qnamaker-go** GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Skapa en Go-fil

Öppna VSCode och skapa en ny fil med namnet `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Längst upp i `publish-kb.go` lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Skapa Main-funktionen

Efter beroendena som krävs lägger du till följande klass:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Lägg till nödvändiga konstanter

Inuti funktionen **main**


 lägger du sedan till de nödvändiga konstanterna för att få åtkomst till QnA Maker. Ersätt värdena med dina egna.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Lägg till POST-begäran för att publicera kunskapsbas

Efter konstanterna som krävs lägger du till följande kod, som gör en HTTPS-begäran för API:et för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext. Koden lägger till innehåll för 204-svar.

För andra svar returneras svaret oförändrat.

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Ange följande kommando för att kompilera filen. Kommandotolken returnerar inte någon information för ett lyckat bygge.

```bash
go build publish-kb.go
```

Ange följande kommando på en kommandorad för att köra programmet. Den skickar förfrågan till API:et för QnA Maker för att publicera kunskapsbasen och skriva ut 204 för genomfört eller fel.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nästa steg

När kunskapsbasen publiceras behöver du [slutpunktens webbadress för att generera ett svar](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
