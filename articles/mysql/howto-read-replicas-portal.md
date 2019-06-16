---
title: Skapa och hantera skrivskyddade repliker i Azure Database för MySQL
description: Den här artikeln beskriver hur du konfigurerar och hanterar skrivskyddade repliker i Azure Database för MySQL med hjälp av portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b422718a1eaec483acdc2c8ab37442b9aea78aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510786"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Hur du skapar och hanterar Läs repliker i Azure Database för MySQL med Azure portal

I den här artikeln får lära du dig att skapa och hantera skrivskyddade repliker i tjänsten Azure Database for MySQL med Azure portal.

> [!IMPORTANT]
> Du kan skapa en skrivskyddad replik i samma region som din huvudservern eller i alla andra Azure-regioner valfri. Replikering över flera regioner är för närvarande i offentlig förhandsversion.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md) som ska användas som huvudserver.

> [!IMPORTANT]
> Läs replica-funktionen är endast tillgänglig för Azure Database för MySQL-servrar i generell användning eller Minnesoptimerade prisnivåer. Kontrollera huvudservern är i något av dessa prisnivåer.

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

En skrivskyddad replikserver kan skapas med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj befintlig Azure Database for MySQL-server som du vill använda som en. Den här åtgärden öppnar den **översikt** sidan.

3. Välj **replikering** från menyn, under **inställningar**.

4. Välj **lägga till en replik**.

   ![Azure Database för MySQL - replikering](./media/howto-read-replica-portal/add-replica.png)

5. Ange ett namn för replikservern.

    ![Azure Database för MySQL - namn](./media/howto-read-replica-portal/replica-name.png)

6. Välj en plats för replikservern. Du kan skapa en replik i alla Azure-regioner. Standardplatsen är samma som huvudserver

    ![Azure Database för MySQL - replikplatsen](./media/howto-read-replica-portal/replica-location.png)

7. Välj **OK** att bekräfta att skapa en replik.

> [!NOTE]
> Läs repliker skapas med samma serverkonfiguration som huvudserver. Serverkonfigurationen repliken kan ändras när den har skapats. Du rekommenderas att repliken serverkonfigurationen bör hållas lika med eller större värden än huvudservern så repliken kan hålla jämna steg med huvudservern.

När du har skapat replikservern, den kan visas från den **replikering** bladet.

   ![Azure Database för MySQL - listan repliker](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver

> [!IMPORTANT]
> Stoppa replikering till en server kan inte ångras. När replikering har upphört mellan huvud- och repliken och kan inte den ångras. Replikservern sedan blir en fristående server och stöder nu både läs- och skrivåtgärder. Den här servern kan inte göras i en replik igen.

Om du vill stoppa replikering mellan en och en replikserver från Azure portal, använder du följande steg:

1. Välj master Azure Database för MySQL-server i Azure-portalen. 

2. Välj **replikering** från menyn, under **inställningar**.

3. Välj replikservern som du vill stoppa replikering för.

   ![Azure Database för MySQL - Stoppa replikering Välj server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Välj **replikeringen stoppas**.

   ![Azure Database för MySQL - replikeringen stoppas](./media/howto-read-replica-portal/stop-replication.png)

5. Bekräfta att du vill stoppa replikering genom att klicka på **OK**.

   ![Azure Database för MySQL - replikeringen stoppas bekräfta](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Ta bort en replikserver

Använd följande steg för att ta bort en skrivskyddad replikserver från Azure portal:

1. Välj master Azure Database för MySQL-server i Azure-portalen.

2. Välj **replikering** från menyn, under **inställningar**.

3. Välj replikservern som du vill ta bort.

   ![Azure Database for MySQL – ta bort repliken väljer server](./media/howto-read-replica-portal/delete-replica-select.png)

4. Välj **ta bort replik**

   ![Azure Database for MySQL – ta bort replik](./media/howto-read-replica-portal/delete-replica.png)

5. Skriv namnet på den repliken och klicka på **ta bort** att bekräfta borttagningen av repliken.  

   ![Azure Database for MySQL – ta bort replik bekräfta](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Ta bort en huvudserver

> [!IMPORTANT]
> Tar bort en huvudserver stoppar replikeringen till alla replikservrar och borttagningar huvudservern själva. Replikservrar bli fristående servrar som stöder nu både läs- och skrivåtgärder.

Använd följande steg för att ta bort en huvudserver från Azure portal:

1. Välj master Azure Database för MySQL-server i Azure-portalen.

2. Från den **översikt**väljer **ta bort**.

   ![Azure Database for MySQL – ta bort master](./media/howto-read-replica-portal/delete-master-overview.png)

3. Skriv namnet på huvudservern och klicka på **ta bort** att bekräfta borttagningen av huvudservern.  

   ![Azure Database for MySQL – ta bort master](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Övervakare för replikering

1. I den [Azure-portalen](https://portal.azure.com/), väljer du repliken Azure Database for MySQL-server som du vill övervaka.

2. Under den **övervakning** avsnittet på sidopanelen, Välj **mått**:

3. Välj **replikeringsfördröjning i sekunder** från den nedrullningsbara listan över tillgängliga mått.

   ![Välj replikeringsfördröjning](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Välj det tidsintervall som du vill visa. Bilden nedan väljer en 30 minuters tidsintervall.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visa replikeringsfördröjning för det valda tidsintervallet. Bilden nedan visar de senaste 30 minuterna.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [läsa repliker](concepts-read-replicas.md)