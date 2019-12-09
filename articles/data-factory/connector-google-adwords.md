---
title: Kopiera data från Google AdWords
description: Lär dig hur du kopierar data från Google AdWords till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: b01dcad71747da6b7aa770e3993cb82892ae55fe
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929440"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Kopiera data från Google AdWords med Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Google AdWords. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Google AdWords-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)


Du kan kopiera data från Google AdWords till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, måste du därför inte att manuellt installera en drivrutin som använder den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Google AdWords connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Google AdWords länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **GoogleAdWords** | Ja |
| clientCustomerID | Klient-kund-ID för AdWords-konto som du vill hämta rapportdata för.  | Ja |
| developerToken | Developer token som är associerade med det konto som används för att ge åtkomst till AdWords-API.  Du kan välja att markera det här fältet som en SecureString för att lagra det på ett säkert sätt i ADF eller lagra lösen ord i Azure Key Vault och låta ADF kopierings aktivitet hämta från där när data kopian ska utföras – Läs mer från [lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| authenticationType | OAuth 2.0 autentiseringsmekanism som används för autentisering. ServiceAuthentication kan bara användas på lokal IR. <br/>Tillåtna värden är: **ServiceAuthentication**, **UserAuthentication** | Ja |
| refreshToken | Uppdateringstoken som hämtas från Google för att auktorisera åtkomst till AdWords för UserAuthentication. Du kan välja att markera det här fältet som en SecureString för att lagra det på ett säkert sätt i ADF eller lagra lösen ord i Azure Key Vault och låta ADF kopierings aktivitet hämta från där när data kopian ska utföras – Läs mer från [lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Nej |
| clientId | Klient-ID för det Google-program som används för att hämta uppdateringstoken. Du kan välja att markera det här fältet som en SecureString för att lagra det på ett säkert sätt i ADF eller lagra lösen ord i Azure Key Vault och låta ADF kopierings aktivitet hämta från där när data kopian ska utföras – Läs mer från [lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Nej |
| clientSecret | Klienthemlighet för google-programmet används för att få uppdateringstoken. Du kan välja att markera det här fältet som en SecureString för att lagra det på ett säkert sätt i ADF eller lagra lösen ord i Azure Key Vault och låta ADF kopierings aktivitet hämta från där när data kopian ska utföras – Läs mer från [lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Nej |
| e-post | Den servicekonto e-post-ID som används för ServiceAuthentication och kan bara användas på lokal IR.  | Nej |
| keyFilePath | Den fullständiga sökvägen till den .p12-nyckelfil som används för att autentisera den tjänst e-postadressen och kan bara användas på lokal IR.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till filen .pem som innehåller certifikat från betrodda Certifikatutfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara anges när du använder SSL på lokal IR. Standardvärdet är filen cacerts.pem installerad med i IR.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är FALSKT.  | Nej |

**Exempel:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Google AdWords datauppsättning.

Kopiera data från Google AdWords genom att ange typegenskapen på datauppsättningen till **GoogleAdWordsObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **GoogleAdWordsObject** | Ja |
| tableName | Namnet på tabellen. | Nej (om ”query” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Google AdWords källa.

### <a name="google-adwords-as-source"></a>Google AdWords som källa

För att kopiera data från Google AdWords, ange typ av datakälla i kopieringsaktiviteten till **GoogleAdWordsSource**. Följande egenskaper stöds i kopieringsaktiviteten **source** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **GoogleAdWordsSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
