---
title: 'Snabb start: skapa en Windows-Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurera och skapa en Data Science Virtual Machine på Azure för analys och maskin inlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: afcb676f68e7be9d3ebef11ea2c6876a86bbd062
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80281790"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Snabb start: Konfigurera Data Science Virtual Machine för Windows

Kom igång med en Windows Server 2019 Data Science Virtual Machine.

## <a name="prerequisite"></a>Krav

Om du vill skapa en Windows-Data Science Virtual Machine måste du ha en Azure-prenumeration. [Prova Azure kostnads fritt](https://azure.com/free).
Observera att de kostnads fria Azure-kontona inte stöder GPU-aktiverade virtuella datorer SKU: er.

## <a name="create-your-dsvm"></a>Skapa din DSVM

Så här skapar du en DSVM-instans:

1. Gå till [Azure Portal](https://portal.azure.com) du kan uppmanas att logga in på ditt Azure-konto om du inte redan är inloggad.
1. Hitta listan med virtuella datorer genom att skriva in "data science Virtual Machine" och välja "Data Science Virtual Machine-Windows 2019".

1. Välj knappen **skapa** längst ned.

1. Du bör omdirigeras till bladet "skapa en virtuell dator".

1. Fyll i fliken **grundläggande** :
      * **Prenumeration**: om du har mer än en prenumeration väljer du den som datorn ska skapas i och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
      * **Resurs grupp**: skapa en ny grupp eller Använd en befintlig grupp.
      * **Namn på virtuell dator**: Ange namnet på den virtuella datorn. Så här kommer det att visas i Azure Portal.
      * **Plats**: Välj det data Center som är lämpligast. För snabbast nätverks åtkomst är det data Center som har de flesta data eller som är närmast din fysiska plats. Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Bild**: låt standardvärdet vara kvar.
      * **Storlek**: detta bör fyllas i automatiskt med en storlek som passar för allmänna arbets belastningar. Läs mer om [storlekar för virtuella Windows-datorer i Azure](../../virtual-machines/windows/sizes.md).
      * **Användar namn**: Ange administratörens användar namn. Detta är det användar namn som du ska använda för att logga in på den virtuella datorn och behöver inte vara samma som ditt Azure-användarnamn.
      * **Lösen ord**: Ange det lösen ord som du ska använda för att logga in på den virtuella datorn.    
1. Välj **Granska + skapa**.
1. **Granska + skapa**
   * Kontrol lera att all information som du har angett är korrekt. 
   * Välj **Skapa**.


> [!NOTE]
> * Du betalar inte licens avgifter för den program vara som är förinstallerad på den virtuella datorn. Du betalar beräknings kostnaden för den server storlek som du valde i steget **storlek** .
> * Etableringen tar 10 till 20 minuter. Du kan visa statusen för den virtuella datorn på Azure Portal.

## <a name="access-the-dsvm"></a>Åtkomst till DSVM

När den virtuella datorn har skapats och allokerats följer du stegen som visas för att [ansluta till din Azure-baserade virtuella dator](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Använd de autentiseringsuppgifter för administratörs kontot som du konfigurerade i **grundläggande** steg för att skapa en virtuell dator. 

Du är redo att börja använda de verktyg som är installerade och konfigurerade på den virtuella datorn. Många av verktygen kan nås via **Start** -menyns paneler och skriv bords ikoner.

Du kan också bifoga en DSVM till Azure Notebooks för att köra Jupyter-anteckningsböcker på den virtuella datorn och kringgå begränsningarna i den kostnads fria tjänst nivån. Mer information finns i [Hantera och konfigurera antecknings projekt](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Nästa steg

* Utforska verktygen på DSVM genom att öppna **Start** -menyn.
* Läs mer om Azure Machine Learning genom att läsa [Vad är Azure Machine Learning?](../overview-what-is-azure-ml.md) och prova [själv studie kurserna](../index.yml).
* Läs artikeln [tio saker du kan göra på data science Virtual Machine](https://aka.ms/dsvmtenthings).
* Besök [Azure AI Gallery](https://gallery.cortanaintelligence.com) för Machine Learning-och data analys-exempel som använder Azure Machine Learning och relaterade data tjänster på Azure. Vi har också tillhandahållit en ikon för det här galleriet på **Start** -menyn och på Skriv bordet på den virtuella datorn.

