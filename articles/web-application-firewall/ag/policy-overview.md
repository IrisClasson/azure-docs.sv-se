---
title: Översikt över principer för Azure Web Application-brandvägg (WAF)
description: Den här artikeln är en översikt över globala WAF-, per-plats-och per-URI-principer.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: 10a90a7f94633fac52086953697eb90a98d9509d
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143838"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Översikt över principer för Azure Web Application-brandvägg (WAF)

Brand Väggs principer för webb program innehåller alla WAF-inställningar och konfigurationer. Detta omfattar undantag, anpassade regler, hanterade regler och så vidare. Dessa principer associeras sedan till en Programgateway (global), en lyssnare (per plats) eller en Sök vägs baserad regel (per URI) för att de ska börja gälla.

> [!NOTE]
> Azure Web Application Firewall (WAF) per-URI-principer finns i offentlig för hands version.
> 
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det finns ingen gräns för antalet principer som du kan skapa. När du skapar en princip måste den kopplas till en Programgateway för att börja gälla. Den kan kopplas till valfri kombination av programgatewayer, lyssnare och Sök vägs regler.

## <a name="global-waf-policy"></a>Global WAF-princip

När du associerar en WAF-princip globalt skyddas alla platser bakom din Application Gateway WAF med samma hanterade regler, anpassade regler, undantag och andra konfigurerade inställningar.

Om du vill att en enda princip ska tillämpas på alla platser kan du associera principen med programgatewayen. Mer information finns i [skapa brand Väggs principer för webb program för Application Gateway](create-waf-policy-ag.md) skapa och tillämpa en WAF-princip med hjälp av Azure Portal. 

## <a name="per-site-waf-policy"></a>Princip för WAF per plats

Med WAF-principer per plats kan du skydda flera platser med olika säkerhets behov bakom en enda WAF med hjälp av principer för varje webbplats. Om det till exempel finns fem platser bakom din WAF, kan du ha fem separata WAF-principer (en för varje lyssnare) för att anpassa undantag, anpassade regler, hanterade regel uppsättningar och alla andra WAF inställningar för varje plats.

Anta att din Application Gateway har en global princip som tillämpas på den. Sedan tillämpar du en annan princip på en lyssnare på programgatewayen. Profilens princip gäller nu bara för den lyssnaren. Programgatewayens globala princip gäller fortfarande alla andra lyssnare och Sök vägsbaserade regler som inte har tilldelats någon specifik princip.

## <a name="per-uri-policy"></a>Per URI-princip

För ännu mer anpassning till URI-nivå kan du associera en WAF-princip med en Sök vägs baserad regel. Om det finns vissa sidor på en plats som kräver olika principer, kan du göra ändringar i WAF-principen som endast påverkar en viss URI. Detta kan gälla för en betalnings-eller inloggnings sida eller andra URI: er som behöver en ännu mer speciell WAF-princip än de andra platserna bakom din WAF.

Precis som med WAF-principer per plats åsidosätter mer specifika principer mindre specifika. Det innebär att en per URI-princip på en URL-sökväg mappar åsidosätter alla principer för varje webbplats eller global WAF ovanför.

## <a name="example"></a>Exempel

Anta att du har tre platser: contoso.com, fabrikam.com och adatum.com alla bakom samma Application Gateway. Du vill att en WAF ska tillämpas på alla tre platserna, men du behöver extra säkerhet med adatum.com eftersom det är där kunder besöker, bläddrar och köper produkter.

Du kan använda en global princip för WAF, med vissa grundläggande inställningar, undantag eller anpassade regler om det behövs för att stoppa vissa falska positiva identifieringar från att blockera trafik. I det här fallet behöver du inte ha några globala SQL-inmatnings regler som körs eftersom fabrikam.com och contoso.com är statiska sidor utan SQL-backend. Så du kan inaktivera dessa regler i den globala principen.

Den här globala principen är lämplig för contoso.com och fabrikam.com, men du måste vara mer försiktig med adatum.com där inloggnings information och betalningar hanteras. Du kan tillämpa en princip för varje-plats på Adatum-lyssnaren och lämna SQL-regler som körs. Anta också att en cookie blockerar viss trafik, så att du kan skapa ett undantag för cookien för att stoppa det falska positiva värdet. 

Adatum.com/payments-URI: n är där du måste vara försiktig. Tillämpa en annan princip på denna URI och lämna alla regler aktiverade, och ta även bort alla undantag.

I det här exemplet har du en global princip som gäller för två platser. Du har en princip för varje plats som gäller för en plats och sedan en per-URI-princip som gäller för en viss Sök vägs baserad regel. Se (Infoga länk här när det finns) så här skapar du per-plats-och per-URI-principer för motsvarande PowerShell i det här exemplet.

## <a name="existing-waf-configurations"></a>Befintliga WAF-konfigurationer

Alla nya WAF-inställningar för webb program brand väggen (anpassade regler, konfigurationer för hanterade regel uppsättningar, undantag och så vidare) finns i en WAF-princip. Om du har en befintlig WAF kan de här inställningarna fortfarande finnas i din WAF-konfiguration. Mer information om hur du flyttar till den nya WAF-principen genom [att MIGRERA WAF config till en WAF-princip](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy). 


## <a name="next-steps"></a>Nästa steg

Skapa per-plats-och per-URI-principer med hjälp av Azure PowerShell.
