---
title: 'Snabbstart: Skapa kunskapsbas – REST, Java – QnA Maker'
titlesuffix: Azure Cognitive Services
description: I den här Java REST-baserade snabbstarten går vi igenom hur du skapar ett exempel på QnA Maker-kunskapsbas, programmatiskt, som visas i Azure-instrumentpanelen för ditt Cognitive Services-API-konto.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: c6fddec80da1baa852a2f3712a94b785d775919e
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151047"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Snabbstart: Skapa en kunskapsbas i QnA Maker med Java

Den här snabbstarten går igenom hur du programmatiskt skapar ett exempel på QnA Maker-kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../Concepts/data-sources-supported.md). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>Skapa en kunskapsbasfil

Skapa en fil som heter `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Högst upp i `CreateKB.java` lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter
När du har lagt till nödvändiga beroenden lägger du till de konstanter som krävs till klassen `CreateKB` för åtkomst till QnA Maker. Ersätt värdet för variabeln `subscriptionKey` med din egen QnA Maker-nyckel. Du behöver inte lägga till den sista klammerparentesen för att avsluta klassen. Den finns i det sista kodfragmentet i slutet av den här snabbstarten.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Lägga till KB-modelldefinitionsklasser
Efter konstanterna lägger du till följande klasser och funktioner i klassen `CreateKB` för att serialisera modelldefinitionsobjektet till JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Lägga till stödfunktioner

Sedan lägger du till följande stödfunktioner i klassen `CreateKB`.

1. Lägg till följande funktioner för att skriva ut JSON i ett lättläst format:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Lägg till följande klass för att hantera HTTP-svaret:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Lägg till följande metod för att göra en POST-begäran för QnA Maker-API: er. `Ocp-Apim-Subscription-Key` är QnA Maker-tjänstenyckeln, som används för autentisering.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Lägg till följande metod för att göra en GET-begäran för QnA Maker-API: er.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Lägg till en metod för att skapa KB
Lägg till följande metod för att skapa kunskapsbasen genom att anropa Post-metoden.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

Detta API-anrop anropar ett JSON-svar som innehåller åtgärds-ID. Använd åtgärds-ID:t för att fastställa om KB har skapats.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Lägg till en metod för att hämta status
Lägg till följande metod för att kontrollera statusen för skapandet.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Upprepa anropet tills det lyckas eller misslyckas:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Lägga till en main-metod
Main-metoden skapar kunskapsbasen och söker sedan efter status. Åtgärden ID returneras i fältet POST svar rubrik **plats**, används som en del av vägen i GET-begäran. Den `while` loop försöker status om den inte har slutförts.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Skapa och köra programmet

1. Kontrollera att gson-biblioteket finns i katalogen `./libs`. På kommandoraden kompilerar du filen `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Ange följande kommando på en kommandorad för att köra programmet. Begäran skickas till API:et för QnA Maker för att skapa KB, och sedan söker den efter resultat var 30:e sekund. Varje svar skrivs ut i konsolfönstret.

    ```base
    java -cp ",;libs/*" CreateKB
    ```

När kunskapsbasen har skapats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
