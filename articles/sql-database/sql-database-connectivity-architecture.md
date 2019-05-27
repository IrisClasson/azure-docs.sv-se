---
title: Dirigera Azure trafik till Azure SQL Database och SQL Data Warehouse | Microsoft Docs
description: Det här dokumentet beskriver Azcure SQL onnectivity arkitekturen för databasanslutningar från Azure eller från utanför Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 04/03/2019
ms.openlocfilehash: 4ff6cc0ba18074f353eb5b99af7052edd658a80e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66164469"
---
# <a name="azure-sql-connectivity-architecture"></a>Arkitektur för Azure SQL-anslutning

Den här artikeln förklarar Azure SQL Database och SQL Data Warehouse-anslutning arkitekturen samt hur de olika komponenterna fungerar därigenom dirigera trafik till din Azure SQL-instans. Dessa komponenter-funktion för anslutningen att dirigera nätverkstrafik till Azure SQL Database eller SQL Data Warehouse med klienter som ansluter från i Azure och klienter som ansluter från utanför Azure. Den här artikeln innehåller också skriptexempel för att ändra hur anslutning sker, samt den information som rör ändra standardinställningar för anslutningen.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Följande diagram ger en översikt över arkitekturen för Azure SQL Database-anslutning.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Följande steg beskriver hur upprättas en anslutning till en Azure SQL database:

- Klienterna ansluter till den gateway som har en offentlig IP-adress och lyssnar på port 1433.
- Gatewayen, beroende på effektiva anslutningsprincip, omdirigeringar eller proxyservrar trafiken till rätt databas-klustret.
- Inne i databasen vidarebefordras kluster-trafik till lämplig Azure SQL-databasen.

## <a name="connection-policy"></a>Anslutningsprincip för

Azure SQL Database stöder följande tre alternativ för den här inställningen för anslutning av en SQL Database-server:

