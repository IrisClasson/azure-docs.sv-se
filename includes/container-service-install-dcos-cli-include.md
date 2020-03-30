---
title: Installera DC/OS CLI | Microsoft Docs
description: Installera DC/OS CLI.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Containrar, Micro-tjänster, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: e2601d5200f0b8ebcfb856bffb871f01b3acb215
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187991"
---
> [!NOTE]
> Det här är för att arbeta med DC/OS-baserade ACS-kluster. Du behöver inte göra det här för Swarm-baserade ACS-kluster.
> 
> 

[Anslut först till ditt DC/OS-baserade ACS-kluster](../articles/container-service/container-service-connect.md). När du har gjort det, kan du installera DC/OS CLI på din klientdator med nedanstående kommandon:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Om du använder en äldre version av Python, kan du få en del "InsecurePlatformWarnings". Du kan ignorera dem.

För att komma igång utan att starta om gränssnittet, kör du:

```bash
source ~/.bashrc
```

Det här steget är inte nödvändigt när du startar nya gränssnitt.

Nu kan du bekräfta att CLI är installerad:

```bash
dcos --help
```