---
title: Neka offentlig nätverks åtkomst – Azure Portal-Azure Database for MySQL
description: Lär dig hur du konfigurerar neka offentlig nätverks åtkomst med Azure Portal för din Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: f8156b01244012d78214f2ba8c49ed76dbceed6d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118791"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Neka offentlig nätverks åtkomst i Azure Database for MySQL att använda Azure Portal

I den här artikeln beskrivs hur du kan konfigurera en Azure Database for MySQL-server för att neka alla offentliga konfigurationer och bara tillåta anslutningar via privata slut punkter för att ytterligare förbättra nätverks säkerheten.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

* En [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ange neka offentlig nätverks åtkomst

Följ dessa steg om du vill ställa in MySQL server neka offentlig nätverks åtkomst:

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL-server.

1. På sidan MySQL server, under **Inställningar**, klickar du på **anslutnings säkerhet** för att öppna sidan anslutnings säkerhets konfiguration.

1. I **neka offentlig nätverks åtkomst**väljer du **Ja** för att aktivera neka offentlig åtkomst för MySQL-servern.

    ![Azure Database for MySQL neka nätverks åtkomst](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Klicka på **Spara** för att spara ändringarna.

1. Ett meddelande bekräftar att anslutnings säkerhets inställningen har Aktiver ATS.

    ![Azure Database for MySQL nekad nätverks åtkomst](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).