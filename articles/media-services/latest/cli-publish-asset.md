---
title: Exempel på Azure CLI-skript – Publicera en tillgång | Microsoft Docs
description: Den här artikeln visar hur du använder Azure CLI-skriptet för att publicera en till gång.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.custom: devx-track-azurecli
ms.openlocfilehash: 95bdd3b3dddd1a04e00d705449681985400e9621
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87487640"
---
# <a name="cli-example-publish-an-asset"></a>CLI-exempel: Publicera en tillgång

Azure CLI-skriptet i den här artikeln visar hur du skapar en strömningspositionerare och får tillbaka strömnings-URL:er. 

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services](media-services-overview.md)
