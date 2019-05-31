---
title: Skapa en Azure Blockchain-tjänst med Azure CLI
description: Använd Azure Blockchain-tjänsten för att skapa medlem blockchain med Azure CLI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: be5a8151f0de0a33db09194a7159aded6848c78a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416184"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Snabbstart: Skapa en Azure Blockchain Service blockchain medlem med hjälp av Azure CLI

Azure Blockchain-Service är en blockchain-plattform som du kan använda för att köra affärslogiken i ett smarta kontrakt. Den här snabbstarten visar hur du kommer igång genom att skapa medlem blockchain med Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du vill installera och använda CLI lokalt kan den här snabbstarten kräver Azure CLI version 2.0.51 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [installerar Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>Skapa en blockkedjemedlem

Skapa en blockchain-medlem i Azure Blockchain-tjänst som körs kvorum transaktionsregister-protokollet i en ny consortium. Det finns flera parametrar och egenskaper måste du skicka. Ersätt parametrarna exempel med dina värden.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn var Azure Blockchain-tjänstresurser ska skapas. Använd den resursgrupp du skapade i föregående avsnitt.
| **name** | Ett unikt namn som identifierar din Azure Blockchain Service blockchain medlem. Namnet används för offentlig slutpunkt-adress. Till exempel `myblockchainmember.blockchain.azure.com`.
| **Plats** | Azure-region där den blockchain-medlemmen har skapats. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program.
| **Lösenord** | Lösenordet för medlemmens standardnoden transaktion. Använd lösenordet för grundläggande autentisering när du ansluter till blockchain medlem standard transaktion noden offentlig slutpunkt.
| **consortium** | Namnet på consortium att ansluta till eller skapa.
| **consortiumAccountPassword** | Lösenordet för consortium kallas även kontolösenordet medlem. Lösenordet för medlem används för att kryptera den privata nyckeln för det Ethereum-konto som har skapats för din medlem. Du kan använda medlemskonto och medlemmen kontolösenord för hantering av consortium.
| **skuName** | Nivåtyp. Använd S0 för Standard och B0 för Basic.

Det tar cirka 10 minuter för att skapa blockchain-medlem och stödresurser.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den blockchain-medlemmen som du skapade för nästa Snabbstart eller självstudie. När den inte längre behövs kan du ta bort resurserna genom att ta bort den `myResourceGroup` resursgruppen du skapade med Azure Blockchain-tjänsten.

Kör följande kommando för att ta bort resursgruppen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en blockchain-medlem kan du prova någon av anslutningen snabbstarter för [Geth](connect-geth.md), [MetaMask](connect-metamask.md), eller [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Använd Truffle för att ansluta till en tjänstnätverk Azure Blockchain](connect-truffle.md)
