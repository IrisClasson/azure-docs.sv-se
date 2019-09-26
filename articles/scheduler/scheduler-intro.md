---
title: Vad är Azure Scheduler? | Microsoft Docs
description: Lär dig att skapa, schemalägga och köra automatiska jobb som anropar tjänster inuti eller utanför Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2f418a78f80d65cbb784685804a4cc6790c28b99
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300908"
---
# <a name="what-is-azure-scheduler"></a>Vad är Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) hjälper dig att skapa [jobb](../scheduler/scheduler-concepts-terms.md) som körs i molnet genom att deklarativt beskriva åtgärder. Tjänsten schemalägger och kör sedan dessa åtgärder automatiskt. Du kan till exempel anropa tjänster inuti och utanför Azure, till exempel anropa HTTP- eller HTTPS-slutpunkter, och också publicera meddelanden till Azure Storage-köer och Azure Service Bus-köer eller ämnen. Du kan köra jobb direkt eller vid ett senare tillfälle. Scheduler kan enkelt hantera [komplexa scheman och avancerade upprepningar](../scheduler/scheduler-advanced-complexity.md). Scheduler anger när jobb ska köras och sparar en historik över jobbresultaten. Du kan sedan granska dessa och förutsägbart och säkert schemalägga arbetsbelastningar som ska köras.

Du kan använda Scheduler för att skapa, underhålla och köra schemalagda arbetsbelastningar, men Scheduler är inte värd för arbetsbelastningarna och kör heller inte kod. Tjänsten *anropar* endast tjänster eller kod som finns på en annan plats, till exempel i Azure, lokalt eller hos en annan provider. Scheduler kan anropa via HTTP, HTTPS, en Storage-kö, en Service Bus-kö eller ett Service Bus-ämne. Om du vill skapa, hantera och schemalägga jobb kan du använda [Azure Portal](../scheduler/scheduler-get-started-portal.md), kod, [Scheduler REST API:et](https://docs.microsoft.com/rest/api/scheduler/) eller [referensen för Azure Scheduler PowerShell-cmdletar](scheduler-powershell-reference.md). Du kan till exempel programmässigt skapa, visa, uppdatera, hantera eller radera jobb och [jobbsamlingar](../scheduler/scheduler-concepts-terms.md) med hjälp av skript och i Azure Portal.

Andra schemaläggningsfunktioner i Azure använder också Scheduler i bakgrunden, till exempel [Azure WebJobs](../app-service/webjobs-create.md), som är en [Web Apps](https://azure.microsoft.com/services/app-service/web/)-funktion i Azure App Service. Du kan hantera kommunikationen för dessa åtgärder genom att använda [REST-API:et för Scheduler](https://docs.microsoft.com/rest/api/scheduler/). hjälper till att hantera kommunikationen för dessa åtgärder.

Det här är några scenarier där Scheduler kan hjälpa dig att göra följande:

* **Köra återkommande appåtgärder**: till exempel regelbunden insamling av data från Twitter till ett flöde.

* **Utföra dagligt underhåll**: som att rensa loggar dagligen, utföra säkerhetskopieringar och andra underhållsåtgärder. 

  Som administratör kanske du vill säkerhetskopiera din databas kl 01.00 varje dag under de kommande nio månaderna.

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)
* Läs mer om [planer och fakturering för Azure Scheduler](scheduler-plans-billing.md)
* Läs mer om [hur du kan skapa komplexa scheman och avancerade upprepningsåtgärder med Azure Scheduler](scheduler-advanced-complexity.md)