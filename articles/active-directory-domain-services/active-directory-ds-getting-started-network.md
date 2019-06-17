---
title: 'Azure Active Directory Domain Services: Komma igång | Microsoft Docs'
description: Aktivera Azure Active Directory Domain Services med Azure portal
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 65cc63b32afcc565f1901c4df2893ad103ec0da3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234917"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivera Azure Active Directory Domain Services med Azure portal


## <a name="before-you-begin"></a>Innan du börjar
Se [Nätverksrelaterade aspekter att tänka på med Azure Active Directory Domain Services](network-considerations.md).


## <a name="task-2-configure-network-settings"></a>Uppgift 2: Konfigurera nätverksinställningar
Nästa konfigurationsåtgärd är att skapa ett Azure-nätverk och ett dedikerat undernät i den. Du aktiverar Azure Active Directory Domain Services i detta undernät inom ditt virtuella nätverk. Du kan också välja ett befintligt virtuellt nätverk eller skapa dedikerat undernät i den.

1. Klicka på **virtuellt nätverk** att välja ett virtuellt nätverk.
    > [!NOTE]
    > **Klassiska virtuella nätverk stöds inte för nya distributioner.** Klassiska virtuella nätverk stöds inte för nya distributioner. Befintliga hanterade domäner som distribuerats i klassiska virtuella nätverk fortfarande användas. Microsoft gör att du kan migrera en befintlig hanterad domän från ett klassiskt virtuellt nätverk till ett virtuellt nätverk för Resource Manager inom en snar framtid.
    >

2. På den **Välj ett virtuellt nätverk** kan du se alla befintliga virtuella nätverk. Du ser bara de virtuella nätverk som tillhör resursgruppen och Azure-plats som du har valt på den **grunderna** sida i guiden.
3. Välj det virtuella nätverket där Azure AD Domain Services ska aktiveras. Du kan välja ett befintligt virtuellt nätverk, eller så kan du skapa en ny.

   > [!TIP]
   > **Du kan inte flytta den hanterade domänen till ett annat virtuellt nätverk när du har aktiverat Azure AD Domain Services.** Välj rätt virtuellt nätverk för att aktivera din hanterade domän. När du har skapat en hanterad domän kan du flytta den till ett annat virtuellt nätverk utan att ta bort den hanterade domänen. Vi rekommenderar att du granskar den [Nätverksöverväganden för Azure Active Directory Domain Services](network-considerations.md) innan du fortsätter.  
   >

4. **Skapa virtuellt nätverk:** Klicka på **Skapa nytt** att skapa ett nytt virtuellt nätverk. Använd ett dedikerat undernät för Azure AD Domain Services. Till exempel skapa ett undernät med namnet DomainServices, vilket gör det enkelt för andra administratörer att förstå vad som har distribuerats i undernätet. Klicka på **OK** när du är klar.

    ![Välj virtuellt nätverk](./media/getting-started/domain-services-blade-network-pick-vnet.png)

   > [!WARNING]
   > Se till att välja ett adressutrymme som ligger inom det privata IP-adressutrymmet. IP-adresser som du inte äger som finns i offentligt adressutrymme orsaka fel i Azure AD Domain Services.

5. **Befintligt virtuellt nätverk:** Om du planerar att välja ett befintligt virtuellt nätverk, [skapa ett dedikerat undernät med hjälp av tillägget för virtuella nätverk](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), och välj sedan det undernätet. Klicka på **virtuellt nätverk** att välja det befintliga virtuella nätverket. Klicka på **undernät** att välja dedikerat undernät i det befintliga virtuella nätverket i som du vill aktivera den nya hanterade domänen. Klicka på **OK** när du är klar.

    ![Välj undernät i det virtuella nätverket](./media/getting-started/domain-services-blade-network-pick-subnet.png)

   > [!NOTE]
   > **Riktlinjer för att välja ett undernät**
   > 1. Använd ett dedikerat undernät för Azure AD Domain Services. Distribuera inte virtuella datorer i det här undernätet. Den här konfigurationen kan du konfigurera nätverkssäkerhetsgrupper (NSG) för dina arbetsbelastningar för virtuella datorer utan att avbryta din hanterade domän. Mer information finns i [Nätverksöverväganden för Azure Active Directory Domain Services](network-considerations.md).
   > 2. Markera inte Gateway-undernätet för att distribuera Azure AD Domain Services, eftersom det inte är en konfiguration som stöds.
   > 3. Det undernät som du har valt måste ha minst 3-5 tillgängliga IP-adresser i sitt adressutrymme.

6. När du är klar klickar du på **OK** för att komma till den **administratörsgruppen** i guiden.


## <a name="next-step"></a>Nästa steg
[Uppgift 3: Konfigurera administratörsgrupp och aktivera Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
