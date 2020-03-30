---
title: Skapa en SQL Server Windows-VM i portalen | Microsoft Docs
description: Den här kursen visar hur du skapar virtuell Windows SQL Server 2017-dator i Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 801a6fc0602882d1af49c06bafcfd51942e6da2e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75965657"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Windows-dator med SQL Server 2017 i Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Den här snabbstarten beskriver hur du skapar en virtuell SQL Server-dator i Azure Portal.


  > [!TIP]
  > - I snabbstarten finns en sökväg för snabb etablering och anslutning till en SQL-VM. Mer information om andra etableringsalternativ för SQL-VM finns i [Etableringsguide för Windows SQL Server-datorer i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md).
  > - Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Skaffa en Azure-prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Välj en avbildning av en virtuell SQL Server-dator

1. Logga in på [Azure-portalen](https://portal.azure.com) med ditt konto.

1. Välj **Azure SQL** i menyn till vänster i Azure-portalen. Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure *SQL* i sökrutan.
1. Välj **+Lägg till** om du vill öppna **alternativsidan Välj SQL-distribution.** Du kan visa ytterligare information genom att välja **Visa information** på panelen **virtuella SQL-datorer.**
1. Välj **den kostnadsfria SQL Server-licensen: SQL Server 2017 Developer på Windows Server 2016-avbildningen** i listrutan.

   ![Nytt sökfönster](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. Välj **Skapa**.

   ![Nytt sökfönster](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Ange grundläggande information

Ange följande information på fliken **Grunderna:**

1. I avsnittet **Projektinformation** väljer du din Azure-prenumeration och väljer sedan **Skapa ny** för att skapa en ny resursgrupp. Skriv _SQLVM-RG_ för namnet.

   ![Prenumeration](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. Under **Instansinformation:**
    1. Skriv _SQLVM_ för **namnet på den virtuella datorn**. 
    1. Välj en plats för **din region**. 
    1. I den här snabbstarten lämnar du **tillgänglighetsalternativ** inställda på _Ingen infrastrukturredundans krävs_. Mer information om tillgänglighetsalternativ finns i [Tillgänglighet](../../windows/availability.md). 
    1. Välj Gratis SQL _Server-licens i listan Bild: SQL Server 2017 Developer på Windows Server 2016_. **Image** 
    1. Välj att **ändra storlek** för **storleken på** den virtuella datorn och välj **A2 Basic-erbjudandet.** Var noga med att rensa upp dina resurser när du är klar med dem för att förhindra oväntade avgifter. 

   ![Information om instans](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. Under **Administratörskonto**anger du ett användarnamn, till exempel _azureuser_ och ett lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administratörskonto](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. Under **Regler för inkommande port**väljer du Tillåt valda **portar** och väljer sedan **RDP (3389)** i listrutan. 

   ![Regler för inkommande portar](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server-inställningar

Konfigurera följande alternativ på fliken **INSTÄLLNINGAR för SQL Server:**

1. Under **& Nätverk**väljer du _Offentlig (Internet)_ för **SQL Connectivity** `1401` och ändrar porten för att undvika att använda ett välkänt portnummer i det offentliga scenariot. 
1. Under **SQL-autentisering**väljer du **Aktivera**. SQL-inloggningen konfigureras till samma användarnamn och lösenord som du konfigurerade för den virtuella datorn. Använd standardinställningen för [**Azure Key Vault-integrering**](virtual-machines-windows-ps-sql-keyvault.md). **Lagringskonfiguration är** inte tillgänglig för den grundläggande SQL Server VM-avbildningen, men du kan hitta mer information om tillgängliga alternativ för andra avbildningar vid [lagringskonfiguration](virtual-machines-windows-sql-server-storage-configuration.md#new-vms).  

   ![Säkerhetsinställningar för SQL-servern](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. Ändra andra inställningar om det behövs och välj sedan **Granska + skapa**. 

   ![Granska + skapa](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Skapa den virtuella SQL Server-datorn

På fliken **Granska + skapa** granskar du sammanfattningen och väljer **Skapa** för att skapa SQL Server, resursgrupp och resurser som angetts för den här virtuella datorn.

Du kan övervaka distributionen från Azure Portal. Knappen **Meddelanden** längst upp på skärmen visar grundläggande status för distributionen. Distributionen kan ta flera minuter. 

## <a name="connect-to-sql-server"></a>Anslut till SQL Server

1. Leta reda på den **offentliga IP-adressen** för den virtuella datorn för SQL Server i avsnittet **Översikt** över den virtuella datorns egenskaper.

1. Öppna SQL Server Management Studio [(SSMS) på](/sql/ssms/download-sql-server-management-studio-ssms)en annan dator som är ansluten till Internet .


1. I dialogrutan **Anslut till server** eller **Anslut till databasmotor**, redigerar du värdet för **Servernamn**. Ange den virtuella datorns offentliga IP-adress. Sedan lägger du till ett kommatecken och den anpassade porten **1401** som angavs när du konfigurerade den nya virtuella datorn. Till exempel `11.22.33.444,1401`.

1. I rutan **Autentisering**, markerar du **SQL Server-autentisering**.

1. I rutan **Inloggning**, skriver du namnet på en giltig SQL-inloggning.

1. I rutan **Lösenord**, skriver du lösenordet för inloggningen.

1. Välj **Anslut**.

    ![ssms anslut](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Logga in på den virtuella datorn via en fjärranslutning

Använd följande anvisningar för att ansluta till den virtuella SQL Server-datorn med Fjärrskrivbord:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

När du ansluter till den virtuella SQL Server-datorn kan du starta SQL Server Management Studio och ansluta med Windows-autentisering med hjälp av dina autentiseringsuppgifter som lokal administratör. Om du har aktiverat SQL Server-autentisering kan du också ansluta med SQL-autentisering med hjälp av SQL-inloggningsnamnet och SQL-lösenordet som du konfigurerade under etableringen.

När du har anslutit till datorn kan du direkt ändra inställningarna för datorn och SQL Server efter behov. Du kan till exempel konfigurera brandväggsinställningarna eller ändra konfigurationsinställningarna för SQL Server.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver köra den virtuella SQL-datorn kontinuerligt kan du undvika onödiga kostnader genom att stoppa den när den inte används. Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn genom att ta bort deras kopplade resursgrupper i portalen. Det här tar även permanent bort den virtuella datorn, så använd det här kommandot med försiktighet. Mer information finns i [Manage Azure resources through portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) (Hantera Azure-resurser genom portalen).


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell SQL Server 2017-dator i Azure-portalen. Mer information om hur du migrerar data till den nya SQL-servern finns i följande artikel.

> [!div class="nextstepaction"]
> [Migrera en databas till en virtuell SQL-dator](virtual-machines-windows-migrate-sql.md)
