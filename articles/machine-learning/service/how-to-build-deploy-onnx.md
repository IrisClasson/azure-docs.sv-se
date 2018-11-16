---
title: ONNX och Azure Machine Learning | Skapa och distribuera modeller
description: Läs mer om ONNX och hur du använder Azure Machine Learning för att skapa och distribuera ONNX-modeller
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: 2e5c0e479d5564a48048b9fa9c67ad8870122601
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706066"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX och Azure Machine Learning: skapa och distribuera samverkande AI-modeller

Den [öppna Neural Network Exchange](https://onnx.ai) (ONNX)-formatet är en öppen standard för att representera machine learning-modeller. ONNX stöds av en [community med partner](https://onnx.ai/supported-tools), inklusive Microsoft, som skapar kompatibelt ramverk och verktyg. Microsoft strävar efter att öppna och samverkande AI så att datatekniker och utvecklare kan:

+ Använd ramverket för valfri för att skapa och träna modeller
+ Distribuera modeller plattformsoberoende med minimal integration arbete

Microsoft stöder ONNX över dess produkter, inklusive Azure och Windows för att hjälpa dig att uppnå dessa mål.  

## <a name="why-choose-onnx"></a>Varför ska jag välja ONNX?
Du får med ONNX samverkan gör det möjligt att få bra idéer till produktionen snabbare. Dataexperter kan ONNX välja sin önskade ramverk för jobbet. På samma sätt kan utvecklare tid och förbereder modeller för produktion och distribuera i molnet och gränsen.  

Du kan skapa ONNX-modeller från många ramverk, inklusive PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet, ML.Net, TensorFlow, Keras, lär du dig SciKit med mera.

Det finns också ett ekosystem av verktyg för att visualisera och påskynda ONNX-modeller. Det finns också ett antal förtränade ONNX-modeller för vanliga scenarier.

[ONNX-modeller kan distribueras](#deploy) till molnet med Azure Machine Learning och ONNX-Runtime. De kan också distribueras till Windows 10-enheter med hjälp av [Windows ML](https://docs.microsoft.com/windows/ai/). De kan även distribueras till andra plattformar med hjälp av konverterare som är tillgängliga från ONNX-communityn. 

[ ![ONNX flow diagram som visar utbildning, konverterare och distribuera](media/concept-onnx/onnx.png) ] (. / media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Hämta ONNX-modeller

Du kan hämta ONNX-modeller på flera olika sätt:
+ Hämta en förtränade ONNX-modell från den [ONNX modellen Zoo](https://github.com/onnx/models) (se exemplet längst ned i den här artikeln)
+ Skapa en anpassad ONNX-modell från [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Konvertera befintlig modell från ett annat format till ONNX (se exemplet längst ned i den här artikeln) 
+ Skapa en ny ONNX-modell i Azure Machine Learning-tjänsten (se exemplet längst ned i den här artikeln)

## <a name="saveconvert-your-models-to-onnx"></a>Spara/konvertera dina ONNX-modeller

Du kan konvertera befintliga modeller till ONNX eller spara dem som ONNX i slutet av utbildning.

|Ramverk för modellen|Exempel på konvertering eller verktyg|
|-----|-------|
|PyTorch|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx-konverterare](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Läs CoreML<br/>XGBoost och libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

Du hittar den senaste listan över ramverk som stöds och konverterare på den [ONNX självstudier plats](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Distribuera ONNX-modeller i Azure

Du kan använda Azure Machine Learning-tjänsten för att distribuera, hantera och övervaka ONNX-modeller. Standarden [arbetsflöde för distribution](concept-model-management-and-deployment.md) och ONNX-Runtime som du kan skapa en REST-slutpunkt som ligger i molnet. Se ett fullständigt exempel Jupyter-anteckningsbok i slutet av den här artikeln att prova själv. 

### <a name="install-and-configure-the-onnx-runtime"></a>Installera och konfigurera ONNX-körning

ONNX-Runtime är en högpresterande inferens motor för ONNX-modeller. Den levereras med en Python-API och ger maskinvaruacceleration på processor- och GPU. För närvarande stöder 1.2 ONNX-modeller och körs på Ubuntu 16.04 Linux. Båda [CPU](https://pypi.org/project/onnxruntime) och [GPU](https://pypi.org/project/onnxruntime-gpu) paket är tillgängliga på [PyPi.org](https://pypi.org).

Om du vill installera ONNX-Runtime, använder du:
```python
pip install onnxruntime
```

För att anropa ONNX-körning i Python-skriptet, använder du:
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

I dokumentationen som medföljer modellen vanligtvis visar indata och utdata för användning av modellen. Du kan också använda ett visualiseringsverktyg som [Netron](https://github.com/lutzroeder/Netron) att visa modellen. Du kan också fråga modellmetadata, in- och utdata ONNX Runtime:
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Att inferens din modell, Använd `run` och skicka in listan över utdata du vill ha returnerade (lämna tomt om du vill att alla) och en karta över indatavärdena. Resultatet är en lista över utdata.
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Fullständig API-referens, finns det [ONNX Runtime referensdokument](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Exempel distributionssteg

Här är ett exempel för att distribuera en ONNX-modell:

1. Initiera din arbetsyta för Azure Machine Learning-tjänsten. Om du inte har något ännu, lär du dig hur du skapar en arbetsyta i [snabbstarten](quickstart-get-started.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Registrera modellen med Azure Machine Learning.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Skapa en avbildning med modellen och eventuella beroenden.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   Filen `score.py` innehåller bedömnings logik och måste tas med i avbildningen. Den här filen används för att köra modellen i avbildningen. Se den här [självstudien](tutorial-deploy-models-with-aml.md#create-scoring-script) för instruktioner om hur du skapar en bedömning skript. En exempelfil för en modell för ONNX visas nedan:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   Filen `myenv.yml` beskriver de beroenden som krävs för avbildningen. Se den här [självstudien](tutorial-deploy-models-with-aml.md#create-environment-file) anvisningar om hur du skapar en miljöfil, t.ex den här exempelfilen:

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("numpy")
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Distribuera din ONNX-modell med Azure Machine Learning för att:
   + Azure Container Instances (ACI): [Lär dig hur...](how-to-deploy-to-aci.md)

   + Azure Kubernetes Service (AKS): [Lär dig hur...](how-to-deploy-to-aks.md)


## <a name="examples"></a>Exempel
 
Följande anteckningsböcker visar hur du kan skapa ONNX-modeller och distribuera dem med Azure Machine Learning: 
+ [onnx/onnx-modelzoo-aml-distribuera-resnet50.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-modelzoo-aml-deploy-resnet50.ipynb)
+ [onnx/onnx-convert-aml-distribuera-tinyyolo.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-convert-aml-deploy-tinyyolo.ipynb)
+ [onnx/onnx-Train-pytorch-AML-Deploy-mnist.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb)

Följande anteckningsböcker visar hur du distribuerar befintliga ONNX-modeller med Azure Machine Learning: 
+ [onnx/onnx-inferens-mnist-deploy.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-mnist-deploy.ipynb) 
+ [onnx/onnx-inference-facial-Expression-Recognition-Deploy.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-facial-expression-recognition-deploy.ipynb)
 
Hämta dessa anteckningsböcker:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Mer information

Mer information om ONNX eller bidra till projektet:
+ [ONNX projektwebbplatsen](https://onnx.ai)

+ [ONNX-kod på GitHub](https://github.com/onnx/onnx)
