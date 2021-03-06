---
title: 'Snabb start: tal-SDK för Java Script (webbläsare) plattforms konfiguration – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för att använda Java Script (webbläsare) med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: 54fe96ecee99f3de37667d255b57b89c34aecc14
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406440"
---
Den här guiden visar hur du installerar [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för Java Script för användning med en webb sida.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Skapa en ny webbplatsmapp

Skapa en ny, tom mapp. Om du vill värdbasera exemplet på en webbserver ser du till att webbservern kan komma åt mappen.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Packa upp Speech SDK för JavaScript i den mappen

Ladda ned Speech SDK som ett [.zip-paket](https://aka.ms/csspeech/jsbrowserpackage) och packa upp det i den nya mappen. Detta resulterar i att fem filer packas upp:
* `microsoft.cognitiveservices.speech.sdk.bundle.js`En läslig version av talet SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map`En mappnings fil som används för att felsöka SDK-kod.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts`Objekt definitioner som ska användas med TypeScript
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js`En minified-version av talet SDK.
* `speech-processor.js`Kod för att förbättra prestanda i vissa webbläsare.

## <a name="create-an-indexhtml-page"></a>Skapa en index.html-sida

Skapa en ny fil i mappen, med namnet `index.html` och öppna filen med en textredigerare.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]