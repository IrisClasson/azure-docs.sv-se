---
title: Snabbstart – skapa en Unity iOS-app med Azure Spatial ankare | Microsoft Docs
description: I den här snabbstarten lär du dig att skapa en iOS-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37856c0833ecde1478d4bd588b8e3122e8eac0ca
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135241"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en Unity iOS-app med Azure Spatial ankare

Den här snabbstarten beskrivs hur du skapar en Unity iOS med [Azure Spatial ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARKit iOS-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Förbereda Unity-bygginställningar
> * Ladda ned och importera Unity ARKit-plugin-programmet
> * Konfigurera kontoidentifierare och kontonyckel för Spatial Anchors
> * Exportera Xcode-projektet
> * Distribuera och köra på en iOS-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En macOS-dator med <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a>, <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> och <a href="https://cocoapods.org" target="_blank">CocoaPods</a> installerat.
- Git installerade via HomeBrew. Ange följande kommando i en enda rad terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Kör sedan `brew install git`.
- En utvecklaraktiverad <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibel</a> iOS-enhet.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ned och öppna exempelprojektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Arkiv** -> **Spara**.

## <a name="export-the-xcode-project"></a>Exportera Xcode-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

> [!NOTE]
> Om du inte ser kameran som bakgrund när du kör appen (om du till exempel ser en tom, blå eller annan textur) behöver du förmodligen återimportera tillgångarna i Unity. Stoppa appen. Från den översta menyn i Unity väljer du **Assets -> Re-import all** (Tillgångar -> Återimportera alla). Kör sedan appen igen.

I Xcode stoppar du appen genom att trycka på **Stoppa**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: Dela Spatial Anchors mellan olika enheter](../tutorials/tutorial-share-anchors-across-devices.md)
