---
title: Föråldrad kognitiva funktioner – Azure Search
description: Den här sidan innehåller en lista över kognitiv sökning färdigheter som betraktas som inaktuella och stöds inte inom en snar framtid.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a73c7e381cb6001b773251a1812466b3c82373f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541743"
---
# <a name="deprecated-cognitive-search-skills"></a>Föråldrad kognitiv sökning kunskaper

Det här dokumentet beskriver kognitiva kunskaper som betraktas som inaktuella. Använd följande guide för innehållet:

* Kunskapsnamn på: Namnet på den färdighet upphör att gälla, det mappas till den @odata.type attribut.
* Senaste tillgängliga api-version: Den senaste versionen av Azure Sök offentliga API: N via vilken kompetens som innehåller motsvarande föråldrad färdighet kan vara skapats/uppdaterats.
* Support upphör: Den sista dagen efter vilka motsvarande färdighet anses vara stöds inte. Skapat kompetens bör fortfarande att fungera, men användare rekommenderas för att migrera från en föråldrad färdighet.
* Rekommendationer: Migreringsvägen fram emot att använda en färdighet som stöds. Användare bör följa rekommendationerna för att fortsätta att få support.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Senaste tillgängliga api-version

2019-05-06-förhandsversion

### <a name="end-of-support"></a>Support upphör

Den 15 februari 2019

### <a name="recommendations"></a>Rekommendationer 

Använd [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) i stället. Den innehåller de flesta av funktionerna i NamedEntityRecognitionSkill med högre kvalitet. Det har även mer omfattande information i dess komplexa utdata-fält.

Att migrera till den [entitet erkännande färdighet](cognitive-search-skill-entity-recognition.md), måste du utföra en eller flera av följande ändringar till din kompetens-definition. Du kan uppdatera en färdighet definition med hjälp av den [uppdatera kompetens API](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Förtroendepoäng som ett begrepp som stöds för närvarande inte. Den `minimumPrecision` parametern finns på den `EntityRecognitionSkill` för framtida användning och för bakåtkompatibilitet kompatibilitet.

1. *(Krävs)*  Ändra den `@odata.type` från `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` till `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Valfritt)*  Om du gör användning av den `entities` utdata, Använd den `namedEntities` komplex samling utdata från den `EntityRecognitionSkill` i stället. Du kan använda den `targetName` i kompetensen definitionen för att mappa den till en anteckning med namnet `entities`.

3. *(Valfritt)*  Om du inte uttryckligen anger den `categories`, `EntityRecognitionSkill` kan returnera olika slags kategorier än de som stöddes av den `NamedEntityRecognitionSkill`. Om det här beteendet är oönskade, se till att uttryckligen ställa in den `categories` parameter `["Person", "Location", "Organization"]`.

    _Exemplet migrering definitioner_

    * Enkel migrering

        _(Före) NamedEntityRecognition färdighet definition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Efter) EntityRecognition färdighet definition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Lite komplicerat migrering

        _(Före) NamedEntityRecognition färdighet definition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Efter) EntityRecognition färdighet definition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Entiteten erkännande färdighet](cognitive-search-skill-entity-recognition.md)
