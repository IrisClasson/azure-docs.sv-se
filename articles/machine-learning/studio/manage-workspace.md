---
title: 'ML Studio (klassisk): hantera arbets ytor – Azure'
description: Hantera åtkomst till Azure Machine Learning Studio (klassiska) arbets ytor och distribuera och hantera Machine Learning API-webbtjänster
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 6f3661956c61eb20f4c336f8644f85eb7e1b4100
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433116"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Hantera en Azure Machine Learning Studio (klassisk)-arbets yta

**gäller för:** ![ Ja ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ inga](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


> [!NOTE]
> Information om hur du hanterar webb tjänster i Machine Learning Web Services-portalen finns i [hantera en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md).
> 
> 

Du kan hantera Machine Learning Studio (klassiska) arbets ytor i Azure Portal.



## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Så här hanterar du en Studio (klassisk) arbets yta i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett administratörs konto för Azure-prenumeration.
2. I rutan Sök högst upp på sidan anger du "Machine Learning Studio (klassisk)-arbets ytor" och väljer sedan **Machine Learning Studio (klassiska) arbets ytor**.
3. Klicka på den arbets yta som du vill hantera.

Förutom den standard resurs hanterings information och de alternativ som är tillgängliga kan du:

- Visa **Egenskaper** – den här sidan visar arbets ytan och resursinformationen, och du kan ändra den prenumeration och resurs grupp som den här arbets ytan är ansluten till.
- **Omsynkronisering av lagrings nycklar** – arbets ytan hanterar nycklar till lagrings kontot. Om lagrings kontot ändrar nycklar kan du klicka på **omsynkronisera nycklar** för att synkronisera nycklarna med arbets ytan.

Om du vill hantera de webb tjänster som är associerade med den här Studio-arbetsytan (klassisk) använder du Machine Learning Web Services-portalen. Mer information finns i [hantera en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md) .

> [!NOTE]
> Om du vill distribuera eller hantera nya webb tjänster måste du tilldelas rollen deltagare eller administratör för den prenumeration som webb tjänsten har distribuerats till. Om du bjuder in en annan användare till en Machine Learning Studio-arbetsyta (klassisk) måste du tilldela dem till en deltagare eller administratörs roll i prenumerationen innan de kan distribuera eller hantera webb tjänster. 
> 
>Mer information om hur du ställer in åtkomst behörighet finns i [Hantera åtkomst med RBAC och Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om att [distribuera Machine Learning med Azure Resource Manager mallar](deploy-with-resource-manager-template.md). 
