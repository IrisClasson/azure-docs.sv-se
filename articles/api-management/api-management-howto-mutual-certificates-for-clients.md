---
title: 'Skydda API: er med klienten certifikatautentisering i API Management – Azure API Management | Microsoft Docs'
description: 'Lär dig hur du skyddar åtkomsten till API: er med klientcertifikat'
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: ac9910358cf19eac3f704f1bf3e259e9a1543dcc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141522"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Hur du skyddar API: er med klienten certifikatautentisering i API Management

API Management ger möjlighet att säkra åtkomst till API: er (d.v.s. klient till API Management) med hjälp av klientcertifikat. För närvarande kan du kontrollera tumavtrycket för ett certifikat mot ett önskat värde. Du kan också kontrollera tumavtrycket mot befintliga certifikat som har överförts till API Management.  

Information om hur du skyddar åtkomst till backend-tjänst i ett API som använder klientcertifikat (t.ex, API Management till backend-server) finns i [hur du skyddar backend-tjänster med hjälp av klienten certifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-the-expiration-date"></a>Kontrollera utgångsdatumet

Nedan principer kan konfigureras för att kontrollera om certifikatet har upphört att gälla:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Kontroll av utfärdare och ämne

Nedan principer kan konfigureras för att kontrollera utfärdare och ämnet för ett klientcertifikat:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Kontrollera tumavtrycket

Nedan principer kan konfigureras för att kontrollera tumavtrycket för ett klientcertifikat:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrollerar ett tumavtryck mot certifikat har överförts till API Management

I följande exempel visas hur du kontrollerar tumavtrycket för ett certifikat mot certifikat som har överförts till API Management: 

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Nästa steg

*  [Hur du skyddar backend-tjänster med hjälp av klienten certifikatautentisering](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Ladda upp certifikat](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

