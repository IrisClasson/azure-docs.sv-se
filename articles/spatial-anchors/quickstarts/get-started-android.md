---
title: Snabbstart – Skapa Android-app med Azure Spatial Anchors | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du skapar en Android-app med hjälp av Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9046af82388f99cfdd82c8cb816d4cda1e5f8237
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206871"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en Android-app med Azure Spatial Anchors

I den här snabbstarten beskrivs hur du skapar en Android-app med hjälp av [Azure Spatial Anchors](../overview.md) i antingen Java eller C++/NDK. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa Mixed Reality-upplevelser med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARCore Android-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera kontoidentifierare och kontonyckel för Spatial Anchors
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows- eller macOS-dator med <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Om körs på Windows kan du också se <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a>.
  - Om du kör på macOS, får du Git installerade via HomeBrew. Ange följande kommando i en enda rad terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Kör sedan `brew install git`.
  - För att skapa NDK-exemplet behöver du även installera NDK- och CMake 3.6 SDK-verktyg i Android Studio.
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
- Din app måste peka på ARCore 1.7.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Om du skapar Android NDK-exemplet behöver du ladda ned `arcore_c_api.h` [härifrån](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.7.0/libraries/include/arcore_c_api.h) och placera den i `Android\NDK\libraries\include`.

Öppna Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Välj **Öppna ett befintligt Android Studio-projekt** och välj det projekt som finns i `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Välj **Öppna ett befintligt Android Studio-projekt** och välj det projekt som finns i `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

Nästa steg är att konfigurera appen att använda ditt konto-ID och kontonyckeln. Du kopierade dem i en textredigerare när [konfigurerar Spatial ankare resursen](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Öppna `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Öppna `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

---

## <a name="deploy-the-app-to-your-android-device"></a>Distribuera appen till din Android-enhet

Slå på Android-enheten, logga in och anslut den till datorn via en USB-kabel.

Välj **Kör** från Android Studio-verktygsfältet.

![Android Studio – distribuera och kör](./media/get-started-android/android-studio-deploy-run.png)

Välj Android-enheten i dialogrutan **Select Deployment Target** (Välj distributionsmål) och välj **OK** för att köra appen på Android-enheten.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

Stoppa appen genom att välja **Stoppa** från Android Studio-verktygsfältet.

![Android Studio – stoppa](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: Dela Spatial Anchors mellan olika enheter](../tutorials/tutorial-share-anchors-across-devices.md)
