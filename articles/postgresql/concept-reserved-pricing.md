---
title: Reserverade beräknings priser – Azure Database for PostgreSQL-enskild server
description: Förskotts betalning för Azure Database for PostgreSQL beräknings resurser med reserverad kapacitet
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 9c5d6359ce0e79fce2e80911ff78d19cc88162b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208713"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Förskottsbetala för Azure Database for PostgreSQL-beräknings resurser för en server med reserverad kapacitet

Azure Database for PostgreSQL nu kan du spara pengar genom att betala för beräknings resurser jämfört med priser enligt principen betala per användning. Med Azure Database for PostgreSQL reserverad kapacitet gör du ett åtagande på PostgreSQL Server under en eller tre års period för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa Azure Database for PostgreSQL reserverad kapacitet måste du ange Azure-region, distributions typ, prestanda nivå och period. </br>

Du behöver inte tilldela reservationen till vissa Azure Database for PostgreSQL-servrar. Om du redan kör Azure Database for PostgreSQL (eller som nyligen har distribuerats) får du automatiskt fördelarna med reserverad prissättning. Genom att köpa en reservation är du förbetald för beräknings kostnaderna under en period på en eller tre år. Så snart du köper en reservation debiteras inte längre Azure Database for PostgreSQL-beräknings avgifter som matchar reservations attributen enligt priserna för betala per användning. En reservation omfattar inte program vara, nätverk eller lagrings kostnader som är kopplade till PostgreSQL-databas servrarna. I slutet av reservations perioden upphör fakturerings förmånen och Azure Database for PostgreSQL debiteras enligt priset betala per användning. Reservationer förnyas inte automatiskt. För pris information, se [Azure Database for PostgreSQL reserverat kapacitets erbjudande](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Priset för reserverad kapacitet är tillgängligt för Azure Database for PostgreSQL både i [en enskild server](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) och [citus](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) distributions alternativ. Information om priser för RI om storskalighet (citus) finns på [den här sidan](concepts-hyperscale-reserved-pricing.md).

Du kan köpa Azure Database for PostgreSQL reserverad kapacitet i [Azure Portal](https://portal.azure.com/). Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/monthly-payments-reservations.md). Så här köper du den reserverade kapaciteten:

* Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
* För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa Azure Database for PostgreSQL reserverad kapacitet. </br>

Information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp, se [förstå Azure reservation-användning för företagets registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) och [förstå Azure reservation-användning för din prenumeration enligt principen betala per](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)användning.


## <a name="determine-the-right-server-size-before-purchase"></a>Fastställ rätt server storlek innan köpet

Reservations storleken bör baseras på den totala mängd data som används av befintliga eller närmast distribuerade servrar inom en angiven region och som använder samma prestanda nivå och maskin varu generering.</br>

Anta till exempel att du kör ett allmänt syfte Gen5 – 32 vCore PostgreSQL Database och två minnesoptimerade Gen5 – 16 vCore PostgreSQL-databaser. Vi vill också att du planerar att distribuera inom en och samma månad ytterligare ett allmänt Gen5 – 32 vCore-databasserver och en minnesoptimerade Gen5 – 16 vCore Database Server. Anta att du vet att du kommer att behöva dessa resurser i minst ett år. I det här fallet bör du köpa en 64 (2x32) virtuella kärnor, ett års reservation för en enkel databas generell användning-Gen5 och en 48 (2x16 + 16) vCore ett års reservation för enkel databas minne optimerad-Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Köp Azure Database for PostgreSQL reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj **Lägg till** och sedan i fönstret köp reservationer väljer du **Azure Database for PostgreSQL** för att köpa en ny reservation för dina postgresql-databaser.
4. Fyll i de obligatoriska fälten. Befintliga eller nya databaser som matchar de attribut du väljer kvalificera för att hämta den reserverade kapacitets rabatten. Det faktiska antalet Azure Database for PostgreSQL-servrar som får rabatten beroende på omfattning och vald kvantitet.


![Översikt över reserverade priser](media/concepts-reserved-pricing/postgresql-reserved-price.png)


I följande tabell beskrivs obligatoriska fält.

| Fält | Beskrivning |
| :------------ | :------- |
| Prenumeration   | Prenumerationen som används för att betala för den Azure Database for PostgreSQL reserverade kapacitets reservationen. Betalnings metoden för prenumerationen debiteras mot startkostnader för den Azure Database for PostgreSQL reserverade kapacitets reservationen. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). För en företagsprenumeration dras avgifterna från registreringens återstående åtagandebelopp eller debiteras som överförbrukning. För en enskild prenumeration med priset betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.
| Omfång | VCore-reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: </br></br> **Delat**används reservations rabatten för vCore på Azure Database for PostgreSQL servrar som körs i en prenumeration i din fakturerings kontext. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen. För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat.</br></br> **Enskild prenumeration**tillämpas reservations rabatten för vCore på Azure Database for PostgreSQL servrar i den här prenumerationen. </br></br> **En enda resurs grupp**, reservations rabatten tillämpas på Azure Database for PostgreSQL servrar i den valda prenumerationen och den valda resurs gruppen i den prenumerationen.
| Region | Den Azure-region som omfattas av reservationen Azure Database for PostgreSQL reserverad kapacitet.
| Distributions typ | Den Azure Database for PostgreSQL resurs typ som du vill köpa reservationen för.
| Prestanda nivå | Tjänst nivån för Azure Database for PostgreSQL-servrarna.
| Term | Ett år
| Kvantitet | Mängden data bearbetnings resurser som köps i Azure Database for PostgreSQL reserverade kapacitets reservationen. Antalet är ett antal virtuella kärnor i den valda Azure-regionen och prestanda nivån som reserveras och kommer att få fakturerings rabatten. Om du till exempel kör eller planerar att köra en Azure Database for PostgreSQL-server med den totala beräknings kapaciteten för Gen5 16 virtuella kärnor i regionen USA, östra, skulle du ange kvantitet som 16 för att maximera förmånen för alla servrar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilitet för vCore-storlek

vCore storleks flexibilitet hjälper dig att skala upp eller ned i en prestanda nivå och region, utan att förlora den reserverade kapacitets förmånen. 

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten för vCore tillämpas automatiskt på antalet Azure Database for PostgreSQL servrar som Azure Database for PostgreSQL matchar omfånget och attributen för reserverade kapacitets reservationer. Du kan uppdatera omfånget för Azure Database för PostgreSQL-reserverad kapacitets reservation via Azure Portal, PowerShell, CLI eller via API: et.

Du kan läsa mer om Azure-reservationer i följande artiklar:

* [Vad är Azure reservations](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Hantera Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Förstå reservationsrabatter i Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Förstå reservationsanvändning för din Betala per användning-prenumeration](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Förstå reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)
