---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 220723988f349bf015d2de7633af78782bc03bac
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203239"
---
## <a name="register-your-application"></a>Registrera ditt program

Du kan registrera ditt program på något av två sätt.

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge

Du kan snabbt registrera ditt program genom att göra följande:
1. Gå till [Microsoft-portalen för appregistrering](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Välj **lägga till en app**.

3. I rutan **Programnamn** anger du namnet på programmet.

4. Se till att den **interaktiva installation** kryssrutan är markerad och välj sedan **skapa**.

5. Följ anvisningarna för att hämta program-ID, och klistra in den i din kod.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till programregistreringsinformationen i din lösning genom att göra följande:
1. Om du inte redan har registrerat ditt program går du till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app).

2. Välj **lägga till en app**.

3. I rutan **Programnamn** anger du namnet på programmet.

4. Kontrollera att kryssrutan **Interaktiv installation** är avmarkerad och välj sedan **Skapa**.

5. Välj **Lägg till plattform**, välj **Internt program** och välj sedan **Spara**.

6. I den **program-ID** rutan, kopiera GUID.

7. Gå till Visual Studio, öppna den *App.xaml.cs* filen och Ersätt sedan `your_client_id_here` med program-ID som du just registrerade och kopieras.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
