---
title: Snabbstart Ladda ned en granskningsrapport med Azure-portalen | Microsoft Docs
description: Lär dig hur du laddar ned en granskningsrapport med Azure-portalen
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3f4090f1724850b0263905a0593fc77cc6dbfd16
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51620742"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Snabbstart: Ladda ned en granskningsrapport med Azure-portalen

I den här snabbstarten får du lära dig hur du hämtar granskningsloggarna för din klient för de senaste 24 timmarna.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver:

* En Azure Active Directory-klientorganisation. 
* En användare som har rollen **säkerhetsadministratör**, **säkerhetsläsare** eller **global administratör** för klienten. Dessutom kan alla användare i klienten kan komma åt sina egna granskningsloggar.

## <a name="quickstart-download-an-audit-report"></a>Snabbstart: Ladda ned en granskningsrapport

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** från det vänstra navigeringsfönstret och använd **Växla katalog** för att välja din active directory.
3. Från instrumentpanelen väljer du **Azure Active Directory** och sedan **Granskningsloggar**. 
4. Välj **senaste 24 timmarna** i filterlistrutan **datumintervall** och välj **Tillämpa** för att visa granskningsloggarna för de senaste 24 timmarna. 
5. Välj knappen **Hämta** för att ladda ned en CSV-fil som innehåller de filtrerade posterna. 

![Rapportering](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Nästa steg

* [Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen](concept-sign-ins.md)
* [Azure Active Directory-rapporteringskvarhållning](reference-reports-data-retention.md)
* [Azure Active Directory-rapporteringssvarstider](reference-reports-latencies.md)
