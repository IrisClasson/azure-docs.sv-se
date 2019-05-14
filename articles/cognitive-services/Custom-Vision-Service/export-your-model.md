---
title: Exportera din modell till mobile – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att exportera din modell som ska användas när mobila program.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 7bf8217f5076c0a95d4db6c1c7cbea7bc93b91f3
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550545"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exportera din modell för användning med mobila enheter

Custom Vision Service gör klassificerare som ska exporteras för att köra offline. Du kan bädda in din exporterade klassificerare i ett program och köra det lokalt på en enhet för i realtid klassificering.

Custom Vision Service har stöd för följande export:

* __Tensorflow__ för __Android__.
* __CoreML__ for __iOS11__.
* __ONNX__ för __Windows ML__.
* En Windows- eller Linux __behållare__. Behållaren innehåller en Tensorflow modellera och kod för att använda Custom Vision Service API-tjänsten. 

> [!IMPORTANT]
> Custom Vision Service exporterar endast __komprimera__ domäner. Modeller som genererats av compact domäner är optimerade för begränsningar i realtid klassificeringen på mobila enheter. Klassificerare som skapats med en kompakt domän kan vara något sämre än en standard domän med samma mängd träningsdata.
>
> Information om att förbättra din klassificerare, finns det [kontinuerligt förbättra din klassificerare](getting-started-improving-your-classifier.md) dokumentet.

## <a name="convert-to-a-compact-domain"></a>Konvertera till en kompakt domän

> [!NOTE]
> Stegen i det här avsnittet gäller endast om du har en befintlig klassificerare som inte är inställt på att komprimera domän.

Använd följande steg om du vill konvertera en befintlig klassificerare domän:

1. Från den [Custom vision sidan](https://customvision.ai)väljer den __Start__ ikon för att visa en lista över dina projekt. Du kan också använda den [ https://customvision.ai/projects ](https://customvision.ai/projects) att se dina projekt.

    ![Bild av listan över hem-ikonen och projekt](./media/export-your-model/projects-list.png)

2. Välj ett projekt och välj sedan den __Gear__ ikonen längst upp till höger på sidan.

    ![Bild av kugghjulsikonen](./media/export-your-model/gear-icon.png)

3. I den __domäner__ väljer en __komprimera__ domän. Välj __spara ändringar__ att spara ändringarna.

    ![Bild av val av domäner](./media/export-your-model/domains.png)

4. Högst upp på sidan Välj __träna__ att träna med hjälp av den nya domänen.

## <a name="export-your-model"></a>Exportera din modell

Om du vill exportera modellen efter träna, använder du följande steg:

1. Gå till den **prestanda** fliken och markera __exportera__. 

    ![Bild av ikonen Exportera](./media/export-your-model/export.png)

    > [!TIP]
    > Om den __exportera__ posten är inte tillgänglig och den valda upprepningen använder inte en kompakt domän. Använd den __iterationer__ på den här sidan för att välja en iteration som använder en kompakt domän och sedan välja __exportera__.

2. Välj exportformat och välj sedan __exportera__ att ladda ned modellen.

## <a name="next-steps"></a>Nästa steg

Integrera din exporterade modell i ett program genom att utforska en av följande artiklar eller exempel:

* [Använd din Tensorflow-modell med Python](export-model-python.md)
* [Använd din ONNX-modell med Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Se exempel för [CoreML modell i en iOS-App](https://go.microsoft.com/fwlink/?linkid=857726) för i realtid bildklassificering med Swift.
* Se exempel för [Tensorflow-modell i en Android-App](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) för i realtid bildklassificering på Android.
* Se exempel för [CoreML modell med Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) för i realtid bildklassificering i en Xamarin iOS-app.
