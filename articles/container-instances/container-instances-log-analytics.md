---
title: Containerinstansloggning med Azure Monitor-loggar
description: Lär dig hur du skickar loggar från Azure Container instances till Azure Monitor loggar.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: 4099bc0b15f02faade02f47aeb00fb7c4b4a3332
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325891"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Containerinstansloggning med Azure Monitor-loggar

Log Analytics-arbetsytor är en central plats för att lagra och skicka frågor till loggdata från inte bara Azure-resurser, utan även lokala resurser och resurser i andra moln. Azure Container Instances innehåller inbyggt stöd för att skicka data till Azure Monitor-loggar.

Om du vill skicka behållar instans data till Azure Monitor loggar måste du ange ett ID och Log Analytics en nyckel för arbets ytan när du skapar en behållar grupp. I följande avsnitt beskrivs hur du skapar loggaktiverad containergrupp och frågning av loggar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera loggning i dina containerinstanser, behöver du följande:

* [Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (eller [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Hämta Log Analytics-autentiseringsuppgifter

Azure Container Instances behöver behörighet för att skicka data till din Log Analytics-arbetsyta. Om du vill ge behörighet och aktivera loggning, måste du ange ID för Log Analytics-arbetsytan och en av dess nycklar (primär eller sekundär) när du skapar containergrupp.

Gör följande för att hämta ID och den primära nyckeln för Log Analytics-arbetsytan:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Inställningar**väljer du **Avancerade inställningar**
1. Välj **Anslutna källor** > **Windows-servrar** (eller **Linux-servrar**. ID och nycklar är samma för båda)
1. Anteckna:
   * **Arbetsplats-ID**
   * **Primär nyckel**

## <a name="create-container-group"></a>Skapa containergrupp

Nu när du har Log Analytics-arbetsytans ID och primärnyckel är du redo att skapa en grupp för loggningsaktiverad containergrupp.

Följande exempel visar två sätt att skapa en behållar grupp med en enda, [flytande][fluentd] behållare: Azure CLI och Azure CLI med en YAML-mall. Fluentd-containern skapar flera rader utdata i sin standardkonfiguration. Eftersom dessa utdata skickas till din Log Analytics-arbetsyta fungerar det bra för att demonstrera visning och frågning av loggar.

### <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Om du vill distribuera med Azure CLI anger du `--log-analytics-workspace` parametrarna `--log-analytics-workspace-key` och i kommandot [AZ container Create][az-container-create] . Ersätt de två arbetsytevärdena med de värden du hämtade i föregående steg (och uppdatera resursgruppens namn) innan du kör följande kommando.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Distribuera med YAML

Använd den här metoden om du föredrar att distribuera containergrupper med YAML. Följande YAML definierar en containergrupp med en enda container. Kopiera YAML-koden till en ny fil och ersätt `LOG_ANALYTICS_WORKSPACE_ID` och `LOG_ANALYTICS_WORKSPACE_KEY` med värdena du hämtade i föregående steg. Spara filen som **deploy-aci.yaml**.

```yaml
apiVersion: 2018-10-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Kör sedan följande kommando för att distribuera behållar gruppen. Ersätt `myResourceGroup` med en resurs grupp i din prenumeration (eller skapa först en resurs grupp med namnet "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Du bör få ett svar från Azure som innehåller distributionsinformation strax efter kommandot utfärdats.

## <a name="view-logs-in-azure-monitor-logs"></a>Visa loggar i Azure Monitor-loggar

När du har distribuerat containergruppen, kan det ta flera minuter (upp till 10) för de första loggposterna att visas i Azure-portalen. Så här visar du behållar gruppens loggar:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Allmänt**väljer du **loggar**  
1. Skriv följande fråga:`search *`
1. Välj **Kör**

Du bör se flera resultat som visas av `search *`-frågan. Om du inte ser några resultat i första hand väntar du några minuter och väljer sedan **Kör** -knappen för att köra frågan igen. Som standard visas logg poster i **tabell** format. Du kan därefter expandera en rad för att visa innehållet i en enskild loggpost.

![Logga sökresultat i Azure-portalen][log-search-01]

## <a name="query-container-logs"></a>Fråga containerloggar

Azure Monitor loggar innehåller ett omfattande [frågespråk][query_lang] för att hämta information från potentiellt tusentals rader med logg data.

Azure Container Instances-loggningsagenten skickar poster till `ContainerInstanceLog_CL`-tabellen i din Log Analytics-arbetsyta. Den grundläggande strukturen i en fråga är källtabellen (`ContainerInstanceLog_CL`) följt av en serie operatorer avgränsade av vertikalstrecket (`|`). Du kan länka flera operatorer för att förfina resultatet och utför avancerade funktioner.

Om du vill se exempel på frågeresultat klistrar du in följande fråga i frågetextrutan (under Visa äldre språkkonverteraren) och välj knappen **Kör** för att köra frågan. Den här frågan visar alla loggposter vars Meddelande-fält innehåller ordet varning:

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Mer komplexa frågor stöds också. Den här frågan visar till exempel bara de loggposter för behållargruppen mycontainergroup001 som skapats den senaste timmen:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Nästa steg

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Mer information om att köra frågor mot loggar och konfigurera aviseringar i Azure Monitor-loggar finns i:

* [Förstå loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-search.md)
* [Enhetliga aviseringar i Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Övervaka containerns CPU och minne

Information om övervakning av containerinstansens CPU- och minnesresurser finns i:

* [Övervaka behållarens resurser i Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create