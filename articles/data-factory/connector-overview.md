---
title: Översikt över Azure Data Factory anslutning
description: Lär dig om de anslutningar som stöds i Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 334d5b5113dba17c5abc2b4f2520bde0d16e4c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007459"
---
# <a name="azure-data-factory-connector-overview"></a>Översikt över Azure Data Factory anslutning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory stöder följande data lager och format via kopiera, data flöde, slå upp, hämta metadata och ta bort aktiviteter. Klicka på varje data lager för att lära dig vilka funktioner som stöds och motsvarande konfigurationer i detalj.

## <a name="supported-data-stores"></a>Datalager som stöds

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Filformat som stöds

Azure Data Factory stöder följande fil format. Se varje artikel för formatbaserade inställningar.

- [Avro-format](format-avro.md)
- [Binärt format](format-binary.md)
- [Common Data Model-formatet](format-common-data-model.md)
- [Avgränsat textformat](format-delimited-text.md)
- [Deltaformat](format-delta.md)
- [Excel-format](format-excel.md)
- [JSON-format](format-json.md)
- [ORC-format](format-orc.md)
- [Parquet-format](format-parquet.md)
- [XML-format](format-xml.md)

## <a name="next-steps"></a>Nästa steg

- [Kopierings aktivitet](copy-activity-overview.md)
- [Mappa dataflöden](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)
