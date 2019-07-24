---
title: Ändra, träna appen, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här C#-snabbstarten lägger du till exempelyttranden till en app för hemautomatisering och tränar appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: d44877e35687745ddba51d4d015729e62106c9ca
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348450"
---
# <a name="quickstart-change-model-using-c"></a>Snabbstart: Ändra modell med hjälp av C#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Senaste [**Visual Studio Community-versionen**](https://www.visualstudio.com/downloads/).
* Programmeringsspråket C# installerat.
* NuGet-paketen [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) och [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Kod för att skapa snabbstart 

I Visual Studio skapar du en ny **Windows klassisk stationär program konsol** med hjälp av .NET Framework. Namnge projektet `ConsoleApp1`.

![Visual Studio-projekttyp](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Lägga till System.Web-beroendet

Visual Studio-projektet behöver **System.Web**. Högerklicka på **referenser** i Solution Explorer och välj **Lägg till referens** i avsnittet sammansättningar.

![Lägga till System.web-referens](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Lägg till andra beroenden

Visual Studio-projektet behöver **JsonFormatterPlus** och **CommandLineParser**. I Solution Explorer högerklickar du på **Referenser** i väljer **Manage NuGet Packages...** (Hantera NuGet-paket...). Bläddra efter och Lägg till var och en av de två paketen. 

![Lägga till beroenden från tredje part](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Skriva C#-koden
**Program.cs**-filen ska vara:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Uppdatera beroendena så att är:

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Lägg till LUIS-ID:n och strängarna till klassen **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Lägg till klassen för att hantera kommandoradsparametrar till klassen **Program**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Lägg till GET-begärandemetoden till klassen **Program**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Lägg till POST-begärandemetoden till klassen **Program**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Lägg till metoden för exempelyttranden från fil till klassen **Program**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

När ändringarna har tillämpats på modellen tränar du modellen. Lägg till metoden till klassen **Program**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

Träningen slutförs kanske inte omedelbart. Kontrollera status för att verifiera att träningen är klar. Lägg till metoden till klassen **Program**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

För att hantera kommandoradsargumenten lägger du till huvudkoden. Lägg till metoden till klassen **Program**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Kopiera utterances.json till utdatakatalogen

I Solution Explorer lägger du till `utterances.json` genom att högerklicka på Solution Explorer projekt namn och sedan välja **Lägg till**och sedan på **befintligt objekt**. `utterances.json` Välj filen. Detta lägger till filen i projektet. Sedan måste den läggas till i utmatnings riktningen. Högerklicka på och välj egenskaper.  `utterances.json` I fönstret Properties (Egenskaper) märker du **Build Action** (Skapandeåtgärd) för `Content` och **Copy to Output Directory** (Kopiera till utdatakatalog) för `Copy Always`.  

![Märk JSON-filen som innehåll](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Kompilera koden

Kompilera koden i Visual Studio. 

## <a name="run-code"></a>Köra koden

I projektets katalog /bin/Debug kör du programmet från en kommandorad. 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

Den här kommandoraden visar resultatet av anrop av API för att lägga till yttranden. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med snabbstarten tar du bort alla filer som skapas i den här snabbstarten. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en LUIS-app programmässigt](luis-tutorial-node-import-utterances-csv.md) 
