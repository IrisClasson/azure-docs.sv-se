---
title: CreateUiDefinition.jspå fil för fönstret Portal
description: Beskriver hur du skapar användar gränssnitts definitioner för Azure Portal. Används när du definierar Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 0e2aee194d3c97655dd4ec5aaeea46fb607c4c5e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210962"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json för Azure-upplevelsen för att skapa hanterade program

I det här dokumentet beskrivs de viktigaste begreppen i **createUiDefinition.jsi** filen. Azure Portal använder den här filen för att definiera användar gränssnittet när du skapar ett hanterat program.

Mallen är följande

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

En CreateUiDefinition innehåller alltid tre egenskaper: 

* protokollhanteraren
* version
* parametrar

Hanteraren bör alltid vara `Microsoft.Azure.CreateUIDef` och den senaste versionen som stöds är `0.1.2-preview` .

Schemat för Parameters-egenskapen är beroende av kombinationen av den angivna hanteraren och versionen. För hanterade program är de egenskaper som stöds `basics` , `steps` ,, `outputs` och `config` . Egenskaperna grundläggande och steg innehåller [elementen](create-uidefinition-elements.md) , t. ex. text rutor och list rutor, som ska visas i Azure Portal. Egenskapen outputs används för att mappa utmatnings värden för de angivna elementen till parametrarna i Azure Resource Manager-mallen. Du `config` kan bara använda när du behöver åsidosätta standard beteendet för `basics` steget.

Inklusive `$schema` rekommenderas, men valfritt. Om det anges måste värdet för `version` matcha versionen i `$schema` URI: n.

Du kan använda en JSON-redigerare för att skapa din createUiDefinition och sedan testa den i [sand boxen createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) för att förhandsgranska den. Mer information om sandbox finns i [Testa ditt Portal gränssnitt för Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Grunder

**Grundläggande** steg är det första steget som genereras när Azure Portal tolkar filen. Som standard kan du med hjälp av grundläggande steg välja prenumeration, resurs grupp och plats för distribution.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Standard för grundläggande":::

Du kan lägga till fler element i det här avsnittet. När det är möjligt kan du lägga till element som frågar om globala parametrar, t. ex. namnet på ett kluster eller administratörsautentiseringsuppgifter.

I följande exempel visas en text ruta som har lagts till i standard elementen.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="config"></a>Konfigurera

Du anger konfigurations elementet när du behöver åsidosätta standard beteendet för grundläggande steg. I följande exempel visas tillgängliga egenskaper.

```json
"config": {
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

För `description` , anger du en markdown-aktiverad sträng som beskriver din resurs. Flera rader och länkar stöds.

För `location` anger du egenskaperna för den plats kontroll som du vill åsidosätta. Alla egenskaper som inte åsidosätts har angetts till standardvärdena. `resourceTypes` accepterar en sträng mat ris som innehåller fullständigt kvalificerade resurs typs namn. Plats alternativen är begränsade till endast regioner som stöder resurs typerna.  `allowedValues`   accepterar en matris med regions strängar. Endast de regionerna visas i list rutan.Du kan ange både `allowedValues`   och  `resourceTypes` . Resultatet är skärningen mellan båda listorna. Slutligen `visible` kan egenskapen användas för att villkorligt eller helt inaktivera List rutan plats.  

Med `subscription` `resourceGroup` elementen och kan du ange ytterligare verifieringar. Syntaxen för att ange verifieringar är identisk med [text rutan](microsoft-common-textbox.md)anpassad verifiering för text. Du kan också ange `permission` valideringar för prenumerationen eller resurs gruppen.  

Prenumerations kontrollen accepterar en lista över resurs leverantörens namn områden. Du kan till exempel ange **Microsoft. Compute**. Ett fel meddelande visas när användaren väljer en prenumeration som inte stöder resurs leverantören. Felet uppstår när resurs leverantören inte är registrerad på den prenumerationen och användaren inte har behörighet att registrera resurs leverantören.  

Resurs grupps kontrollen har ett alternativ för `allowExisting` . När `true` kan användarna välja resurs grupper som redan har resurser. Den här flaggan gäller mest för Solution templates, där standard beteende användare måste välja en ny eller tom resurs grupp. I de flesta andra scenarier är det inte nödvändigt att ange den här egenskapen.  

## <a name="steps"></a>Steg

Egenskapen steg innehåller noll eller fler ytterligare steg att visa efter grunderna. Varje steg innehåller ett eller flera element. Överväg att lägga till steg per roll eller nivå för det program som distribueras. Du kan till exempel lägga till ett steg för indata för huvudnoder och ett steg för arbetsnoder i ett kluster.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Utdata

Azure Portal använder `outputs` egenskapen för att mappa element från `basics` och `steps` till parametrarna i mallen för Azure Resource Manager distribution. Nycklarna för den här ord listan är namnen på mallparametrar och värdena är egenskaper för de utgående objekten från de refererade elementen.

Om du vill ange resurs namnet för den hanterade appen måste du inkludera ett värde med namnet `applicationResourceName` i egenskapen outputs. Om du inte anger det här värdet tilldelar programmet ett GUID för namnet. Du kan inkludera en text ruta i användar gränssnittet som begär ett namn från användaren.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Resurstyper

Om du vill filtrera tillgängliga platser till endast de platser som har stöd för de resurs typer som ska distribueras, anger du en matris av resurs typerna. Om du anger mer än en resurs typ returneras bara de platser som stöder alla resurs typer. Den här egenskapen är valfri.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition innehåller [funktioner](create-uidefinition-functions.md) för att arbeta med element indata och utdata, samt funktioner som till exempel villkor. Dessa funktioner liknar funktionerna i både syntax och funktioner i Azure Resource Manager mallar.

## <a name="next-steps"></a>Nästa steg

Själva filen createUiDefinition.jshar ett enkelt schema. Det verkliga djupet i den kommer från alla element och funktioner som stöds. Dessa objekt beskrivs mer detaljerat på:

- [Element](create-uidefinition-elements.md)
- [Funktioner](create-uidefinition-functions.md)

Ett aktuellt JSON-schema för createUiDefinition finns här: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` .

Ett exempel på en användar gränssnitts fil finns i [createUiDefinition.jspå](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
