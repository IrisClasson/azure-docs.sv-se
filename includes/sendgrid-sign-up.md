---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: e38cecfe206f21f9189493e7ed6e8f0cadda9cd9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75463579"
---
Azure-kunder kan låsa upp 25 000 kostnadsfria e-postmeddelanden varje månad. Med de här 25 000 kostnads fria e-postmeddelandena får du till gång till avancerad rapportering och analys och [alla API: er][all APIs] (webb, SMTP, evenemang, analys och annat). Information om ytterligare tjänster från SendGrid finns på sidan om [SendGrid-lösningar][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>Registrera ett SendGrid-konto
1. Logga in på [Azure-portalen][Azure portal].
2. På Azure Portal-menyn eller start sidan väljer du **skapa en resurs**.

    ![command-bar-new][command-bar-new]
3. Sök efter och välj **SendGrid**.

    ![sendgrid-store][sendgrid-store]
4. Fyll i registreringsformuläret och välj **Skapa**.

    ![sendgrid-create][sendgrid-create]
5. Ange ett **namn** som ska identifiera SendGrid-tjänsten i dina Azure-inställningar. Namnet måste vara mellan 1 och 100 tecken långt och får bara innehålla alfanumeriska tecken, bindestreck, punkter och understreck. Namnet måste vara unikt i listan med de objekt du prenumererar på i Azure Store.
6. Ange och bekräfta ett **lösenord**.
7. Välj din **prenumeration**.
8. Skapa en ny **resursgrupp** eller välj en befintlig.
9. I avsnittet **Prisnivå** väljer du den SendGrid-plan som du vill registrera dig för.

    ![sendgrid-pricing][sendgrid-pricing]
10. Ange en **kampanjkod** om du har en.
11. Ange dina **kontaktuppgifter**.
12. Läs igenom och godkänn de **juridiska villkoren**.
13. När du har bekräftat ditt köp ser du att **distributionen lyckades** och att ditt konto visas.

    ![all-resources][all-resources]

    När du har slutfört köpet och klickar på knappen **Hantera** för att starta processen med e-postverifiering så får du ett e-postmeddelande från SendGrid där du uppmanas att verifiera ditt konto. Om du inte får det här e-postmeddelandet eller om du har problem med att verifiera ditt konto kan du läsa vanliga frågor och svar.

    ![manage][manage]

    **Du kan bara skicka upp till 100 e-postmeddelanden per dag innan du har verifierat ditt konto.**

    Om du vill byta prenumerationsavtal eller visa kontaktinställningarna för SendGrid klickar du på namnet på SendGrid-tjänsten så att instrumentpanelen för SendGrid Marketplace öppnas.

    ![inställningar][settings]

    Om du vill skicka ett e-postmeddelande med SendGrid måste du ange din API-nyckel.

### <a name="to-find-your-sendgrid-api-key"></a>Så här hittar du din SendGrid API-nyckel
1. Klicka på **Hantera**.

    ![manage][manage]
2. Välj **Inställningar** på instrumentpanelen för SendGrid sedan **API Keys** (API-nycklar) på menyn till vänster.

    ![api-keys][api-keys]

3. Klicka på **skapa API-nyckel**.

    ![general-api-key][general-api-key]
4. Ange minst **Name of this key** (Den här nyckelns namn), ge fullständig åtkomst till **Mail Send** (Skicka e-post) och välj **Spara**.

    ![access][access]
5. Din API-nyckel visas nu en enda gång. Se till att förvara den säkert.

### <a name="to-find-your-sendgrid-credentials"></a>Så här hittar du dina inloggningsuppgifter för SendGrid API
1. Om du klickar på nyckelikonen så visas ditt **användarnamn**.

    ![key][key]
2. Lösenordet är det du valde vid installationen. Om du vill ändra det så kan du välja **Ändra lösenord** eller **Återställ lösenord**.

Om du vill hantera inställningarna för e-postleverans så klickar du på knappen **Hantera**. Detta kommer att omdirigeras till SendGrid-instrumentpanelen.

![manage][manage]

Mer information om hur du skickar e-post via SendGrid finns i [Översikt över e-post-API][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
