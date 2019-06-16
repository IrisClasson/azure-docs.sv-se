---
title: Delegera en underdomän för Azure DNS med Azure PowerShell
description: Lär dig hur du delegerar en Azure DNS-underdomänen med Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: victorh
ms.openlocfilehash: 4ee4d9e6390c9a091096bb7c06160b76fd8af90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730289"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegera en underdomän för Azure DNS med Azure PowerShell

Du kan använda Azure PowerShell för att delegera DNS-underdomänen. Till exempel om du äger domänen contoso.com, kan du delegera en underdomän som kallas *engineering* till en annan, separat zon som du kan administrera separat från zonen contoso.com.

Om du vill kan du delegera en underdomän med hjälp av den [Azure-portalen](delegate-subdomain.md).

> [!NOTE]
> Contoso.com används som exempel i den här artikeln. Använd ditt eget domännamn i stället för contoso.com.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill delegera en underdomän i Azure DNS måste du först Delegera din offentliga domän till Azure DNS. Se [delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) anvisningar om hur du konfigurerar dina namnservrar för delegering. När din domän har delegerats till Azure DNS-zon kan delegera du din underdomän.

## <a name="create-a-zone-for-your-subdomain"></a>Skapa en zon för din underdomän

Börja med att skapa zonen för den **engineering** underdomänen.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Observera namnservrarna

Därefter Observera fyra namnservrarna för underdomänen tekniker.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Skapa en testpost

Skapa en **A** poster i zonen engineering för testning.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Skapa en NS-post

Skapa sedan en namnet namnserver-post för den **engineering** zonen i zonen contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testa delegeringen

Använda nslookup för att testa delegeringen.

1. Öppna ett PowerShell-fönster.
2. Kommandotolken skriver du: `nslookup www.engineering.contoso.com.`
3. Du bör få ett icke-auktoritativa svar som visar adressen **10.10.10.10**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurera omvänd DNS för tjänster som hanteras i Azure](dns-reverse-dns-for-azure-services.md).