---
title: 'Självstudier: Konfigurera Zscaler Beta för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Zscaler Beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 82f9746b8f2f6665506491c328841e6a88438472
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672965"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Zscaler Beta för automatisk användaretablering

Målet med den här självstudien är att ange vilka åtgärder som ska utföras i Zscaler Beta och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Zscaler Beta.

> [!NOTE]
> Den här självstudien beskrivs en koppling som bygger på Azure AD-användare Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor och svar finns i [automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Den här anslutningsappen är för närvarande i offentlig förhandsversion. Läs mer på allmänna Microsoft Azure-villkor för användning av förhandsversionsfunktioner [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klient
* En Zscaler Beta-klient
* Ett användarkonto i betaversionen av Zscaler med administratörsbehörighet

> [!NOTE]
> Azure AD-etablering-integrering förlitar sig på Zscaler Beta SCIM API, som är tillgängliga för Zscaler Beta-utvecklare för konton med Enterprise-paketet.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Lägga till Zscaler Beta från galleriet

Du måste lägga till Zscaler Beta från Azure AD-programgalleriet i listan över hanterade SaaS-program innan du konfigurerar Zscaler Beta för automatisk användarförsörjning med Azure AD.

**Utför följande steg för att lägga till Zscaler Beta från Azure AD-programgalleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zscaler Beta**, väljer **Zscaler Beta** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Zscaler Beta i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Tilldela användare till Zscaler Beta

Azure Active Directory använder ett begrepp som kallas ”tilldelningar” för att avgöra vilka användare får åtkomst till valda appar. I samband med automatisk användaretablering, synkroniseras endast de användare och/eller grupper som är ”kopplade” till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering, bör du bestämma vilka användare och/eller grupper i Azure AD behöver åtkomst till Zscaler Beta. När du valt, kan du tilldela dessa användare och/eller grupper till Zscaler Beta genom att följa instruktionerna här:

* [Tilldela en användare eller grupp till en företagsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Viktiga tips för att tilldela användare till Zscaler Beta

* Vi rekommenderar att en enda Azure AD-användare har tilldelats Zscaler Beta att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Zscaler Beta, måste du välja någon giltig programspecifika-roll (om tillgängligt) i dialogrutan för tilldelning. Användare med den **standard åtkomst** rollen är undantagna från etablering.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurera automatisk användaretablering till Zscaler Beta

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD provisioning-tjänst för att skapa, uppdatera och inaktivera användare och/eller grupper i betaversionen av Zscaler baserat på användare och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Zscaler Beta, följa anvisningarna enligt den [Zscaler Beta enkel inloggning för självstudien](zscaler-beta-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om de här två funktionerna komplettera varandra.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Konfigurera automatisk användaretablering för Zscaler Beta i Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) och välj **företagsprogram**väljer **alla program**och välj sedan **Zscaler Beta**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Zscaler Beta**.

    ![Zscaler Beta-länk i programlistan](common/all-applications.png)

3. Välj den **etablering** fliken.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Ange den **Etableringsläge** till **automatisk**.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Under den **administratörsautentiseringsuppgifter** avsnittet, ange den **klient-URL** och **hemlighet Token** för Zscaler Beta-kontot enligt beskrivningen i steg 6.

6. Att hämta den **klient-URL** och **hemlighet Token**, gå till **Administration > autentiseringsinställningar** i användargränssnittet för Zscaler Beta-portalen och klicka på  **SAML** under **autentiseringstyp**.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Klicka på **konfigurera SAML** att öppna den **Configuration SAML** alternativ.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Välj **Enable SCIM-Based etablering** att hämta **bas-URL** och **ägar-Token**, spara inställningarna. Kopiera den **bas-URL** till **klient-URL**, och **ägar-Token** till **hemlighet Token** i Azure-portalen.

7. För att fylla i fälten som visas i steg 5, klickar du på **Testanslutningen** att se till att Azure AD kan ansluta till Zscaler Beta. Om anslutningen misslyckas, kontrollera Zscaler Beta-kontot har administratörsbehörighet och försök igen.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. I den **e-postmeddelande** fältet, anger du den e-postadressen för en person eller grupp som ska ta emot meddelanden etablering fel och markera kryssrutan **skicka ett e-postmeddelande när ett fel inträffar**.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Klicka på **Spara**.

10. Under den **mappningar** väljer **synkronisera Azure Active Directory-användare till Zscaler Beta**.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Granska användarattribut som ska synkroniseras från Azure AD med Zscaler Beta i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha användarkonton i betaversionen av Zscaler för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Under den **mappningar** väljer **synkronisera Azure Active Directory-grupper till Zscaler Beta**.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Granska Gruppattribut som synkroniseras från Azure AD till Zscaler Beta i den **attributmappning** avsnittet. Attribut som har markerats som **matchande** egenskaper som används för att matcha grupper i betaversionen av Zscaler för uppdateringsåtgärder. Välj den **spara** knappen för att genomföra ändringarna.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Om du vill konfigurera Omfångsfilter avser följande instruktionerna i den [Scoping filter självstudien](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera den Azure AD-etableringstjänsten för Zscaler Beta, ändra den **Etableringsstatus** till **på** i den **inställningar** avsnittet.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Ange användare och/eller grupper som du vill att etablera till Zscaler Beta genom att välja de önskade värdena i **omfång** i den **inställningar** avsnittet.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. När du är redo att etablera, klickar du på **spara**.

    ![Zscaler Beta etablering](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som angetts i **omfång** i den **inställningar** avsnittet. Den första synkroniseringen tar längre tid att genomföra än efterföljande synkroniseringar som sker ungefär var 40 minut så länge som den Azure AD-etableringtjänsten körs. Du kan använda den **synkroniseringsinformation** avsnitt för att övervaka förloppet och följer länkar till att etablera aktivitetsrapporten som beskriver alla åtgärder som utförs av den Azure AD-etableringtjänsten på Zscaler Beta.

Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantering av användarkontoetablering för Företagsappar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
