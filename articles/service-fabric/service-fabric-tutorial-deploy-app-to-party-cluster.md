---
title: Distribuera en Service Fabric-app till ett kluster i Azure | Microsoft Docs
description: Läs mer om hur du distribuerar ett program till ett kluster från Visual Studio.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/14/2019
ms.author: aljo,mikhegn
ms.custom: mvc
ms.openlocfilehash: 4b3922ea97391a83d729bcf8b25c489a45119046
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302443"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Självstudier: Distribuera en Service Fabric-app till ett kluster i Azure

Den här självstudien är del två i en serie. Här får du se hur du distribuerar ett Azure Service Fabric-program till ett nytt kluster i Azure.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett kluster.
> * distribuera ett program till ett fjärrkluster med Visual Studio.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md).
> * Distribuera programmet till ett fjärrkluster.
> * [Lägga till en HTTPS-slutpunkt i en klienttjänst i ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Konfigurera CI/CD med hjälp av Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Installera Visual Studio 2019](https://www.visualstudio.com/), och installera den **Azure development** och **ASP.NET och webbutveckling** arbetsbelastningar.
* [Installera Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet för röstning

Om du inte skapade exempelprogrammet för röstning i [del ett av den här självstudieserien](service-fabric-tutorial-create-dotnet-app.md) kan du ladda ned det. Kör följande kod i ett kommandofönster för att klona databasen för exempelprogrammet till den lokala datorn.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Öppna programmet i Visual Studio, kör som administratör, och kompilera programmet.

## <a name="create-a-cluster"></a>Skapa ett kluster

Nu när programmet är klart kan du skapa ett Service Fabric-kluster och sedan distribuera programmet till klustret. Ett [Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från.

I den här självstudien får du skapa ett nytt testkluster med tre noder i Visual Studio IDE och sedan publicera programmet till klustret. Se [självstudien om att skapa och hantera ett kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) för att få information om hur du skapar ett produktionskluster. Du kan också distribuera programmet till ett befintligt kluster som du skapade tidigare via [Azure-portalen](https://portal.azure.com), med hjälp av [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)- eller [Azure CLI](./scripts/cli-create-cluster.md)-skript, eller från en [Azure Resource Manager-mall](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Programmet Röstning, och många andra program, använder omvänd Service Fabric-proxy för att kommunicera mellan tjänster. Kluster som skapas från Visual Studio har en omvänd proxy som är aktiverad som standard. Om du distribuerar till ett befintligt kluster måste du [aktivera omvänd proxy i klustret](service-fabric-reverseproxy-setup.md) för att programmet Röstning ska fungera.


### <a name="find-the-votingweb-service-endpoint"></a>Hitta tjänsten VotingWebs slutpunkt

Klientwebbtjänsten för röstningsprogrammet lyssnar på en viss port (8080 om du har följt stegen i [del ett i den här självstudieserien](service-fabric-tutorial-create-dotnet-app.md). När programmet distribueras till ett kluster i Azure, körs både klustret och programmet bakom en Azure-lastbalanserare. Programporten måste öppnas med hjälp av en regel i Azure-lastbalanseraren. Regeln skickar inkommande trafik via lastbalanseraren till webbtjänsten. Du hittar porten i filen **VotingWeb/PackageRoot/ServiceManifest.xml** i elementet **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Notera tjänstens slutpunkt, som krävs i ett senare steg.  Om du distribuerar till ett befintligt kluster, öppna den här porten genom att skapa en regel och en avsökning som belastningsutjämning i Azure load balancer med en [PowerShell-skript](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) eller via belastningsutjämnaren för det här klustret i den [Azure-portalen ](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Skapa ett testkluster i Azure
Högerklicka på **Voting** i Solution Explorer och välj **Publicera**.

I **Connection Endpoint** (Anslutningsslutpunkt) väljer du **Skapa ett nytt kluster**.  Om du distribuerar till ett befintligt kluster, väljer du klusterslutpunkten i listan.  Dialogrutan Skapa Service Fabric-kluster öppnas.

På fliken **Kluster** anger du **klusternamnet** (till exempel ”mytestcluster”), väljer din prenumeration, väljer en region för klustret (till exempel USA, södra centrala), anger antalet klusternoder (vi rekommenderar att tre noder för ett testkluster) och anger en resursgrupp (till exempel ”mytestclustergroup”). Klicka på **Nästa**.

![Skapa ett kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

På fliken **Certifikat** anger du sökvägen för lösenord och utdata för klustercertifikatet. Ett självsignerat certifikat har skapats som en PFX-fil och sparats i den angivna utdatasökvägen.  Certifikatet används för både nod till nod- och klient till nod-säkerhet.  Använd inte ett självsignerat certifikat för produktionskluster.  Det här certifikatet används av Visual Studio för att autentisera med klustret och distribuera program. Välj **Importera certifikat** för att installera PFX i certifikatarkivet CurrentUser\My på din dator.  Klicka på **Nästa**.

![Skapa ett kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

På fliken **VM Detail** (VM-information) anger du **användarnamn** och **lösenord** för kluster-administratörskontot.  Välj **Avbildning av virtuell dator** för klusternoderna och **Storlek på virtuell dator** för varje nod i klustret.  Klicka på fliken **Avancerat**.

![Skapa ett kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

I **Portar** anger du tjänstslutpunkten VotingWeb från föregående steg (till exempel 8080).  När klustret har skapats öppnas programportarna i Azure Load Balancer för att vidarebefordra trafik till klustret.  Klicka på **Skapa** för att skapa klustret, vilket tar flera minuter.

![Skapa ett kluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publicera programmet till klustret

När det nya klustret är klart kan du distribuera det till programmet Röstning direkt från Visual Studio.

Högerklicka på **Voting** i Solution Explorer och välj **Publicera**. Dialogrutan **Publish** (Publicera) visas.

I **Anslutningens slutpunkt** väljer du slutpunkten för klustret som du skapade i föregående steg.  Till exempel ”mytestcluster.southcentral.cloudapp.azure.com:19000”. Om du väljer **Advanced Connection Parameters** (Avancerade anslutningsparametrar) bör certifikatinformationen fyllas i automatiskt.  
![Publicera ett Service Fabric-program](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Välj **Publicera**.

När programmet har distribuerats öppnar du en webbläsare och anger klusteradressen följt av **:8080**. Alternativt anger du en annan port om en sådan har konfigurerats. Ett exempel är `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Du ser nu att programmet körs i klustret i Azure. På röstningswebbplatsen provar du att lägga till och ta bort röstningsalternativ och rösta för ett eller flera av dessa alternativ.

![Service Fabric-röstningsexempel](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett kluster.
> * distribuera ett program till ett fjärrkluster med Visual Studio.

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Aktivera HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
