---
title: Använda mallreferens
description: Använd Azure Resource Manager-mallreferensen för att skapa en mall för distribution av ett krypterat lagringskonto.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 99ec64529b90c7a80aea62090f80c55cf4e23510
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326483"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>Självstudie: Använd Azure Resource Manager mal len referens

Lär dig hur du hittar information om mallschema och använd informationen för att skapa Azure Resource Manager-mallar.

I den här självstudien använder du en basmall från Azure-snabbstartmallar. Använd referensdokumentationen för mallar för att anpassa mallen och skapa ett krypterat lagringskonto.

![Referens för Resource Manager-mall distribuera krypterat lagrings konto](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Förstå mallen
> * Leta upp mallreferensen
> * Redigera mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. [Skapa Azure Resource Manager mallar i använda Visual Studio Code](./resource-manager-tools-vs-code.md).

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

[Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/) är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här snabbstarten kallas [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto). Mallen definierar en Azure Storage-kontoresurs.

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Välj **Arkiv**>**Spara som** för att spara filen som **azuredeploy.json** till den lokala datorn.

## <a name="understand-the-schema"></a>Förstå schemat

1. Från VS Code döljer du mallen till rotnivån. Du får den enklaste strukturen med följande element:

    ![Enklaste struktur för Resource Manager-mall](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: ange platsen för den JSON-schemafil som beskriver versionen av mallspråket.
    * **contentVersion**: ange valfritt värde för det här elementet för att dokumentera betydande förändringar i mallen.
    * **parameters** (parametrar): ange de värden som tillhandahålls när distributionen körs för att anpassa resursdistributionen.
    * **variables** (variabler): ange de värden som används som JSON-fragment i mallen för att förenkla mallspråksuttryck.
    * **resources** (resurser): ange de resurstyper som distribueras eller uppdateras i en resursgrupp.
    * **outputs** (utdata): ange de värden som returneras efter distributionen.

2. Expandera **resurser**. Det finns en `Microsoft.Storage/storageAccounts`-resurs som definierats. Mallen skapar ett okrypterat lagringskonto.

    ![Definition av lagringskonto för Resource Manager-mall](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Leta upp mallreferensen

1. Bläddra till [referens för Azure-mall](https://docs.microsoft.com/azure/templates/).
2. I rutan **Filtrera efter namn** anger du **lagrings konton**.
3. Välj **referens/mall referens/lagring/&lt;Version >/Storage-konton** som visas på följande skärm bild:

    ![Resource Manager, mallreferens, lagringskonto](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    Om du inte vet vilken version du ska välja använder du den senaste versionen.

4. Hitta den krypteringsrelaterade definitionsinformationen.

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    På samma webbplats bekräftar följande beskrivning att objektet `encryption` används för att skapa ett krypterat lagringskonto.

    ![Kryptering av lagringskonto för Resource Manager-mallreferens](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    Och det finns två sätt att hantera krypteringsnyckeln. Du kan använda Microsoft-hanterade krypteringsnycklar med Kryptering för lagringstjänst eller dina egna krypteringsnycklar. För att hålla den här självstudien enkel använder du alternativet `Microsoft.Storage` så att du inte behöver skapa ett Azure-nyckelvalv.

    ![Krypteringsobjekt för lagringskonto för Resource Manager-mallreferens](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    Krypteringsobjektet ska se ut så här:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Redigera mallen

Från Visual Studio Code ändrar du mallen så att resurselementet ser ut så här:

![Krypterade lagringskontoresurser för Resource Manager-mall](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Distribuera mallen

Mer information finns i avsnittet [Distribuera mallen](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) i Visual Studio Code-snabbstarten för distributionen.

Följande skärmbild visar CLI-kommandot för att lista det nya lagringskontot, vilket anger att kryptering har aktiverats för blob-lagringen.

![Krypterat lagringskonto för Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder mallreferensen för att anpassa en befintlig mall. Du hittar mer information om att skapa flera instanser av lagringskonton i:

> [!div class="nextstepaction"]
> [Skapa flera instanser](./resource-manager-tutorial-create-multiple-instances.md)
