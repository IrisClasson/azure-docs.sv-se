---
title: Skapa automatiska ML-experiment
titleSuffix: Azure Machine Learning
description: Automatisk maskin inlärning väljer en algoritm åt dig och genererar en modell som är klar för distribution. Läs om de alternativ som du kan använda för att konfigurera automatiserade maskin inlärnings experiment.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 08/10/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 05a70274e075ddda8770e57c71a7f55807cf3dcc
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182144"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurera automatiserade ML-experiment i Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här guiden får du lära dig hur du definierar olika konfigurations inställningar för dina automatiserade maskin inlärnings experiment med [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatisk maskin inlärning väljer en algoritm och sina egenskaper för dig och genererar en modell som är klar för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskin inlärnings experiment.

Om du vill visa exempel på automatiserade Machine Learning-experiment, se [Självstudier: träna en klassificerings modell med automatiserad maskin inlärning](tutorial-auto-train-models.md) eller [träna modeller med automatiserad maskin inlärning i molnet](how-to-auto-train-remote.md).

Konfigurations alternativ som är tillgängliga i Automatisk maskin inlärning:

* Välj experiment typ: klassificering, regression eller tids serie prognoser
* Data källa, format och hämta data
* Välj ditt beräknings mål: lokal eller fjärran sluten
* Automatiserade experiment inställningar för Machine Learning
* Köra ett automatiserat maskin inlärnings experiment
* Utforska modell mått
* Registrera och distribuera modell

Om du föredrar en ingen kod upplevelse kan du också [skapa dina automatiserade maskin inlärnings experiment i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Krav

För den här artikeln behöver du 
* En Azure Machine Learning-arbetsyta. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning arbets yta](how-to-manage-workspace.md).

