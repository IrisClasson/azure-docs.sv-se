---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140567"
---
1. I Visual Studio Solution Explorer högerklickar du på approjektet Windows Store. Välj sedan **Store** > **associera App med Store**.

    ![Associera app med Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. I guiden väljer du **nästa**. Logga sedan in med ditt Microsoft-konto. I **reservera ett nytt appnamn**, Skriv ett namn för din app och välj sedan **reservera**.
3. När appregistreringen har skapats väljer du namnet på nya appen. Välj **nästa**, och välj sedan **associera**. Den här processen lägger till registreringsinformationen som krävs Windows Store i programmanifestet.
4. Upprepa steg 1 och 3 för Windows Phone Store-app-projekt med hjälp av samma registreringen du skapade tidigare för Windows Store-app.  
5. Gå till den [Windows Dev Center](https://dev.windows.com/en-us/overview), och sedan logga in med ditt Microsoft-konto. I **Mina appar**, Välj ny appregistrering. Expandera sedan **Services** > **Push-meddelanden**.
6. På den **Push-meddelanden** sidan under **Windows Push Notification Services (WNS) och Microsoft Azure Mobile Apps**väljer **webbplatsen Live-tjänster**.  Anteckna värdena för den **paket-SID** och *aktuella* värde i **Programhemlighet**. 

    ![Appinställningen i developer center](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Programhemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Inte dela dessa värden med vem som helst eller distribuera dem med din app.
   >
   >
