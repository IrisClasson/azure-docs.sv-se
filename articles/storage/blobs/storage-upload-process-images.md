---
title: Överföra avbildningsdata i molnet med Azure Storage | Microsoft Docs
description: Använda Azure blobblagring med ett webbprogram för att lagra programdata
services: storage
documentationcenter: ''
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: c58bb19a18e6f0f78e07e403df5489f2f7342203
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853106"
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Överföra avbildningsdata i molnet med Azure Storage

Den här självstudien ingår i en serie. De här självstudierna visar hur du distribuerar ett webbprogram som använder Azure Storage-klientbiblioteket för att ladda upp avbildningar till ett lagringskonto. När du är klar har du en webbapp som lagrar och visar avbildningar från Azure Storage.

# <a name="nettabnet"></a>[\..NET](#tab/net)
![Vy för avbildningscontainer](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Vy för avbildningscontainer](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapar en container och anger behörigheter
> * Hämta en åtkomstnyckel
> * Konfigurera programinställningar
> * Distribuera en webbapp till Azure
> * Interagerar med webbappen

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp 

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus 
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Exemplet överför avbildningar till en blobcontainer på ett Azure-lagringskonto. Ett Azure-lagringskonto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure-lagringdataobjekt. Skapa ett lagringskonto i resursgruppen du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create).

> [!IMPORTANT]
> I del 2 av självstudierna använder du händelseprenumerationer för blobblagring. Händelseprenumerationer stöds för närvarande endast för Blob Storage-konton på följande platser: Asien, sydöstra, Asien, östra, Australien, östra, Australien, sydöstra, USA, centrala, USA, östra, USA, östra 2, Europa, västra, Europa, norra, Japan, östra, Japan, västra, USA, västra centrala, USA, västra och USA, västra 2. På grund av den här begränsningen måste du skapa ett blobblagringskonto som används av exempelappen för att lagra avbildningar och miniatyrer.

I följande kommando infogar du ditt globalt unika lagringskontonamn på blobblagringskontot istället för platshållaren `<blob_storage_account>`.  

```azurecli-interactive
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>Skapa bloblagringscontainrar

Appen använder två containrar i bloblagringskontot. Container liknar mappar och används för att lagra blobar. Det är till containern _avbildningar_ som appen överför högupplösta bilder. I en senare del av självstudierna överför en funktionsapp i Azure ändrade miniatyrbilder till containern för _miniatyrer_.

Hämta nyckeln till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list). Du använder den här nyckeln för att skapa två behållare med kommandot [az storage container create](/cli/azure/storage/container#az_storage_container_create).  

I det här fallet är `<blob_storage_account>` namnet på det blobblagringskonto som du skapade. Den offentliga åtkomsten för containern _avbildningar_ har angetts till `off`, medan den offentliga åtkomsten för containern _miniatyrer_ är inställd på `container`. Inställningen för offentlig åtkomst för `container` ska visas för personer som besöker webbsidan.

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..."
echo $blobStorageAccountKey
```

Anteckna namnet och nyckeln till ditt blobblagringskonto. Exempelappen använder dessa inställningar för att ansluta till lagringskontot för att överföra avbildningar. 

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

