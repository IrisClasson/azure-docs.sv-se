---
title: Ansluta till och hantera Microsoft Azure Stack Edge-enhet via Windows PowerShell-gränssnittet | Microsoft Docs
description: Beskriver hur du ansluter till och sedan hanterar Azure Stack Edge via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: 973c618b46d1b6be902d9629ca63ee120cae6855
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85313203"
---
# <a name="manage-an-azure-stack-edge-device-via-windows-powershell"></a>Hantera en Azure Stack gräns enhet via Windows PowerShell

Med Azure Stack Edge-lösning kan du bearbeta data och skicka dem via nätverket till Azure. I den här artikeln beskrivs några konfigurations-och hanterings uppgifter för din Azure Stack Edge-enhet. Du kan använda Azure Portal, det lokala webb gränssnittet eller Windows PowerShell-gränssnittet för att hantera enheten.

Den här artikeln fokuserar på de uppgifter du gör med PowerShell-gränssnittet. 

Den här artikeln innehåller följande procedurer:

- Ansluta till PowerShell-gränssnittet
- Skapa ett support paket
- Överför certifikat
- Återställa enheten
- Visa enhets information
- Hämta beräknings loggar
- Övervaka och felsöka Compute-moduler

## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Skapa ett support paket

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Överför certifikat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Du kan också ladda upp IoT Edge certifikat för att aktivera en säker anslutning mellan din IoT Edge-enhet och de efterföljande enheterna som kan ansluta till den. Det finns tre IoT Edge-certifikat (*. pem* -format) som du måste installera:

- Rot certifikat utfärdare eller ägarens certifikat UTFÄRDAre
- Enhetens CA-certifikat
- Enhets nyckel certifikat

I följande exempel visas användningen av den här cmdleten för att installera IoT Edge certifikat:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
När du kör den här cmdleten uppmanas du att ange lösen ordet för nätverks resursen.

Om du vill ha mer information om certifikat går du till [Azure IoT Edge certifikat](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) eller [installerar certifikat på en gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Visa enhets information
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Återställa din enhet

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Hämta beräknings loggar

Om Compute-rollen har kon figurer ATS på din enhet kan du också hämta beräknings loggarna via PowerShell-gränssnittet.

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Get-AzureDataBoxEdgeComputeRoleLogs` för att hämta beräknings loggarna för din enhet.

    I följande exempel visas användningen av denna cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Här följer en beskrivning av parametrarna som används för cmdleten:
    - `Path`: Ange en nätverks Sök väg till den resurs där du vill skapa beräknings logg paketet.
    - `Credential`: Ange användar namnet för nätverks resursen. När du kör den här cmdleten måste du ange resurs lösen ordet.
    - `FullLogCollection`: Den här parametern säkerställer att logg paketet innehåller alla beräknings loggar. Som standard innehåller logg paketet bara en delmängd av loggarna.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Övervaka och felsöka Compute-moduler

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Avsluta fjärrsessionen

Stäng PowerShell-fönstret om du vill avsluta PowerShell-sessionen.

## <a name="next-steps"></a>Nästa steg

- Distribuera [Azure Stack Edge](azure-stack-edge-deploy-prep.md) i Azure Portal.
