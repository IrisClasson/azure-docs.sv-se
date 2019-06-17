---
title: Flera IP-adresser för virtuella datorer i Azure - portalen | Microsoft Docs
description: Lär dig att tilldela flera IP-adresser till en virtuell dator med Azure-portalen | Resource Manager.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
ms.openlocfilehash: b1873b770a6b4280b7098c68ecb75cc1411fe453
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60747020"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Tilldela flera IP-adresser till virtuella datorer med Azure portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Den här artikeln beskriver hur du skapar en virtuell dator (VM) med Azure Resource Manager-distributionsmodellen med Azure portal. Flera IP-adresser kan inte tilldelas till resurser som skapats via den klassiska distributionsmodellen. Mer information om Azures distributionsmodeller i [förstå distributionsmodeller](../resource-manager-deployment-model.md) artikeln.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Om du vill skapa en virtuell dator med flera IP-adresser eller en statisk privat IP-adress, måste du skapa den med hjälp av PowerShell eller Azure CLI. Läs hur, klickar du på PowerShell eller CLI-alternativ överst i den här artikeln. Du kan skapa en virtuell dator med en dynamisk privat IP-adress och (frivilligt) en offentlig IP-adress. Använda portalen genom att följa stegen i den [skapa en virtuell Windows-dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md) eller [skapa en Linux VM](../virtual-machines/linux/quick-create-portal.md) artiklar. När du har skapat den virtuella datorn kan du ändra IP-adresstyp från dynamisk till statisk och lägga till ytterligare IP-adresser med hjälp av portalen genom att följa stegen i den [Lägg till IP-adresser till en virtuell dator](#add) i den här artikeln.

## <a name="add"></a>Lägg till IP-adresser till en virtuell dator

