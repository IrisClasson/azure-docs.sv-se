---
title: Konfigurera Remote Rendering för Unity
description: Så här initierar du Azure Remote rendering i ett Unity-projekt
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: f3400d82a6aa184daabfa2ebbe6b775b8e4c1562
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565455"
---
# <a name="set-up-remote-rendering-for-unity"></a>Konfigurera Remote Rendering för Unity

För att aktivera Azure Remote rendering (ARR) i Unity tillhandahåller vi dedikerade metoder som tar hand om vissa delar av Unit-särskilda aspekter.

## <a name="startup-and-shutdown"></a>Start och avstängning

Använd för att initiera fjärrrendering `RemoteManagerUnity` . Den här klassen anropar generiskt `RemoteManager` men implementerar redan uppdelad information åt dig. Enhets systemet använder till exempel ett angivet koordinatsystem. När du anropar `RemoteManagerUnity.Initialize` kommer rätt konvention att konfigureras. Anropet kräver också att du tillhandahåller den Unity-kamera som ska användas för att visa det fjärranslutna innehållet.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Anropa för att stänga av fjärrrendering `RemoteManagerStatic.ShutdownRemoteRendering()` .

När en `AzureSession` har skapats och valts som den primära åter givnings sessionen måste den registreras med `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Fullständig exempel kod

Koden nedan visar alla steg som krävs för att initiera Azure-fjärrrendering i Unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Bekvämlighets funktioner

### <a name="session-state-events"></a>Händelser i sessionstillstånd

`RemoteManagerUnity.OnSessionUpdate`genererar händelser när dess sessionstillstånd ändras, se kod dokumentationen för mer information.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`är en valfri komponent för att effektivisera installationen och sessionen. Den innehåller alternativ för att stoppa sessionen automatiskt när programmet avslutas eller uppspelnings läget avslutas i redigeraren, samt automatiskt förnya sessionens lån vid behov. Den cachelagrar data, till exempel sessionens egenskaper (se dess `LastProperties` variabel) och exponerar händelser för ändringar i sessionstillstånd och sessions fel.

Det får inte finnas mer än en instans av `ARRServiceUnity` i taget. Den är avsedd för att komma igång snabbare genom att implementera några vanliga funktioner. För ett större program kan det vara bättre att göra dessa saker själv, även om.

Ett exempel på hur du konfigurerar och använder `ARRServiceUnity` finns i [Självstudier: Visa fjärranslutna modeller](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Nästa steg

* [Installera Remote Rendering-paketet för Unity](install-remote-rendering-unity-package.md)
* [Självstudie: Visa fjärranslutna modeller](../../tutorials/unity/view-remote-models/view-remote-models.md)
