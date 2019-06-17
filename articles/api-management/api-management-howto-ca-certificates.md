---
title: Lägg till en anpassad CA-certifikat – Azure API Management | Microsoft Docs
description: Lär dig hur du lägger till ett anpassat CA-certifikat i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 5161a35fd52b2f3d8374c76bdab60281e33dacf6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141823"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Hur du lägger till ett anpassat CA-certifikat i Azure API Management

Azure API Management kan installera CA-certifikat på datorn i betrodda rotcertifikatutfärdare och mellanliggande certifikatarkiv. Den här funktionen ska användas om dina tjänster kräver ett anpassat CA-certifikat.

Artikeln visar hur du hanterar CA-certifikat för en Azure API Management-tjänstinstans i Azure-portalen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Ladda upp ett CA-certifikat

![Lägga till CA-certifikat](media/api-management-howto-ca-certificates/00.png)

Följ stegen nedan för att ladda upp ett nytt CA-certifikat. Om du inte har skapat en API Management-tjänstinstans ännu, finns i självstudierna [skapa en API Management-tjänstinstans](get-started-create-service-instance.md).

1. Gå till din Azure API Management-tjänstinstans i Azure-portalen.

2. Välj **certifikatutfärdarcertifikat** på menyn.

3. Klicka på knappen **+ Lägg till**.  

    ![Lägga till CA-certifikat](media/api-management-howto-ca-certificates/01.png)  

4. Bläddra fram certifikatet och fatta beslut om arkivet. Endast den offentliga nyckeln krävs så att lösenordet inte är obligatoriskt.

    ![Lägga till CA-certifikat](media/api-management-howto-ca-certificates/02.png)  

5. Klicka på **Spara**. Den här åtgärden kan ta några minuter.

    ![Lägga till CA-certifikat](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Du kan ladda upp en CA-certifikat med den `New-AzApiManagementSystemCertificate` Powershell-kommando.

## <a name="step1a"> </a>Ta bort ett klientcertifikat

Ta bort ett certifikat genom att klicka på snabbmenyn **...**  och välj **ta bort** bredvid certifikatet.

![Ta bort CA-certifikat](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
