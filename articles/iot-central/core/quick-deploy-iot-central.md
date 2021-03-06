---
title: Skapa ett Azure IoT Central-program | Microsoft Docs
description: Skapa ett nytt Azure IoT Central-program. Skapa programmet med antingen den kostnads fria pris planen eller en av standard pris avtalen.
author: viv-liu
ms.author: viviali
ms.date: 07/30/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 4b939505f807385f235def2606d0f29564f5d08f
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552585"
---
# <a name="create-an-azure-iot-central-application"></a>Skapa ett Azure IoT Central-program

Den här snabb starten visar hur du skapar ett Azure IoT Central-program.

## <a name="create-an-application"></a>Skapa ett program

Gå till bygg webbplatsen för [Azure IoT Central](https://aka.ms/iotcentral) . Logga sedan in med ett Microsoft personal-, arbets-eller skol konto.

Du skapar ett nytt program antingen från listan över bransch relevanta IoT Central mallar som hjälper dig att komma igång snabbt eller börja från början med en **anpassad Apps** -mall. I den här snabb starten använder du den **anpassade program** mal len.

Så här skapar du ett nytt Azure IoT Central-program från den **anpassade program** mal len:

1. Gå till **Bygg** sidan:

    ![Bygg din IoT-programsida](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Välj **anpassade appar** och kontrol lera att mallen för **anpassat program** är markerad.

1. Azure IoT Central föreslår automatiskt ett **program namn** baserat på den program mall som du har valt. Du kan använda det här namnet eller ange ett eget vänligt program namn.

1. Azure IoT Central genererar också ett unikt **program-URL** -prefix åt dig, baserat på program namnet. Du använder den här URL: en för att få åtkomst till ditt program. Ändra detta URL-prefix till något mer minnes värde om du vill.

    ![Sidan skapa en program-Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Information om Azure IoT Central fakturering](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Om du väljer **anpassad app** på föregående sida visas en listruta för **Programmall** . List rutan kan visa andra mallar som har gjorts tillgängliga för dig av din organisation. 

    >[!IMPORTANT]
    >Den **anpassade** programmallen (v2) har dragits tillbaka eftersom alla funktioner som tidigare var tillgängliga i den äldre program mal len är nu tillgängliga i den senaste **anpassade program** mal len (v3). 
    
1. Välj att skapa det här programmet med hjälp av pris avtalet för den kostnads fria utvärderings versionen eller en av standard pris avtalen:

    - Program som du skapar med hjälp av den *kostnads fria* planen är kostnads fria i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem till att använda en standard pris plan när som helst innan de upphör att gälla.
    - Program som du skapar med en *standard* -plan debiteras per enhet. du kan välja standard pris Planen för **standard 1** eller **Standard 2** med de två första enheterna som är kostnads fria. Läs mer om kostnads fria priser och standard priser på [sidan med priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Om du skapar ett program med hjälp av en standard pris plan måste du välja din *katalog*, *Azure-prenumeration*och *plats*:
        - *Katalog* är den Azure Active Directory som du skapar programmet i. En Azure Active Directory innehåller användar identiteter, autentiseringsuppgifter och annan organisations information. Om du inte har en Azure Active Directory skapas en åt dig när du skapar en Azure-prenumeration.
        - Med en *Azure-prenumeration* kan du skapa instanser av Azure-tjänster. IoT Central etablerar resurser i din prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en kostnads fri på [sidan för Azure-registrering](https://aka.ms/createazuresubscription). När du har skapat Azure-prenumerationen går du tillbaka till sidan **nytt program** . Den nya prenumerationen visas nu i list rutan för **Azure-prenumeration** .
        - *Plats* är den [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill skapa ditt program. Normalt bör du välja den plats som är fysiskt närmast dina enheter för att få optimala prestanda. När du har valt en plats kan du inte senare flytta programmet till en annan plats.

1. Granska de allmänna villkoren och välj **skapa** längst ned på sidan. Efter några minuter kan du IoT Central programmet är redo att använda:

    ![Azure IoT Central-program](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett IoT Central-program. Här är det föreslagna nästa steg för att fortsätta att lära dig om IoT Central:

> [!div class="nextstepaction"]
> [Lägg till en simulerad enhet till ditt IoT Central program](./quick-create-simulated-device.md)

Om du är enhets utvecklare och vill gå in i viss kod är det föreslagna nästa steg att:
> [!div class="nextstepaction"]
> [Skapa och ansluta ett klient program till ditt Azure IoT Central-program](./tutorial-connect-device-nodejs.md)
