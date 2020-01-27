---
title: Livscykel för Team Data Science Process
description: Team Data Science Process (TDSP) innehåller en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt.
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
ms.openlocfilehash: a043a1655950f3ed7688e59352f8a912146e12c9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720460"
---
# <a name="the-team-data-science-process-lifecycle"></a>Livscykel för Team Data Science Process

Team Data Science Process (TDSP) innehåller en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livs cykeln beskriver de fullständiga stegen som slutfört projekt. Om du använder en annan data science-livscykeln, till exempel mellan bransch standardprocessen för datautvinning [(SKARPA-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), Knowledge Discovery i databaser [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), eller din egen process som organisationens egen , du kan fortfarande använda TDSP uppgiftsbaserade. 

Den här livscykeln har utformats för data science-projekt som är avsedda att levereras som en del av intelligenta program. Dessa program distribuera machine learning eller artificiell intelligens modeller för förutsägelseanalys. Undersökande data – vetenskaps projekt och Improvised analys projekt kan också dra nytta av användningen av den här processen. Men för projekt, vissa av stegen som beskrivs här kanske inte behövs. 

## <a name="five-lifecycle-stages"></a>Fem faser i livscykeln

TDSP-livscykeln består av fem viktiga steg som utförs upprepade gånger. Dessa steg omfattar:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Data förvärv och förståelse av](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


TDSP-livscykeln modelleras som en sekvens av resultatuppsättningen kan upprepas steg som ger vägledning om de uppgifter som krävs för att använda förutsägelsemodeller. Du kan distribuera förutsägelsemodeller i produktionsmiljön som du planerar att använda för att skapa intelligenta program. Målet med den här processen livscykeln är att fortsätta att flytta en data science-projekt mot en tydlig engagement slutpunkt. Datavetenskap är en övning i forskning och identifiering. Möjlighet att kommunicera uppgifter så att ditt team och dina kunder med hjälp av en väldefinierad uppsättning artefakter som använder standardiserade mallar hjälper till att texten missförstås. Med hjälp av dessa mallar ökar även risken för ett projekt för komplexa datavetenskap slutförs.

För varje steg erbjuder vi följande information:

   * **Mål**: särskilda mål.
   * **Gör så**: en översikt över specifika uppgifter och anvisningar för hur du utför dem.
   * **Artefakter**: slutprodukterna och stöd för att skapa dem.

## <a name="next-steps"></a>Nästa steg

Vi tillhandahåller fullständig från slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgångar](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
