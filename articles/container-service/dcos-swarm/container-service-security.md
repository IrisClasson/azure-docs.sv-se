---
title: FÖRÅLDRAD Behållar säkerhet i Azure Container Service
description: Att tänka på när du skyddar Docker-behållare som distribuerats i Azure Container Service och relaterade Azure-tjänster.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/28/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d90d872175febf775e7d0892e133883f1a8ff8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552400"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>FÖRÅLDRAD Skydda Docker-behållare i Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Den här artikeln innehåller överväganden och rekommendationer som hjälper dig att skydda Docker-behållare som distribueras i Azure Container Service. Många av dessa överväganden och rekommendationer gäller generellt för Docker-containrar som distribueras i Azure och i andra miljöer. 

## <a name="image-security"></a>Avbildningssäkerhet

Container skapas från avbildningar som lagras i en eller flera databaser. Dessa databaser kan höra till offentliga eller privata containerregister. Ett exempel på ett offentligt register är [Docker Hub](https://hub.docker.com/). Ett exempel på ett privat register är [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), som kan installeras lokalt eller i ett virtuellt privat moln. Det finns också molnbaserade tjänster för privata registerbehållare, t.ex. [Azure Container Registry](../../container-registry/container-registry-intro.md).

### <a name="public-and-private-images"></a>Offentliga och privata avbildningar
Normalt, som med andra offentligt publicerade programvarupaket, är säkerheten i en offentligt tillgänglig containeravbildning inte garanterad. Containeravbildningar består av flera programvarulager och varje programvarulager kan medföra säkerhetsrisker. Det är viktigt att känna till containeravbildningens ursprung, inklusive avbildningens ägare (för att fastställa om det är en pålitlig källa eller inte), de programvarulager som den består av samt programvaruversionerna. 

Du kan till exempel gå till det officiella [nginx-centrallagret](https://hub.docker.com/_/nginx/) i Docker Hub och navigera till fliken **Taggar**, så ser du färgkodade säkerhetsrisker i varje avbildning. Varje färg representerar säkerhetsrisken i ett programvarulager i avbildningen. Mer information om hur du granskar säkerhetsrisker i Docker Hub finns i [Understanding official repos on Docker Hub](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/) (Så här fungerar officiella databaser i Docker Hub).

![Nginx-avbildningar i Docker Hub](./media/container-service-security/docker-hub-nginx.png)

Säkerheten har högsta prioritet på de flesta företag, och för att skydda sig mot säkerhetsangrepp bör de lagra och hämta avbildningar från ett privat register, t.ex. Azure Container Registry eller Docker Trusted Registry. Förutom att tillhandahålla ett hanterat privat register stöder Azure Container Registry [autentisering baserat på tjänstens huvudnamn](../../container-registry/container-registry-authentication.md) via Azure Active Directory för grundläggande autentiseringsflöden, inklusive rollbaserad åtkomst för läs-, skriv- och ägarbehörigheter.

### <a name="image-security-scanning"></a>Säkerhetsgenomsökning för avbildningar

Även om du använder ett privat register är det en bra idé att använda lösningar för avbildningsgenomsökning som en extra säkerhetskontroll. Varje programvarulager i en behållaravbildning kan potentiellt medföra säkerhetsrisker oberoende av andra lager i containeravbildningen. I takt med att företag i allt högre utsträckning börjar distribuera sina produktionsarbetsbelastningar baserat på containertekniker, blir avbildningsgenomsökning en allt viktigare faktor för att förebygga säkerhetshot mot dessa organisationer. 

Lösningar för säkerhetsövervakning och säkerhetsgenomsökning som till exempel [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) och [Aqua Security](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry) kan användas för att söka igenom containeravbildningar i ett privat register och för att identifiera potentiella säkerhetsrisker. Det är viktigt att veta hur djupt olika genomsökningslösningar går. Till exempel kanske vissa lösningar bara verifierar avbildningslager mot kända säkerhetsrisker. Dessa lösningar kanske inte kan verifiera programvara med avbildningslager som byggts med särskilda pakethanterarprogram. Andra lösningar har djupare genomsökningsintegration och kan hitta sårbarheter i all paketerad programvara.

### <a name="production-deployment-rules-and-audit"></a>Regler för och granskning av distributioner i produktionsmiljön
När ett program har distribuerats i produktionsmiljön är det viktigt att regler tillämpas som säkerställer att avbildningar som används i produktionsmiljön är säkra och att de inte har några sårbarheter.

* Som regel bör avbildningar med säkerhetsrisker, även mindre sådana, inte tillåtas att köra i en produktionsmiljö. Dessutom bör alla avbildningar som distribueras i produktionsmiljön helst sparas i ett privat register som är tillgängligt för några få utvalda användare. Det är också viktigt att hålla nere antalet produktionsavbildningar så att de kan hanteras effektivt.

* Eftersom det är svårt att fastställa ursprunget för programvara från en offentligt tillgänglig containeravbildning, är det ofta bäst att skapa avbildningar från källan så att lagrets ursprung är känt. När en säkerhetsrisk identifieras i en egenutvecklad containeravbildning är det lättare för kunderna att snabbt komma fram till en lösning. Med offentliga avbildningar måste kunderna hitta roten till den offentliga avbildningen för att kunna åtgärda den, eller få en annan säker avbildning från utgivaren.

* En noggrant genomsökt avbildning som distribuerats i en produktionsmiljö är inte nödvändigtvis uppdaterad under hela programmets livslängd. Säkerhetsproblem kan rapporteras för lager i avbildningen som inte tidigare var kända eller som uppstått efter distributionen i produktionsmiljön. Det är viktigt att regelbundet granska avbildningar som har distribuerats i en produktionsmiljö för att identifiera avbildningar som är inaktuella eller som inte har uppdaterats på ett tag. Blue-green-distributionsmetoder och mekanismer för löpande uppgradering kan användas för att uppdatera containeravbildningar utan driftavbrott. Avbildningsgenomsökningar kan göras med verktygen som beskrivs i föregående avsnitt. 

* En pipeline för kontinuerlig integration (CI) för generering av avbildningar och integrerad säkerhetsgenomsökning gör det lättare att upprätthålla säkra privata register med säkra containeravbildningar. Den inbyggda säkerhetsgenomsökningen i CI-lösningen ser till att avbildningar som klarar alla testerna skickas till det privata registret som produktionsarbetsbelastningarna distribueras från. Ett fel i CI-pipelinen gör att avbildningar med identifierade säkerhetsrisker inte skickas till det privata registret som används för distribution av produktionsarbetsbelastningar. Dessutom körs säkerhetsgenomsökningar av avbildningarna automatiskt om det finns ett stort antal avbildningar. Att manuellt söka efter säkerhetsrisker i avbildningar kan vara en enormt tidskrävande och felbenägen uppgift.

## <a name="host-level-container-isolation"></a>Containerisolering på värdnivå
När en kund distribuerar containerprogram på Azure-resurser distribueras de på en prenumerationsnivå i resursgrupper och är inte konfigurerade för flera klientorganisationer. Det innebär att om en kund delar en prenumeration med andra, så går det inte att skapa några gränser mellan två distributioner i samma prenumeration. Det betyder i sin tur att det inte går att garantera säkerhet på containernivå. 

Det är också viktigt att förstå att behållare delar värdens kernel och resurser (som i Azure Container Service är en virtuell Azure-dator i ett kluster). Av den anledningen måste containrar som körs i en produktionsmiljö köras i icke-privilegierat användarläge. Om en container körs med rotprivilegier kan hela miljön komprometteras. Med åtkomst på rotnivå i en container kan en hackare få åtkomst till alla rotprivilegier på värden. Det är också viktigt att containrar körs med skrivskyddade filsystem. Detta förhindrar att någon som har åtkomst till den komprometterade containern kan skriva skadliga skript till filsystemet och få tillgång till andra filer. På motsvarande sätt är det viktigt att begränsa de resurser (till exempel minne, processor och nätverkets bandbredd) som allokeras till en container. Detta bidrar till att förhindra hackare från ta upp-resurser och med illegala aktiviteter som kredit korts bedrägerier eller bit-/utvinnar utvinning, vilket kan förhindra att andra behållare körs på värden eller klustret.

## <a name="orchestrator-considerations"></a>Orkestreringsöverväganden

Varje orkestrering som är tillgänglig i Azure Container Service har sina egna säkerhetsöverväganden. Till exempel bör direkt SSH-åtkomst begränsas till orkestreringsnoder i Container Service. I stället bör du använda varje orkestrerings användargränssnitt eller kommandoradsverktyg (t.ex. `kubectl` för Kubernetes) för att hantera containermiljön utan att använda värdarna.

Ytterligare orkestreringsspecifik säkerhetsinformation finns i följande resurser:

* **Kubernetes**: [Security Best Practices for Kubernetes Deployment](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/) (Säkerhetsrekommendationer för Kubernetes-distributioner)

* **DC/OS**: [Securing Your Cluster](https://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster) (Skydda ditt kluster)

* **Docker Swarm**: [Docker Security](https://www.docker.com/docker-security) (Säkerhet i Docker)

## <a name="next-steps"></a>Nästa steg

* Mer information om arkitekturen och behållarsäkerheten i Docker finns i [Introduction to Container Security](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf) (Introduktion till behållarsäkerhet).

* Information om säkerheten i Azure-plattformen finns i [Azure Security Center](https://www.microsoft.com/trustcenter/cloudservices/azure).
