---
title: 'Snabb start: publicera kunskaps bas, REST, Java-QnA Maker'
description: Den här Java REST-baserade snabb starten publicerar din kunskaps bas och skapar en slut punkt som kan anropas i ditt program eller chatt-robot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: a9eb97848e755c230c6ba8664cfaa51bfb4fb64c
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325924"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Snabbstart: Publicera en kunskapsbas i QnA Maker med hjälp av Java

Den här REST-baserade snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publiceringen skickar push-överför den senaste versionen av kunskaps basen till ett dedikerat Azure Kognitiv sökning-index och skapar en slut punkt som kan anropas i ditt program eller chatt-robot.

Den här snabbstarten anropar API:er för QnA Maker:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Publicera) – detta API kräver inte någon information i brödtexten för begäran.

## <a name="prerequisites"></a>Krav

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* I det här exemplet används Apache [HTTP-klient](https://hc.apache.org/httpcomponents-client-ga/) från HTTP Components. Du behöver lägga till följande Apache HTTP-klientbibliotek i projektet:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta din nyckel och slut punkt (som innehåller resurs namnet) väljer du **snabb start** för resursen i Azure Portal.
* QnA Maker kunskaps bas-ID (KB) hittades i URL: en i frågesträngparametern `kbid` som visas nedan.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Om du inte har en kunskapsbas ännu kan du kan skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas](create-new-kb-csharp.md).

> [!NOTE]
> Fullständiga lösnings fil (er) är tillgängliga från [ **Azure-samples/kognitiv-Services-qnamaker-Java** GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Skapa en Java-fil

Öppna VSCode och skapa en ny fil med namnet `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Längst upp i `PublishKB.java`, ovanför klassen, lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Skapa PublishKB-klass med main-metoden

Efter beroendena lägger du till följande klass:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Lägg till nödvändiga konstanter

I **main** -metoden lägger du till de konstanter som krävs för att få åtkomst till QNA Maker. Ersätt värdena med dina egna.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Lägga till POST-begäran för att publicera kunskapsbas

Efter konstanterna som krävs lägger du till följande kod, som gör en HTTPS-begäran för API:et för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext. Koden lägger till innehåll för 204-svar.

För andra svar returneras svaret oförändrat.

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet från kommandoraden. Det skickar automatiskt begäran till API:et för QnA Maker, och sedan skrivs svaret ut till konsolfönstret.

1. Skapa filen:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Kör filen:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

När kunskapsbasen publiceras behöver du [slutpunktens webbadress för att generera ett svar](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)