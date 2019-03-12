---
title: Introduktion till virtuella Azure Stack-datorer
description: Lär dig mer om Azure Stack-datorer
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: bf075372da4cd23c89c24dcb30ffbcfac6775fe4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775092"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introduktion till virtuella Azure Stack-datorer

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack har virtuella datorer (VM) som en typ av en behovsbaserade och skalbara datorresurs. Du kan välja en virtuell dator när du behöver mer kontroll över datormiljön. Den här artikeln innehåller information innan du skapar din första virtuella dator.

En Azure Stack-VM ger dig virtualiseringsflexibilitet utan att behöva hantera kluster eller enskilda datorer. Du behöver dock fortfarande underhålla den virtuella datorn genom att utföra uppgifter som att konfigurera, korrigera och underhålla programvaran som körs på den.

Du kan använda Azure Stack virtuella datorer på flera olika sätt. Exempel:

- **Utveckling och testning**  
    Azure Stack virtuella datorer kan du skapa en dator med en specifik konfiguration som krävs för att koda och testa ett program.

- **Program i molnet**  
    Eftersom efterfrågan på ditt program kan variera, kan det vara ekonomiskt meningsfullt att köra den på en virtuell dator i Azure Stack. Du betalar extra för virtuella datorer när du behöver dem och stänga dem när du inte.

- **Utökat datacenter**  
    Virtuella datorer i ett Azure Stack-nätverk kan anslutas till din organisations nätverk eller till Azure.

De virtuella datorerna som ditt program använder kan skala upp eller skala ut, beroende på vilka krävs för att uppfylla dina behov.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Vad behöver jag tänka på innan du skapar en virtuell dator

Det finns alltid designaspekter när du bygga ut en programinfrastruktur i Azure Stack. Följande aspekter av en virtuell dator är viktiga att tänka på innan du börjar skapa din infrastruktur:

- Namnen på din programresurser.
- Storleken på den virtuella datorn.
- Det maximala antalet virtuella datorer som kan skapas.
- Det operativsystem som den virtuella datorn körs.
- Konfiguration av den virtuella datorn när den har startat.
- Relaterade resurser som den virtuella datorn.

### <a name="naming"></a>Namngivning

En virtuell dator har ett namn som tilldelats och har ett datornamn som konfigurerats som en del av operativsystemet. En virtuell dators namn kan bestå av upp till 15 tecken.

Om du använder Azure Stack för att skapa operativsystemsdisken är datornamnet och namnet på virtuella datorn samma. Om du laddar upp och använda en egen avbildning som innehåller ett tidigare konfigurerat operativsystem och använda den för att skapa en virtuell dator, skilja namnen sig. När du laddar upp en egen avbildningsfil som du bör kontrollera att datornamnet i operativsystemet och virtuella datornamn är samma.

### <a name="vm-size"></a>Storlek på virtuell dator

Storleken på den virtuella datorn som du använder bestäms av den arbetsbelastning som du vill köra. Storleken som du väljer avgör sedan faktorer som processorkraft, minne och lagringskapacitet. Azure Stack erbjuder en mängd olika storlekar för att olika användningar.

### <a name="vm-limits"></a>VM-gränser

Din prenumeration har Standardkvotgränser som kan påverka distributionen av virtuella datorer för ditt projekt. Den aktuella gränsen på basis av per prenumeration är 20 virtuella datorer per region.

### <a name="operating-system-disks-and-images"></a>Operativsystemsdiskar och avbildningar

Virtuella datorer använder virtuella hårddiskar (VHD:ar) för att lagra sitt operativsystem (OS) och sin data. VHD:ar används också för de avbildningar du kan välja mellan för att installera ett operativsystem. Azure Stack ger ett spektrum ska användas med olika versioner och typer av operativsystem. Marketplace-avbildningar identifieras av avbildningens utgivare, erbjudande, SKU och version (vanligtvis versionen har angetts som **senaste**.)

I följande tabell visar hur du hittar information för en bild:

