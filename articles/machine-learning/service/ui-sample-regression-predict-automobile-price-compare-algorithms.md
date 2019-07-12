---
title: 'Regression: Förutsäga pris och jämföra algoritmer'
titleSuffix: Azure Machine Learning service
description: Den här artikeln visar hur du skapar en komplexa machine learning-experiment utan att behöva skriva en enda rad kod med hjälp av det visuella gränssnittet. Lär dig hur du finjustera och jämföra flera regressionsmodeller för att förutsäga priset för en bil baserat på tekniska funktioner
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: aa0a1fc2acdc9687030040c23cdb1781e9529169
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605680"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Exempel 2 – Regression: Förutsäga pris och jämföra algoritmer

Lär dig hur du skapar en komplexa machine learning-experiment utan att behöva skriva en enda rad kod med hjälp av det visuella gränssnittet. Det här exemplet träna och jämför flera regressionsmodeller för att förutsäga priset för en bil baserat på dess tekniska funktioner. Anledningen tillhandahåller vi för valen som gjorts i det här experimentet så att du kan hantera din egen machine learning problem.

Om du precis har kommit igång med machine learning, kan du ta en titt på de [grundläggande version](ui-sample-regression-predict-automobile-price-basic.md) med det här experimentet för att se en grundläggande regression experimentera.

Här är färdiga diagrammet det här experimentet:

[![Diagram över experimentet](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Välj den **öppna** knappen för experimentet exempel 2:

    ![Öppna experimentet](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Sammanfattning för experiment

Vi kan använda de här stegen för att skapa experimentet:

1. Hämta data.
1. Förbearbeta data.
1. Träna modellen.
1. Testa, utvärdera och jämför modeller.

## <a name="get-the-data"></a>Hämta data

I det här experimentet använder vi den **Automobile price data (Raw)** datauppsättningen, vilket är från UCI Machine Learning-databasen. Den här datauppsättningen innehåller 26 kolumner som innehåller information om bilar, inklusive märke, modell, pris, vehicle funktioner (t.ex. antalet cylindrar), MPG och en försäkring riskpoäng. Målet med det här experimentet är att förutsäga priset på en bil.

## <a name="pre-process-the-data"></a>Förbearbeta data

Förberedelseuppgifter viktigaste data inkluderar datarensning, integration, transformation, minskning, och discretization eller kvantifieringsfel. Visuella gränssnittet hittar moduler för att utföra dessa åtgärder och andra data förbearbetning uppgifter i den **Dataomvandling** i den vänstra panelen.

I det här experimentet använder vi den **Välj kolumner i datauppsättning** modul för att exkludera normalized-losses som har många värden som saknas. Sedan använder vi **Rensa Data som saknas** att ta bort de rader som innehåller värden som saknas. Detta hjälper dig för att skapa en ren uppsättning träningsdata.

![Förbearbetning av data](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Träna modellen

Machine learning problem variera. Vanliga inkluderar klassificering och klustring, regression och rekommenderare system, som kan kräva en annan algoritm. Ditt val av algoritmen beror ofta på kraven för användningsfallet. När du väljer en algoritm, måste du justera dess parametrar för att träna en modell för exaktare. Sedan måste du utvärdera alla modeller som är baserat på mått som noggrannhet, uppfattbarheten avsevärt och effektivitet.

Eftersom målet med det här experimentet är att förutsäga bilpriser och eftersom etikettkolumnen (pris) innehåller reella tal, en regressionsmodell är ett bra val. Överväger att antalet funktioner som är relativt liten (mindre än 100) och dessa funktioner inte är null-optimerade troligen beslut gränsen kommer att vara icke-linjära.

Om du vill jämföra prestanda för olika algoritmer, använder vi två icke-linjära algoritmer, **förstärkta Regression för beslut** och **beslut skog Regression**, för att skapa modeller. Båda algoritmer har parametrar som du kan ändra, men vi använder standardvärden för det här experimentet.

Vi använder den **dela Data** modul för att dela upp indata slumpmässigt så att innehåller 70% av den ursprungliga informationen för datauppsättning för träning och testning datauppsättningen innehåller 30% av den ursprungliga informationen.

## <a name="test-evaluate-and-compare-the-models"></a>Testa, utvärdera och jämför modeller

Vi använder två olika uppsättningar av slumpmässigt valda data att träna och testa modellen, enligt beskrivningen i föregående avsnitt. Vi dela upp datauppsättningen och använda olika datauppsättningar för att träna och testa modellen för att göra flera mål för utvärderingen av modellen.

När modellen tränas, använder vi den **Poängmodell** och **utvärdera modell** moduler för att generera förutsagt resultat och utvärdera modellerna. **Bedömningsmodell** genererar förutsägelser för test-datauppsättning med hjälp av den tränade modellen. Vi skickar sedan poängen till **utvärdera modell** att generera utvärderingsmått.

I det här experimentet använder vi två instanser av **utvärdera modell** att jämföra två par av modeller.

Först måste jämför vi två algoritmerna för utbildning-datauppsättningen.
Andra jämför vi två algoritmerna för testning datauppsättningen.
Här följer resultaten:

![Jämför resultatet](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Resultaten visar att modellen som byggts med **förstärkta Regression för beslut** har en lägre rot innebära kvadratfel än modellen som byggts **beslut skog Regression**.

Båda algoritmer har en lägre fel på datamängden för träning än på den överblivna datauppsättningen för testning.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Utforska de andra exempel som är tillgängliga för det visuella gränssnittet:

- [Exempel 1 – Regression: Förutsäga priset på en bil](ui-sample-regression-predict-automobile-price-basic.md)
- [Exempel 3 – klassificering: Förutsäga kreditrisk](ui-sample-classification-predict-credit-risk-basic.md)
- [Exempel 4 - klassificering: Förutsäga kreditrisken (kostnad känsliga)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exempel 5 - klassificering: Förutsäga kundomsättning](ui-sample-classification-predict-churn.md)
- [Exempel 6 - klassificering: Förutsäga flygförseningar](ui-sample-classification-predict-flight-delay.md)
