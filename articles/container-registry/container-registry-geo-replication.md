---
title: GEO-replikera ett register
description: Kom igång med att skapa och hantera ett geo-replikerat Azure Container Registry, vilket gör att registret kan betjäna flera regioner med regionala repliker med flera huvud servrar.
author: stevelas
ms.topic: article
ms.date: 08/16/2019
ms.author: stevelas
ms.openlocfilehash: d238de30e458261a11c941c03ac127c732ca8d3d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456444"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replikering i Azure Container Registry

Företag som vill ha en lokal närvaro, eller en direkt säkerhetskopia, väljer att köra tjänster från flera Azure-regioner. Som bästa praxis kan ett containerregister placeras i varje region där avbildningarna körs för att medge nätverksnära åtgärder med snabba och tillförlitliga avbildningslageröverföringar. Geo-replikering gör att ett Azure-containerregister kan fungera som ett enskilt register som betjänar flera regioner med regionala register med flera original. 

Ett geo-replikerat register ger följande fördelar:

* Namn på enskilda register/avbildningar/taggar kan användas i flera regioner
* Nätverksnära registeråtkomst från regionala distributioner
* Inga ytterligare utgående avgifter eftersom avbildningar hämtas från ett lokalt replikerat register i samma region som din containervärd
* Enskild hantering av ett register i flera regioner

> [!NOTE]
> Om du behöver upprätthålla kopior av containeravbildningar i mer än ett Azure-containerregister har Azure Container Registry även stöd för [avbildningsimport](container-registry-import-images.md). Till exempel kan du i ett DevOps-arbetsflöde importera en avbildning från ett utvecklingsregister till ett produktionsregister utan att behöva använda Docker-kommandon.
>

## <a name="example-use-case"></a>Exempel på användningsfall
Contoso kör en webbplats med offentlig närvaro i USA, Kanada och Europa. För att betjäna dessa marknader med lokalt och nätverksnära innehåll kör Contoso [Azure Kubernetes Service](/azure/aks/)-kluster (AKS) i USA, västra; USA, östra; Kanada, centrala samt Europa, västra. Webbappen, som distribueras som en Docker-avbildning, använder samma kod och avbildning i alla regioner. Innehåll, som är lokalt för den regionen, hämtas från en databas som etableras unikt i varje region. Varje regional distribution har en unik konfiguration för resurser som den lokala databasen.

Utvecklingsteamet finns i Seattle, WA, och använder datacentret för USA, västra.

