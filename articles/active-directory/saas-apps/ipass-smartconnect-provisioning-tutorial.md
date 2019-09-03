---
title: 'Självstudier: Konfigurera iPass-SmartConnect för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 3141a32437e56bcefe830bc94109e93208e0258d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232151"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Självstudier: Konfigurera iPass-SmartConnect för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i iPass SmartConnect och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till iPass SmartConnect.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En IPass SmartConnect-klient](https://www.ipass.com/buy-ipass/).
* Ett användar konto i iPass-SmartConnect med administratörs behörighet.

## <a name="assigning-users-to-ipass-smartconnect"></a>Tilldela användare till iPass-SmartConnect

Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till iPass SmartConnect. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till iPass SmartConnect genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Viktiga tips för att tilldela användare till iPass SmartConnect

* Vi rekommenderar att en enda Azure AD-användare tilldelas iPass SmartConnect för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till iPass SmartConnect måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>Konfigurera iPass SmartConnect för etablering

Innan du konfigurerar iPass-SmartConnect för automatisk användar etablering med Azure AD måste du hämta konfigurations information från administratörs konsolen för iPass-SmartConnect:

1. För att hämta Bearer-token som behövs för att autentisera mot din iPass SmartConnect-SCIM-slutpunkt, se första gången du konfigurerar iPass SmartConnect eftersom det här värdet endast anges. 
2. Om du inte har en Bearer-token kan du kontakta [IPass SmartConnect s support team](mailto:help@ipass.com) för att hämta en ny.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>Lägg till iPass SmartConnect från galleriet

Om du vill konfigurera iPass-SmartConnect för automatisk användar etablering med Azure AD måste du lägga till iPass-SmartConnect från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till iPass-SmartConnect från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **IPass SmartConnect**, väljer **iPass SmartConnect** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![iPass SmartConnect i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Konfigurera automatisk användar etablering till iPass SmartConnect 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i iPass-SmartConnect baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
>  Du kan också välja att aktivera SAML-baserad enkel inloggning för BitaBIZ, genom att följa anvisningarna i självstudien om [IPass SmartConnect enkel inloggning](ipasssmartconnect-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Konfigurera automatisk användar etablering för iPass-SmartConnect i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **IPass SmartConnect**.

    ![IPass SmartConnect-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://openmobile.ipass.com/moservices/scim/v1` i klient- **URL**. Ange Bearer-token som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till iPass SmartConnect. Om anslutningen Miss lyckas kontrollerar du att iPass SmartConnect-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-post för aviseringar](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till iPass SmartConnect**.

    ![iPass SmartConnect användar mappningar](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till iPass SmartConnect i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i IPass-SmartConnect för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![iPass SmartConnect användar mappningar](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för iPass-SmartConnect ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till iPass SmartConnect genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på iPass SmartConnect.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* iPass SmartConnect accepterar bara användar namn som har sina domäner registrerade i administratörs konsolen iPass SmartConnect.  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
