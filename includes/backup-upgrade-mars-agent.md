---
title: Uppgradera Azure Backup-agenten
description: Den här informationen beskriver varför du bör uppgradera Azure Backup-agenten och var du kan hämta uppgraderingen.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 56310b7356dd9e263238234cf3e28bd498fa70fc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127816"
---
## <a name="upgrade-the-mars-agent"></a>Uppgradera MARS-agenten

Versioner av Microsoft Azure Recovery Service MARS-agenten nedan 2.0.9083.0 är beroende av Azure Access Control service (ACS). MARS-agenten kallas även för Azure Backup-agenten. I 2018 Azure [inaktuella Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Från och den 19 mars 2018, uppstår alla versioner av MARS-agenten nedan 2.0.9083.0 fel vid säkerhetskopiering. Att undvika eller lösa Säkerhetskopieringsfel, [uppgradera MARS-agenten till den senaste versionen](https://go.microsoft.com/fwlink/?linkid=229525). Om du vill identifiera servrar som kräver en uppgradering av MARS-agenten, följer du stegen i den [Backup-bloggen för att uppgradera MARS agenter](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). MARS-agenten används för att säkerhetskopiera filer och mappar och systemtillstånd till Azure. System Center DPM och Azure Backup Server kan du använda MARS-agenten för att säkerhetskopiera data till Azure.
