---
title: Batch-test – LUIS
titleSuffix: Azure Cognitive Services
description: Den här kursen visar hur du använder batch testning för att hitta uttryck förutsägelse problem i din app och korrigera detta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: 0a3a9330eaa977f72cdbaba4e11aaa706b437fad
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945906"
---
# <a name="tutorial-batch-test-data-sets"></a>Självstudier: Data uppsättningar för batch-test

Den här kursen visar hur du använder batch testning för att hitta uttryck förutsägelse problem i din app och korrigera detta.  

Batch-testning kan du kontrollera aktiva tränas modellens tillstånd med en känd uppsättning av taggade yttranden och entiteter. I JSON-formaterade kommandofilen, Lägg till talade och ange etiketter för entiteten som du behöver förutse inuti uttryck. 

Krav för att testa batch:

* Högst 1000 yttranden per test. 
* Inga dubbletter. 
* Tillåtna entitetstyper: endast enhets belärt entiteter för enkel och sammansatt. Batch testning är endast användbart för bearbetning-lärt dig avsikter och entiteter.

När du använder en app än den här självstudien gör *inte* använder exemplet talade redan lagts till i en avsikt. 

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera en exempelapp
> * Skapa en batchfil för testning 
> * Köra ett batch-test
> * Granskningsresultat
> * Åtgärda fel 
> * Testa om batch

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera en exempelapp

Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Använd följande steg:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `batchtest`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser. 

4. Träna appen.

## <a name="batch-file"></a>Kommandofil

1. Skapa `HumanResources-jobs-batch.json` i en textredigerare eller [hämta](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) den. 

2. Lägg till yttranden med i JSON-formaterade kommandofilen, den **avsikt** du vill att förväntade i testet. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Kör om gruppen

1. Välj **Test** i det övre navigeringsfältet. 

