---
title: LUIS-app samarbete – Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS appar kräver en enda ägaren och valfritt medarbetare.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9acc0b04cbfa074f888d785930f357eaf94f310b
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637331"
---
# <a name="collaborating"></a>Samarbeta

LUIS innehåller samarbete för att tillåta en grupp människor att skapa en app.

## <a name="luis-account"></a>LUIS-konto
En LUIS-konto är kopplat till en enda [Microsoft Live](https://login.live.com/) konto. Varje LUIS-konto får en kostnadsfri [redigering nyckeln](luis-concept-keys.md#authoring-key) ska användas för att redigera alla appar som LUIS kontot har åtkomst till. 

En LUIS-konto kan ha många LUIS-appar.

Se [Azure Active Directory-klientanvändare](luis-how-to-collaborate.md#azure-active-directory-tenant-user) mer information om Active Directory-användarkonton. 

## <a name="luis-app-owner"></a>LUIS appägare
Kontot som skapar en app är ägaren. Varje app har en enda ägare. Ägaren anges på app  **[inställningar](luis-how-to-collaborate.md)**. Det här är det konto som kan ta bort appen. Detta är också det konto som tar emot e-post när slutpunkten kvoten är nådd 75% av den månatliga begränsningen. 

## <a name="authorization-roles"></a>Auktoriseringsroller
LUIS stöder inte olika roller för ägare och medarbetare med ett undantag. Ägaren är det enda kontot som kan ta bort appen.

Om du är intresserad av att kontrollera åtkomst till modellen kan du överväga att uppdelning modellen i mindre LUIS-appar, där varje mindre app har en mer begränsad uppsättning medarbetare. Använd [Dispatch](https://aka.ms/dispatch-tool) så att en förälder LUIS-app för att hantera samordning mellan överordnade och underordnade appar.

## <a name="transfer-ownership"></a>Överför ägarskap
LUIS ger inte överlåtelse av äganderätt, men alla medarbetare kan exportera en app och sedan skapa en app genom att importera den. Tänk på den nya appen har ett annat App-ID. Ny krävs för appen ska vara utbildade, publiceras och den nya slutpunkten som används.

## <a name="luis-app-collaborators"></a>LUIS-app medarbetare
En appägare kan lägga till medarbetare till en app. Ägaren måste lägga till den medarbetarens e-postadress på app  **[inställningar](luis-how-to-collaborate.md)**. Medarbetaren har fullständig åtkomst till appen. Om medarbetaren tar bort appen, appen tas bort från den medarbetarens konto men finns kvar i ägarens konto. 

Om du vill dela flera appar med medarbetare, måste den medarbetarens e-post har lagts till i varje app. 

## <a name="managing-multiple-authors"></a>Hantera flera författare
Den [LUIS](luis-reference-regions.md#luis-website) webbplats omfattas inte för närvarande transaktionsnivå redigering. Du kan tillåta redigerare att arbeta med oberoende versioner från en grundläggande version. Det finns två metoder beskrivs i följande avsnitt.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Hantera flera versioner i samma app
Börja med att [kloning](luis-how-to-manage-versions.md#clone-a-version), från en grundläggande version för varje författare. 

Var och gör ändringar i sin egen version av appen. När var och är nöjd med modellen kan du exportera de nya versionerna till JSON-filer.  

Exporterade appar är JSON-formaterade filer som kan jämföras för ändringar. Kombinera filer för att skapa en JSON-fil för den nya versionen. Ändra den **versionId** egenskap i JSON för att ange den nya sammanfogade versionen att. Importera den här versionen till den ursprungliga appen. 

Den här metoden låter dig ha en aktiv version, en steg-version och en publicerad version. Du kan jämföra resultaten i det interaktiva fönstret för testning mellan tre versioner.

## <a name="manage-multiple-versions-as-apps"></a>Hantera flera versioner som appar
[Exportera](luis-how-to-manage-versions.md#export-version) basversionen. Var och importerar versionen. Den person som importerar appen är ägare av versionen. När de är klar ändra appen, exportera versionen. 

Exporterade appar är JSON-formaterade filer som kan jämföras med den grundläggande exporten av ändringar. Kombinera filer för att skapa en JSON-fil för den nya versionen. Ändra den **versionId** egenskap i JSON för att ange den nya sammanfogade versionen att. Importera den här versionen till den ursprungliga appen.

## <a name="next-steps"></a>Nästa steg

Förstå [versionshantering](luis-concept-version.md) begrepp. 

Se [Appinställningar](luis-how-to-collaborate.md) att lära dig hur du hanterar medarbetare i din LUIS-app.

Se [lägga till e-post till åtkomstlista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) med API: er redigering.