* Azure Machine Learning python SDK har installerats.
    Om du vill installera SDK kan du antingen 
    * Skapa en beräknings instans som automatiskt installerar SDK och som är förkonfigurerat för ML-arbetsflöden. Se [Vad är en Azure Machine Learning beräknings instans?](concept-compute-instance.md#managing-a-compute-instance) för mer information. 

    * [Installera SDK själv](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Se bara till att inkludera `automl` extra. 

## <a name="select-your-experiment-type"></a>Välj experimenttyp

Innan du påbörjar experimentet bör du bestämma vilken typ av Machine Learning-problem som du löser. Automatiserad Machine Learning stöder aktivitets typer av `classification` , `regression` och `forecasting` . Läs mer om [aktivitets typer](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Följande kod använder- `task` parametern i `AutoMLConfig` konstruktorn för att ange experiment typen som `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Data källa och format

Automatisk Machine Learning stöder data som finns på ditt lokala skriv bord eller i molnet, till exempel Azure Blob Storage. Data kan läsas i en Pandas- **DataFrame** eller en **Azure Machine Learning TabularDataset**. [Läs mer om data uppsättningar](how-to-create-register-datasets.md).

Krav för tränings data:
- Data måste vara i tabell form.
- Värdet som ska förutsäga, mål kolumnen, måste finnas i datan.

**För fjärrexperiment**måste inlärnings data vara tillgängliga från fjärrdatorn. AutoML accepterar bara [Azure Machine Learning TabularDatasets](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) när du arbetar med en fjärrberäkning. 

Azure Machine Learning data uppsättningar visar funktioner för att:

* Överför enkelt data från statiska filer eller URL-källor till din arbets yta.
* Gör dina data tillgängliga för att träna skript när de körs på moln beräknings resurser. Se [hur du tränar med data uppsättningar](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) för att få ett exempel på hur du använder `Dataset` klassen för att montera data till fjärrdatorns beräknings mål.

Följande kod skapar en TabularDataset från en webb-URL. Se [skapa en TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) för kod exempel för hur du skapar data uppsättningar från andra källor som lokala filer och data lager.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**För lokala Compute-experiment**rekommenderar vi Pandas-dataframes för snabbare bearbetnings tider.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Utbildning, verifiering och test av data

Du kan ange separata **inlärnings-och validerings uppsättningar** direkt i `AutoMLConfig` konstruktorn. Läs mer om [hur du konfigurerar data delningar och kors validering](how-to-configure-cross-validation-data-splits.md) för dina AutoML experiment. 

Om du inte uttryckligen anger en `validation_data` -eller `n_cross_validation` -parameter, använder AutoML standard tekniker för att avgöra hur valideringen utförs. Den här bestämningen beror på antalet rader i data uppsättningen som har tilldelats till din `training_data` parameter. 

|Tränings &nbsp; data &nbsp; storlek| Verifierings teknik |
|---|-----|
|**Större &nbsp; än &nbsp; 20 000 &nbsp; rader**| Data delning för träna/verifiering används. Standardvärdet är att ta 10% av den inledande tränings data uppsättningen som validerings uppsättningen. I sin tur används validerings uppsättningen för mått beräkning.
|**Mindre &nbsp; än &nbsp; 20 000 &nbsp; rader**| Metoden för kors validering används. Standard antalet vikningar beror på antalet rader. <br> **Om data uppsättningen är mindre än 1 000 rader**används 10 vikning. <br> **Om raderna är mellan 1 000 och 20 000**används tre vik.

För tillfället måste du ange dina egna **test data** för utvärdering av modeller. Ett kod exempel på hur du får dina egna test data för modell utvärdering finns i avsnittet **test** i [den här Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Härnäst bestämmer var modellen ska tränas. Ett automatiskt experiment med Machine Learning-utbildning kan köras med följande beräknings alternativ. Lär dig [om-och nack delar med lokala och fjärranslutna beräknings](concept-automated-ml.md#local-remote) alternativ. 

* Din **lokala** dator, till exempel ett lokalt skriv bord eller en bärbar dator – vanligt vis när du har en liten data uppsättning och du fortfarande befinner dig i utforsknings fasen. Se [den här antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) för ett lokalt Compute-exempel. 
 
* En **fjärrdator** i molnet – [Azure Machine Learning hanterad beräkning](concept-compute-target.md#amlcompute) är en hanterad tjänst som gör det möjligt att träna maskin inlärnings modeller på kluster av virtuella Azure-datorer. 

    Se [den här antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) för ett fjärrexempel med Azure Machine Learning Managed Compute. 

* Ett **Azure Databricks kluster** i din Azure-prenumeration. Du hittar mer information här – [konfigurera Azure Databricks kluster för automatisk ml](how-to-configure-environment.md#azure-databricks). Se den här [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) för exempel på antecknings böcker med Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurera dina experiment inställningar

Det finns flera alternativ som du kan använda för att konfigurera ditt automatiserade experiment för maskin inlärning. Dessa parametrar anges genom att instansiera ett `AutoMLConfig` objekt. Se [AutoMLConfig-klassen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) för en fullständig lista över parametrar.

Några exempel är:

1. Klassificerings experiment med AUC viktat som primärt mått med experiment tids gräns minuter angivet till 30 minuter och 2 kors validerings vikning.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. I följande exempel är ett Regressions experiment inställt på att sluta efter 60 minuter med fem verifierings kors veck.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. Prognosticering av uppgifter kräver ytterligare konfiguration. mer information finns i artikeln [Auto-träna en Time-Series prognos modell](how-to-auto-train-forecast.md) . 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Modeller som stöds

Automatisk Machine Learning försöker med olika modeller och algoritmer under Automation-och fin justerings processen. Som användare behöver du inte ange algoritmen. 

De tre olika `task` parameter värdena (den tredje aktivitets typen är `forecasting` och använder en liknande algoritm som `regression` aktiviteter) för att fastställa listan över algoritmer, modeller, som ska användas. Använd `allowed_models` parametrarna eller `blocked_models` för att ytterligare ändra iterationer med tillgängliga modeller som ska tas med eller undantas. Listan över modeller som stöds finns i SupportedModels- [klassen](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) för [klassificering](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Prognosticering](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)och [regression](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression).


### <a name="primary-metric"></a>Primärt mått
`primary metric`Parametern bestämmer det mått som ska användas vid modell träning för optimering. Tillgängliga mått som du kan välja bestäms av den aktivitets typ som du väljer och följande tabell visar giltiga primära mått för varje aktivitets typ.

Lär dig mer om de olika definitionerna av dessa mått i [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).

|Klassificering | Regression | Prognostisering för tidsserier
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>Data funktionalisering

I varje automatiserad maskin inlärnings experiment skalas dina data automatiskt och normaliseras för att hjälpa *vissa* algoritmer som är känsliga för funktioner som är i olika skalor. Den här skalningen och normaliseringen kallas funktionalisering. Mer information och kod exempel finns i [funktionalisering i AutoML](how-to-configure-auto-features.md#) . 

När du konfigurerar experiment i `AutoMLConfig` objektet kan du aktivera/inaktivera inställningen `featurization` . I följande tabell visas de accepterade inställningarna för funktionalisering i [AutoMLConfig-objektet](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Funktionalisering-konfiguration | Beskrivning |
| ------------- | ------------- |
|`"featurization": 'auto'`| Anger att [data guardrails och funktionalisering-steg](how-to-configure-auto-features.md#featurization) utförs automatiskt när en del av förbearbetningen. **Standardinställning**.|
|`"featurization": 'off'`| Anger att funktionalisering-steget inte ska göras automatiskt.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Anger att det anpassade funktionalisering-steget ska användas. [Lär dig hur du anpassar funktionalisering](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Automatiserade funktionalisering-steg för Machine Learning (funktions normalisering, hantering av data som saknas, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma funktionalisering-steg som tillämpades under träningen på dina indata automatiskt.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Konfiguration av Ensemble

Ensemble-modeller är aktiverade som standard och visas som de slutliga körnings iterationerna i en AutoML-körning. För närvarande stöds **VotingEnsemble** och **StackEnsemble** . 

Röstningen implementerar mjuk röstning som använder viktade medelvärden. Stack implementeringen använder en implementering på två nivåer, där det första lagret har samma modeller som en ensemble-Ensemble, och den andra skikt modellen används för att hitta den optimala kombinationen av modeller från det första lagret. 

Om du använder ONNX-modeller **eller** om modell förklarande är aktiverat, är stackning inaktive rad och endast röstning används.

Ensemble-utbildning kan inaktive ras `enable_voting_ensemble` med `enable_stack_ensemble` parametrarna och booleska.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Om du vill ändra standard-Ensemble-beteendet finns det flera standard argument som kan anges som `kwargs` i ett `AutoMLConfig` objekt.

> [!IMPORTANT]
>  Följande parametrar är inte explicita parametrar för AutoMLConfig-klassen. 

* `ensemble_download_models_timeout_sec`: Vid genereringen av **VotingEnsemble** och **StackEnsemble** -modellen laddas flera monterade modeller från de tidigare underordnade körningarna ned. Om du stöter på det här felet `AutoMLEnsembleException: Could not find any models for running ensembling` kan du behöva ange mer tid för att modeller ska laddas ned. Standardvärdet är 300 sekunder för nedladdning av dessa modeller parallellt och det finns ingen maximal tids gräns. Konfigurera den här parametern med ett högre värde än 300 sekunder, om mer tid behövs. 

  > [!NOTE]
  >  Om tids gränsen har nåtts och det finns hämtade modeller, fortsätter ensembling med så många modeller som den har laddat ned. Det krävs inte att alla modeller måste laddas ned för att slutföras inom tids gränsen.

Följande parametrar gäller endast för **StackEnsemble** -modeller: 

* `stack_meta_learner_type`: meta-eleven är en modell som är utbildad i resultatet av de enskilda heterogena modellerna. Standard-metadata är `LogisticRegression` för klassificerings aktiviteter (eller `LogisticRegressionCV` om kors validering är aktiverat) och `ElasticNet` för Regressions-/prognos aktiviteter (eller `ElasticNetCV` om kors validering är aktive rad). Den här parametern kan vara en av följande strängar:,,,,, `LogisticRegression` `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` eller `LinearRegression` .

* `stack_meta_learner_train_percentage`: anger den del av inlärnings uppsättningen (när du väljer tåg-och validerings typ för utbildning) som ska reserveras för att träna meta-eleven. Standardvärdet är `0.2` . 

* `stack_meta_learner_kwargs`: valfria parametrar som ska skickas till initieraren för meta-eleven. Dessa parametrar och parameter typer speglar parametrar och parameter typer från motsvarande modell-konstruktor och vidarebefordras till modell-konstruktorn.

Följande kod visar ett exempel på hur du anger anpassade Ensemble-beteenden i ett `AutoMLConfig` objekt.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

## <a name="run-experiment"></a>Kör experiment

För automatisk ML skapar du ett `Experiment` -objekt, som är ett namngivet objekt i en som `Workspace` används för att köra experiment.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Skicka experimentet för att köra och generera en modell. Skicka `AutoMLConfig` till- `submit` metoden för att generera modellen.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Beroenden installeras först på en ny dator.  Det kan ta upp till 10 minuter innan utdata visas.
>Inställning `show_output` till `True` resultat i utdata som visas i-konsolen.

 <a name="exit"></a> 

### <a name="exit-criteria"></a>Avslutnings villkor

Det finns några alternativ som du kan definiera för att avsluta experimentet.

|Kriterie| beskrivning
|----|----
Inga &nbsp; kriterier | Om du inte definierar några avslutnings parametrar fortsätter experimentet tills inga ytterligare framsteg görs på ditt primära mått.
Efter &nbsp; en &nbsp; viss &nbsp; &nbsp; tid| Använd `experiment_timeout_minutes` i dina inställningar för att definiera hur lång tid, i minuter, som experimentet ska fortsätta att köras. <br><br> För att undvika tids brist på experiment, det finns minst 15 minuter eller 60 minuter om raden efter kolumn storlek överskrider 10 000 000.
&nbsp;Poängen &nbsp; har &nbsp; &nbsp; nåtts| Använd `experiment_exit_score` Slutför experimentet när en angiven primär mått Poäng har nåtts.

## <a name="explore-models-and-metrics"></a>Utforska modeller och mått

Du kan visa dina utbildnings resultat i en widget eller infogad om du befinner dig i en bärbar dator. Se [spåra och utvärdera modeller](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) för mer information.

Se [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md) för definitioner och exempel på prestanda diagram och mått som tillhandahålls för varje körning. 

För att få en funktionalisering-Sammanfattning och förstå vilka funktioner som har lagts till i en viss modell, se [funktionalisering Transparency](how-to-configure-auto-features.md#featurization-transparency). 

## <a name="register-and-deploy-models"></a>Registrera och distribuera modeller

Information om hur du hämtar eller registrerar en modell för distribution till en webb tjänst finns i [hur och var du distribuerar en modell](how-to-deploy-and-where.md).


<a name="explain"></a>

## <a name="model-interpretability"></a>Modelltolkning

Med modell tolkning kan du förstå varför dina modeller har gjort förutsägelser och de underliggande funktions prioritets värdena. SDK: n innehåller olika paket för att aktivera modell tolknings funktioner, både vid utbildning och drift störningar, för lokala och distribuerade modeller.

Se anvisningar [för kod exempel för hur](how-to-machine-learning-interpretability-automl.md) du aktiverar tolknings funktioner specifikt inom automatiserade maskin inlärnings experiment.

Allmän information om hur modell förklaringar och funktions prioritet kan aktive ras på andra områden i SDK utanför Automatisk maskin inlärning finns i avsnittet [begrepp](how-to-machine-learning-interpretability.md) om tolkning.

> [!NOTE]
> ForecastTCN-modellen stöds för närvarande inte av förklarings klienten. Den här modellen returnerar ingen förklarings instrument panel om den returneras som den bästa modellen och inte stöder förklarings körningar på begäran.

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [hur och var du distribuerar en modell](how-to-deploy-and-where.md).

+ Lär dig mer om [hur du tränar en Regressions modell med automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar användning av automatiserad maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).

+ Lär dig hur du tränar flera modeller med AutoML i [många modeller lösnings acceleratorer](https://aka.ms/many-models).