2. Välj **Batch-testning panelen** i den högra panelen. 

    [![Skärmbild av LUIS-app med Batch test panelen markerat](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Välj **importera datauppsättningen**.

    [![Skärmbild av LUIS-app med importera datauppsättningen markerat](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Välj plats för filen för den `HumanResources-jobs-batch.json` filen.

5. Namnge datauppsättningen `intents only` och välj **klar**.

    ![Välj fil](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Klicka på knappen **Kör**. 

7. Välj **se resultat**.

8. Granska resultatet i diagram och legend.

    [![Skärmbild av LUIS-app med testresultaten för batch](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Granska resultatet från batch

Batch-diagrammet visar fyra quadrants resultat. Är ett filter till höger om diagrammet. Filtret är som standard första avsikten i listan. Filtret innehåller alla avsikter och bara enkla och sammansatta entiteter. När du väljer en [delen av diagrammet](luis-concept-batch-test.md#batch-test-results) eller en punkt i diagrammet visas de associerade utterance(s) under diagrammet. 

Vid hovring över diagrammet, ett mushjul förstora eller minska visas i diagrammet. Detta är användbart när det finns många saker i diagrammet klustrade nära tillsammans. 

Diagrammet är i fyra quadrants med två av de avsnitt som visas i rött. **Dessa finns i avsnitt fokuserar på**. 

### <a name="getjobinformation-test-results"></a>GetJobInformation testresultat

Den **GetJobInformation** test visas i filtret visar att 2 av fyra förutsägelser har genomförts. Välj namnet **falsklarm** över övre högra quadrant att se yttranden under diagrammet. 

![LUIS batch test yttranden](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Varför är två av talade förutse som **ApplyForJob**, i stället för rätt avsikten **GetJobInformation**? Två avsikter relaterade mycket nära när det gäller word val och word placering. Det finns dessutom nästan tre gånger så många exempel för **ApplyForJob** än **GetJobInformation**. Den här ojämnheter av exempel yttranden väger **ApplyForJob** avsikts fördel. 

Observera att båda avsikter har samma antal fel. En felaktig förutsägelse i en avsikt påverkar andra avsikten samt. De båda innehåller fel eftersom talade har felaktigt förväntas en avsikt, och även felaktigt inte förväntad för ett annat syfte. 

![LUIS batch test filterfel](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

Tidpunkt för talade motsvarande upp den **falsklarm** avsnittet är `Can I apply for any database jobs with this resume?` och `Can I apply for any database jobs with this resume?`. För den första uttryck ordet `resume` har endast används i **ApplyForJob**. För andra uttryck, ordet `apply` har endast används i den **ApplyForJob** avsikt.

## <a name="fix-the-app"></a>Åtgärda appen

Målet med det här avsnittet är att låta alla talade korrekt förutse för **GetJobInformation** genom att åtgärda appen. 

En till synes snabb korrigering är att lägga till dessa batch filen yttranden till rätt avsikt. Det är inte vad du vill göra om. Vill du LUIS för att förutsäga dessa yttranden utan att lägga till dem som exempel. 

Du kanske också undrar om att ta bort yttranden från **ApplyForJob** tills antalet uttryck är samma som **GetJobInformation**. Som kan åtgärdas testresultaten men skulle hindra LUIS från att förutsäga detta syfte korrekt nästa gång. 

Första korrigeringen är att lägga till fler yttranden till **GetJobInformation**. Andra korrigeringen är att minska vikten av ord som till exempel `resume` och `apply` mot den **ApplyForJob** avsikt. 

### <a name="add-more-utterances"></a>Lägg till mer yttranden

1. Stänga panelen batch test genom att välja den **testa** knappen i övre navigeringsfönstret. 

2. Välj **GetJobInformation** från listan över avsikter. 

3. Lägg till mer yttranden som varieras för längd, word val och word placering, se till att inkludera villkoren `resume`, `c.v.`, och `apply`:

    |Exempel yttranden för **GetJobInformation** avsikt|
    |--|
    |Kräver det nya projektet i lagret för en stocker jag använda med en återuppta?|
    |Var finns takentreprenör jobb idag?|
    |Jag har hört det uppstod ett medicinska kodning jobb som kräver en återuppta.|
    |Jag vill ha ett jobb som hjälper college barn skriva sina c.v.s. |
    |Här är min resume, letar du efter ett nytt inlägg på gymnasium med datorer.|
    |Vilka positioner är tillgängliga i underordnade och hemifrån försiktighet?|
    |Finns det ett intern skrivbord på tidningen?|
    |Min C.v. visar jag är bra på att analysera inköp, budgetar och förlorade pengar. Finns det något för den här typen av arbete?|
    |Där är jorden Detaljgranskning jobb just nu?|
    |Jag har jobbat åtta år som ett EMS-drivrutin. Eventuella nya jobb?|
    |Nya mat hantering av jobb kräver program?|
    |Hur många nya yard arbete jobb är tillgängliga?|
    |Finns det ett nytt HR-inlägg för arbete relationer och förhandlingar?|
    |Jag har en huvudservrar i biblioteket och Arkiv. Alla nya positioner?|
    |Finns det några babysitting jobb för 13 åringarna i staden idag?|

    Inte etikettera den **jobbet** entitet i talade. Det här avsnittet av självstudiekursen fokuserar på avsikt förutsägelse.

4. Träna appen genom att välja **träna** i det övre högra navigeringsfältet.

## <a name="verify-the-new-model"></a>Kontrollera den nya modellen

För att verifiera att yttranden i batch-testet är korrekt förutse, kör du batch-testet igen.

1. Välj **Test** i det övre navigeringsfältet. Om batch-resultat är fortfarande öppen väljer **tillbaka till listan**.  

2. Välj ellipsen (***...*** ) till höger om batch-namn och välj **kör datauppsättning**. Vänta tills det batch-testet är klart. Observera att den **se resultat** är nu grön. Det innebär att hela batchen har körts.

3. Välj **se resultat**. Avsikter ska ha grön ikonerna till vänster om avsiktlig namnen. 

    ![Skärmbild av LUIS med batch resulterar knappen markerad](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>Skapa en batchfil med entiteter 

För att verifiera entiteter i ett batch-test, måste entiteterna ska förses i batch JSON-fil. Endast de enheter som har lärts från enheten används: enkla och sammansatta entiteter. Lägg inte till icke-machine-lärt dig entiteter, eftersom de finns alltid genom reguljära uttryck eller explicit text matchar.

Variationen för entiteter för totalt antal ord ([token](luis-glossary.md#token)) antal kan påverka förutsägelse kvalitet. Kontrollera att träningsdata som angetts för avsikten med märkta yttranden innehåller en mängd längder för entiteten. 

När du först skriva och testa batch-filer, är det bäst att börja med ett par yttranden och entiteter som du vet fungerar, samt några som du kan fundera över förutsägas felaktigt. Det här kan du fokusera på på problemområden snabbt. När du testar den **GetJobInformation** och **ApplyForJob** avsikter med hjälp av flera olika jobbnamn, som inte har förutsade, den här batchfilen test har utarbetats för att se om det finns ett problem för förutsägelse med vissa värden för **jobbet** entitet. 

Värdet för en **jobbet** entiteten i test-uttryck är vanligtvis en eller två ord med några exempel som flera ord. Om _egna_ personalapp har vanligtvis jobbet namnen på många ord, exempel yttranden som är märkt med **jobbet** entitet i den här appen inte skulle fungera bra.

1. Skapa `HumanResources-entities-batch.json` i en textredigerare som [VSCode](https://code.visualstudio.com/) eller [hämta](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) den.


2. I JSON-formaterade batch-filen lägger du till en matris med objekt som innehåller yttranden med den **avsikt** du vill att förväntade i test samt platserna för alla entiteter i uttryck. Eftersom en entitet är tokenbaserad kan du se till att starta och stoppa varje entitet på ett tecken. Inte börja eller sluta uttryck på ett blanksteg. Detta orsakar ett fel under importen för batch-fil.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Kör om gruppen med entiteter

1. Välj **Test** i det övre navigeringsfältet. 

2. Välj **Batch-testning panelen** i den högra panelen. 

3. Välj **importera datauppsättningen**.

4. Välj system sökvägen till den `HumanResources-entities-batch.json` filen.

5. Namnge datauppsättningen `entities` och välj **klar**.

6. Klicka på knappen **Kör**. Vänta tills testet är klart.

7. Välj **se resultat**.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="review-entity-batch-results"></a>Granska batch-enhetsresultat

Diagrammet öppnas med alla avsikter korrekt förutse. Rulla nedåt i det högra filtret för att hitta enheten förutsägelser med fel. 

1. Välj den **jobbet** entitet i filtret.

    ![Fel vid enhets förutsägelser i filter](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Diagrammet ändras för att visa entitet förutsägelser. 

2. Välj **falska negativa** kvar i undre quadrant i diagrammet. Använd sedan tangentbord kombination SKIFT + E byter du till vyn token. 

    [![Token vy över entiteten förutsägelser](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Granska yttranden under diagrammet visar ett konsekvent fel när Jobbnamnet innehåller `SQL`. Granska exempel yttranden och frasen jobblistan kan SQL används endast en gång och endast som en del av ett större jobbnamn `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Åtgärda appen baserat på batch-enhetsresultat

Åtgärda appen kräver LUIS för att korrekt fastställa varianter av SQL-jobb. Det finns flera alternativ för att åtgärda. 

* Uttryckligen lägga till fler exempel yttranden som använder SQL och märka orden som en enhet för jobbet. 
* Uttryckligen lägga till flera SQL-jobb i listan med fraser

Dessa uppgifter finns kvar för dig att göra.

Att lägga till en [mönstret](luis-concept-patterns.md) innan entiteten korrekt förväntas inte kommer att åtgärda problemet. Det beror på att mönstret inte matcha tills alla entiteter i mönstret identifieras. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I självstudiekursen används ett batch-test för att hitta problem med den aktuella modellen. Modellen har åtgärdats och testas med batchfil för att verifiera ändringen var korrekt.

> [!div class="nextstepaction"]
> [Lär dig mer om mönster](luis-tutorial-pattern.md)

