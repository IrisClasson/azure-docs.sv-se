---
title: Skapa integrerade lösningar med SQL Data Warehouse | Microsoft Docs
description: 'Verktyg och partner med lösningar som kan integreras med SQL Data Warehouse. '
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 43a714ae175e0d60f20b5e7ad79e1fa90125b0f8
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873345"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Integrera andra tjänster med SQL Data Warehouse
Utöver dess huvudfunktioner kan SQL Data Warehouse du integrera med många av de andra tjänsterna i Azure. Några av de här tjänsterna är:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse fortsätter att integrera med fler tjänster i Azure och mycket mer [integreringspartners](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Power BI-integrering kan du kombinera beräkningskraft i SQL Data Warehouse med dynamiska rapportering och visualisering av Power BI. Power BI-integrering omfattar för närvarande:

* **Direktanslut**: En mer avancerad anslutning med logiska pushdown mot SQL Data Warehouse. Pushdown ger snabbare analys i större skala.
* **Öppna i Powerbi**: Knappen ”Öppna i Power BI-skickar information om processinstans till Power BI för ett förenklat sätt att ansluta.

Mer information finns i [integrera med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), eller [Power BI-dokumentationen](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ger användare en hanterad plattform för att skapa komplexa extrahera och läsa in pipelines. SQL Data Warehouse-integrering med Azure Data Factory omfattar:

* **Lagrade procedurer**: Dirigera körningen av lagrade procedurer i SQL Data Warehouse.
* **Kopiera**: Använd ADF för att flytta data till SQL Data Warehouse. Den här åtgärden kan använda mekanismen för ADF: s standard data movement eller PolyBase under försättsbladen. 

Mer information finns i [integrera med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning är en fullständigt hanterad analystjänst som gör att du kan skapa komplicerade modeller med hjälp av en stor uppsättning förutsägande verktyg. SQL Data Warehouse kan användas som både käll- och mål för dessa modeller med följande funktioner:

* **Läsa Data:** Hantera modeller i hög skala med T-SQL mot SQL Data Warehouse.
* **Skriva Data:** Genomför ändringar från någon modell tillbaka till SQL Data Warehouse.

Mer information finns i [integrera med Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics är en komplex, fullständigt hanterad infrastruktur för bearbetning och förbrukar händelsedata som genereras från Azure Event Hub.  Integrering med SQL Data Warehouse kan strömmande data för att effektivt ska bearbetas och lagras tillsammans med relationsdata som aktiverar djupare, mer avancerade analys.  

* **Jobbutdata:** Skicka utdata från Stream Analytics-jobb direkt till SQL Data Warehouse.

Mer information finns i [integrera med Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


