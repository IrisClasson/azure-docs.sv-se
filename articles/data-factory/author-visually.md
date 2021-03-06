---
title: Visuell redigering
description: Lär dig hur du använder visuell redigering i Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 05/15/2020
ms.openlocfilehash: ac51fd63adcc0328ff67f5fbe13dbfdb768f4bfe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343129"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visuell redigering i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med Azure Data Factory användar gränssnitts upplevelse (UX) kan du visuellt skapa och distribuera resurser för din data fabrik utan att behöva skriva någon kod. Du kan dra aktiviteter till en pipeline-arbetsyta, utföra test körningar, felsöka iterativt och distribuera och övervaka dina pipeline-körningar.

För närvarande stöds Azure Data Factory UX bara i Microsoft Edge och Google Chrome.

## <a name="authoring-canvas"></a>Redigera arbets yta

Klicka på Penn ikonen för att öppna arbets **ytan redigering**. 

![Redigera arbets yta](media/author-visually/authoring-canvas.png)

Här skapar du pipelines, aktiviteter, data uppsättningar, länkade tjänster, data flöden, utlösare och integrerings körningar som utgör din fabrik. För att komma igång med att skapa en pipeline med hjälp av redigerings arbets ytan, se [Kopiera data med kopierings aktiviteten](tutorial-copy-data-portal.md). 

Den förvalda visuella redigerings upplevelsen fungerar direkt med Data Factory tjänsten. Azure databaser git eller GitHub-integration stöds också för att tillåta käll kontroll och samarbete för arbete på dina data Factory-pipeliner. Läs mer om skillnaderna mellan dessa redigerings upplevelser i [käll kontroll i Azure Data Factory](source-control.md).

### <a name="properties-pane"></a>Egenskapsfönster

För resurser på högsta nivå, till exempel pipelines, data uppsättningar och data flöden, kan du redigera egenskaper på hög nivå i rutan egenskaper till höger på arbets ytan. Fönstret Egenskaper innehåller egenskaper som namn, beskrivning, anteckningar och andra övergripande egenskaper. Under resurser som pipeline-aktiviteter och data flödes omvandlingar redige ras med hjälp av panelen längst ned på arbets ytan. 

![Redigera arbets yta](media/author-visually/properties-pane.png)

Fönstret Egenskaper öppnas bara som standard när en resurs skapas. Om du vill redigera det klickar du på ikonen egenskaper som finns i det övre högra hörnet på arbets ytan.

## <a name="management-hub"></a>Hanteringshubb

Hanterings hubben, som nås via fliken *Hantera* i Azure Data Factory UX, är en portal som är värd för globala hanterings åtgärder för din data fabrik. Här kan du hantera dina anslutningar till data lager och externa beräkningar, käll kontroll konfiguration och utlösnings inställningar. Mer information finns i [hanterings hubbens](author-management-hub.md)funktioner.

![Hantera länkade tjänster](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>Uttryck och funktioner

Uttryck och funktioner kan användas i stället för statiska värden för att ange många egenskaper i Azure Data Factory.

Om du vill ange ett uttryck för ett egenskaps värde väljer du **Lägg till dynamiskt innehåll** eller klickar på **Alt + P** samtidigt som du fokuserar på fältet.

![Lägg till dynamiskt innehåll](media/author-visually/dynamic-content-1.png)

Detta öppnar **Data Factory Expression Builder** där du kan bygga uttryck från systemvariabler som stöds, aktivitetens utdata, funktioner och användardefinierade variabler eller parametrar. 

![Uttrycksverktyget](media/author-visually/dynamic-content-2.png)

Information om uttrycks språk finns i [uttryck och funktioner i Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Ge feedback

Välj **feedback** för att kommentera om funktioner eller meddela Microsoft om problem med verktyget:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning och hantering av pipelines finns i [övervaka och hantera pipelines program mässigt](monitor-programmatically.md).
