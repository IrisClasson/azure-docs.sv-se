---
title: Utveckla och köra Azure functions lokalt | Microsoft Docs
description: Lär dig mer om att koda och testa Azure functions lokalt på datorn innan du kör dem på Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 1f430454bf994f9aac4ad6c113937f3798392319
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492862"
---
# <a name="code-and-test-azure-functions-locally"></a>Koda och testa Azure Functions lokalt

När du använder kan du utveckla och testa Azure Functions i den [Azure Portal], många utvecklare som föredrar en lokal utvecklingsmiljö. Functions gör det enkelt att använda dina favoritverktyg kod redigeraren och utveckling verktyg för att skapa och testa functions lokalt på datorn. Din lokala funktioner kan ansluta levande Azure-tjänster och du kan felsöka dem på din lokala dator med hjälp av den fullständiga Functions-körningen.

## <a name="local-development-environments"></a>Lokal utvecklingsmiljö

Hur som du utvecklar funktioner på din lokala dator beror på din [språk](supported-languages.md) och verktygsinställningar. Miljöer i tabellen nedan har stöd för lokal utveckling:

|Miljö                              |Språk         |Beskrivning|
|-----------------------------------------|------------|---|
| [Kommandotolk eller terminal](functions-run-local.md) | [C# (klassbibliotek)](functions-dotnet-class-library.md), [C#-skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Azure Functions Core Tools] ger core runtime och mallar för att skapa funktioner som gör det möjligt för lokal utveckling. Version 2.x stöder utveckling på Linux, MacOS och Windows. Alla miljöer som förlitar sig på Core Tools för lokal Functions-körningen. |
|[Visual Studio Code](functions-create-first-function-vs-code.md)| [C# (klassbibliotek)](functions-dotnet-class-library.md), [C#-skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | Den [Azure Functions-tillägg för VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) lägger till funktioner stödjer att VS Code. Kräver de viktigaste verktygen. Har stöd för utveckling på Linux, MacOS och Windows, när du använder version 2.x av de viktigaste verktygen. Mer information finns i [skapa din första funktion med Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (klassbibliotek)](functions-dotnet-class-library.md) | Azure Functions-verktygen som ingår i den **Azure development** arbetsbelastningen [Visual Studio 2019](https://www.visualstudio.com/vs/) och senare versioner. Kan du kompilera funktioner i en klassbiblioteket och publicera DLL-filen till Azure. Innehåller de viktigaste verktygen för lokal testning. Mer information finns i [utveckla Azure Functions med Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (olika) | [Java](functions-reference-java.md) | Integreras med Core verktyg för utveckling av Java-funktioner. Version 2.x stöder utveckling på Linux, MacOS och Windows. Mer information finns i [skapa din första funktion med Java och Maven](functions-create-first-java-maven.md). Stöder också snabbare med [Eclipse](functions-create-maven-eclipse.md) och [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Var och en av dessa miljöer för lokal utveckling kan du skapa funktionen app-projekt och använder fördefinierade Functions-mallar för att skapa nya funktioner. Var och en använder de viktigaste verktygen så att du kan testa och felsöka dina funktioner mot verkliga Functions-körningen på din egen dator precis som andra appar. Du kan också publicera du funktionsappsprojekt från någon av dessa miljöer till Azure.  

## <a name="next-steps"></a>Nästa steg

+ Mer information om lokal utveckling av kompilerade C# funktioner med hjälp av Visual Studio-2019 Se [utveckla Azure Functions med Visual Studio](functions-develop-vs.md).
+ Läs mer om lokal utveckling av funktioner som använder VS Code på en Mac, Linux eller Windows-dator i den [VS Code-dokumentation för Azure Functions](https://docs.microsoft.com/azure/azure-functions/tutorial-javascript-vscode-get-started).
+ Mer information om hur du utvecklar funktioner från Kommandotolken eller terminal finns [arbeta med Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
