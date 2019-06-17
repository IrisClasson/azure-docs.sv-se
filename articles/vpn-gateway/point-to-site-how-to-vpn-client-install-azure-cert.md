---
title: 'Installera ett klientcertifikat för punkt-till-plats: Azure | Microsoft Docs'
description: Installera klientcertifikatet för certifikatautentisering för P2S - Windows, Mac, Linux.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: c278c1c85961fbeb0779cad98f8ac16d4961ba75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60679989"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Installera klientcertifikat för P2S-anslutningar med autentisering

Alla klienter som ansluter till ett virtuellt nätverk med punkt-till-plats Azure-certifikatautentisering kräver ett klientcertifikat. Den här artikeln hjälper dig att installera ett klientcertifikat som används för autentisering när du ansluter till ett virtuellt nätverk med P2S.

## <a name="generate"></a>Skaffa ett klientcertifikat

Oavsett vilka klientoperativsystem som du vill ansluta från, måste du alltid ha ett klientcertifikat. Du kan generera ett klientcertifikat från ett rotcertifikat som genererades med en certifikatutfärdarlösning för företag eller ett självsignerat rotcertifikat. Se den [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md), eller [Linux](vpn-gateway-certificates-point-to-site-linux.md) instruktioner för hur du skapar ett klientcertifikat. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN-klienter har stöd för endast distributionsmodellen Resource Manager. De stöds inte för den klassiska distributionsmodellen.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

Linux-Klientcertifikatet har installerats på klienten som en del av klientkonfigurationen. Se [klientkonfigurationen - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) anvisningar.

## <a name="next-steps"></a>Nästa steg

Fortsätt med konfigurationssteg för punkt-till-plats att [skapa och installera VPN-klientkonfigurationsfiler](point-to-site-vpn-client-configuration-azure-cert.md).