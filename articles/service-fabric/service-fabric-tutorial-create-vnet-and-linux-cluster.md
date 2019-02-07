---
title: Skapa ett Linux Service Fabric-kluster i Azure | Microsoft Docs
description: I den här självstudien får du lära dig att distribuera ett Linux Service Fabric-kluster till ett befintligt virtuellt nätverk i Azure med Azure CLI.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/27/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 265e99d18d8660f149d33b1b4a37a7d32eae794d
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755204"
---
# <a name="tutorial-deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Självstudier: Distribuera ett Service Fabric-kluster i Linux till ett virtuellt Azure-nätverk

Den här självstudien ingår i en serie. Du lär dig att distribuera ett Linux Service Fabric-kluster till ett [virtuellt nätverk i Azure (VNET)](../virtual-network/virtual-networks-overview.md) med Azure CLI och en mall. När du är färdig körs ett kluster i molnet som du kan distribuera program till. Om du vill skapa ett Windows-kluster med PowerShell läser du informationen om att [skapa ett säkert Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett VNET i Azure med Azure CLI
> * Skapa ett säkert Service Fabric-kluster i Azure med hjälp av Azure CLI
> * Gör klustret säkert med ett X.509-certifikat
> * Anslut till klustret med Service Fabric CLI
> * Ta bort ett kluster

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * skapa ett säkert kluster i Azure
> * [skala upp eller ned ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Service Fabric CLI](service-fabric-cli.md)
* Installera [Azure CLI](/cli/azure/install-azure-cli)

I följande procedurer skapas ett Service Fabric-kluster med fem noder. Du kan beräkna kostnaden för att köra ett Service Fabric-kluster i Azure med [Azures prissättningsberäknare](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Viktiga begrepp

Ett [Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras till och hanteras från. Kluster kan skalas upp till tusentals datorer. En dator eller virtuell dator som ingår i ett kluster kallas för en nod. Varje nod har tilldelats ett nodnamn (en sträng). Noder har egenskaper, till exempel placeringsegenskaper.

En nodtyp definierar storlek, antal och egenskaper för en uppsättning virtuella datorer i klustret. Varje definierad nodtyp är konfigurerad som en [VM-skalningsuppsättning](/azure/virtual-machine-scale-sets/), en Azure-beräkningsresurs du använder till att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”.  Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster).  [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen.

Klustret skyddas med ett klustercertifikat. Ett klustercertifikat är ett X.509-certifikat som används för att skydda kommunikationen mellan noderna och att autentisera slutpunkterna för klusterhantering i en hanteringsklient.  Klustercertifikatet tillhandahåller också en SSL för API:t för HTTPS-hantering och för Service Fabric Explorer över HTTPS. Självsignerade certifikat är praktiska för testkluster.  För produktionskluster ska du använda ett certifikat från en certifikatutfärdare som klustercertifikat.

Klustercertifikatet måste:

* innehålla en privat nyckel
* vara skapat för nyckelutbyte som kan exporteras till en Personal Information Exchange-fil (.pfx)
* ha ett ämnesnamn som överensstämmer med domänen du använder för åtkomst till Service Fabric-klustret. Matchningen krävs för att tillhandahålla SSL för klustrets HTTPS-hanteringsslutpunkter och Service Fabric Explorer. Du kan inte hämta ett SSL-certifikat från en certifikatutfärdare (CA) för domänen .cloudapp.azure.com. Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn du använder för klustret.

Azure Key Vault används till att hantera certifikat för Service Fabric-kluster i Azure.  När ett kluster distribueras i Azure hämtar Azure-resursprovidern som ansvarar för att skapa Service Fabric-kluster certifikat från Key Vault och installerar dem på klustrets virtuella datorer.

I den här självstudien distribueras ett kluster med fem noder av en enda nodtyp. Vid distribution av kluster till produktion är det emellertid viktigt med [kapacitetsplanering](service-fabric-cluster-capacity.md). Här är några saker att tänka på i samband med den här processen.

* Antalet noder och nodtyper som behövs i klustret
* Egenskaperna för respektive nodtyp (exempelvis storlek, primär, offentlig och antal virtuella datorer)
* Klustrets egenskaper för tillförlitlighet och hållbarhet

## <a name="download-and-explore-the-template"></a>Ladda ned och titta närmare på mallen

Ladda ned följande mallfiler för Resource Manager:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

Den här mallen distribuerar ett säkert kluster med fem virtuella datorer och en enda nodtyp till ett virtuellt nätverk.  Andra exempelmallar finns på [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). [AzureDeploy.json][template] distribuerar ett antal resurser, däribland följande.

### <a name="service-fabric-cluster"></a>Service Fabric-kluster

I resursen **Microsoft.ServiceFabric/clusters** distribueras ett Linux-kluster med följande egenskaper:

* en enda nodtyp
* fem noder av den primära nodtypen (kan konfigureras i mallparametrarna)
* Operativsystem: Ubuntu 16.04 LTS (kan konfigureras i mallparametrarna)
* skyddat med certifikat (kan konfigureras i mallparametrarna)
* [DNS-tjänst](service-fabric-dnsservice.md) är aktiverad
* [Hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Brons (kan konfigureras i mallparametrarna)
* [Tillförlitlighetsnivå](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (kan konfigureras i mallparametrarna)
* klientanslutningsslutpunkt: 19000 (kan konfigureras i mallparametrarna)
* HTTP-gatewayslutpunkt: 19080 (kan konfigureras i mallparametrarna)

### <a name="azure-load-balancer"></a>Azure-lastbalanserare

I resursen **Microsoft.Network/loadBalancers** har en belastningsutjämnare konfigurerats och avsökningar samt regler har konfigurerats för följande portar:

* klientanslutningsslutpunkt: 19000
* HTTP-gatewayslutpunkt: 19080
* programport: 80
* programport: 443

### <a name="virtual-network-and-subnet"></a>Virtuellt nätverk och undernät

Namnen på det virtuella nätverket och undernätet deklareras också i mallparametrarna.  Adressutrymmen i det virtuella nätverket och undernätet deklareras också i mallparametrarna och konfigureras i resursen **Microsoft.Network/virtualNetworks**:

* det virtuella nätverkets adressutrymme: 10.0.0.0/16
* Service Fabric-undernätets adressutrymme: 10.0.2.0/24

Om du behöver andra programportar måste du justera resursen Microsoft.Network/loadBalancers för att låta trafiken komma in.

## <a name="set-template-parameters"></a>Ställa in mallparametrar

Parameterfilen [AzureDeploy.Parameters][parameters] deklarerar många värden som används till att distribuera klustret och associerade resurser. Här är några av parametrarna du kan behöva ändra för distributionen:

|Parameter|Exempelvärde|Anteckningar|
|---|---||
|adminUserName|vmadmin| Administratörsnamn för virtuella datorer i klustret. |
|adminPassword|Password#1234| Administratörslösenord för virtuella datorer i klustret.|
|clusterName|mysfcluster123| Namnet på klustret. |
|location|southcentralus| Klustrets placering. |
|certificateThumbprint|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets SHA1-tumavtrycksvärde. Till exempel ”6190390162C988701DB5676EB81083EA608DCCF3”. </p>|
|certificateUrlValue|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets webbadress. Exempel: "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i källans nyckelvärde. Till exempel ”/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuera det virtuella nätverket och klustret

Konfigurera sedan nätverkstopologin och distribuera Service Fabric-klustret. Resource Manager-mallen [AzureDeploy.json][template] skapar ett virtuellt nätverk (VNET) och ett undernät för Service Fabric. Mallen distribuerar också ett kluster med certifikatsäkerhet aktiverad.  För produktionskluster ska du använda ett certifikat från en certifikatutfärdare som klustercertifikat. Ett självsignerat certifikat kan användas för att skydda testkluster.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Skapa ett kluster med ett befintligt certifikat

Följande skript använder kommandot [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) och mallen för att distribuera ett nytt kluster som skyddas med ett befintligt certifikat. Kommandot skapar även ett nytt nyckelvalv i Azure och laddar upp certifikatet.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Skapa ett kluster med ett nytt, självsignerat certifikat

Följande skript använder kommandot [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) och en mall för att distribuera ett nytt kluster i Azure. Kommandot skapar även ett nytt nyckelvalv i Azure, lägger till ett nytt självsignerat certifikat i nyckelvalvet och laddar ned certifikatfilen lokalt.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Ansluta till det skyddade klustret

Anslut till klustret med Service Fabric CLI `sfctl cluster select`-kommandot med din nyckel.  Använd enbart alternativet **--no-verify** för ett självsignerat certifikat.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Kontrollera att du är ansluten och att klustret är felfritt med kommandot `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Rensa resurser

De andra artiklarna i självstudieserien använder klustret du nyss skapade. Om du inte genast fortsätter till nästa artikel kanske du vill [ta bort klustret](service-fabric-cluster-delete.md) för att undvika kostnader.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett VNET i Azure med Azure CLI
> * Skapa ett säkert Service Fabric-kluster i Azure med hjälp av Azure CLI
> * Gör klustret säkert med ett X.509-certifikat
> * Anslut till klustret med Service Fabric CLI
> * Ta bort ett kluster

Fortsätt sedan till följande självstudie för att lära dig att skala klustret.
> [!div class="nextstepaction"]
> [Skala ett kluster](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Ubuntu-1-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/5-VM-Ubuntu-1-NodeTypes-Secure/AzureDeploy.Parameters.json
