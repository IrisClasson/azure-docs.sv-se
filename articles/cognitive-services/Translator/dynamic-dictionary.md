---
title: Dynamisk ordlista - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Hur du använder funktionen dynamiskt ordlista i Translator Text API.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 72c23270d6e8cdef55f07d0b702fd1858a7710f8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389558"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Hur du använder funktionen dynamiskt ordlista i Translator Text API

Om du redan vet översättning som du vill använda på ett ord eller en fras kan ange du den som markup i begäran. Dynamisk ordlistan är endast säkra för sammansatt substantiv som egennamn och produktnamn.

**Syntax:**

< mstrans:dictionary translation = ”översättning av frasen” > frasen < / mstrans:dictionary >

**Exempel: en-de:**

Källa: Ordet < mstrans:dictionary translation =\"wordomatic\"> ord eller fraser < / mstrans:dictionary > är en ordlista-post.

Utdata: DAS med en ”wordomatic” ist ein Wörterbucheintrag.

Den här funktionen fungerar på samma sätt med och utan HTML-läge.

Funktionen bör användas sparsamt. Det lämpliga och mycket bättre sättet att anpassa translation är med hjälp av anpassade Translator. Anpassade Translator blir fullt ut utnyttja kontext och statistiska sannolikhet. Om du har eller kan skapa träningsdata som visar ditt arbete eller en fras i sammanhanget, får mycket bättre resultat. Du hittar mer information om anpassade Translator på [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
