---
title: Läs in data till Azure Data Lake Storage Gen1 med hjälp av Azure Data Factory | Microsoft Docs
description: Använda Azure Data Factory för att kopiera data till Azure Data Lake Storage Gen1
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 522b9743af28dedb2aec5682a1ae95b9d52ad2d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549187"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Läs in data till Azure Data Lake Storage Gen1 med hjälp av Azure Data Factory

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (kallades tidigare Azure Data Lake Store) är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Data Lake Storage Gen1 kan du samla in data av alla storlekar, typer och inmatning. Data samlas på en enda plats för driftsanalyser och undersökande analyser.

Azure Data Factory är en fullständigt hanterad molnbaserad dataintegreringstjänst. Du kan använda tjänsten för att fylla i sjön med data från ditt befintliga system och spara tid när du skapar Analyslösningar.

Azure Data Factory erbjuder följande fördelar vid inläsning av data i Data Lake Storage Gen1:

* **Enkelt att konfigurera**: En intuitiv 5-steg-guide med inga skript som krävs.
* **Omfattande stöd för datalager**: Inbyggt stöd för ett stort utbud av lokala och molnbaserade datalager. En detaljerad lista finns i tabellen med [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
* **Säker och kompatibel**: Data överförs via HTTPS- eller ExpressRoute. Tjänsten för global närvaro säkerställer att dina data aldrig lämnar geografisk gräns.
* **Högpresterande**: Upp till 1 GB/s data läses in hastighet i Data Lake Storage Gen1. Mer information finns i [kopiera aktivitet prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget kopieringsdata i Data Factory för att _läser in data från Amazon S3 i Data Lake Storage Gen1_. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

> [!NOTE]
> Mer information finns i [kopiera data till och från Data Lake Storage Gen1 med hjälp av Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Data Lake Storage Gen1 konto: Om du inte har ett Data Lake Storage Gen1-konto, se anvisningarna i [skapa ett Data Lake Storage Gen1 konto](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Den här artikeln visar hur du kopierar data från Amazon S3. Du kan använda andra datalager genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På menyn till vänster väljer **skapa en resurs** > **Analytics** > **Data Factory**:
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. I den **ny datafabrik** anger värden för fälten som visas i följande bild: 
      
   ![Sida för ny datafabrik](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure data factory. Om du får felet ”datafabriksnamnet \"LoadADLSG1Demo\" är inte tillgänglig”, ange ett annat namn för data factory. Du kan till exempel använda namnet  _**dittnamn**_**ADFTutorialDataFactory**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data factory. 
    * **Resursgrupp**: Välj en befintlig resursgrupp från den nedrullningsbara listan eller Välj den **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av data factory kan finnas på andra platser och regioner. Dessa datalager omfattar Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När datafabriken har skapats går du till din datafabrik. Du ser den **Data Factory** startsida, enligt följande bild: 
   
   ![Datafabrikens startsida](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Välj den **författare och Övervakare** att starta Dataintegrationsprogrammet i en separat flik.

## <a name="load-data-into-data-lake-storage-gen1"></a>Läs in data till Data Lake Storage Gen1

1. I den **börjar** väljer den **kopieringsdata** att starta verktyget kopieringsdata: 

   ![Panel för verktyget Kopiera data](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. I den **egenskaper** anger **CopyFromAmazonS3ToADLS** för den **aktivitetsnamn** och markerar **nästa**:

    ![Sidan Egenskaper](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. I den **källdatalagret** klickar du på **+ Skapa ny anslutning**:

    ![Sidan Källdatalager](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Välj **Amazon S3**, och välj **Fortsätt**
    
    ![Sidan s3 källdatalager](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. I den **ange Amazon S3 anslutning** gör du följande steg: 
   1. Ange den **Åtkomstnyckelns ID** värde.
   2. Ange den **hemliga åtkomstnyckel** värde.
   3. Välj **Slutför**.
   
      ![Ange Amazon S3-konto](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Du ser en ny anslutning. Välj **Nästa**.
   
   ![Ange Amazon S3-konto](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. I den **Välj indatafil eller mapp** sidan, bläddra till mappen och filen som du vill kopiera över. Välj mappen/filen, Välj **Välj**, och välj sedan **nästa**:

    ![Välj indatafil eller mapp](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Välj kopieringsbeteendet genom att välja den **kopiera filer rekursivt** och **binär kopia** (kopiera filer som – är) alternativ. Välj **nästa**:

    ![Ange Utdatamappen](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. I den **måldatalager** klickar du på **+ Skapa ny anslutning**, och välj sedan **Azure Data Lake Storage Gen1**, och välj **Fortsätt**:

    ![Sidan Måldatalager](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. I den **ny länkad tjänst (Azure Data Lake Storage Gen1)** gör du följande steg: 

   1. Välj ditt Data Lake Storage Gen1 konto i den **Data Lake Store-kontonamn**.
   2. Ange den **klient**, och klicka på Slutför.
   3. Välj **Nästa**.
   
   > [!IMPORTANT]
   > I den här genomgången använder du en hanterad identitet för Azure-resurser för att autentisera ditt Data Lake Storage Gen1-konto. Se till att ge MSI rätt behörighet i Data Lake Storage Gen1 genom att följa [instruktionerna](connector-azure-data-lake-store.md#managed-identity).
   
   ![Ange Data Lake Storage Gen1 konto](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. I den **Välj utdatafil eller mapp** anger **copyfroms3** som utdata mappnamn och välj **nästa**: 

    ![Ange Utdatamappen](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. I den **inställningar** väljer **nästa**:

    ![Sidan Inställningar](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. I den **sammanfattning** , granskar du inställningarna och välj **nästa**:

    ![Sammanfattningssida](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. I den **distributionssida**väljer **övervakaren** att övervaka pipelinen (aktiviteten):

    ![Distributionssida](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observera att fliken **Övervaka** till vänster väljs automatiskt. Den **åtgärder** -kolumnen innehåller länkar för att visa information om aktivitetskörningar och köra pipelinen på nytt:

    ![Övervaka pipelinekörningar](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningen, väljer den **visa Aktivitetskörningar** länken i den **åtgärder** kolumn. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Växla tillbaka till vyn med pipelinekörningar, Välj den **Pipelines** länken längst upp. Om du vill uppdatera listan väljer du **Uppdatera**. 

    ![Övervaka aktivitetskörningar](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. För att övervaka körning-information för varje kopieringsaktiviteten, Välj den **information** länka **åtgärder** i övervakningsvyn-aktivitet. Du kan övervaka information som mängden data som kopieras från källan till mottagare, dataflöde, utförande med motsvarande tid och används konfigurationer:

    ![Övervaka aktivitetskörningsinformation](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Kontrollera att data har kopierats till ditt Data Lake Storage Gen1 konto: 

    ![Verifiera Data Lake Storage Gen1 utdata](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande artikel om du vill veta mer om Data Lake Storage Gen1 support: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1-anslutningsapp](connector-azure-data-lake-store.md)
