---
title: Prova Speech Service kostnadsfritt
titleSuffix: Azure Cognitive Services
description: Kom igång med Speech service är enkelt och prisvärt. Det finns två tillgängliga alternativ kostnads fritt så att du kan identifiera vad tjänsten kan göra och bestämma om den passar dina behov.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: d7464d1788101eef7bdad95545056bb32e46c395
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245476"
---
# <a name="try-the-speech-service-for-free"></a>Prova Speech Service kostnadsfritt

I den här artikeln väljer du ett alternativ för att enkelt testa röst tjänsten kostnads fritt så att du kan identifiera vad tjänsten kan göra och bestämma om den passar dina behov. Välj något av de två följande alternativen beroende på din situation och användnings fall:

- [Alternativ 1](#no-card): Hämta omedelbart **utvärderings** -API-nycklar utan att ange någon kreditkorts information (du måste ha ett befintligt Azure-konto). Den **kostnads fria utvärderings versionen** varar 30 dagar och data tas bort i slutet. Det här alternativet är bäst för snabb experimentering med tjänsten.
- [Alternativ 2](#new-resource): skapa en ny tal resurs i Azure, utan kostnad med en **kostnads fri prenumeration** (kreditkorts information krävs). En **kostnads fri prenumeration** har huvudsakligen en snävare pris begränsning än en betald prenumeration. Det här alternativet är bäst om du vill testa tjänsten, men även planera att uppgradera till en betald prenumeration i framtiden och inte vill förlora data.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Prova tjänsten Speech service utan kreditkorts information

Utför följande steg för att aktivera en 30-dagars kostnads fri utvärderings version och hämta API-nycklar. Utvärderings perioden startar omedelbart när följande steg har slutförts.

1. Gå till [testa kognitiva tjänster](https://azure.microsoft.com/free/cognitive-services).
1. Välj fliken **Speech-API: er** .
1. Välj **Hämta API-nyckel**.

Du kommer att visas med fakturerings alternativ. Välj alternativet gratis och Läs och godkänn användar avtalet. Du kommer att visas med nycklar som du kan använda för att prova röst tjänsten kostnads fritt i 30 dagar.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Prova röst tjänsten genom att skapa en Azure-resurs

För följande steg behöver du både ett Microsoft-konto och ett Azure-konto. Om du inte har en Microsoft-konto kan du registrera dig för en kostnads fri avgift på [Microsoft-konto portalen](https://account.microsoft.com/account). Välj **Logga in med Microsoft** och välj sedan **skapa en Microsoft-konto**när du uppmanas att logga in. Följ stegen för att skapa och verifiera din nya Microsoft-konto.

När du har en Microsoft-konto går du till [sidan för Azure-registrering](https://azure.microsoft.com/free/ai/), väljer **Starta kostnads fritt**och skapar ett nytt Azure-konto med hjälp av en Microsoft-konto.

> [!NOTE]
> Tjänsten Speech har två tjänst nivåer: kostnads fri och prenumeration, som har olika begränsningar och fördelar. När du registrerar dig för ett kostnads fritt Azure-konto levereras det med $200 i service kredit som du kan använda mot en betald Speech service-prenumeration, som är giltig i upp till 30 dagar.
>
> Om du använder den kostnads fria, låga tal Service nivån kan du behålla den här kostnads fria prenumerationen även efter att din kostnads fria utvärderings version eller service kredit upphör att gälla.
>
> Mer information finns i [Cognitive Services priss igenkännings tjänst](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Skapa resursen

Så här lägger du till en tjänst resurs (kostnads fri eller betald) till ditt Azure-konto:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Microsoft-konto.

1. Välj **skapa en resurs** längst upp till vänster i portalen. Om du inte ser **skapa en resurs**, kan du alltid hitta den genom att välja menyn komprimerad i det övre vänstra hörnet:

   ![minimerad navigerings knapp](media/index/collapsed-nav.png)

1. Skriv "tal" i rutan Sök i det **nya** fönstret och tryck på RETUR.

1. I Sök resultaten väljer du **tal**.

   ![tal Sök Resultat](media/index/speech-search.png)

1. Välj **skapa**och sedan:

   - Ange ett unikt namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer som är kopplade till samma tjänst.
   - Välj den Azure-prenumeration som den nya resursen är kopplad till för att avgöra hur avgifterna faktureras.
   - Välj den [region](regions.md) där resursen ska användas.
   - Välj antingen en kostnads fri (F0) eller betald (S0) pris nivå. Om du vill ha fullständig information om priser och användnings kvoter för varje nivå väljer du **Visa fullständig pris information**.
   - Skapa en ny resurs grupp för den här tal prenumerationen eller tilldela prenumerationen till en befintlig resurs grupp. Med resurs grupper kan du hålla dina olika Azure-prenumerationer organiserade.
   - Välj **Skapa**. Detta tar dig till distributions översikten och visar meddelanden om distributions förlopp.

> [!NOTE]
> Du kan skapa ett obegränsat antal prenumerationer på standard nivå i en eller flera regioner. Du kan dock bara skapa en prenumeration på den kostnads fria nivån. Modell distributioner på den kostnads fria nivån som fortsätter att vara oanvända i 7 dagar inaktive ras automatiskt.

Det tar en stund att distribuera den nya tal resursen. När distributionen är klar väljer **du gå till resurs** och i det vänstra navigerings fönstret väljer du **nycklar** för att Visa prenumerations nycklar för din röst tjänst. Varje prenumeration har två nycklar. Du kan använda någon av nycklarna i ditt program. Om du snabbt vill kopiera eller klistra in en nyckel i kod redigeraren eller på en annan plats, väljer du kopierings knappen bredvid varje nyckel, växlar fönster för att klistra in innehållet i Urklipp till önskad plats.

> [!IMPORTANT]
> Dessa prenumerations nycklar används för att få åtkomst till ditt kognitiva tjänst-API. Dela inte dina nycklar. Lagra dem på ett säkert sätt – till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar nycklarna regelbundet. Endast en nyckel krävs för att göra ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.

## <a name="switch-to-a-new-subscription"></a>Växla till en ny prenumeration

Om du vill växla från en prenumeration till en annan, till exempel när din kostnads fria utvärderings version upphör att gälla eller när du publicerar programmet, ersätter du region och prenumerations nyckel i koden med region och prenumerations nyckel för den nya Azure-resursen.

## <a name="about-regions"></a>Om regioner

- Om programmet använder ett [tal-SDK](speech-sdk.md)anger du regions-ID: t, till exempel `westus` när du skapar en tal konfiguration.
- Om ditt program använder en av de olika tal tjänstens [REST-API: er](rest-apis.md)är regionen en del av slut punkts-URI: n som du använder när du gör förfrågningar.
- Nycklar som skapats för en region är bara giltiga i den regionen. Om du försöker använda dem med andra regioner resulterar det i autentiseringsfel.

## <a name="next-steps"></a>Nästa steg

Slutför en av våra snabb starter på 10 minuter eller kolla våra SDK-exempel:

> [!div class="nextstepaction"]
> [Snabb start: identifiera tal med tal-CLI](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programmer-tool-spx) 
>  [Exempel för tal-SDK](speech-sdk.md#sample-source-code)
