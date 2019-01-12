---
title: Utveckla appar för Azure Stack | Microsoft Docs
description: Utvecklingsöverväganden för prototyper program på Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 60336a56437b3d5c5d39d8c1584d91c909ac47ff
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246202"
---
# <a name="develop-for-azure-stack"></a>Utveckla för Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan börja utveckla program idag, även om du inte har åtkomst till en Azure Stack-miljö. Eftersom Azure Stack levererar Microsoft Azure-tjänster som körs i ditt datacenter, kan du använda liknande verktyg och processer som du utvecklar mot Azure Stack, precis som med Azure.

## <a name="development-considerations"></a>Utvecklingsöverväganden

Vissa förberedelser och genom att följa vägledningen i följande avsnitt kan använda du Azure för att emulera en Azure Stack-miljö.

* Du kan skapa Azure Resource Manager-mallar som kan distribueras till Azure Stack i Azure. Se [mall överväganden](azure-stack-develop-templates.md) anvisningar om hur du utvecklar mallar så portabilitet.
* Det finns skillnader i tjänstens tillgänglighet och versionshantering mellan Azure och Azure Stack. Du kan använda den [Azure Stack-principmodulen](azure-stack-policy-module.md) att begränsa Azure tjänsttyper för tillgänglighet och resursen till vad som är tillgängligt i Azure Stack. Begränsa tjänster garanterar att dina program är beroende av tjänster som är tillgängliga i Azure Stack.
* Den [Azure Stack-Snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates) är vanliga Scenarioexempel som visar hur du utvecklar mallar som kan distribueras till Azure och Azure Stack.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Stack development finns i följande artiklar:

* [Azure Resource Manager mall Metodtips](azure-stack-develop-templates.md)
* [Azure Stack-Snabbstartsmallarna på GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)