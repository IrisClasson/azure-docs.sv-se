---
title: Omvandla tal med hjälp av tal-SDK för C++
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller exempelkod för översättning av tal med hjälp av SDK för tal i en C++-miljö.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 917eb170ee1798546f4ba6dcf228097d5a5d853c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092221"
---
# <a name="translate-speech-with-the-cognitive-services-speech-sdk-for-c"></a>Omvandla tal med Cognitive Services tal SDK för C++

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-cpp[Translation using a microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#TranslationWithMicrophone)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter den kod som används i den här artikeln i mappen samples/cpp/windows /-konsolen.

## <a name="next-steps"></a>Nästa steg

- [Så identifierar du tal](how-to-recognize-speech-cpp.md)
- [Förstå avsikter från tal](how-to-recognize-intents-from-speech-cpp.md)
