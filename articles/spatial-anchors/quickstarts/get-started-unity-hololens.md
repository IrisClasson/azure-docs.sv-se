---
title: 'Snabb start: skapa en HoloLens-app med Unity'
description: I den här snabbstarten lär du dig att skapa en HoloLens-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3d5a5d7ec07587b20a8a6285deafa6215db990a5
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810385"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Snabb start: skapa en enhets-HoloLens-app som använder Azure spatiala ankare

I den här snabb starten skapar du en enhet för enhets-HoloLens som använder [Azures ankare för spatialdata](../overview.md). Spatiala ankare är en plattforms oberoende utvecklare som gör att du kan skapa mixade verklighet med objekt som bevarar sin plats över flera enheter över tid. När du är klar har du en HoloLens-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

- Skapa ett konto för spatial ankare.
- Förbered Unity build-inställningar.
- Konfigurera konto identifieraren för spatiala ankare och konto nyckeln.
- Exportera HoloLens Visual Studio-projektet.
- Distribuera appen och kör den på en HoloLens-enhet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- Du behöver en Windows-dator med <a href="https://unity3d.com/get-unity/download" target="_blank">unity 2019,4 (LTS)</a> och <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> eller senare installerat. Din Visual Studio-installation måste omfatta arbets belastningen **universell Windows-plattform utveckling** och **Windows 10 SDK-komponenten (10.0.18362.0 eller senare)** . Du måste också installera <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a> och <a href="https://git-lfs.github.com/">git LF</a>.
- Du behöver en HoloLens-enhet där [utvecklarläge](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) är aktiverat. [Windows 10 oktober 2018-uppdateringen](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (även kallat RS5) måste vara installerad på enheten. Om du vill uppdatera till den senaste versionen av HoloLens öppnar du appen **Inställningar** , går till **Uppdatera & säkerhet**och väljer sedan **Sök efter uppdateringar**.
- I din app måste du aktivera **SpatialPerception** -funktionen. Den här inställningen finns **Build Settings**i inställningar för  >  **Player Settings**  >  **publicerings inställningar**för inställnings  >  **hantering**i versions spelaren.
- I din app måste du aktivera **virtuell verklighet som stöds** med **Windows Mixed Reality SDK**. Den här inställningen finns **i inställningar för inställningar för**  >  **Player Settings**  >  **XR**inställningar.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ned och öppna exempel projektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Öppna **Build Settings** (Bygginställningar) genom att välja **Arkiv** > **Build Settings** (Bygginställningar).

I avsnittet **Plattform** väljer du **Universell Windows-plattform**. Ändra **mål enheten** till **HoloLens**.

Välj **Switch Platform** (Växla plattform) för att ändra plattformen till **Universell Windows-plattform**. Unity kan uppmana dig att installera UWP support-komponenter om de saknas.

![Fönstret Unit build-inställningar](./media/get-started-unity-hololens/unity-build-settings.png)

Stäng fönstret **Build Settings** (Bygginställningar).

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>Exportera HoloLens Visual Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Välj **build**. I dialog rutan väljer du en mapp där du vill exportera HoloLens Visual Studio-projektet.

När exporten är klar visas en mapp som innehåller det exporterade HoloLens-projektet.

## <a name="deploy-the-hololens-application"></a>Distribuera HoloLens-programmet

I mappen dubbelklickar du på **HEJE U3D. SLN** för att öppna projektet i Visual Studio.

Ändra **lösnings konfigurationen** till **version**, ändra **lösnings plattformen** till **x86**och välj **enhet** från alternativ för distributions mål.

Om du använder HoloLens 2 använder du **arm64** som **lösnings plattform**i stället för **x86**.

   ![Visual Studio-konfiguration](./media/get-started-unity-hololens/visual-studio-configuration.png)

Sätt på HoloLens-enheten, logga in och Anslut enheten till datorn med hjälp av en USB-kabel.

Välj **Felsök**  >  **Starta fel sökning** för att distribuera din app och starta fel sökning.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

Stoppa appen genom att välja **stoppa fel sökning** eller Shift + F5 i Visual Studio.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Gör så här: Konfigurera Azure spatiala ankare i ett Unity-projekt](../how-tos/setup-unity-project.md)
