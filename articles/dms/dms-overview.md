---
title: Översikt över Azure Database Migration Service | Microsoft Docs
description: Översikt över Azure Database Migration Service, som ger sömlös migrering från många databaskällor till Azure-Dataplattformar.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 053e571b6285cd405ea17f43fec1d3ea99732070
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235589"
---
# <a name="what-is-the-azure-database-migration-service"></a>Vad är Azure Database Migration Service?
Azure Database Migration Service är en helt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid (online migreringar).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrera databaser till Azure med välbekanta verktyg
Azure Database Migration Service integrerar några av funktionerna i vår befintliga verktyg och tjänster. Tjänsten ger kunderna med en omfattande lösning med hög tillgänglighet. Tjänsten använder den [Data Migration Assistant](https://aka.ms/dma) att generera bedömningsrapporter som ger rekommendationer att guida dig genom de ändringar som krävs innan du utför en migrering. Det är upp till du kan utföra alla åtgärder som krävs. När du är redo att påbörja migreringen, utför alla nödvändiga steg i Azure Database Migration Service. Du kan utlöses och Glöm ditt migreringsprojekt med tryggheten att veta att processen drar nytta av bästa praxis enligt Microsofts bedömning.

> [!NOTE]
> Med hjälp av Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån den affärskritisk (förhandsversion) prisnivå.

## <a name="regional-availability"></a>Regional tillgänglighet
Azure Database Migration Service är nu tillgänglig i följande regioner:

![Azure Database Migration Service regional tillgänglighet](media\overview\dms-regional-availability1.png)

Den senaste informationen om regional tillgänglighet för Azure Database Migration Service, på global infrastruktur med Azure-webbplats, se [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>Nästa steg
- [Skapa en instans av Azure Database Migration Service med hjälp av Azure portal](quickstart-create-data-migration-service-portal.md).
- [Migrera SQLServer till Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
- [Översikt över krav för att använda Azure Database Migration Service](pre-reqs.md).
- [Vanliga frågor och svar om hur du använder Azure Database Migration Service](faq.md).
