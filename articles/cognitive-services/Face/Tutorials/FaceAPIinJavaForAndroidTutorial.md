---
title: 'Självstudier: Identifiera och rama in ansikten i en bild med hjälp av Android SDK'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en enkel Android-app som använder Ansiktsigenkänning till att identifiera och rama in ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: 740b3fae81521fec2cba31e3b8fd161f767c4380
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858982"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Självstudier: Skapa en Android-app för att upptäcka och rama in ansikten i en bild

I den här självstudien får du skapa en enkel Android-app som använder Azure ansikts-API, via Java SDK, för att identifiera ansikten i en bild. Appen visar en utvald bild och ritar en ram runt varje identifierat ansikte.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> - Skapa ett Android-program
> - Installera ansikts-API-klientbiblioteket
> - Använda klientbiblioteket för att identifiera ansikten i en bild
> - Rita en ram runt varje identifierat ansikte

![Android-skärmbild av ett foto med ansikten inramade i en röd rektangel](../Images/android_getstarted2.1.PNG)

Den fullständiga koden finns på [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample)-lagringsplatsen på GitHub.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

- En ansikts-API-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på tjänsten Ansikts-API och få din nyckel. Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och tjänst slut punkts strängen, `FACE_SUBSCRIPTION_KEY` med `FACE_ENDPOINT`namnet respektive.
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).
- [Android Studio](https://developer.android.com/studio/) med API-nivå 22 eller senare (krävs av klientbiblioteket för ansiktsigenkänning).

## <a name="create-the-android-studio-project"></a>Skapa Android Studio-projektet

Följ dessa steg för att skapa ett nytt Android-approjekt.

1. Välj **Start a new Android Studio project** (Starta ett nytt Android Studio-projekt) i Android Studio.
1. På skärmen **Create Android Project** (Skapa Android-projekt) ändrar du standardfälten vid behov och klickar sedan på **Next** (Nästa).
1. På skärmen **Target Android Devices** (Android-målenheter) använder du listrutan för att välja **API 22** eller senare och klickar sedan på **Next** (Nästa).
1. Välj **Empty Activity** (Tom aktivitet) och klicka sedan på **Next** (Nästa).
1. Avmarkera **Backwards Compatibility** (Bakåtkompatibilitet) och klicka sedan på **Finish** (Slutför).

## <a name="add-the-initial-code"></a>Lägg till den första koden

### <a name="create-the-ui"></a>Skapa användargränssnittet

Öppna *activity_main.xml*. Välj fliken **Text** i layoutredigeraren och ersätt innehållet med följande kod.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Skapa klassen main

Öppna *MainActivity.java* och ersätt de befintliga `import`-instruktionerna med följande kod.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Ersätt sedan innehållet i klassen **MainActivity** med följande kod. Det skapar en händelsehanterare på **knappen** som startar en ny aktivitet så att användaren kan välja en bild. Den visar bilden i **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Prova appen

Kommentera ut anropet till **detectAndFrame** i metoden **onActivityResult**. Tryck sedan på **Run** (Kör) på menyn för att testa appen. När appen öppnas klickar du i en emulator eller en ansluten enhet på **Browse** (Bläddra) längst ned. Enhetens dialogruta för filval bör visas. Välj en bild och verifiera att den visas i fönstret. Stäng sedan appen och gå till nästa steg.

![Android-skärmbild av ett foto med ansikten](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Lägga till SDK för ansiktsigenkänning

### <a name="add-the-gradle-dependency"></a>Lägga till Gradle-beroende

I fönsterrutan **Project** (Projekt) använder du listrutan för att välja **Android**. Expandera **Gradle Scripts** (Gradle-skript) och öppna sedan *build.gradle (Module: app)* . Lägg till ett beroende för klientbiblioteket för Ansiktsigenkänning, `com.microsoft.projectoxford:face:1.4.3`, enligt skärmbilden nedan, och klicka sedan på **Sync Now** (Synkronisera nu).

![Android Studio-skärmbild på appens build.gradle-fil](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Lägga till ansiktsrelaterad projektkod

Gå tillbaka till **MainActivity.java** och lägg till följande `import`-instruktioner:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Infoga sedan följande kod i klassen **MainActivity**, ovanför metoden **onCreate**:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

I fönsterrutan **Project** (Projekt) expanderar du **app** och sedan **manifests** (manifest) och öppnar *AndroidManifest.xml*. Infoga följande element som ett direkt underordnat element till `manifest`-elementet:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Ladda upp bilden och identifiera ansikten

Din app identifierar ansikten genom att anropa metoden **faceClient. Face. DetectWithStreamAsync** , som omsluter [identifierings](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API och returnerar en lista över **Face** -instanser.

Varje returnerat **ansikte** innehåller en rektangel för att ange dess plats samt en serie med valfria ansiktsattribut. I det här exemplet begärs bara ansiktsrektanglarna.

Infoga följande två metoder i klassen **MainActivity**. Observera att när ansiktsigenkänningen slutförs anropar appen metoden **drawFaceRectanglesOnBitmap** för att ändra **ImageView**. Du definierar den metoden härnäst.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Rita ansiktsrektanglar

Infoga följande hjälpmetod i klassen **MainActivity**. Den här metoden ritar en rektangel runt varje identifierat ansikte, med hjälp av rektangelkoordinater för varje **ansiktsförekomst**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Slutligen tar du bort kommentaren för anropet till metoden **detectAndFrame** i **onActivityResult**.

## <a name="run-the-app"></a>Kör appen

Kör programmet och bläddra efter en bild med ett ansikte. Vänta några sekunder på att Ansiktstjänsten ska svara. Du bör se en röd rektangel kring varje ansikte i bilden.

![Android-skärmbild av ansikten med röda rektanglar ritade runt dem](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig den grundläggande processen för att använda Java SDK för ansikts-API och skapat ett program för att visa och rama in ansikten i en bild. Härnäst får du mer information om ansiktsigenkänning.

> [!div class="nextstepaction"]
> [Så identifierar du ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
