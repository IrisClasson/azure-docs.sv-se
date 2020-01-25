---
title: Bearbeta Azure-blobbdata med avancerade analyser – Team Data Science Process
description: Utforska data och skapa funktioner från data som lagras i Azure Blob storage med hjälp av avancerad analys.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4c47dfb8b221b6cb4b6237669ecd17c1637107a2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721106"
---
# <a name="heading"></a>Bearbeta Azure-blobbdata med avancerade analyser
Det här dokumentet beskriver utforska data och skapar funktioner från data lagrade i Azure Blob storage. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Läsa in data i en dataram Pandas
För att kunna utforska och ändra en data uppsättning måste den laddas ned från BLOB-källan till en lokal fil som sedan kan läsas in i en Pandas data ram. Här följer stegen för den här proceduren:

1. Hämta data från Azure-bloben med följande exempel på python-kod med Blob Service. Ersätt variabeln i koden nedan med ditt specifika värden: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))
2. Läsa in datan i en Pandas-dataram från den hämta filen.
   
        #LOCALFILE is the file path    
        dataframe_blobdata = pd.read_csv(LOCALFILE)

Nu är du redo att utforska data och generera funktioner för den här datauppsättningen.

## <a name="blob-dataexploration"></a>Datautforskning
Här följer några exempel på hur du kan utforska data med hjälp av Pandas:

1. Granska antalet rader och kolumner 
   
        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
2. Granska de första eller sista raderna i datauppsättningen enligt nedan:
   
        dataframe_blobdata.head(10)
   
        dataframe_blobdata.tail(10)
3. Kontrollera den datatyp som varje kolumn har importerats som med hjälp av följande exempelkod
   
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
4. Kontrollera grundläggande statistik för kolumner i datauppsättningen på följande sätt
   
        dataframe_blobdata.describe()
5. Titta på antalet poster för varje kolumnvärde enligt följande
   
        dataframe_blobdata['<column_name>'].value_counts()
6. Antal saknade värden jämfört med det faktiska antalet poster i varje kolumn med hjälp av följande exempelkod
   
        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
7. Om du har värden som saknas för en viss kolumn i data kan släppa du dem på följande sätt:
   
        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape
   
   Ett annat sätt att ersätta värden som saknas är med funktionen läge:
   
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        
8. Skapa ett histogram diagram med hjälp av variabeln antal lagerplatser ska ritas distributionen av en variabel    
   
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
9. Titta på korrelationer mellan variabler med hjälp av en spridningsdiagrammet eller inbyggda korrelationen
   
        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()

## <a name="blob-featuregen"></a>Funktionen Generation
Vi kan ge funktioner med hjälp av Python enligt följande:

### <a name="blob-countfeature"></a>Generering av indikator värde-baserad funktion
Kategoriska funktioner kan skapas på följande sätt:

1. Kontrollera distributionen av kategoriska kolumnen:
   
        dataframe_blobdata['<categorical_column>'].value_counts()
2. Generera indikator värden för var och en av kolumnvärdena
   
        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
3. Ansluta till kolumnen indikator med ursprungliga dataram 
   
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
4. Ta bort själva ursprungliga variabeln:
   
        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

### <a name="blob-binningfeature"></a>Datagruppering funktionen Generation
För att generera binned funktioner, fortsätter vi på följande sätt:

1. Lägg till en sekvens med kolumner till lagerplats en numerisk kolumn
   
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
2. Konvertera datagruppering till en sekvens med booleska variabler
   
        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
3. Slutligen kan ansluta till dummy variablerna tillbaka till den ursprungliga dataramen
   
        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)    

## <a name="sql-featuregen"></a>Skriva data tillbaka till Azure-blob och använda i Azure Machine Learning
När du har utforskat data och skapat de nödvändiga funktionerna kan du överföra data (sampled eller bearbetas) till en Azure-blob och använda den i Azure Machine Learning med hjälp av följande steg: ytterligare funktioner kan skapas i Azure Machine Learning Studio (klassisk) också. 

1. Skriv dataramen som lokal fil
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Ladda upp data till Azure-blob enligt följande:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Nu kan data läsas från bloben med hjälp av modulen Azure Machine Learning [Importera data][import-data] som visas på skärmen nedan:

![läsare blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

