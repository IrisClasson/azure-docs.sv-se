---
title: 'Snabbstart: Taligenkänning, Python – Speech Services'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av Speech SDK för Python. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: chlandsi
ms.openlocfilehash: 30e25fa8ea9e49b3005badc3db8f6ada3c0aed50
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65885174"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Snabbstart: Taligenkänning med Speech SDK för Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Den här artikeln visar hur du använder Taltjänster via tal SDK för Python. Den visar hur du kan känna igen tal från mikrofonens indata.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration-nyckel för Speech Services. [Skaffa en kostnadsfritt](get-started.md).
* [Python 3.5 eller senare](https://www.python.org/downloads/).
* Python Speech SDK-paketet är tillgängligt för dessa operativsystem:
    * Windows: x64 och x86.
    * Mac: Mac OS X version 10.12 eller senare.
    * Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9 on x64.
* På Linux, kör du följande kommandon för att installera de nödvändiga paketen:

  * I Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * On Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* På Windows, måste den [Microsoft Visual C++ Redistributable för Visual Studio-2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Det här kommandot installerar Python-paketet från [PyPI](https://pypi.org/) för Speech SDK:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Support och uppdateringar

Uppdateringar av Python Speech SDK-paketet distribueras via PyPI och visas i [Viktig information](./releasenotes.md).
Om en ny version är tillgänglig kan du uppdatera till den med kommandot `pip install --upgrade azure-cognitiveservices-speech`.
Kontrollera vilken version som är installerad genom att granska variabeln `azure.cognitiveservices.speech.__version__`.

Om du har problem eller saknar en funktion kan du ta en titt på [alternativen för support och hjälp](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Skapa ett Python-program som använder Speech SDK

### <a name="run-the-sample"></a>Kör exemplet

Du kan kopiera [exempelkoden](#sample-code) till en källfil från den här snabbstarten `quickstart.py` och köra den i din IDE eller i konsolen:

```sh
python quickstart.py
```

Eller så kan du ladda ned den här snabbstartssjälvstudien som en [Jupyter](https://jupyter.org) Notebook från [exempellagringsplatsen för Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) och köra den som en notebook-fil.

### <a name="sample-code"></a>Exempelkod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installera och använda Speech SDK med Visual Studio Code

1. Ladda ned och installera en 64-bitarsversion (3.5 eller senare) av [Python](https://www.python.org/downloads/) på din dator.
1. Ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Öppna Visual Studio Code och installera Python-tillägget. Välj **Arkiv** > **Inställningar** > **Tillägg** på menyn. Sök efter **Python**.

   ![Installera Python-tillägget](media/sdk/qs-python-vscode-python-extension.png)

1. Skapa en mapp att lagra projektet i. Ett exempel är med hjälp av Windows Explorer.
1. Från Visual Studio Code väljer du ikonen **Arkiv**. Öppna sedan den mapp du skapade.

   ![Öppna en mapp](media/sdk/qs-python-vscode-python-open-folder.png)

1. Skapa en ny Python-källfil `speechsdk.py` genom att välja den nya filikonen.

   ![Skapa en fil](media/sdk/qs-python-vscode-python-newfile.png)

1. Kopiera, klistra in och spara [Python-koden](#sample-code) till den nyskapade filen.
1. Infoga Speech Services prenumerationsinformationen.
1. Om en Python-tolk har valts visas den på statusfältets vänstra sida, längst ner i fönstret.
   Annars kan du ta fram en lista över tillgängliga Python-tolkar. Öppna kommandopaletten (Ctrl + Skift + P) och ange **Python: Välj tolk**. Välj en som passar.
1. Du kan installera Speech SDK Python-paketet i Visual Studio Code. Gör det om det inte är installerat än för Python-tolken som du valt.
   Öppna en terminal för att installera Speech SDK-paketet. Öppna kommandopaletten igen (Ctrl + Skift + P) igen och ange **Terminal: Skapa en ny integrerad terminal**.
   I terminalen som öppnas anger du kommandot `python -m pip install azure-cognitiveservices-speech`, eller lämpligt kommando för ditt system.
1. Kör exempelkoden genom att högerklicka någonstans i redigeraren. Välj **Kör Python-fil i Terminal**.
   Säg några få ord när du uppmanas. Den transkriberade texten visas strax därefter.

   ![Köra ett exempel](media/sdk/qs-python-vscode-python-run.png)

Om det finns problem med att följa dessa anvisningar finns den mer omfattande [självstudien Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://aka.ms/csspeech/samples)