- **Omdirigering (rekommenderas):** Klienter ansluta direkt till den nod som värd för databasen. Om du vill aktivera anslutningen klienter måste tillåta utgående brandväggsregler till alla Azure-IP-adresser i regionen med Nätverkssäkerhetsgrupper (NSG) med [tjänsttaggar](../virtual-network/security-overview.md#service-tags)) för portar 11000 till 11999, inte bara Azure SQL Database gateway-IP adresser på port 1433. Eftersom paket går direkt till databasen, har svarstid och dataflöde bättre prestanda.
- **Proxy:** I det här läget är alla anslutningar via proxy via Azure SQL Database-gatewayer. Om du vill aktivera anslutning, måste klienten ha utgående brandväggsregler som tillåter endast Azure SQL Database-gateway IP-adresser (vanligtvis två IP-adresser per region). Om du väljer det här läget kan resultera i högre svarstider och lägre dataflöde, beroende på typen av arbetsbelastning. Vi rekommenderar starkt att den `Redirect` anslutningsprincip över den `Proxy` anslutningsprincip för lägsta svarstid och högsta dataflöde.
- **standard:** Detta tillämpas principen på alla servrar när du har skapat, såvida inte du uttryckligen ändrar principen till antingen `Proxy` eller `Redirect`. Principen som beror på om anslutningar kommer från i Azure (`Redirect`) eller utanför Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Anslutningen från i Azure

Om du ansluter från inom Azure dina anslutningar har en princip för `Redirect` som standard. En princip av `Redirect` innebär att när TCP-sessionen har upprättats till Azure SQL-databasen, klientsessionen sedan omdirigeras till rätt databas-kluster med en ändring av den virtuella mål-IP från som Azure SQL Database-gateway med den kluster. Alla efterföljande paket som flödar därefter direkt till klustret, vilket kringgår Azure SQL Database-gateway. Följande diagram illustrerar det här flödet i nätverkstrafiken.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Anslutningen från utanför Azure

Om du ansluter från platser utanför Azure, dina anslutningar har en princip för `Proxy` som standard. En princip av `Proxy` innebär att TCP-sessionen har upprättats via gatewayen för Azure SQL-databasen och alla efterföljande paket som flödar via gatewayen. Följande diagram illustrerar det här flödet i nätverkstrafiken.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database gateway IP-adresser

Om du vill ansluta till en Azure SQL database från lokala resurser, som du vill tillåta utgående trafik till Azure SQL Database-gatewayen för din Azure-region. Dina anslutningar bara gå via gatewayen när du ansluter i `Proxy` läge, vilket är standard när du ansluter från lokala resurser.

I följande tabell visas de primära och sekundära IP-adresserna för Azure SQL Database-gateway för alla dataområden. Det finns två IP-adresser för vissa regioner. Den primära IP-adressen är den aktuella IP-adressen till gatewayen i dessa regioner och den andra IP-adressen är en IP-adress för redundans. Redundans-adressen är den adress som vi kan också flytta din server för att hålla hög tjänsternas tillgänglighet. För dessa regioner rekommenderar vi att du tillåter utgående trafik till båda IP-adresserna. Den andra IP-adressen ägs av Microsoft och lyssnar inte på alla tjänster förrän den aktiveras genom Azure SQL Database för att acceptera anslutningar.

| Regionsnamn | Primär IP-adress | Sekundär IP-adress |
| --- | --- |--- |
| Australien, östra | 13.75.149.87 | 40.79.161.1 |
| Sydöstra Australien | 191.239.192.109 | 13.73.109.251 |
| Brasilien, södra | 104.41.11.5 | |
| Kanada, centrala | 40.85.224.249 | |
| Kanada, östra | 40.86.226.166 | |
| Centrala USA | 23.99.160.139 | 13.67.215.62 |
| Östra Kina 1 | 139.219.130.35 | |
| Kina, östra 2 | 40.73.82.1 | |
| Norra Kina 1 | 139.219.15.17 | |
| Kina, norra 2 | 40.73.50.0 | |
| Asien, östra | 191.234.2.139 | 52.175.33.150 |
| Östra USA 1 | 191.238.6.43 | 40.121.158.30 |
| USA, östra 2 | 191.239.224.107 | 40.79.84.180 * |
| Centrala Frankrike | 40.79.137.0 | 40.79.129.1 |
| Tyskland, centrala | 51.4.144.100 | |
| Nordöstra Tyskland | 51.5.144.179 | |
| Indien, centrala | 104.211.96.159 | |
| Indien, södra | 104.211.224.146 | |
| Indien, västra | 104.211.160.80 | |
| Japan, östra | 191.237.240.43 | 13.78.61.196 |
| Japan, västra | 191.238.68.11 | 104.214.148.156 |
| Sydkorea, centrala | 52.231.32.42 | |
| Sydkorea, södra | 52.231.200.86 |  |
| USA, norra centrala | 23.98.55.75 | 23.96.178.199 |
| Europa, norra | 191.235.193.75 | 40.113.93.91 |
| USA, södra centrala | 23.98.162.75 | 13.66.62.124 |
| Asien, sydöstra | 23.100.117.95 | 104.43.15.0 |
| Södra Storbritannien | 51.140.184.11 | |
| Västra Storbritannien | 51.141.8.11| |
| USA, västra centrala  | 13.78.145.25 | |
| Europa, västra | 191.237.232.75 | 40.68.37.158 |
| Västra USA 1 | 23.99.34.75 | 104.42.238.205 |
| USA, västra 2 | 13.66.226.202 | |
||||

\* **OBS:** *Östra USA 2* har också en tertiär IP-adressen för `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Ändra principen för Azure SQL Database-anslutning

Du kan ändra principen för Azure SQL Database för en Azure SQL Database-server med den [an-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) kommando.

- Om din princip har angetts till `Proxy`, alla nätverksanslutningar paket flow via Azure SQL Database-gateway. Du måste tillåta utgående trafik till endast Azure SQL Database gateway-IP för den här inställningen. Med hjälp av en inställning av `Proxy` har flera svarstider än inställningen `Redirect`.
- Om din anslutning principinställningen `Redirect`, alla nätverksanslutningar paket flow direkt till databasen-klustret. Du måste tillåta utgående trafik till flera IP-adresser för den här inställningen.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript för att ändra anslutningsinställningar via PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

> [!IMPORTANT]
> Det här skriptet kräver den [Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du ändrar principen.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript för att ändra anslutningsinställningar via Azure CLI

> [!IMPORTANT]
> Det här skriptet kräver den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Följande CLI-skript visar hur du ändrar principen.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du ändrar principen för Azure SQL Database för en Azure SQL Database-server finns i [an-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Information om Azure SQL Database-anslutningsbeteendet för klienter som använder ADO.NET 4.5 eller senare finns i [portar utöver 1433 för ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Allmän utveckling översiktlig information finns i [översikt över SQL Database Application Development](sql-database-develop-overview.md).
