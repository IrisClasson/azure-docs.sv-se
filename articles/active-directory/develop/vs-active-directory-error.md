---
title: Hur du diagnostiserar fel med Azure Active Directory ansluten tjänst
description: Tjänsten active directory-anslutna upptäckte ett inkompatibelt autentiseringstyp
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ed328b29c853e5ff75d64332f0228277cff90d4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203683"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostisera fel med den Azure Active Directory Connected Service

Vid identifiering av föregående Autentiseringskod, ett Azure Active Directory connect-servern upptäckte ett inkompatibelt autentiseringstyp.

För att korrekt identifiera tidigare Autentiseringskod i ett projekt, skapas projektet.  Om du har stött på det här felet och du inte har en tidigare Autentiseringskod i ditt projekt, återskapa och försök igen.

## <a name="project-types"></a>Projekttyper

Anslutna tjänsten kontrollerar vilken typ av projekt som du utvecklar så att den kan mata in rätt autentiseringslogiken i projektet. Om det inte finns några domänkontrollanter som härleds från `ApiController` i projektet projektet anses vara ett WebAPI-projekt. Om det finns endast de domänkontrollanter som härleds från `MVC.Controller` i projektet projektet anses vara ett MVC-projekt. Anslutna tjänsten stöder inte någon annan projekttyp av.

## <a name="compatible-authentication-code"></a>Kompatibel Autentiseringskod

Tjänsten anslutna söker också efter autentiseringsinställningar som tidigare har konfigurerats eller som är kompatibel med tjänsten. Om alla inställningar finns, anses det Reentrant fall och anslutna tjänsten öppnas visningsinställningar.  Om det bara vissa inställningar, anses en felsituation.

I ett MVC-projekt anslutna tjänsten kontrollerar för någon av följande inställningar, som tidigare användning av tjänsten:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Anslutna tjänsten kontrollerar dessutom för någon av följande inställningar i ett webb-API-projekt som tidigare användning av tjänsten:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Inkompatibla Autentiseringskod

Slutligen försöker anslutna tjänsten identifiera versioner av Autentiseringskod som har konfigurerats med tidigare versioner av Visual Studio. Om du har fått det här felet betyder projektet innehåller ett inkompatibelt autentiseringstyp. Anslutna tjänsten identifierar följande typer av autentisering från tidigare versioner av Visual Studio:

* Windows-autentisering
* Enskilda användarkonton
* Organisationskonton

För att identifiera Windows-autentisering i ett MVC-projekt som den är ansluten som söker efter den `authentication` element i din `web.config` fil.

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

För att identifiera Windows-autentisering i ett webb-API-projekt, anslutna tjänsten söker efter den `IISExpressWindowsAuthentication` element i projektets `.csproj` fil:

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

För att identifiera enskilda användarkonton autentisering, anslutna tjänsten söker efter paket-element i din `packages.config` fil.

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

För att identifiera en gamla form av autentisering av Organisationskonto, anslutna tjänsten ser ut för följande element i`web.config`:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

Om du vill ändra autentiseringstyp, ta bort inkompatibla autentiseringstypen och försök lägga till den anslutna tjänsten igen.

Mer information finns i [Autentiseringsscenarier för Azure AD](authentication-scenarios.md).
