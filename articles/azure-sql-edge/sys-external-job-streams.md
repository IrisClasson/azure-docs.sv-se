---
title: sys. external_job_streams (Transact-SQL) – Azure SQL Edge (för hands version)
description: Lär dig mer om att använda sys. external_job_streams i Azure SQL Edge (för hands version)
keywords: sys. external_job_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 2634128f4d431e4283f59032c6474a71f2af364d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233089"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Returnerar en rad för det indata-eller utdata externa Stream-objektet som har mappats till ett externt strömmande jobb.

|Kolumnnamn|Datatyp|Beskrivning|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Objekt identifierings nummer för objektet strömmande jobb. Den här kolumnen mappas till object_id kolumn i sys. external_streaming_jobs.|
|**stream_id**|**int**| Objekt identifierings nummer för Stream-objektet. Den här kolumnen mappas till object_id kolumn i sys. external_streams. |
|**is_input**|**bit**| 1 om Stream-objektet används som indata för strömnings jobbet, annars 0.|
|**is_output**|**bit**| 1 om Stream-objektet används som utdata för strömnings jobbet, annars 0.|

## <a name="example"></a>Exempel

Den här vyn används tillsammans med `sys.external_streams` och `sys.external_streaming_jobs` katalogvyer. En exempel fråga visas nedan

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Behörigheter

Synligheten för metadata i katalogvyer är begränsad till skydd bara objekt som en användare antingen äger eller som användaren har beviljats behörighet till. Mer information finns i [konfiguration av metadata-synlighet](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Se även

- [Katalogvyer (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Systemvyer (Transact-SQL)](/sql/t-sql/language-reference/)
