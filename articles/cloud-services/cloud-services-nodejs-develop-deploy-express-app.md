---
title: Skapa och distribuera en Node.js-Express app till Azure Cloud Services
description: Skapa och distribuera ett program för Express.js i Node.js i Azure molntjänster
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: b212eaffb977846d40270a5f2abc76192aee4c0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60528144"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Skapa och distribuera en Node.js-webbprogram med Express på en Azure-molntjänster

Node.js innehåller en minimal uppsättning funktioner i core-runtime.
Utvecklare använder ofta 3 part moduler för att tillhandahålla ytterligare funktioner när du utvecklar ett Node.js-program. I den här självstudien skapar du ett nytt program med den [Express](https://github.com/expressjs/express) modul som ger ett MVC-ramverk för att skapa Node.js-webbprogram.

En skärmbild av det färdiga programmet understiger:

![En webbläsare som visar Välkommen till Express i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Skapa ett Molntjänstprojekt
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Utför följande steg för att skapa ett nytt molntjänstprojekt med namnet ”expressapp”:

1. Från den **Start-menyn** eller **startskärmen**, Sök efter **Windows PowerShell**. Slutligen, högerklicka på **Windows PowerShell** och välj **kör som administratör**.
   
    ![Azure PowerShell-ikonen](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Ändra sökvägen till den **c:\\nod** katalog och ange sedan följande kommandon för att skapa en ny lösning med namnet **expressapp** och en webbroll med namnet **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Som standard **Add-AzureNodeWebRole** använder en äldre version av Node.js. Den **Set-AzureServiceProjectRole** instruktionen ovan instruerar Azure för att använda v0.10.21 för nod.  Observera att parametrarna är skiftlägeskänsliga.  Du kan kontrollera att rätt version av Node.js har valts genom att markera den **motorer** -egenskapen i **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Installera Express
1. Installera Express generator genom att följande kommando:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Npm kommandots utdata bör se ut som nedan resultatet. 
   
    ![Windows PowerShell visar utdata från npm installationskommando uttryckliga.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Ändra sökvägen till den **WebRole1** katalogen och Använd kommandot express för att skapa ett nytt program:
   
        PS C:\node\expressapp\WebRole1> express
   
    Du uppmanas att skriva över tidigare programmet. Ange **y** eller **Ja** att fortsätta. Express genererar app.js-filen och en mappstruktur för att skapa ditt program.
   
    ![Utdata från kommandot express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Om du vill installera ytterligare beroenden som definierats i package.json-fil, anger du följande kommando:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Utdata från npm installationskommando](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Använd följande kommando för att kopiera den **bin/www** filen till **server.js**. Det här är så att Molntjänsten kan hitta startpunkten för det här programmet.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   När det här kommandot har slutförts bör du ha en **server.js** filen i katalogen WebRole1.
5. Ändra den **server.js** ta bort en av de '.' tecken från följande rad.
   
       var app = require('../app');
   
   När du har gjort den här ändringen bör raden visas på följande sätt.
   
       var app = require('./app');
   
   Den här ändringen är obligatoriskt eftersom vi har flyttat filen (tidigare **bin/www**,) till samma katalog som app-filen som krävs. När du har gjort den här ändringen, spara den **server.js** fil.
6. Använd följande kommando för att köra programmet i Azure-emulatorn:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![En webbsida som innehåller Välkommen till express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Ändra vyn
Ändra nu för att visa meddelandet ”Välkommen till Express i Azure”.

1. Ange följande kommando för att öppna index.jade-filen:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Innehållet i index.jade-filen.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Ve jade är standard visningsmotor används av Express-program. Mer information om Jade visningsmotor finns [ http://jade-lang.com ] [ http://jade-lang.com].
2. Ändra den sista raden i texten genom att lägga till **i Azure**.
   
   ![Läser av den sista raden i filen index.jade: p Välkommen till \#{title} i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Spara filen och avsluta Anteckningar.
4. Uppdatera din webbläsare så ser du dina ändringar.
   
   ![Ett webbläsarfönster innehåller sidan Välkommen till Express i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

När du testar programmet kan använda den **Stop-AzureEmulator** cmdlet för att stoppa emulatorn.

## <a name="publishing-the-application-to-azure"></a>Publicera programmet till Azure
I Azure PowerShell-fönstret använder den **Publish-AzureServiceProject** cmdlet för att distribuera programmet till en molntjänst

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

När distributionen är klar öppnar webbläsaren och visar sidan.

![En webbläsare som visar sidan Express. URL: en anger finns nu på Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Node.js Developer Center](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


