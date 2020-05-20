---
title: 'Självstudie: Konfigurera Juno-resan för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Juno resan.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 41a6cc3a4fd604c4272783f528ed3d37372044fb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647738"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Juno-resan för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Juno-resan och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS, etablerar och avetablerar Azure AD automatiskt användare och grupper i [Juno resan](https://www.junojourney.com/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Juno resan
> * Ta bort användare i Juno-resan när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Juno resan
> * [Enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) till Juno-resa (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användar konto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t. ex. program administratör, moln program administratör, program ägare eller global administratör). 
*  En [Juno resan-klient](https://www.junojourney.com/blank).
*  Ett användar konto i Juno-resan med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablerings distributionen
1. Läs om [hur etablerings tjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som kommer att vara inom [omfånget för etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bestäm vilka data som ska [mappas mellan Azure AD och Juno resan](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Juno-resan för att ge stöd för etablering med Azure AD

1. För den **hemliga token** och **klient webb adressen** kontaktar du support teamet för Juno resan på support@the-juno.com . Det här värdet anges i fälten för **hemliga token** och **klient-URL: er** på fliken etablering i ditt Juno-resa-program i Azure Portal. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Juno-resa från Azure AD-programgalleriet

Lägg till Juno-resan från Azure AD-programgalleriet för att börja hantera etablering till Juno-resan. Om du tidigare har konfigurerat Juno-resan för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du använda ett omfångs filter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Juno-resan måste du välja en annan roll än **standard åtkomst**. Användare med standard åtkomst rollen undantas från etablering och markeras som inte faktiskt berättigade i etablerings loggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) för att lägga till ytterligare roller. 

* Starta litet. Testa med en liten uppsättning användare och grupper innan de distribueras till alla. När omfång för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfång är inställt på alla användare och grupper kan du ange ett [omfångs filter för attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Steg 5. Konfigurera automatisk användar etablering till Juno-resa 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Konfigurera automatisk användar etablering för Juno-resan i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Juno resan**.

    ![Juno resa-länken i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du mata in klient webb adressen som hämtades tidigare i **klient-URL: en**. Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Juno-resan. Om anslutningen Miss lyckas kontrollerar du att ditt Juno-konto har administratörs behörighet och försöker igen.

    ![etablerings](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare för att Juno resan**.

9. Granska de användarattribut som synkroniseras från Azure AD till Juno-resan i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Juno-resan för uppdaterings åtgärder. Om du väljer att ändra det [matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du kontrol lera att Juno transport-API: et stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Variabel|Typ|
   |---|---|
   |userName|Sträng|
   |externalId|Sträng|
   |displayName|Sträng|
   |rubrik|Sträng|
   |aktiv|Boolesk|
   |preferredLanguage|Sträng|
   |e-postmeddelanden [typ EQ "Work"]. värde|Sträng|
   |adresser [Type EQ "Work"]. land|Sträng|
   |adresser [Type EQ "Work"]. region|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |adresser [Type EQ "Work"]. Postnr|Sträng|
   |adresser [Type EQ "Work"]. formaterad|Sträng|
   |adresser [Type EQ "Work"]. streetAddress|Sträng|
   |Name. givenName|Sträng|
   |Name. familyName|Sträng|
   |Name. middleName|Sträng|
   |namn. formaterad|Sträng|
   |phoneNumbers [Type EQ "fax"]. värde|Sträng|
   |phoneNumbers [Type EQ "Mobile"]. värde|Sträng|
   |phoneNumbers [typ EQ "Work"]. värde|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: costCenter|Sträng|
   urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Division|Sträng|
   urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Sträng|
   urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: organisation|Sträng|


10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Juno transport ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera för att Juno resan genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etablerings loggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att avgöra vilka användare som har etablerats eller har misslyckats
* Kontrol lera [förlopps indikatorn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etablerings cykeln och hur nära den är att slutföras
* Om etablerings konfigurationen verkar vara i ett ohälsosamt tillstånd, kommer programmet att placeras i karantän. Lär dig mer om karantän tillstånd [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../manage-apps/check-status-user-account-provisioning.md)