Du kan lägga till privata och offentliga IP-adresser till en Azure-nätverksgränssnitt genom att följa stegen nedan. Exemplen i avsnitten nedan förutsätter att du redan har en virtuell dator med de tre IP-konfigurationer som beskrivs i den [scenariot](#scenario), men det inte krävs.

### <a name="coreadd"></a>Grundläggande steg

1. Bläddra till Azure-portalen på https://portal.azure.com och loggar in, om det behövs.
2. I portalen klickar du på **fler tjänster** > typ *virtuella datorer* i filterrutan och sedan på **virtuella datorer**.
3. I den **virtuella datorer** du på den virtuella datorn som du vill lägga till IP-adresser till. Klicka på **nätverksgränssnitt** fönstret som visas och välj sedan de nätverksgränssnitt som du vill lägga till IP-adresser till i den virtuella datorn. I exemplet som visas i följande bild, nätverkskortet med namnet *myNIC* från den virtuella datorn med namnet *myVM* väljs:

    ![Nätverksgränssnitt](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. I fönstret som visas för det nätverkskort som du har valt, klickar du på **IP-konfigurationer**.

Slutför stegen i ett av avsnitten som följer, beroende på vilken typ av IP-adress som du vill lägga till.

### <a name="add-a-private-ip-address"></a>**Lägg till en privat IP-adress**

Utför följande steg för att lägga till en ny privat IP-adress:

1. Utför stegen i den [viktiga steg](#coreadd) i den här artikeln.
2. Klicka på **Lägg till**. I den **Lägg till IP-konfiguration** fönstret som visas, skapa en IP-konfiguration med namnet *IPConfig 4* med *10.0.0.7* som en *statiska* privata IP-adress och klicka sedan på **OK**.

    > [!NOTE]
    > När du lägger till en statisk IP-adress, måste du ange en oanvända, giltig adress på undernätet som nätverkskortet är anslutet till. Om den adress som du väljer inte är tillgänglig i portalen visas ett X för IP-adressen och du måste välja ett annat.

3. När du klickar på OK fönstret stängs och du ser den nya IP-konfigurationen visas. Klicka på **OK** att Stäng den **Lägg till IP-konfiguration** fönstret.
4. Du kan klicka på **Lägg till** att lägga till ytterligare IP-konfigurationer eller stänga alla öppna bladen för att slutföra att lägga till IP-adresser.
5. Lägga till de privata IP-adresserna till VM-operativsystem genom att följa stegen i den [Lägg till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln.

### <a name="add-a-public-ip-address"></a>Lägg till en offentlig IP-adress

En offentlig IP-adress har lagts till genom att associera en offentlig IP-adressresurs till en ny IP-konfiguration eller en befintlig IP-konfiguration.

> [!NOTE]
> Offentliga IP-adresser har en nominell avgift. Mer information om priser för IP-adress i [prissättning för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-public-ip"></a>Skapa en offentlig IP-adressresurs

En offentlig IP-adress är en inställning för en offentlig IP-adressresurs. Om du har en offentlig IP-adressresurs som inte är för närvarande är kopplad till en IP-konfiguration som du vill koppla till en IP-konfiguration kan hoppa över följande steg och slutföra stegen i ett av avsnitten som följer, som du behöver. Om du inte har en tillgänglig offentlig IP-adressresurs, utför du följande steg för att skapa en:

1. Bläddra till Azure-portalen på https://portal.azure.com och loggar in, om det behövs.
3. I portalen klickar du på **skapa en resurs** > **nätverk** > **offentliga IP-adressen**.
4. I den **skapa offentlig IP-adress** fönstret som visas, ange en **namn**väljer en **IP-adresstilldelning** typ, en **prenumeration**, ett **Resursgrupp**, och en **plats**, klicka sedan på **skapa**, enligt följande bild:

    ![Skapa en offentlig IP-adressresurs](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Slutför stegen i ett av avsnitten som följer för att associera den offentliga IP-adressresursen till en IP-konfiguration.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associera den offentliga IP-adressresursen till en ny IP-konfiguration

1. Utför stegen i den [viktiga steg](#coreadd) i den här artikeln.
2. Klicka på **Lägg till**. I den **Lägg till IP-konfiguration** fönstret som visas, skapa en IP-konfiguration med namnet *IPConfig 4*. Aktivera den **offentliga IP-adressen** och välj en befintlig, tillgängliga offentlig IP-adressresurs från den **Välj offentlig IP-adress** fönstret som visas.

    När du har valt den offentliga IP-adressresursen, klickar du på **OK** och stänger fönstret. Om du inte har en befintlig offentlig IP-adress kan du skapa en genom att följa stegen i den [skapa en offentlig IP-adressresurs](#create-public-ip) i den här artikeln. 

3. Granska den nya IP-konfigurationen. Även om en privat IP-adress inte har tilldelats tilldelades någon automatiskt IP-konfiguration, eftersom alla IP-konfigurationer måste ha en privat IP-adress.
4. Du kan klicka på **Lägg till** att lägga till ytterligare IP-konfigurationer eller stänga alla öppna bladen för att slutföra att lägga till IP-adresser.
5. Lägga till den privata IP-adressen till VM-operativsystem genom att följa stegen för operativsystemet i den [Lägg till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till offentliga IP-adress för operativsystemet.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associera den offentliga IP-adressresursen till en befintlig IP-konfiguration

1. Utför stegen i den [viktiga steg](#coreadd) i den här artikeln.
2. Klicka på IP-konfiguration som du vill lägga till den offentliga IP-adressresursen till.
3. Klicka på i fönstret IPConfig **IP-adress**.
4. I den **Välj offentlig IP-adress** fönstret som visas, Välj en offentlig IP-adress.
5. Klicka på **spara** och fönstren Stäng. Om du inte har en befintlig offentlig IP-adress kan du skapa en genom att följa stegen i den [skapa en offentlig IP-adressresurs](#create-public-ip) i den här artikeln.
3. Granska den nya IP-konfigurationen.
4. Du kan klicka på **Lägg till** att lägga till ytterligare IP-konfigurationer eller stänga alla öppna bladen för att slutföra att lägga till IP-adresser. Lägg inte till offentliga IP-adress för operativsystemet.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
