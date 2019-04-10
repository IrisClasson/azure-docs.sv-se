---
title: Så här kräver appens skyddsprincip för åtkomst till molnet appen med villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: Lär dig mer om att kräva appens skyddsprincip för åtkomst till molnet appen med villkorlig åtkomst i Azure Active Directory.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288508"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Instruktioner: Kräv appskyddsprincip för cloud app-åtkomst med villkorlig åtkomst (förhandsversion)

Dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter. Att se till att dina anställda kan vara produktiva, vill du också förhindra förlust av data. Du kan använda villkorlig åtkomst för Azure Active Directory (AD Azure), för att skydda företagets data genom att begränsa åtkomsten till dina molnappar på klientappar som har en appskyddsprincip först.

Det här avsnittet förklarar hur du konfigurerar principer för villkorlig åtkomst som kan kräva appens skyddsprincip innan åtkomst till data.

## <a name="overview"></a>Översikt

Med [Azure AD villkorsstyrd åtkomst](overview.md), du kan finjustera hur behöriga användare kan komma åt dina resurser. Du kan exempelvis begränsa åtkomsten till dina molnappar till betrodda enheter.

Du kan använda [Intunes appskyddsprinciper](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data. Intunes appskyddsprinciper behöver inte lösning för hantering av mobila enheter (MDM), där du kan skydda företagets data eller inte registrerar enheter i en enhetshanteringslösning.

Villkorlig åtkomst i Azure Active Directory begränsar åtkomsten till dina appar i molnet för klientprogram som Intune har rapporterats till Azure AD som tar emot en appskyddsprincip. Du kan exempelvis begränsa åtkomsten till Exchange Online till Outlook-appen som har en Intune-appskyddsprincip.

Med villkorlig åtkomst-terminologin dessa klientappar har visat sig vara princip som skyddas med ett **appskyddsprincip**.  

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)

En lista över princip skyddad klient apps finns i [kravet för app protection](technical-reference.md#approved-client-app-requirement).

Du kan kombinera app-protection-principer för villkorlig åtkomst med andra principer som [principer för enhetsbaserad villkorlig åtkomst](require-managed-devices.md) att skapa flexibilitet i hur du skyddar data för både personliga och företagsägda enheter.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Fördelarna med app protection-baserad villkorlig åtkomst krav

Liknar efterlevnad som rapporteras av Intune för iOS och Android för hanterade enheter, Intune nu rapporter till Azure AD om appskyddsprincipen tillämpas så att villkorlig åtkomst kan använda den som en åtkomstkontroll. Den här nya principen för villkorlig åtkomst **appskyddsprincip** ökar säkerheten genom att skydda mot admin-fel som:

- användare som inte har en Intune-licens
- användare som inte kan ta emot en Intune-appskyddsprincip
- Intune app protection policy appar som inte har konfigurerats för att ta emot en princip


## <a name="before-you-begin"></a>Innan du börjar

Det här avsnittet förutsätter att du är bekant med:

- Den [kravet för app protection](technical-reference.md#app-protection-policy-requirement) Teknisk referens.

- Den [godkända kravet på klienten app](technical-reference.md#approved-client-app-requirement) Teknisk referens.

- Grundläggande begrepp för [villkorlig åtkomst i Azure Active Directory](overview.md).

- Så här [konfigurerar principer för villkorlig åtkomst](app-based-mfa.md).


## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa en princip för skydd-baserad villkorlig åtkomst måste du
- Har en Enterprise Mobility + Security eller Azure Active Directory premium-prenumeration + Intune
- Se till att användarna är licensierade för EMS eller Azure AD och Intune
- Kontrollera att klientappen har konfigurerats i Intune för att ta emot en appskyddsprincip
- Se till att användarna som har konfigurerats i Intune för att ta emot en Intune-appskyddsprincip

## <a name="app-protection-based-policy-for-exchange-online"></a>App protection-baserad princip för Exchange Online

Det här scenariot består av en app protection-baserad villkorlig åtkomstprincip för åtkomst till Exchange Online.

### <a name="scenario-playbook"></a>Scenariot spelbok

Det här scenariot förutsätter att en användare:

- Konfigurerar e-post med en intern e-postprogrammet på iOS eller Android för att ansluta till Exchange

- Tar emot ett e-postmeddelande som anger att åtkomst är bara tillgänglig i Outlook-appen

- Hämtar programmet med länken

- Öppnar Outlook-programmet och loggar in med autentiseringsuppgifter för Azure AD

- Uppmanas att installera Authenticator (iOS) eller Företagsportalen (Android) för att fortsätta

- Installerar program och kan gå tillbaka till Outlook-appen och fortsätt

- Uppmanas att registrera en enhet

- Kan ta emot en Intune-appskyddsprincip

- Komma åt e-post

Alla Intunes appskyddsprinciper måste finnas på programmet åtkomst till företagets data och kan uppmana användaren att starta om programmet, Använd en ytterligare PIN-kod osv (om konfigurerad för programmet och plattformen).

### <a name="configuration"></a>Konfiguration

**Steg 1 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/01.png)

1. Den **namn** av principer för villkorlig åtkomst.

2. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en användare eller grupp valdes.

3. **Appar i molnet:** Som molnappar, måste du välja **Office 365 Exchange Online**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. **Villkor:** Som **villkor**, måste du konfigurera **enhetsplattformar** och **klientappar**:

    a. Som **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

    b. Som **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsappar** och **moderna autentiseringsklienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Som **åtkomstkontroller**, måste du ha **kräver appskyddsprincip (förhandsversion)** valda.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)
 

**Steg 2 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Den **namn** av principer för villkorlig åtkomst.

2. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en användare eller grupp valdes.


3. **Appar i molnet:** Som molnappar, måste du välja **Office 365 Exchange Online**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. **Villkor:** Som **villkor**, måste du konfigurera **klientappar (förhandsgranskning)**. 

    a. Som **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **Exchange ActiveSync-klienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

    b. Som **åtkomstkontroller**, måste du ha **kräver appskyddsprincip (förhandsversion)** valda.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/05.png)


