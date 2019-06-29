---
title: Visa testresultaten för system och distribution – anpassad Translator
titleSuffix: Azure Cognitive Services
description: När utbildning har genomförts, granskar tester för att analysera dina resultat för utbildning. Om du är nöjd med resultaten utbildning kan du placera en distributionsbegäran för den tränade modellen.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: ec15851ae7ff59a752fbf0d823d87aa6e68f10e9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442484"
---
# <a name="view-system-test-results"></a>Visa testresultat för system

När utbildning har genomförts, granskar tester för att analysera dina resultat för utbildning. Om du är nöjd med resultaten utbildning kan du placera en distributionsbegäran för den tränade modellen.

## <a name="system-test-results-page"></a>Resultatsidan för system-test

Välj ett projekt och sedan välja fliken modeller i projektet, letar du upp den modell som du vill använda och slutligen välja fliken test.

Fliken test visar:

1.  **Testresultat för system:** Resultatet av test-processen i utbildningar. Testa processen genererar BLEU poängen.

    **Antal meningen:** Hur många parallella meningar användes i test-uppsättningen.

     **BLEU poäng:** BLEU poäng genereras för en modell efter slutförande för utbildning.

    **Status:** Anger om test-processen är klar eller pågår.

    ![Testresultat för system](media/how-to/how-to-system-test-results.png)

2.  Klicka på resultaten av System och som tar dig att testa resultatet informationssida. Den här sidan visar maskinöversättning i meningar som ingick i test-datauppsättning.

3.  Tabellen på informationssidan för test-resultatet har två kolumner – en för varje språk i paret. The column for the source language shows the sentence to be translated. Kolumnen för målspråket som innehåller två meningar i varje rad.

    **Ref:** Den här meningen är referens översättningen av källa meningen som anges i test-datauppsättning.

    **MT:** Den här meningen är automatisk översättning av källa meningen utförd av modellen som skapats efter utbildningen utfördes.

    ![Jämfört med system test resultat](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Ladda ned test

Klicka på länken ladda ned översättningar för att ladda ned en zip-fil. ZIP-filen innehåller maskinöversättningar av källa meningar i test-datauppsättningen.

![Ladda ned test](media/how-to/how-to-system-test-download.png)

Det här hämtade zip-arkivet innehåller tre filer.

1.  **custom.mt.txt:** Den här filen innehåller maskinöversättningar källspråk versal i målspråk utförd av modellen tränas med användarens data.

2.  **Ref.txt:** Den här filen innehåller anges översättningar av källspråk meningar i målspråket av användaren.

3.  **Source.txt:** Den här filen innehåller meningar på språket som källa.

    ![Testresultat för hämtade system](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Distribuera en modell

Att begära en distribution:

1.  Välj ett projekt, går till fliken modeller.

2. För en har tränad modell, den visar knappen ”distribuera”, om inte distribuerats.

    ![Distribuera modell](media/how-to/how-to-deploy-model.png)

3.  Klicka på distribuera.
4.  Välj **distribuerat** för geografi är de regioner där du vill att din modell ska distribueras, och klicka på Spara. Du kan välja **distribuerat** för flera regioner.

    ![Distribuera modell](media/how-to/how-to-deploy-model-regions.png)

5.  Du kan visa statusen för din modell i kolumnen ”Status”.

>[!Note]
>Anpassade Translator stöder 10 distribuerade modeller i en arbetsyta när som helst i tid.

## <a name="update-deployment-settings"></a>Uppdatera distributionsinställningar

Så här uppdaterar distributionsinställningar:

1.  Välj ett projekt och gå till den **modeller** fliken.

2. För en modell som har distribuerats, visas en **uppdatering** knappen.

    ![Distribuera modell](media/how-to/how-to-update-undeploy-model.png)

3.  Välj **Uppdatera**.
4.  Välj **distribuerat** eller **Undeployed** för geografi är de regioner där du vill att din modell distribueras eller avdistribuerats, klicka sedan på **spara**.

    ![Distribuera modell](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Om du väljer **Undeployed** för alla regioner modellen avdistribuerats från alla regioner och lägga till ett odistribuerade tillstånd. Det är nu tillgängliga.

## <a name="next-steps"></a>Nästa steg

- Börja använda dina distribuerade anpassade översättningsmodellen via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Lär dig [hur du hanterar inställningar](how-to-manage-settings.md) hantera prenumerationsnyckel för att dela din arbetsyta.
- Lär dig [hur du migrerar din arbetsyta och projekt](how-to-migrate.md) från [Microsoft Translator Hub](https://hub.microsofttranslator.com)
