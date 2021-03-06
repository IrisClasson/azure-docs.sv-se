---
title: Mappa fel söknings läge för data flöde
description: Starta en interaktiv felsökningssession när du skapar data flöden
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 02ec26c80a8a64f88a30ded2067a377c292d621d
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475608"
---
# <a name="mapping-data-flow-debug-mode"></a>Mappa fel söknings läge för data flöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Översikt

Med fel söknings läget Azure Data Factory mappnings data flödet kan du interaktivt titta på DataForm-transformeringen medan du skapar och felsöker dina data flöden. Felsökningssessionen kan användas både i data flödets design sessioner samt vid körning av pipeline-felsökning av data flöden. Om du vill aktivera fel söknings läge använder du knappen "fel sökning av data Flow" överst i design ytan.

![Felsöka skjutreglage](media/data-flow/debugbutton.png "Felsöka skjutreglage")

När du har aktiverat skjutreglaget uppmanas du att välja vilken integration runtime-konfiguration som du vill använda. Om du väljer AutoResolveIntegrationRuntime, kommer ett kluster med åtta kärnor i allmän beräkning med en tid på 60 minuter att leva upp. Mer information om data flödes integrerings körningar finns i [prestanda för data flöde](concepts-data-flow-performance.md#ir).

![Felsök IR-val](media/data-flow/debugbutton2.png "Felsök IR-val")

När fel söknings läget är aktiverat kan du interaktivt bygga ditt data flöde med ett aktivt Spark-kluster. Sessionen stängs när du har aktiverat fel sökning i Azure Data Factory. Du bör vara medveten om de timkostnad som Azure Databricks under tiden som du har aktiverat felsökningssessionen.

I de flesta fall är det en bra idé att skapa dina data flöden i fel söknings läge så att du kan validera affärs logiken och Visa dina data transformationer innan du publicerar arbetet i Azure Data Factory. Använd knappen "Felsök" på pipeline-panelen för att testa ditt data flöde i en pipeline.

## <a name="cluster-status"></a>Klusterstatus

Kluster status indikatorn överst i design ytan blir grön när klustret är klart för fel sökning. Om klustret redan är varmt kommer den gröna indikatorn att visas nästan omedelbart. Om klustret inte redan kördes när du har angett fel söknings läge måste du vänta 5-7 minuter för att klustret ska kunna snurra. Indikatorn roteras tills den är klar.

När du är färdig med fel sökningen aktiverar du fel söknings knappen så att ditt Azure Databricks-kluster kan avslutas och du kommer inte längre att faktureras för fel söknings aktivitet.

## <a name="debug-settings"></a>Fel söknings inställningar

Du kan redigera fel söknings inställningar genom att klicka på "Felsök inställningar" i verktygsfältet data flödes arbets yta. Du kan välja den rad gräns eller fil källa som ska användas för varje käll omvandling här. Rad begränsningarna i den här inställningen gäller bara för den aktuella felsökningssessionen. Du kan också välja den mellanlagrade länkade tjänsten som ska användas för en SQL DW-källa. 

![Fel söknings inställningar](media/data-flow/debug-settings.png "Fel söknings inställningar")

Om du har parametrar i ditt data flöde eller någon av dess refererade data uppsättningar, kan du ange vilka värden som ska användas vid fel sökning genom att välja fliken **parametrar** .

![Parametrar för fel söknings inställningar](media/data-flow/debug-settings2.png "Parametrar för fel söknings inställningar")

## <a name="data-preview"></a>Förhandsgranskning

Vid fel sökning på visas fliken Data förhands granskning på den nedre panelen. Utan fel söknings läge på visar data flödet bara aktuella metadata i och ut ur var och en av dina omvandlingar på fliken Granska. Data förhands granskningen kommer bara att fråga antalet rader som du har angett som gräns i fel söknings inställningarna. Klicka på **Uppdatera** för att hämta data förhands granskningen.

![Förhandsgranskning](media/data-flow/datapreview.png "Förhandsgranskning")

> [!NOTE]
> Fil källor begränsar bara de rader som visas, inte raderna som läses. För mycket stora data mängder rekommenderar vi att du tar en liten del av filen och använder den för testning. Du kan välja en temporär fil i fel söknings inställningar för varje källa som är en fil data uppsättnings typ.

När du kör i fel söknings läge i data flöde kommer dina data inte att skrivas till omvandlingen av mottagare. En felsökningssession är avsedd att fungera som ett test-nät för dina transformeringar. Sinks krävs inte under fel sökning och ignoreras i ditt data flöde. Om du vill testa att skriva data i din mottagare kör du data flödet från en Azure Data Factory pipeline och använder fel söknings körningen från en pipeline.

### <a name="testing-join-conditions"></a>Testa kopplings villkor

Se till att du använder en liten uppsättning kända data för ditt test när du vill testa kopplingar, finns eller söka omvandlingar. Du kan använda alternativet för fel söknings inställningar ovan för att ange en temporär fil som ska användas för testningen. Detta är nödvändigt eftersom när du begränsar eller raderar rader från en stor data uppsättning kan du inte förutsäga vilka rader och vilka nycklar som ska läsas in i flödet för testning. Resultatet är icke-deterministiskt, vilket innebär att anslutnings villkoren inte kan fungera.

### <a name="quick-actions"></a>Snabb åtgärder

När du ser data förhands granskningen kan du generera en snabb omvandling till typecast, ta bort eller göra en ändring i en kolumn. Klicka på kolumn rubriken och välj sedan ett av alternativen i verktygsfältet för förhands granskning.

![Snabb åtgärder](media/data-flow/quick-actions1.png "Snabb åtgärder")

När du har valt en ändring uppdateras data förhands granskningen omedelbart. Klicka på **Bekräfta** i det övre högra hörnet för att generera en ny omvandling.

![Snabb åtgärder](media/data-flow/quick-actions2.png "Snabb åtgärder")

**Typecast** och **ändra** genererar en härledd kolumn omvandling och **tar bort** genererar en SELECT-omvandling.

![Snabb åtgärder](media/data-flow/quick-actions3.png "Snabb åtgärder")

> [!NOTE]
> Om du redigerar ditt data flöde måste du hämta data förhands granskningen igen innan du lägger till en snabb omvandling.

### <a name="data-profiling"></a>Data profilering

Genom att markera en kolumn på fliken Data förhands granskning och klicka på **statistik** i verktygsfältet för förhands granskning visas ett diagram längst till höger i data rutnätet med detaljerad statistik om varje fält. Azure Data Factory görs ett avgörande baserat på data samplingen för vilken typ av diagram som ska visas. Fält med hög kardinalitet kommer att standardvärdet är NULL/NOT NULL-diagram medan kategoriska och numeriska data som har låg kardinalitet visar stapeldiagram med data värdes frekvens. Du kan också se max längd längd på sträng fält, minsta/högsta värden i numeriska fält, standard utveckling, percentiler, antal och genomsnitt.

![Kolumn statistik](media/data-flow/stats.png "Kolumn statistik")

## <a name="next-steps"></a>Nästa steg

* När du är klar med att skapa och felsöka ditt data flöde kan du [köra det från en pipeline.](control-flow-execute-data-flow-activity.md)
* När du testar din pipeline med ett data flöde använder du [alternativet för körning](iterative-development-debugging.md) av pipeline-körnings körning.