**Steg 3 – Konfigurera Intunes appskyddsprincip för iOS och Android-klientprogram**


![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Se [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) för mer information.



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>App protection-baserade eller kompatibel enhetsprincip för Exchange Online

Det här scenariot består av en app protection-baserade eller kompatibel enhetsprincip för villkorlig åtkomst för åtkomst till Exchange Online.


### <a name="scenario-playbook"></a>Scenariot spelbok

Det här scenariot förutsätter att:
 
- Vissa användare har redan registrerats (med eller utan företagets enheter)

- Användare som inte har registrerats i och registrerad med Azure AD med en app skyddade program behöver registrera en enhet för att få åtkomst till resurser

- Registrerade användare med hjälp av app som skyddas programmet behöver inte registrera enheten

- Användaren kan ta emot en appskyddsprincip i Intune om det inte har registrerats

- Användare kan komma åt e-postmeddelande med Outlook och en appskyddsprincip i Intune om det inte har registrerats

- Användaren har åtkomst till e-postmeddelande med Outlook om enheten har registrerats


### <a name="configuration"></a>Konfiguration

**Steg 1 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/62.png)

1. Den **namn** av principer för villkorlig åtkomst.

2. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en användare eller grupp valdes.

3. **Appar i molnet:** Som molnappar, måste du välja **Office 365 Exchange Online**. 

     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. **Villkor:** Som **villkor**, måste du konfigurera **enhetsplattformar** och **klientappar**. 
 
    a. Som **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

    b. Som **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **moderna autentiseringsklienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Som **åtkomstkontroller**, måste du ha följande valda:

   - **Kräv att enheten är markerad som kompatibel**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Begär en av de valda kontrollerna**   
 
     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/11.png)



**Steg 2 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Den **namn** av principer för villkorlig åtkomst.

2. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en användare eller grupp valdes.

3. **Appar i molnet:** Som molnappar, måste du välja **Office 365 Exchange Online**. 

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. **Villkor:** Som **villkor**, måste du konfigurera **klientappar**. 

    Som **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **Exchange ActiveSync-klienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

5. Som **åtkomstkontroller**, måste du ha följande valda:

   - **Kräv att enheten är markerad som kompatibel**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Begär en av de valda kontrollerna**   
    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/11.png)



**Steg 3 – Konfigurera Intunes appskyddsprincip för iOS och Android-klientprogram**


![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Se [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) för mer information.





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>App protection-baserade och kompatibel enhetsprincip för Exchange Online

Det här scenariot består av en princip för villkorlig åtkomst för app-protection-baserade och kompatibla enheter för åtkomst till Exchange Online.


### <a name="scenario-playbook"></a>Scenariot spelbok

Det här scenariot förutsätter att en användare:
 
-   Konfigurerar e-post med en intern e-postprogrammet på iOS eller Android för att ansluta till Exchange
-   Tar emot ett e-postmeddelande som anger att åtkomst kräver att enheten registreras
-   Hämtar Företagsportalen och loggar in på Företagsportalen
-   Kontrollerar e-post och uppmanas att använda Outlook-appen
-   Hämtar Outlook-appen
-   Öppnar Outlook-appen och anger de autentiseringsuppgifter som används i registreringen
-   Kan ta emot för att ta emot en Intune-appskyddsprincip
-   Komma åt e-postmeddelande med Outlook och en Intune-appskyddsprincip

Alla Intunes appskyddsprinciper aktiveras innan åtkomst till företagets data och kan uppmana användaren att starta om programmet, Använd en ytterligare PIN-kod osv (om konfigurerad för programmet och plattform)


### <a name="configuration"></a>Konfiguration

**Steg 1 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/01.png)

1. Den **namn** av principer för villkorlig åtkomst.

2. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en användare eller grupp valdes.

3. **Appar i molnet:** Som molnappar, måste du välja **Office 365 Exchange Online**. 

     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. **Villkor:** Som **villkor**, måste du konfigurera **enhetsplattformar** och **klientappar**. 
 
    a. Som **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

    b. Som **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsappar** och **moderna autentiseringsklienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Som **åtkomstkontroller**, måste du ha följande valda:

   - **Kräv att enheten är markerad som kompatibel**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Begär alla de valda kontrollerna**   
 
     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/13.png)



**Steg 2 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med Active Sync (EAS)**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/06.png)

1. Den **namn** av principer för villkorlig åtkomst.

2. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en användare eller grupp valdes.

3. **Appar i molnet:** Som molnappar, måste du välja **Office 365 Exchange Online**. 

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/07.png)

4. **Villkor:** Som **villkor**, måste du konfigurera **klientappar (förhandsgranskning)**. 

    Som **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsklienter** och **Exchange ActiveSync-klienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/92.png)

5. Som **åtkomstkontroller**, måste du ha följande valda:

   - **Kräv att enheten är markerad som kompatibel**

   - **Kräv godkänd klientapp (förhandsversion)**

   - **Begär alla de valda kontrollerna**   
 
     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/13.png)




**Steg 3 – Konfigurera Intunes appskyddsprincip för iOS och Android-klientprogram**


![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Se [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) för mer information.


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>App-protection-baserade eller appbaserad princip för Exchange Online och SharePoint Online

Det här scenariot består av en princip för åtkomst till Exchange Online och SharePoint Online protection-baserade eller godkända appar.


### <a name="scenario-playbook"></a>Scenariot spelbok

Det här scenariot förutsätter att en användare:

- Konfigurerar antingen klientprogram som är på listan över appar som har stöd för kravet för app protection eller kravet på godkända appar.  
- Användare användarprogram som uppfyller kravet för app protection kan ta emot en Intune-appskyddsprincip
- Användare använder klientprogram som uppfyller kravet för godkända appar som har stöd för Intunes appskyddsprincip
- Öppnar programmet för att få åtkomst till e-postmeddelande eller dokument
- Öppnar Outlook-programmet och loggar in med autentiseringsuppgifter för Azure AD
- Du uppmanas att installera Authenticator (iOS) eller Företagsportalen (Android) för att fortsätta (om inte installerat)
- Installerar program och kan gå tillbaka till Outlook-appen och fortsätt
- Uppmanas att registrera en enhet
- Kan ta emot en Intune-appskyddsprincip
- Komma åt e-postmeddelande med Outlook och en Intune-appskyddsprincip
- Är komma åt webbplatser/dokument med en app inte på kravet för app protection men finns i kravet på godkänd app.

Alla Intunes appskyddsprinciper krävs innan åtkomst till företagets data och kan uppmana användaren att starta om programmet, Använd en ytterligare PIN-kod osv (om konfigurerad för programmet och plattform)

**Kommentarer**

- Du kan använda det här scenariot, om du vill stödja båda principerna för app-protection-baserade och appbaserad villkorlig åtkomst.

- I det här *eller* princip, appar med **appskyddsprincip** utvärderas krav för åtkomst innan **godkända klientappar** krav.

### <a name="configuration"></a>Konfiguration

**Steg 1 – konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online**

För principen för villkorlig åtkomst i det här steget måste du konfigurera följande komponenter:

![Villkorlig åtkomst](./media/app-protection-based-conditional-access/62.png)

1. Den **namn** av principer för villkorlig åtkomst.

2. **Användare och grupper**: Varje princip för villkorlig åtkomst måste ha minst en användare eller grupp valdes.

3. **Appar i molnet:** Som molnappar, måste du välja **Office 365 Exchange Online**. 

     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/02.png)

4. **Villkor:** Som **villkor**, måste du konfigurera **enhetsplattformar** och **klientappar**. 
 
    a. Som **enhetsplattformar**väljer **Android** och **iOS**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/03.png)

    b. Som **klientappar (förhandsgranskning)** väljer **mobilappar och skrivbordsappar** och **moderna autentiseringsklienter**.

    ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/91.png)

5. Som **åtkomstkontroller**, måste du ha följande valda:

   - **Kräv godkänd klientapp**

   - **Kräv appskyddsprincip (förhandsversion)**

   - **Begär en av de valda kontrollerna**   
 
     ![Villkorlig åtkomst](./media/app-protection-based-conditional-access/12.png)


**Steg 2 – konfigurera Intunes appskyddsprincip för iOS och Android-klientprogram**


![Villkorlig åtkomst](./media/app-protection-based-conditional-access/09.png)

Se [skydda data och appar med Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) för mer information.




## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md).

Om du är redo att konfigurera principer för villkorsstyrd åtkomst för din miljö kan du läsa sidan om [metodtips för villkorsstyrd åtkomst i Azure Active Directory](best-practices.md). 