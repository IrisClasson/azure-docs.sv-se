---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Ekarda | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ekarda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8e2945aa-46fc-41bc-a530-3807a5dcb76a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aacaec5ff632385a1f1686610370bb92eb63c349
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87017576"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Ekarda

I den här självstudien får du lära dig hur du integrerar Ekarda med Azure Active Directory (Azure AD). När du integrerar Ekarda med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Ekarda.
* Gör det möjligt för användarna att logga in automatiskt till Ekarda med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Ekarda för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Ekarda stöder **SP-och IDP** -INITIERAd SSO
* Ekarda stöder **just-in-Time** User-etablering

* När du har konfigurerat Ekarda kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ekarda-from-the-gallery"></a>Lägga till Ekarda från galleriet

Om du vill konfigurera integreringen av Ekarda i Azure AD måste du lägga till Ekarda från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Ekarda** i sökrutan.
1. Välj **Ekarda** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Konfigurera och testa enkel inloggning med Azure AD för Ekarda

Konfigurera och testa Azure AD SSO med Ekarda med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Ekarda.

Om du vill konfigurera och testa Azure AD SSO med Ekarda, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EKARDA SSO](#configure-ekarda-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Ekarda test User](#create-ekarda-test-user)** -om du vill ha en motsvarighet till B. Simon i Ekarda som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Ekarda** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:
    
    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i text rutan för Ekarda:

    > [!Note]
    > Om **ID** -och **svars-URL** -värdena inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

1. Om du inte har **metadata-filen för en tjänst leverantör**går du till avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, ange värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster:`https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster:`https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:`https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Ekarda client support team](mailto:contact@ekarda.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **certifikatet (base64)** och sparar det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **set-up-Ekarda** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Ekarda.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Ekarda**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-ekarda-sso"></a>Konfigurera Ekarda SSO

1. Logga in på din Ekarda-företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **admin**  ->  **mitt konto**.

    ![Ekarda-konfiguration](./media/ekarda-tutorial/ekarda.png)    

1. Längst ned på sidan hittar du avsnittet **SAML-inställningar** där du kommer att konfigurera SAML-integrationen.
1. Utför följande steg på följande sida:

    ![Ekarda-konfiguration](./media/ekarda-tutorial/ekarda1.png)

    a. Klicka på länken för **service providerns metadata** och spara den som en fil på din dator.

    b. Markera kryss rutan **Aktivera SAML** .

    c. I text rutan **entitets-ID för IDP** klistrar du in värdet för **entitets-ID** , som du har kopierat från Azure Portal.

    d. I text rutan för **inloggnings-URL för IDP** klistrar du in värdet för **inloggnings-URL** , som du har kopierat från Azure Portal.

    e. I text rutan **utloggnings-URL för IDP** klistrar du in URL-värdet för **utloggning** som du har kopierat från Azure Portal.

    f. Öppna den nedladdade **certifikat (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **IDP x509-certifikat** .

    ex. Välj avsnittet **Aktivera service nivå mål** i **alternativ** .

    h. Klicka på **Uppdatera**.

### <a name="create-ekarda-test-user"></a>Skapa Ekarda test användare

I det här avsnittet skapas en användare som heter B. Simon i Ekarda. Ekarda stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Ekarda skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Ekarda på åtkomst panelen, bör du loggas in automatiskt på den Ekarda som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Ekarda med Azure AD](https://aad.portal.azure.com/)

- Använd [Ekarda Enterprise ecard-lösning](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) för att tillhandahålla ett antal anställda för att skicka eCards som är märkta med företagets logo typ för sina klienter och kollegor. Lär dig mer om att tillhandahålla [Ekarda som en SSO-lösning](https://support.ekarda.com/#SSO-Implementation).

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Ekarda med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
