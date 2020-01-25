---
title: Distribution steg i livscykeln för Team Data Science Process
description: Mål, uppgifter och slutprodukter för distribution steg i dina data science-projekt
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720494"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Distribution steg i livscykeln för Team Data Science Process

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är kopplad till distributionen av Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Förståelse för verksamheten**
   2. **Data förvärv och förståelse av**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Mål
Distribuera modeller med en datapipeline till produktions- eller produktionsliknande miljö för godkännande av slutanvändaren. 

## <a name="how-to-do-it"></a>Gör så
Den huvudsakliga uppgift som beskrivs i det här steget:

**Operationalisera modellen**: Distribuera modellen och pipeline till produktions- eller produktionsliknande miljö för användning av programmet.

### <a name="operationalize-a-model"></a>Operationalisera en modell
När du har en uppsättning modeller som gör kan operationalisera du dem för andra program kan använda. Beroende på företagets krav görs förutsägelser i realtid eller på basis av batch. För att distribuera modeller måste du lägga upp dem med ett öppet API-gränssnitt. Gränssnittet gör det möjligt för modellen för att enkelt förbrukning från olika program, till exempel:

   * Online-webbplatser
   * Kalkylblad 
   * Instrumentpaneler
   * Line-of-business-program 
   * Backend-program 

Exempel på modellen driftsättning i en Azure Machine Learning-webbtjänst finns [distribuera en Azure Machine Learning-webbtjänst](../studio/deploy-a-machine-learning-web-service.md). Det är en bra idé att skapa telemetri och övervakningen i produktion-modellen och datapipeline som du distribuerar. Den här metoden hjälper med efterföljande systemstatus reporting och felsökning.  

## <a name="artifacts"></a>Artifacts

* En status-instrumentpanel som visar systemmått hälso- och nyckel
* En slutlig modellering rapport med distributionsinformation
* Ett dokument för slutlig lösning-arkitektur


## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Data förvärv och förståelse av](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för olika scenarier. Den [exempel genomgångar](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
