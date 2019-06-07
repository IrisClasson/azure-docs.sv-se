---
title: Utveckla appar med tal SDK – Speech Services
titleSuffix: Azure Cognitive Services
description: 'Lär dig skapa appar med SDK: N för tal.'
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: d8330ff663a7adc2f1cae3ed6bdf1cec2fa972c5
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808450"
---
# <a name="ship-an-application"></a>Skicka ett program

Notera den [tal SDK licens](https://aka.ms/csspeech/license201809), samt de [meddelanden om programvara från tredje part](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) när du distribuerar Azure Cognitive Services tal SDK. Läs också den [Microsoft Privacy Statement](https://aka.ms/csspeech/privacy).

Beroende på vilken plattform finns olika beroenden för att köra ditt program.

## <a name="windows"></a>Windows

Cognitive Services tal SDK har testats på Windows 10 och Windows Server 2016.

Cognitive Services tal SDK kräver den [Microsoft Visual C++ Redistributable för Visual Studio-2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) i systemet. Du kan hämta installationsprogram för den senaste versionen av den `Microsoft Visual C++ Redistributable for Visual Studio 2019` här:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Om programmet använder förvaltad kod i `.NET Framework 4.6.1` eller senare krävs på måldatorn.

Media Foundation-bibliotek för mikrofon indata måste vara installerad. Dessa bibliotek är en del av Windows 10 och Windows Server 2016. Det går att använda SDK: N för tal utan dessa bibliotek, så länge en mikrofon inte används som enheten för ljudinspelning.

De tal SDK-filerna som krävs kan distribueras i samma katalog som ditt program. Det här sättet som ditt program kan komma åt direkt biblioteken. Kontrollera att du väljer rätt version (Win32/x64) som matchar ditt program.

| Namn | Funktion
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, som krävs för intern och hanterad distribution
| `Microsoft.CognitiveServices.Speech.csharp.dll` | krävs för hanterad distribution

>[!NOTE]
> Från och med 1.3.0 filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levereras i tidigare versioner) behövs inte längre. Funktionerna är nu integrerat i core SDK.

## <a name="linux"></a>Linux

Tal-SDK stöder för närvarande Ubuntu 16.04 och Ubuntu 18.04 Debian 9-distributioner.
För ett internt program, måste du skicka tal SDK-biblioteket `libMicrosoft.CognitiveServices.Speech.core.so`.
Kontrollera att du väljer den versionen (x86, x64) som matchar ditt program. Beroende på Linux-version kan behöva du också omfattar följande beroenden:

* Delade bibliotek av GNU C-bibliotek (inklusive POSIX trådar Programming-biblioteket `libpthreads`)
* OpenSSL-bibliotek (`libssl.so.1.0.0` eller `libssl.so.1.0.2`)
* Det delade biblioteket för ALSA program (`libasound.so.2`)

I Ubuntu bör GNU C-bibliotek vara installerad som standard. Tre senaste kan installeras med hjälp av följande kommandon:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Installera dessa paket på Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
