---
title: 'Snabbstart: Extrahera tryckt text (OCR) – REST, C#'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten extraherar du tryckt text från en bild med hjälp av API:et för visuellt innehåll med C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 18a00c2aebd05d2ed2da54e436d245c70bf25051
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604463"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-c"></a>Snabbstart: Extrahera utskrivna text (OCR) med hjälp av den REST API för visuellt innehåll ochC#

I den här snabbstarten extraherar du tryckt text med optisk teckenläsning (OCR) från en bild med hjälp av REST API:et för visuellt innehåll. Med [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)-funktionen kan du identifiera tryckt text i en bild och extrahera de tecken som identifieras till en teckenström som kan användas på en dator.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) eller senare.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnadsfri utvärderingsversion nyckel från [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på visuellt innehåll och få din nyckel.

## <a name="create-and-run-the-sample-application"></a>Skapa och kör exempelappen

Skapa exemplet i Visual Studio enligt följande:

1. Skapa en ny Visual Studio-lösning i Visual Studio med Visual C#-konsolprogrammallen.
1. Installera NuGet-paketet Newtonsoft.Json.
    1. Klicka på **Verktyg** på menyn, välj **NuGet Package Manager** (NuGet-pakethanteraren) och välj sedan **Manage NuGet Packages for Solution** (Hantera NuGet-paket för lösning).
    1. Klicka på fliken **Bläddra** och skriv ”Newtonsoft.Json” i rutan **Sök**.
    1. Välj **Newtonsoft.Json** när det visas och klicka på kryssrutan bredvid namnet på ditt projekt och sedan på **Installera**.
1. Ersätt koden i `Program.cs` med följande kod och gör sedan följande ändringar i koden där det behövs:
    1. Ersätt värdet för `subscriptionKey` med din prenumerationsnyckel.
    1. Ersätt värdet för `uriBase` med slutpunktsadressen för metoden [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) från den Azure-region där du fått dina prenumerationsnycklar om det behövs.
1. Kör programmet.
1. Ange sökvägen till en lokal bild i kommandotolken.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the "westus" region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Optical Character Recognition:");
            Console.Write("Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeOCRRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text visible in the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with printed text.</param>
        static async Task MakeOCRRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. 
                // The language parameter doesn't specify a language, so the 
                // method detects it automatically.
                // The detectOrientation parameter is set to true, so the method detects and
                // and corrects text orientation before detecting text.
                string requestParameters = "language=unk&detectOrientation=true";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Asynchronously get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>Granska svaret

Ett svar som anger att åtgärden lyckades returneras i JSON. Exempelprogrammet parsar och visar ett lyckat svar i konsolfönstret enligt följande exempel:

```json
{
    "language": "en",
    "textAngle": -1.5000000000000335,
    "orientation": "Up",
    "regions": [
        {
            "boundingBox": "154,49,351,575",
            "lines": [
                {
                    "boundingBox": "165,49,340,117",
                    "words": [
                        {
                            "boundingBox": "165,49,63,109",
                            "text": "A"
                        },
                        {
                            "boundingBox": "261,50,244,116",
                            "text": "GOAL"
                        }
                    ]
                },
                {
                    "boundingBox": "165,169,339,93",
                    "words": [
                        {
                            "boundingBox": "165,169,339,93",
                            "text": "WITHOUT"
                        }
                    ]
                },
                {
                    "boundingBox": "159,264,342,117",
                    "words": [
                        {
                            "boundingBox": "159,264,64,110",
                            "text": "A"
                        },
                        {
                            "boundingBox": "255,266,246,115",
                            "text": "PLAN"
                        }
                    ]
                },
                {
                    "boundingBox": "161,384,338,119",
                    "words": [
                        {
                            "boundingBox": "161,384,86,113",
                            "text": "IS"
                        },
                        {
                            "boundingBox": "274,387,225,116",
                            "text": "JUST"
                        }
                    ]
                },
                {
                    "boundingBox": "154,506,341,118",
                    "words": [
                        {
                            "boundingBox": "154,506,62,111",
                            "text": "A"
                        },
                        {
                            "boundingBox": "248,508,247,116",
                            "text": "WISH"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Utforska ett grundläggande Windows-program som använder Visuellt innehåll för att utföra optisk teckenläsning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriv visuella funktioner, inklusive ansikten, i en bild.

> [!div class="nextstepaction"]
> [API för visuellt innehåll med C# – Självstudie](../Tutorials/CSharpTutorial.md)
