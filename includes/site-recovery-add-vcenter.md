---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171753"
---
I **Add vCenter** anger du ett eget namn för vSphere-värden eller vCenter-servern och sedan serverns IP-adress eller fullständiga domännamn. Lämna porten som 443 om inte dina VMware-servrar är konfigurerade för att lyssna efter begäranden på en annan port. Välj det konto som ska anslutas till VMware vCenter- eller vSphere ESXi-servern. Klicka på **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Om du vill lägga till VMware vCenter-server eller VMware vSphere-värden med ett konto som inte har administratörsbehörighet på servern vCenter- eller värdservern, se till att har kontot dessa behörigheter aktiverade: Datacenter, datalager, mapp, värd, nätverk, resurs, virtuell dator och vSphere Distributed Switch. Dessutom behöver VMware vCenter-servern behörigheter för lagringsvisning aktiverad.
