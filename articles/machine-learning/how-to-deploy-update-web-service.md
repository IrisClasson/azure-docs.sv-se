---
title: Uppdatera en distribuerad WebService
author: gvashishtha
ms.service: machine-learning
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 66c53c7485041ec9abaf72396efcfa3325a13732
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799914"
---
# <a name="update-a-deployed-web-service"></a>Uppdatera en distribuerad webb tjänst

Den här artikeln visar hur du distribuerar en webb tjänst som har distribuerats med Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du redan har distribuerat en webb tjänst med Azure Machine Learning. [Följ dessa steg](how-to-deploy-and-where.md)om du behöver lära dig hur du distribuerar en webb tjänst.

## <a name="update-web-service"></a>Uppdatera webbtjänst

Använd-metoden för att uppdatera en webb tjänst `update` . Du kan uppdatera webb tjänsten så att den använder en ny modell, ett nytt registrerings skript eller nya beroenden som kan anges i en konfiguration för en konfiguration. Mer information finns i dokumentationen för [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

Se [AKS-tjänstens uppdaterings metod.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Se [ACI-tjänstens uppdaterings metod.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> När du skapar en ny version av en modell måste du manuellt uppdatera varje tjänst som du vill använda.
>
> Du kan inte använda SDK för att uppdatera en webb tjänst som publicerats från Azure Machine Learning designer.

**Med SDK**

Följande kod visar hur du använder SDK för att uppdatera modell-, miljö-och registrerings skriptet för en webb tjänst:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Använda CLI**

Du kan också uppdatera en webb tjänst med hjälp av ML CLI. I följande exempel visas hur du registrerar en ny modell och sedan uppdaterar en webb tjänst så att den använder den nya modellen:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> I det här exemplet används ett JSON-dokument för att skicka modell informationen från registrerings kommandot till kommandot Update.
>
> Om du vill uppdatera tjänsten för att använda ett nytt start skript eller en ny miljö skapar du en [konfigurations fil för en konfigurations fil](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema) och anger den med `ic` parametern.

Mer information finns i dokumentationen om [AZ ml-tjänst uppdatering](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .

## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Använd TLS för att skydda en webb tjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)