En [App Service-plan](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app.

Skapa en App Service-plan med kommandot [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create).

I följande exempel skapas en App Service-plan med namnet `myAppServicePlan` på prisnivån **Kostnadsfri**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Skapa en webbapp

Webbappen erbjuder ett värdutrymme för exempelappens kod som visas på GitHub-exempellagringsplatsen. Skapa en [webbapp](../../app-service/app-service-web-overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp#az_webapp_create).  

I följande kommando ska du ersätta `<web_app>` med ett unikt namn (giltiga tecken är `a-z`, `0-9` och `-`). Om `<web_app>` inte är unikt får du ett felmeddelande om att _webbplatsen med det angivna namnet `<web_app>` redan finns._ Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Distribuera exempelappen från GitHub-lagringsplatsen

# <a name="nettabnet"></a>[\..NET](#tab/net)

App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Ersätt `<web_app>` med namnet på den webbapp som du skapade i föregående steg.

Exempelprojektet innehåller en [ASP.NET MVC](https://www.asp.net/mvc)-app som accepterar en avbildning, sparar den till ett lagringskonto och visar avbildningar från en container med miniatyrer. Webbappen använder namnrymderna [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) och [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) från Azure Storage-klientbiblioteket för att interagera med Azure Storage.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Ersätt `<web_app>` med namnet på den webbapp som du skapade i föregående steg.

---

```azurecli-interactive
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

## <a name="configure-web-app-settings"></a>Konfigurera inställningarna för webbappen

Exempelwebbappen använder [Azure Storage-klientbiblioteket](/dotnet/api/overview/azure/storage?view=azure-dotnet) för att begära åtkomsttokens som används för att överföra avbildningar. Autentiseringsuppgifterna för lagringskonto som används av Storage SDK ställs in i webbappens programinställningar. Lägg till tillämpningsinställningar till den distribuerade appen med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

I följande kommando är `<blob_storage_account>` namnet på ditt blobblagringskonto och `<blob_storage_key>` är den associerade nyckeln. Ersätt `<web_app>` med namnet på den webbapp som du skapade i föregående steg.

```azurecli-interactive
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
```

När webbappen har distribuerats och konfigurerats kan du testa funktionen för att ladda upp avbildningar i appen.

## <a name="upload-an-image"></a>Ladda upp en avbildning

Om du vill testa webbappen bläddrar du till URL-adressen till din publicerade app. Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.
Välj region vid **Överföra foton** överför en fil eller dra och släpp en fil på regionen. Bilden försvinner om överföringen lyckas.

# <a name="nettabnet"></a>[\..NET](#tab/net)

![ImageResizer-app](media/storage-upload-process-images/figure1.png)

I exempelkoden används aktiviteten `UploadFiletoStorage` i filen `Storagehelper.cs` för att ladda upp avbildningar till containern `images` på lagringskontot med hjälp av metoden [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). Följande kodexempel innehåller aktiviteten `UploadFiletoStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Följande klasser och metoder som används i den föregående aktiviteten:

|Klass  |Metod  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

![App för bilduppladdning](media/storage-upload-process-images/upload-app-nodejs.png)

I exempelkoden ansvarar vägen `post` för att ladda upp bilden till en blob-container. Flödet använder moduler för att bearbeta uppladdningen:

- [multer](https://github.com/expressjs/multer) implementerar uppladdningsstrategin för routningshanteraren
- [into-stream](https://github.com/sindresorhus/into-stream) konverterar bufferten till en dataström som krävs av [createBlockBlobFromStream](http://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html#createBlockBlobFromStream)

När filen skickas till vägen förblir filens innehåll i minnet tills filen har laddats upp till blob-containern.

> [!IMPORTANT]
> Om mycket stora filer läses in i minnet kan det ha en negativ inverkan på prestandan för webbprogrammet. Om du förväntar dig att användare publicerar stora filer kan du överväga att mellanlagra filer i webbserverns filsystem och sedan schemalägga uppladdningar till blob-lagringen. När filerna finns i blob-lagringen kan du ta bort dem från serverfilsystemet.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Kontrollera att avbildningen visas på lagringskontot

Logga in på [Azure-portalen](https://portal.azure.com). I den vänstra menyn väljer du **Lagringskonton** och sedan namnet på ditt lagringskonto. Under **Översikt** väljer du containern **avbildningar**.

Kontrollera att avbildningen visas i containern.

![Vy för avbildningscontainer](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testa miniatyrvisning

Om du vill testa miniatyrvisning ska du överföra en avbildning till miniatyrcontainern för att säkerställa att appen kan läsa miniatyrcontainern.

Logga in på [Azure-portalen](https://portal.azure.com). I den vänstra menyn väljer du **Lagringskonton** och sedan namnet på ditt lagringskonto. Välj **Containers** under **Blob-tjänst** och välj containern **Miniatyrer**. Välj **Överför** för att öppna fönstret **Överför blobb**.

Välj en fil med filväljaren och välj **Överför**.

Gå tillbaka till din app för att kontrollera att avbildningen som har överförts till containern **Miniatyrer** syns.

# <a name="nettabnet"></a>[\..NET](#tab/net)
![Vy för avbildningscontainer](media/storage-upload-process-images/figure2.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
![Vy för avbildningscontainer](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

I containern **Miniatyrer** på Azure-portalen väljer du den avbildning du överförde och väljer **Ta bort** för att ta bort avbildningen. I del två av självstudierna automatiserar du skapandet av miniatyrbilder, så att den här testbilden inte behövs.

CDN kan aktiveras för att cachelagra innehåll från ditt Azure Storage-konto. Det beskrivs inte i de här självstudierna, men om du vill lära dig att aktivera CDN med ditt Azure Storage-konto kan du gå till: [Integrera ett Azure Storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Nästa steg

I del ett av serien lärde du dig att konfigurera en webbapp som interagerar med lagring för att:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Skapar en container och anger behörigheter
> * Hämta en åtkomstnyckel
> * Konfigurera programinställningar
> * Distribuera en webbapp till Azure
> * Interagera med webbappen

Gå till del två av serien för att lära dig att använda Event Grid för att utlösa en Azure-funktion för att ändra storlek på en avbildning.

> [!div class="nextstepaction"]
> [Använd Event Grid för att utlösa en Azure-funktion för att ändra storlek på en överförd avbildning](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
