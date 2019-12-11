---
title: 'Snabb start: konvertera text till tal, Node. js – tal service'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du konverterar text till tal med Node. js och text till tal-REST API. Exempeltext som ingår i den här guiden är strukturerad som tal syntes Markup Language (SSML). På så sätt kan du välja rösten och språket för tal-svaret.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b120acd25874585a744fb774aafe15d32d7baf08
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976510"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Snabb start: konvertera text till tal med Node. js

I den här snabb starten får du lära dig hur du konverterar text till tal med Node. js och text till tal-REST API. Det begärda innehållet i den här guiden är strukturerad som [tal syntes Markup Language (SSML)](speech-synthesis-markup.md), vilket gör att du kan välja rösten och språket i svaret.

Den här snabb starten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Speech service-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* [Nod 8.12.x eller senare](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumerationsnyckel för tjänsten Speech. [Hämta en kostnads fri!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Skapa ett projekt och Kräv beroenden

Skapa ett nytt Node. js-projekt med hjälp av din favorit-IDE eller-redigerare. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `tts.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `npm install request request-promise xmlbuilder readline-sync`.

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Text till tal REST-API kräver en åtkomsttoken för autentisering. Om du vill få en åtkomsttoken, krävs ett utbyte. Den här funktionen utbyter din röst tjänst prenumerations nyckel för en åtkomsttoken med hjälp av `issueToken` slut punkten.

Det här exemplet förutsätter att din röst tjänst prenumeration är i regionen USA, västra. Om du använder en annan region måste du uppdatera värdet för `uri`. En fullständig lista finns i [regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopiera den här koden till projektet:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Mer information om autentisering finns i [autentisera med en åtkomsttoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

I nästa avsnitt skapar vi funktionen för att anropa text till tal-API: et och spara det syntetiskt tal svaret.

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här skapar du begäran till text till tal-API: et och sparar tal svaret. Det här exemplet förutsätter att du använder slutpunkten som USA, västra. Om din resurs har registrerats till en annan region, kontrollera att du uppdaterar den `uri`. Mer information finns i avsnittet om [tal service områden](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Du måste sedan lägga till nödvändiga sidhuvuden för begäran. Se till att du uppdaterar `User-Agent` med namnet på din resurs (finns i Azure portal) och Ställ in `X-Microsoft-OutputFormat` till din önskade ljud. En fullständig lista över utdataformat finns i [ljud matar ut](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Skapa sedan begärandetexten med tal syntes Markup Language (SSML). Det här exemplet definierar strukturen och använder den `text` indata du skapade tidigare.

>[!NOTE]
> Det här exemplet används den `JessaRUS` rösttyp. En fullständig lista över Microsoft tillhandahålls röster/språk, finns i [språkstöd](language-support.md).
> Om du är intresserad av att skapa en unik, identifierbara ton för ditt varumärke, se [skapar anpassade rösttyper](how-to-customize-voice-font.md).

Slutligen ska du göra en begäran till tjänsten. Om begäran lyckas och en status kod för 200 returneras, skrivs tal svaret som `TTSOutput.wav`.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Färdigställa allt

Nästan klart. Det sista steget är att skapa en asynkron funktion. Den här funktionen kommer att läsa din prenumerations nyckel från en miljö variabel, fråga efter text, hämta en token, vänta tills begäran har slutförts och sedan konvertera text till tal och spara ljudet som. wav.

Om du inte är bekant med miljövariabler eller vill testa med prenumerations nyckeln hårdkodad som en sträng ersätter du `process.env.SPEECH_SERVICE_KEY` med din prenumerations nyckel som en sträng.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt, är du redo att köra din text till tal-exempelapp. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
node tts.js
```

När du uppmanas, anger du i vad du vill konvertera från text till tal. Om detta lyckas, finns tal-filen i projektmappen. Spela upp den med din favorit media player.

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Se också

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Skapa anpassade rösttyper](how-to-customize-voice-font.md)
* [Post voice-exempel för att skapa en anpassad röst](record-custom-voice-samples.md)