|Metod|Beskrivning|
|---------|---------|
|Azure Stack-portalen|Värdena anges automatiskt åt dig när du väljer en avbildning som du vill använda.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API:er     |[Lista över avbildningsutgivare](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Lista över avbildningserbjudanden](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Visa bild SKU: er](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Du kan välja att ladda upp och använda en egen avbildning. Om du gör används inte den Utgivarnamn, erbjudande och SKU.

### <a name="extensions"></a>Tillägg

VM-tillägg ger din virtuella dator fler funktioner genom konfiguration efter distribution och automatiserade uppgifter.
Dessa vanliga uppgifter kan utföras med hjälp av tillägg:

- **Köra anpassade skript**  
    Det anpassade skripttillägget hjälper dig att konfigurera arbetsbelastningar på den virtuella datorn genom att köra skriptet när den virtuella datorn etableras.

- **Distribuera och hantera konfigurationer**  
    PowerShell Desired State Configuration (DSC)-tillägget kan du konfigurera DSC på en virtuell dator för att hantera konfigurationer och miljöer.

- **Samla in diagnostikdata**  
    Azure Diagnostics-tillägget kan du konfigurera den virtuella datorn för att samla in diagnostikdata som kan användas för att övervaka hälsotillståndet för ditt program.

### <a name="related-resources"></a>Relaterade resurser

Resurserna i följande tabell som används av den virtuella datorn och måste finnas eller skapas när den virtuella datorn skapas:

|Resurs|Krävs|Beskrivning|
|---------|---------|---------|
|Resursgrupp|Ja|Den virtuella datorn måste ingå i en resursgrupp.|
|Lagringskonto|Nej|Den virtuella datorn behöver inte lagringskontot för att lagra sina virtuella hårddiskar om du använder Managed Disks. <br>Den virtuella datorn behöver lagringskontot för att lagra sina virtuella hårddiskar om använder ohanterade diskar.|
|Virtuellt nätverk|Ja|Den virtuella datorn måste ingå i ett virtuellt nätverk.|
|Offentlig IP-adress|Nej|Den virtuella datorn kan tilldelas en offentlig IP-adress för att möjliggöra fjärråtkomst till den.|
|Nätverksgränssnitt|Ja|Den virtuella datorn behöver nätverksgränssnittet för att kunna kommunicera i nätverket.|
|Datadiskar|Nej|Den virtuella datorn kan innehålla datadiskar för att expandera lagringskapaciteten.|

## <a name="create-your-first-vm"></a>Skapa din första virtuella dator

Har du flera alternativ för att skapa en virtuell dator. Ditt val beror på din miljö. I följande tabell innehåller information som hjälper dig att komma igång med att skapa den virtuella datorn:

|Metod|Artikel|
|---------|---------|
|Azure Stack-portalen|Skapa en Windows-dator med Azure Stack-portalen<br>[Skapa en Linux-dator med hjälp av Azure Stack-portalen](azure-stack-quick-linux-portal.md)|
|Mallar|Azure Stack-snabbstartsmallar finns på:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Skapa en virtuell Windows-dator med hjälp av PowerShell i Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)<br>[Skapa en Linux-dator med hjälp av PowerShell i Azure Stack](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Skapa en virtuell Windows-dator med hjälp av CLI i Azure Stack](azure-stack-quick-create-vm-windows-cli.md)<br>[Skapa en Linux-dator med hjälp av CLI i Azure Stack](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Hantera din virtuella dator

Du kan hantera virtuella datorer från en webbläsarbaserad portal, kommandoradsverktyg med skriptstöd eller direkt via API: er. Några vanliga hanteringsuppgifter som du kan utföra är:

- Hämta information om en virtuell dator
- Ansluta till en virtuell dator
- Hantera tillgänglighet
- Gör säkerhetskopior

### <a name="get-information-about-your-vm"></a>Få information om den virtuella datorn

I följande tabell visar några av de sätt som du kan få information om en virtuell dator.

|Metod|Beskrivning|
|---------|---------|
|Azure Stack-portalen|På navmenyn klickar du på virtuella datorer och välj sedan den virtuella datorn från listan. På sidan för den virtuella datorn har åtkomst till översikter med information, inställning av värden och övervakning av mått.|
|Azure PowerShell|Hantera virtuella datorer är liknande i Azure och Azure Stack. Mer information om hur du använder PowerShell finns i följande ämne i Azure:<br>[Skapa och hantera Windows virtuella datorer med Azure PowerShell-modulen](../../virtual-machines/windows/tutorial-manage-vm.md#understand-vm-sizes)|
|Klient-SDK: er|Använda C# för att hantera virtuella datorer är liknande i Azure och Azure Stack. Mer information finns i följande ämne i Azure:<br>[Skapa och hantera Windows virtuella datorer i Azure med C#](../../virtual-machines/windows/csharp.md)|

### <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Du kan använda den **Connect** knappen i Azure Stack-portalen för att ansluta till den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- [Överväganden för virtuella datorer i Azure Stack](azure-stack-vm-considerations.md)
