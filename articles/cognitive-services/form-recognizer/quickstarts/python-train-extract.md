---
title: 'Snabbstart: Träna en modell och extrahera formulärdata med hjälp av REST-API med Python - formuläret Igenkännande'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda formatet Igenkännande REST-API med Python och tränar en modell som du kan extrahera data från formulär.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: pafarley
ms.openlocfilehash: 39ff12a853b38b843a73f4a87a24db0292d1accd
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601606"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Snabbstart: Träna en modell för formuläret Igenkännande och extrahera formulärdata med hjälp av REST-API med Python

I den här snabbstarten ska du använda Azure formuläret Igenkännande REST-API med Python för att träna och betygsätta formulär för att extrahera nyckel / värde-par och tabeller.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här snabbstarten måste du ha:
- Åtkomst till förhandsversionen av formuläret Igenkännande begränsad åtkomst. För att få åtkomst till förhandsversionen kan fylla i och skicka den [formuläret Igenkännande åtkomstbegäran](https://aka.ms/FormRecognizerRequestAccess) formuläret.
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En prenumerationsnyckel för formuläret Igenkännande. Följ instruktionerna i en tjänst prenumeration [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#single-service-subscription) att prenumerera på formuläret Igenkännande och få din nyckel. Använd inte flera tjänster prenumeration, eftersom den inte innehåller formuläret Igenkännande-tjänsten.
- En uppsättning minst fem typer av samma typ. Du kan använda en [exempeldatauppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabbstarten.

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Du skapar och kör exemplet gör dessa ändringar till kodfragmentet nedan:
1. Ersätt `<Endpoint>` med slutpunkts-URL för formuläret Igenkännande resursen i Azure-region där du har fått din prenumerationsnycklar.
1. Ersätt `<SAS URL>` med en Azure Blob storage-behållare som delad åtkomst (signatur) URL: en för platsen för träningsdata.  
1. Ersätt `<Subscription Key>` med din prenumerationsnyckel.
    ```python
    ########### Python Form Recognizer Train #############
    from requests import post as http_post

    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    source = r"<SAS URL>"
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<Subscription Key>',
    }
    url = base_url + "/train" 
    body = {"source": source}
    try:
        resp = http_post(url = url, json = body, headers = headers)
        print("Response status code: %d" % resp.status_code)
        print("Response body: %s" % resp.json())
    except Exception as e:
        print(str(e))
    ```
1. Spara koden i en fil med filnamnstillägget .py. Till exempel *formuläret identifiera train.py*.
1. Öppna ett kommandotolksfönster.
1. Kör exemplet i kommandotolken med kommandot `python`. Till exempel `python form-recognize-train.py`.

Du får en `200 (Success)` svar med den här JSON-utdata:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Obs den `"modelId"` värde. Du behöver den för följande steg.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrahera nyckel / värde-par och tabeller från formulär

Nu ska du analysera ett dokument och extrahera nyckel / värde-par och tabeller från den. Anropa den **modellera – analysera** API genom att köra Python-skriptet som följer. Innan du kör kommandot gör dessa ändringar:

1. Ersätt `<Endpoint>` med slutpunkten som du fick med din prenumerationsnyckel för formuläret Igenkännande. Du hittar den på formuläret Igenkännande resursen **översikt** fliken.
1. Ersätt `<File Path>` med filsökväg eller URL: en för platsen för formuläret som du vill extrahera data från.
1. Ersätt `<modelID>` med modell-ID som du fick i föregående avsnitt.
1. Ersätt `<file type>` med filtypen. Typer som stöds: pdf, bild/jpeg, bild/png.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

    ```python
    ########### Python Form Recognizer Analyze #############
    from requests import post as http_post
    
    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    file_path = r"<File Path>"
    model_id = "<modelID>"
    headers = {
        # Request headers
        'Content-Type': 'application/<file type>',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        url = base_url + "/models/" + model_id + "/analyze" 
        with open(file_path, "rb") as f:
            data_bytes = f.read()  
        resp = http_post(url = url, data = data_bytes, headers = headers)
        print("Response status code: %d" % resp.status_code)    
        print("Response body:\n%s" % resp.json())   
    except Exception as e:
        print(str(e))
    ```

1. Spara koden i en fil med filnamnstillägget .py. Till exempel *formuläret identifiera analyze.py*.
1. Öppna ett kommandotolksfönster.
1. Kör exemplet i kommandotolken med kommandot `python`. Till exempel `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i JSON. Nyckel / värde-par och tabeller som extraheras från formuläret representerar:

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du formuläret Igenkännande REST API med Python du tränar en modell och kör den i ett exempelscenario. Därefter finns i referensdokumentationen för att utforska formuläret Igenkännande API: et i mer detalj.

> [!div class="nextstepaction"]
> [Referensdokumentation för REST API](https://aka.ms/form-recognizer/api)
