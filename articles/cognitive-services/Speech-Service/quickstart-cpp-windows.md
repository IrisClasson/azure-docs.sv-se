---
title: 'Snabbstart: Identifiera tal- C++ , (Windows)-tal-service'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar tal i C++ på Windows Desktop med hjälp av Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: c795f1581ae36f100065c39cd47bc4efc564b9fe
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607881"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Snabbstart: Taligenkänning i C++ på Windows med hjälp av Speech SDK

Snabb Starter är också tillgängliga för [text till tal](quickstart-text-to-speech-cpp-windows.md) och [tal översättning](quickstart-translate-speech-cpp-windows.md).

Om du vill kan du välja ett annat programmeringsspråk och/eller miljö:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln får skapa du ett C++-konsolprogram för Windows. Du använder Cognitive Services [Speech SDK](speech-sdk.md) för att transkribera tal till text i realtid från datorns mikrofon. Programmet har skapats med [Speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 eller senare (alla versioner).

## <a name="prerequisites"></a>Förutsättningar

Du behöver en prenumerations nyckel för tal tjänster för att slutföra den här snabb starten. Du kan skaffa en utan kostnad. Mer information finns i [testa tal tjänsterna kostnads fritt](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna källfilen *helloworld.cpp*. Ersätt all kod under den första include-instruktionen (`#include "stdafx.h"` eller `#include "pch.h"`) med följande:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. I menyraden väljer du **Skapa** > **Skapa lösning**. Koden ska kompileras utan fel.

   ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-cpp-windows-06-build.png)

1. Starta programmet. I menyraden väljer du **Felsök** > **Starta felsökning**, eller tryck på **F5**.

   ![Skärmbild av Visual Studio-programmet med Starta felsökning markerat](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Ett konsolfönster öppnas där du uppmanas att säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till tal tjänsterna och skrivs till text som visas i samma fönster.

   ![Skärmbild av konsolutdata efter lyckad taligenkänning](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel hur man läser tal från en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska C++-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
