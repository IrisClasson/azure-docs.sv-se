---
title: Starta om Server – Azure PowerShell-Azure Database for MySQL
description: I den här artikeln beskrivs hur du startar om en Azure Database for MySQL-server med PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71d10078a704b2905cf055347f5ed4272ca8ef72
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502791"
---
# <a name="restart-azure-database-for-mysql-server-using-powershell"></a>Starta om Azure Database for MySQL server med PowerShell

I det här avsnittet beskrivs hur du kan starta om en Azure Database for MySQL-server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott under driften.

Omstart av servern blockeras om tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.

Hur lång tid det tar att slutföra en omstart beror på den MySQL-återställnings processen. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om modulen AZ. MySql PowerShell är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.MySql -AllowPrerelease` .
> När AZ. MySql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-moduler och är tillgängliga internt från Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en Azure Database for MySQL-server med PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)
