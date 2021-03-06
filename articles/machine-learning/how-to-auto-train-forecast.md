---
title: Automatisk träna en tids serie prognos modell
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att träna en Regressions Regressions modell i Time Series med hjälp av automatisk maskin inlärning.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 9b81dbce9f73c76ceea0f7842d731d00f905fb01
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371523"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatisk träna en tids serie prognos modell
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du konfigurerar och tränar en uppskattnings Regressions modell i tids serier med hjälp av automatisk maskin inlärning i [Azure Machine Learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py). 

En låg kod upplevelse finns i [självstudien: prognostisera efter frågan med automatiserad maskin inlärning](tutorial-automated-ml-forecast.md) för ett exempel på en uppskattning av tids serier med hjälp av automatisk maskin inlärning i [Azure Machine Learning Studio](https://ml.azure.com/).

Att konfigurera en prognos modell liknar att konfigurera en standard Regressions modell med hjälp av automatisk maskin inlärning, men vissa konfigurations alternativ och för bearbetnings steg finns för att arbeta med Time Series-data. 

Du kan till exempel [Konfigurera](#config) hur långt fram till framtiden prognosen ska utsträckas (prognos Horisont) samt lags med mera. Med automatisk ML får du en enda, men ofta ingrenad modell för alla objekt i data uppsättningen och förutsägelserna. Mer data är därför tillgängliga för att uppskatta modell parametrar och generalisering till osett-serien blir möjlig.

I följande exempel visas hur du:

* Förbereda data för tids serie modellering
* Konfigurera angivna parametrar för tids serier i ett [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objekt
* Köra förutsägelser med Time Series-data

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Till skillnad från klassiska Time Series-metoder, som är "pivoterade", har tidigare tids serie värden "pivoted" för att bli ytterligare dimensioner för modellerings regressor tillsammans med andra förutsägelser. Den här metoden omfattar flera sammanhangsbaserade variabler och deras relation till varandra under utbildningen. Eftersom flera faktorer kan påverka en prognos justeras den här metoden korrekt med verkliga prognos scenarier. Till exempel, när försäljnings prognoser används, interaktioner över historiska trender, utbytes pris och pris alla gemensamt styr försäljnings resultatet. 

Funktioner som har extraherats från tränings data spelar en viktig roll. Och automatiserade ML utför standard för bearbetnings steg och genererar ytterligare tids serie funktioner för att fånga säsongs effekter och maximera förutsägelse noggrannhet

## <a name="time-series-and-deep-learning-models"></a>Tids serie-och djup inlärnings modeller

Med automatiserad MLs djup inlärning kan du prognostisera univariate-och multivarierad Time Series-data.

Djup inlärnings modeller har tre inbyggda funktioner:
1. De kan lära sig från valfria mappningar från indata till utdata
1. De stöder flera indata och utdata
1. De kan automatiskt extrahera mönster i indata som sträcker sig över långa sekvenser

Med större data kan djup inlärnings modeller, till exempel Microsofts ForecastTCN, förbättra poängen i den resulterande modellen. Lär dig hur du [konfigurerar experimentet för djup inlärning](#configure-a-dnn-enable-forecasting-experiment).

Med automatisk ML får användare både interna Time-och djup inlärnings modeller som en del av rekommendations systemet. 

Modeller| Beskrivning | Fördelar
----|----|---
Prophet (för hands version)|Prophet fungerar bäst med tids serier som har starka säsongs effekter och flera säsonger av historiska data. Om du vill utnyttja den här modellen installerar du den lokalt med `pip install fbprophet` . | Korrekt & snabb, robust för att kunna avvika, saknade data och dramatiska ändringar i din tids serie.
Auto-ARIMA (för hands version)|Autoregressivt Integrated glidande medelvärde (ARIMA) fungerar bäst när data är Station ära. Det innebär att dess statistiska egenskaper, t. ex. medelvärdet och var Ian sen är konstant över hela uppsättningen. Om du till exempel vänder en mynt är sannolikheten för att du får 50%, oavsett om du vänder idag, imorgon eller nästa år.| Perfekt för univariate-serien, eftersom de tidigare värdena används för att förutsäga framtida värden.
ForecastTCN (för hands version)| ForecastTCN är en neurala-nätverks modell som är utformad för att ta itu med de mest krävande prognos uppgifterna, vilket fångar icke-linjära lokala och globala trender i dina data samt relationer mellan tids serier.|Kan använda komplexa trender i dina data och skalas enkelt till största av data uppsättningar.

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Information om hur du skapar arbets ytan finns i [skapa en Azure Machine Learning arbets yta](how-to-manage-workspace.md).
* I den här artikeln förutsätter vi att du har konfigurerat ett automatiserat experiment för maskin inlärning. Följ [själv studie kursen](tutorial-auto-train-models.md) eller [anvisningar](how-to-configure-auto-train.md) för att se design mönster för det grundläggande automatiserade maskin inlärnings experimentet.

## <a name="preparing-data"></a> Förbereda data

Den viktigaste skillnaden mellan en typ av Regressions Regressions typ och Regressions aktivitet i Automatisk maskin inlärning är bland annat en funktion i dina data som representerar en giltig tids serie. En vanlig tids serie har en väldefinierad och konsekvent frekvens och har ett värde vid varje exempel punkt i ett kontinuerligt tidsintervall. Överväg följande ögonblicks bild av en fil `sample.csv` .

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```

Den här data uppsättningen är ett enkelt exempel på dagliga försäljnings data för ett företag som har två olika butiker, A och B. Dessutom finns det en funktion för `week_of_year` som gör att modellen kan identifiera vecko säsongs beroende. Fältet `day_datetime` representerar en ren tids serie med den dagliga frekvensen och fältet `sales_quantity` är mål kolumnen för att köra förutsägelser. Läs data till en Pandas-dataframe och Använd sedan `to_datetime` funktionen för att se till att tids serien är av `datetime` typen.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

I det här fallet är data redan sorterade stigande efter fältet Time `day_datetime` . Men när du konfigurerar ett experiment ser du till att kolumnen önskad tid sorteras i stigande ordning för att skapa en giltig tids serie. Anta att data innehåller 1 000 poster och gör en deterministisk delning i data för att skapa utbildnings-och test data uppsättningar. Identifiera etikettens kolumn namn och ange den som etikett. I det här exemplet är etiketten `sales_quantity` . Separera sedan etikett fältet från `test_data` för att skapa `test_target` uppsättningen.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> När du tränar en modell för att förutsäga framtida värden kan du se till att alla funktioner som används i träningen kan användas när du kör förutsägelser för din avsedda horisont. När du skapar en prognos för efter frågan kan du till exempel öka inlärnings precisionen med en funktion för det aktuella lager priset. Men om du planerar att prognostisera med lång horisont kanske du inte kan förutsäga framtida lager värden som motsvarar framtida tids serie punkter och modell precisionen kan bli lidande.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Träna och verifiera data
Du kan ange separata tåg-och validerings uppsättningar direkt i `AutoMLConfig` konstruktorn.

### <a name="rolling-origin-cross-validation"></a>Kors validering av rullande ursprung
För tids serie prognoser med rullande ursprung mellan validering (ROCV) används för att dela tids serier på ett temporalt konsekvent sätt. ROCV delar upp serien i utbildning och validerings data med hjälp av en start tid. När du drar ursprunget i tiden genererar det kors validerings vecket.  

![alternativ text](./media/how-to-auto-train-forecast/ROCV.svg)

Den här strategin bevarar tids seriens data integritet och eliminerar risken för data läckage. ROCV används automatiskt för att förutse uppgifter genom att skicka utbildningen och verifierings data tillsammans och ange antalet kors validerings veck med `n_cross_validations` . Läs mer om hur automatisk ML använder kors validering för att [förhindra överanpassnings modeller](concept-manage-ml-pitfalls.md#prevent-over-fitting).

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
Läs mer om [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Konfigurera och kör experiment

För prognos uppgifter använder automatisk maskin inlärning för bearbetning och beräknings steg som är aktuella för Time Series-data. Följande steg för bearbetning utförs:

* Identifiera exempel frekvensen för tids serier (till exempel varje timme, varje dag, varje vecka) och skapa nya poster för frånvaro tids punkter för att göra serien kontinuerlig.
* Ange värden som saknas i målet (via Forward-Fill) och funktions kolumner (med kolumn värden i median)
* Skapa funktioner baserade på Time Series-identifierare för att aktivera fasta effekter i olika serier
* Skapa tidsbaserade funktioner för att hjälpa till med utbildnings säsongs mönster
* Koda kategoriska-variabler till numeriska kvantiteter

[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)Objektet definierar de inställningar och data som krävs för en automatiserad maskin inlärnings uppgift. Precis som med ett Regressions problem definierar du standard utbildnings parametrar som aktivitets typ, antal iterationer, tränings data och antalet kors valideringar. För prognos uppgifter finns det ytterligare parametrar som måste anges som påverkar experimentet. I följande tabell beskrivs varje parameter och dess användning.

| Parameter &nbsp; namn | Beskrivning | Krävs |
|-------|-------|-------|
|`time_column_name`|Används för att ange kolumnen datetime i de indata som används för att bygga tids serien och härleda dess frekvens.|✓|
|`time_series_id_column_names`|Kolumn namn som används för att unikt identifiera tids serier i data som har flera rader med samma tidsstämpel. Om Time Series-identifierare inte har definierats antas data uppsättningen vara en tids serie.||
|`forecast_horizon`|Definierar hur många perioder som vidarebefordrar dig till prognos. Horisonten är i enheter av tids serie frekvensen. Enheter baseras på tidsintervallet för dina utbildnings data, till exempel varje månad, varje vecka att prognosen ska förutsäga.|✓|
|`target_lags`|Antal rader att ange för fördröjning av målvärdena baserat på data frekvensen. Fördröjningen visas som en lista eller ett enda heltal. Fördröjning ska användas när relationen mellan oberoende variabler och beroende variabel inte matchar eller korrelerar som standard. När du till exempel försöker prognostisera efter frågan för en produkt kan efter frågan i någon månad bero på priset för vissa råvaruer 3 månader tidigare. I det här exemplet kanske du vill ange ett negativt värde för målet (efter frågan) med tre månader, så att modellen är en utbildning på rätt relation.||
|`target_rolling_window_size`|*n* historiska perioder som ska användas för att generera prognostiserade värden <= storlek för tränings uppsättning. Om det utelämnas är *n* den fullständiga inlärnings uppsättningens storlek. Ange den här parametern när du bara vill ta hänsyn till en viss mängd historik när du tränar modellen.||
|`enable_dnn`|Aktivera Prognosticering av Hyperoptimerade.||

Mer information finns i [referens dokumentationen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Skapa tids serie inställningarna som ett Dictionary-objekt. Ange `time_column_name` till `day_datetime` fältet i data uppsättningen. Definiera `time_series_id_column_names` parametern för att se till att **två separata tids serie grupper** skapas för data, en för Store A och B. Ange `forecast_horizon` till 50 för att förutsäga för hela test uppsättningen. Ange en prognos period på 10 perioder med `target_rolling_window_size` och ange en enda fördröjning på målvärdena för två perioder i förväg med `target_lags` parametern. Vi rekommenderar att du ställer `forecast_horizon` in `target_rolling_window_size` och `target_lags` till "Auto" som automatiskt identifierar dessa värden åt dig. I exemplet nedan har inställningarna "Auto" använts för dessa parametrar. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "time_series_id_column_names": ["store"],
    "forecast_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Automatiserad bearbetning av Machine Learning för bearbetning (funktions normalisering, hantering av saknade data, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma för bearbetnings steg som tillämpas på dina indata-data automatiskt.

Genom att definiera `time_series_id_column_names` i kodfragmentet ovan skapar AutoML två separata Time-Series-grupper, även kallat flera tids serier. Om inga Time Series-identifierare har definierats, kommer AutoML att anta att data uppsättningen är en enda tids serie. Mer information om engångs-serien finns i [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Nu ska du skapa ett standard `AutoMLConfig` objekt, ange `forecasting` uppgifts typ och skicka experimentet. När modellen har slutförts hämtar du den bästa körnings iterationen.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Se [exempel antecknings böcker för Prognosticering](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för detaljerade kod exempel på avancerad prognos konfiguration, inklusive:

* [jul avkänning och funktionalisering](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [kors validering av rullande ursprung](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurerbar lags](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [mängd funktioner för rullande fönster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Konfigurera en DNN aktivera prognos experiment

> [!NOTE]
> DNN-stöd för Prognosticering i automatiserade Machine Learning är i för hands version och stöds inte för lokala körningar.

För att kunna utnyttja Hyperoptimerade för prognostisering måste du ange `enable_dnn` parametern i AutoMLConfig till true. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Läs mer om [AutoMLConfig](#configure-and-run-experiment).

Alternativt kan du välja `Enable deep learning` alternativet i Studio.
![alternativ text](./media/how-to-auto-train-forecast/enable_dnn.png)

Vi rekommenderar att du använder ett AML beräknings kluster med GPU SKU: er och minst två noder som beräknings mål. För att få tillräckligt med tid för att DNN-utbildningen ska slutföras rekommenderar vi att du ställer in experiment tids gränsen på minst ett par timmar.
Mer information om AML-beräkning och VM-storlekar som innehåller GPU: n finns i [AML Compute-dokumentationen](how-to-set-up-training-targets.md#amlcompute) och [GPU-optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Visa [Notebook Production Forecasting-anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) för ett detaljerat kod exempel som utnyttjar hyperoptimerade.

### <a name="customize-featurization"></a>Anpassa funktionalisering
Du kan anpassa dina funktionalisering-inställningar för att säkerställa att de data och funktioner som används för att träna din ML-modell leder till relevanta förutsägelser. 

Om du vill anpassa featurizations anger du `"featurization": FeaturizationConfig` i ditt `AutoMLConfig` objekt. Om du använder Azure Machine Learning Studio för experimentet går du till [instruktions artikeln](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Anpassningar som stöds är:

|Anpassning|Definition|
|--|--|
|**Uppdatering av kolumn syfte**|Åsidosätt den automatiska identifierade funktions typen för den angivna kolumnen.|
|**Transformering av parameter uppdatering** |Uppdatera parametrarna för den angivna transformeraren. Stöder för närvarande *imputerade* (fill_value och median).|
|**Släpp kolumner** |Anger kolumner som ska släppas från att bearbetas.|

Skapa `FeaturizationConfig` objektet genom att definiera dina funktionalisering-konfigurationer:
```python
featurization_config = FeaturizationConfig()
# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']
# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')
# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})
# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

### <a name="target-rolling-window-aggregation"></a>Samling av rullande fönster i mål
Ofta är den bästa informationen som en prognosare kan ha är det senaste värdet för målet. Att skapa en kumulativ statistik för målet kan öka noggrannheten i dina förutsägelser. Med hjälp av en mål grupp med rullande fönster kan du lägga till en rullande agg regering av data värden som funktioner. Om du vill aktivera rullande mål för mål ställer du in `target_rolling_window_size` till önskad heltals fönster storlek. 

Ett exempel på detta kan ses när du förutsäger energi efter frågan. Du kan lägga till en funktion för rullande fönster i tre dagar för att påverka termiska förändringar av upphettade utrymmen. I exemplet nedan har vi skapat den här fönster storleken tre genom `target_rolling_window_size=3` att ange i `AutoMLConfig` konstruktorn. Tabellen visar funktions teknik som inträffar när Window aggregation används. Kolumner för minimum, maximum och sum genereras i ett glidande fönster av tre baserat på de definierade inställningarna. Varje rad har en ny beräknad funktion, när det gäller tidsstämpeln för den 8 september 2017 4:10:00 de högsta, lägsta och sammanlagda värdena beräknas utifrån värdena för efter frågan den 8 september 2017 1:10:00-3:10:00. Det här fönstret innehåller tre skiften och fyller i data för de återstående raderna.

![alternativ text](./media/how-to-auto-train-forecast/target-roll.svg)

Att skapa och använda dessa ytterligare funktioner som extra sammanhangsbaserade data hjälper till att uppnå en bättre noggrannhet i träna-modellen.

Visa en python code-exempel som använder den angivna [mål funktionen för mängd funktions fönster](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="view-feature-engineering-summary"></a>Visa sammanfattning av funktions teknik

För aktivitets typer för Time-serien i Automatisk maskin inlärning kan du Visa information från funktions teknik processen. Följande kod visar varje RAW-funktion tillsammans med följande attribut:

* Oformaterat funktions namn
* Antalet funktioner som skapats av den här RAW-funktionen
* Typ identifierad
* Om funktionen har släppts
* Lista över funktions omvandlingar för RAW-funktionen

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognosticering med bästa modell

Använd den bästa modellen iterationer för att beräkna prognos värden för data uppsättningen test.

`forecast()`Funktionen ska användas i stället för `predict()` , så att det går att göra specifikationer för när förutsägelserna ska starta. I följande exempel ersätter du först alla värden i `y_pred` med `NaN` . Prognosens ursprung är i slutet av tränings data i det här fallet, eftersom det normalt skulle vara när det används `predict()` . Men om du bara ersatte den andra halvan av `y_pred` med `NaN` , lämnar funktionen de numeriska värdena i den första halvan oförändrade, men prognoserar `NaN` värdena i den andra halvan. Funktionen returnerar både de beräknade värdena och de justerade funktionerna.

Du kan också använda- `forecast_destination` parametern i `forecast()` funktionen för att prognostisera värden fram till ett angivet datum.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Beräkna RMSE (rot genomsnitts fel) mellan de `actual_labels` faktiska värdena och de prognostiserade värdena i `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Nu när den övergripande modell precisionen har fastställts är det mest realistiska nästa steg att använda modellen för att förutsäga okända framtida värden. Ange en data mängd i samma format som test uppsättningen `test_data` , men med framtida datetime-värden och den resulterande förutsägelse uppsättningen är de prognostiserade värdena för varje tids serie steg. Anta att de senaste tid serie posterna i data uppsättningen var i 12/31/2018. Om du vill prognostisera efter frågan för nästa dag (eller så många perioder som du behöver prognostisera, <= `forecast_horizon` ) skapar du en enda tids serie post för varje butik för 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Upprepa de nödvändiga stegen för att läsa in framtida data till en dataframe och kör sedan `best_run.predict(test_data)` för att förutsäga framtida värden.

> [!NOTE]
> Det går inte att förutsäga värden för antalet perioder som är större än `forecast_horizon` . Modellen måste tränas om med en större horisont för att förutsäga framtida värden bortom den aktuella horisonten.

## <a name="next-steps"></a>Nästa steg

* Följ [själv studie kursen](tutorial-auto-train-models.md) för att lära dig hur du skapar experiment med automatiserad maskin inlärning.
* Visa [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referens dokumentation.