![Push-överföra till flera register](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Push-överföra till flera register*

Innan Contoso började använda de geo-replikerade funktionerna hade de ett USA-baserat register i USA, västra med ytterligare ett register i Europa, västra. För att betjäna dessa olika regioner push-överförde utvecklingsteamet till två olika register.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Hämta från flera register](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Hämta från flera register*

Typiska utmaningar med flera register är:

* Klustren för USA, västra; USA, östra och Kanada, centrala hämtar alla från registret USA, västra och ådrar sig utgående avgifter när dessa fjärrcontainervärdar hämtar avbildningar från datacenter för USA, västra.
* Utvecklingsteamet måste push-överföra avbildningar till registren för USA, västra och Europa, västra.
* Utvecklingsteamet måste konfigurera och underhålla varje regional distribution med avbildningsnamn som refererar till det lokala registret.
* Registeråtkomst måste konfigureras för varje region.

## <a name="benefits-of-geo-replication"></a>Fördelar med geo-replikering

![Hämta från ett geo-replikerat register](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Användning av funktionen för geo-replikering i Azure Container Registry ger följande fördelar:

* Hantera ett enskilt register i alla regioner: `contoso.azurecr.io`
* Hantera en enskild konfiguration av avbildningsdistributioner eftersom alla regioner använder samma avbildnings-URL: `contoso.azurecr.io/public/products/web:1.2`
* Push-överför till ett enda register medan ACR hanterar geo-replikeringen. Du kan konfigurera regionala [webhookar](container-registry-webhook.md) för att meddela dig om händelser i vissa repliker.

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering

Konfiguration av geo-replikering är så enkelt som att klicka på regioner på en karta. Du kan också hantera geo-replikering med hjälp av verktyg som [AZ ACR Replication](/cli/azure/acr/replication) -kommandon i Azure CLI, eller distribuera ett register som är aktiverat för geo-replikering med en [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Geo-replikering är en funktion som endast finns i [Premium-register](container-registry-skus.md). Om ditt register ännu inte är Premium kan du ändra från Basic och Standard till Premium i [Azure-portalen](https://portal.azure.com):

![Växla SKU:er i Azure-portalen](media/container-registry-skus/update-registry-sku.png)

För att konfigurera geo-replikering för Premium-registret loggar du in på Azure-portalen på https://portal.azure.com.

Gå till ditt Azure Container Registry och välj **Replikeringar**:

![Replikeringar i användargränssnittet i containerregister i Azure-portalen](media/container-registry-geo-replication/registry-services.png)

En karta visas med alla aktuella Azure-regioner:

 ![Regionskarta i Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Blå sexhörningar representerar aktuella repliker
* Gröna sexhörningar representerar möjliga replikregioner
* Grå sexhörningar representerar Azure-regioner som ännu inte är tillgängliga för replikering

Om du vill konfigurera en replik markerar du en grön sexhörning och väljer **Skapa**:

 ![Skapa replikerings-UI i Azure-portalen](media/container-registry-geo-replication/create-replication.png)

Om du vill konfigurera ytterligare repliker markerar du de gröna sexhörningarna för andra regioner och klickar på **Skapa**.

ACR börjar synkronisera avbildningar mellan de konfigurerade replikerna. När det är klart visar portalen *Klar*. Replikstatus i portalen uppdateras inte automatiskt. Använd uppdateringsknappen om du vill visa uppdaterad status.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Att tänka på när du använder ett geo-replikerat register

* Varje region i ett geo-replikerat register är oberoende när den har kon figurer ATS. Azure Container Registry service avtal gäller för varje geo-replikerad region.
* När du push-överför eller hämtar bilder från ett geo-replikerat register skickar Azure Traffic Manager i bakgrunden begäran till registret i den region som är närmast dig.
* När du har push-överfört en avbildning eller tagga till den närmaste regionen tar det lite tid för Azure Container Registry att replikera manifest och lager till de återstående regioner som du har valt. Större bilder tar längre tid att replikera än de mindre. Bilder och taggar synkroniseras i de replikerade regionerna med en eventuell konsekvens modell.
* Om du vill hantera arbets flöden som är beroende av push-uppdateringar till en geo-replikerad, rekommenderar vi att du konfigurerar [Webhooks](container-registry-webhook.md) så att de svarar på push-händelserna. Du kan ställa in regionala webhookar i ett geo-replikerat register för att spåra push-händelser när de är klara i de geo-replikerade regionerna.

## <a name="delete-a-replica"></a>Ta bort en replik

När du har konfigurerat en replik för registret kan du när som helst ta bort den om den inte längre behövs. Ta bort en replik med hjälp av Azure Portal eller andra verktyg som kommandot [AZ ACR Replication Delete](/cli/azure/acr/replication#az-acr-replication-delete) i Azure CLI.

Så här tar du bort en replik i Azure Portal:

1. Navigera till Azure Container Registry och välj **replikeringar**.
1. Välj namnet på en replik och välj **ta bort**. Bekräfta att du vill ta bort repliken.

> [!NOTE]
> Du kan inte ta bort register repliken i *hem regionen* för registret, det vill säga den plats där du skapade registret. Du kan bara ta bort hem repliken genom att ta bort själva registret.

## <a name="geo-replication-pricing"></a>Prissättning för Geo-replikering

Geo-replikering är en funktion i [Premium SKU](container-registry-skus.md) för Azure Container Registry. När du replikerar ett register till din önskade regioner debiteras du avgifter för Premium-register för varje region.

I föregående exempel konsoliderade Contoso två register till ett och lade till repliker i USA, östra; Kanada, centrala samt Europa, västra. Contoso betalade då fyra gånger Premium per månad, utan ytterligare konfiguration eller hantering. Varje region hämtar nu sina avbildningar lokalt, vilket förbättrar prestanda och tillförlitlighet utan utgående nätverksavgifter från USA, västra till Kanada och USA, östra.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Felsöka push-åtgärder med geo-replikerade register
 
En Docker-klient som skickar en avbildning till ett geo-replikerat register får inte skicka alla avbildnings lager och dess manifest till en enda replikerad region. Detta kan inträffa eftersom Azure Traffic Manager dirigerar register begär anden till det nätverks närmast replikerade registret. Om registret har två *närliggande* replikerade regioner kan bild lager och manifestet distribueras till de två platserna och push-åtgärden Miss lyckas när manifestet verifieras. Det här problemet beror på hur DNS-namnet på registret matchas på vissa Linux-värdar. Det här problemet uppstår inte i Windows, som tillhandahåller en DNS-cache på klient sidan.
 
Om det här problemet uppstår är en lösning att tillämpa en DNS-cache på klient sidan, till exempel `dnsmasq` på Linux-värden. Detta säkerställer att register namnet matchas konsekvent. Om du använder en virtuell Linux-dator i Azure för att skicka till ett register, se alternativ i alternativ [för DNS-namnmatchning för virtuella Linux-datorer i Azure](../virtual-machines/linux/azure-dns.md).

Om du vill optimera DNS-matchningen till den närmaste repliken när du skickar avbildningar konfigurerar du ett geo-replikerat register i samma Azure-regioner som källan till push-åtgärderna eller den närmaste regionen när du arbetar utanför Azure.

## <a name="next-steps"></a>Nästa steg

Läs självstudieserien i tre delar, [Geo-replikering i Azure Container Registry](container-registry-tutorial-prepare-registry.md). Gå igenom att skapa ett geo-replikerat register samt skapa en container och sedan distribuera den med ett enda `docker push`-kommando till flera regionala Web Apps for Containers-instanser.

> [!div class="nextstepaction"]
> [Geo-replikering i Azure Container Registry](container-registry-tutorial-prepare-registry.md)
