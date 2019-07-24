---
title: 'Snabbstart: Taligenkänning, Unity – Speech Services'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden till att skapa ett tal till text-program med Unity och Speech SDK för Unity (betaversion). När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 425cacb22865e64a996c653477120a5f7f410c9a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405932"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Snabbstart: Taligenkänning med Speech SDK för Unity (betaversion)

Snabb Starter är också tillgängliga för [text till tal](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Använd den här guiden till att skapa ett tal till text-program med hjälp av [Unity](https://unity3d.com/) och Speech SDK för Unity (betaversion).
När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
Om du inte är bekant med Unity rekommenderar vi att du läser [användarhandboken för Unity](https://docs.unity3d.com/Manual/UnityManual.html) innan du börjar med programutvecklingen.

> [!NOTE]
> Speech SDK för Unity är för närvarande i betaversion.
> Det stöder Windows Desktop (x86 och x64) eller Universell Windows-plattform (x86, x64, ARM/ARM64) och Android (x86, ARM32/64).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här projektet behöver du:

* [2018,3 eller senare](https://store.unity.com/) med [Unity 2019,1 lägga till stöd för UWP-arm64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * För ARM64-support installerar du de [valfria build-verktygen för arm64 och Windows 10 SDK för arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)
* En prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).
* Åtkomst till datorns mikrofon.

## <a name="create-a-unity-project"></a>Skapa ett Unity-projekt

* Starta Unity, gå till fliken **Projekt** och välj **Nytt**.
* Ange **Projektnamn** som **csharp-unity**, **Mall** som **3D** och välj en plats.
  Välj sedan **Skapa projekt**.
* Efter en stund bör Unity Editor-fönstret visas.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Speech SDK för Unity (betaversion) packas som ett Unity-resurspaket (.unitypackage).
  Ladda ned det [här](https://aka.ms/csspeech/unitypackage).
* Importera tal Speech SDK genom att välja **Resurser** > **Importera paket** > **Anpassat paket**.
  Mer information finns i [dokumentationen för Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* I filväljaren väljer du den Speech SDK .unitypackage-fil som du laddade ned ovan.
* Se till att alla filer är markerade och klicka på **Importera**:

  ![Skärmbild av Unity Editor vid import av Speech SDK Unity-resurspaketet](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Lägga till användargränssnitt

Vi lägger till ett minimalt användargränssnitt i scenen som består av en knapp för att utlösa taligenkänning och ett textfält för att visa resultatet.

* I [hierarkifönstret](https://docs.unity3d.com/Manual/Hierarchy.html) (som standard finns till vänster) visas en exempelscen som Unity skapade med det nya projektet.
* Klicka på knappen **skapa** längst upp i fönstret hierarki och välj **UI** > -**knappen**.
* Detta skapar tre spelobjekt som visas i hierarkifönstret: ett **knappobjekt** som är kapslat i ett **arbetsyteobjekt** samt ett **EventSystem**-objekt.
* [Navigera i vyn scen](https://docs.unity3d.com/Manual/SceneViewNavigation.html) så att du har en lämplig vy över arbets ytan och knappen i [vyn scen](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Klicka på **knappobjektet** i hierarkifönstret för att visa dess inställningar i fönstret [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspektör, som standard till höger).
* Ange egenskaperna **Pos X** och **Pos Y** till **0** så att knappen centreras i mitten av arbetsytan.
* Klicka på knappen **skapa** överst i hierarkifönstret igen och välj **UI** > -**text** för att skapa ett textfält.
* Klicka på **textobjektet** i hierarkifönstret för att visa dess inställningar i fönstret [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspektör, som standard till höger).
* Ange egenskaperna **Pos X** och **Pos Y** till **0** respektive **120**, och ange egenskaperna **Bredd** och **Höjd** till **240** respektive **120** så att textfältet och knappen inte överlappar.

När du är klar bör användargränssnittet ut ungefär som på den här skärmbilden:

[![Skärmbild av användargränssnittet för snabbstart i Unity Editor](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. I [projektfönstret](https://docs.unity3d.com/Manual/ProjectView.html) (som standard längst ned till vänster) klickar du på knappen **Skapa** och väljer sedan **C#-skript**. Ge skriptet namnet `HelloWorld`.

1. Redigera skriptet genom att dubbelklicka på det.

   > [!NOTE]
   > Du kan konfigurera vilken kodredigerare som startas under **Redigera** > **Inställningar**. Se [användarhandboken för Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Ersätt all kod med följande:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Leta upp och ersätt strängen `YourSubscriptionKey` med din prenumerations nyckel för tal tjänster.

1. Leta upp och ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i skriptet.

1. När du är tillbaka i Unity Editor måste skriptet läggas till som en komponent i ett av spelobjekten.

   * Klicka på **arbetsyteobjektet** i hierarkifönstret. Då öppnas inställningen i [fönstret kontrollant](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
   * Klicka på knappen **Lägg till komponent** i fönstret Inspector (Inspektör) och sök sedan efter det HelloWorld-skript som vi skapade ovan och lägg till det.
   * Observera att Hello World-komponenten har två oinitierade egenskaper, **Output Text** (Utdatatext) och **Start Reco Button** (Knappen Börja spela in), som matchar offentliga egenskaper i klassen `HelloWorld`.
     För att koppla dem klickar du på objektväljaren (den lilla cirkelikonen till höger om egenskapen) och väljer de textobjekt och knappobjekt som du skapade tidigare.

     > [!NOTE]
     > Knappen har även ett kapslat textobjekt. Se till att du inte oavsiktligen väljer det för textutdata (eller byt namn på ett av de textobjekt som använder fältet Namn på i fönstret Inspector (Inspektör) för att undvika en sådan förväxling).

## <a name="run-the-application-in-the-unity-editor"></a>Köra programmet i Unity Editor

* Tryck på knappen **Play** (Spela upp) i verktygsfältet i Unity Editor (under menyraden).

* När appen har startats klickar du på knappen och talar in en fras eller mening på engelska i datorns mikrofon. Ditt tal överförs till tal tjänsterna och skrivs till text, som visas i fönstret.

  [![Skärmbild med den snabbstart som körs i Unity-spelfönstret](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Titta efter felsökningsmeddelanden i [konsolfönstret](https://docs.unity3d.com/Manual/Console.html).

* När du är klar med taligenkänningen klickar du på knappen **Play** (Spela upp) i verktygsfältet i Unity Editor för att stoppa appen.

## <a name="additional-options-to-run-this-application"></a>Ytterligare alternativ för att köra det här programmet

Det här programmet kan även distribueras till Android, som en fristående Windows-app eller som ett UWP-program.
Se vår [exempellagringsplats](https://aka.ms/csspeech/samples) i mappen quickstart/csharp-unity som beskriver konfigurationen för dessa ytterligare mål.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
