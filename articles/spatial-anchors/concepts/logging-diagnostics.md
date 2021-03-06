---
title: Loggning och diagnostik
description: Djupgående förklaring av hur du genererar och hämtar loggning och diagnostik i Azures spatialdata.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74270135"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Loggning och diagnostik i Azure spatiala ankare

Azures spatiala ankare ger en standard loggnings funktion som är användbar för utveckling av appar. Loggnings läget för avstånds ankare är användbart när du behöver mer information för fel sökning. Diagnostikloggning lagrar avbildningar av miljön.

## <a name="standard-logging"></a>Standard loggning
I API: et spatiala ankare kan du prenumerera på loggnings mekanismen för att få användbara loggar för program utveckling och fel sökning. Standard loggnings-API: er lagrar inte bilder i miljön på enhets disken. SDK: n tillhandahåller dessa loggar som händelse återanrop. Det är upp till dig att integrera dessa loggar i programmets loggnings funktion.

### <a name="configuration-of-log-messages"></a>Konfiguration av logg meddelanden
Det finns två återanrop av intresse för användaren. I följande exempel visas hur du konfigurerar sessionen.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Händelser och egenskaper

De här händelse återanropen tillhandahålls för att bearbeta loggar och fel från sessionen:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): anger detalj nivån för de händelser som ska tas emot från körnings miljön.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): innehåller standard fel söknings logg händelser.
- [Fel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): innehåller logg händelser som körningen anser vara fel.

## <a name="diagnostics-logging"></a>Diagnostikloggning

Förutom standard läget för loggning har spatiala ankare också ett diagnostik-läge. Diagnostic mode fångar avbildningar av miljön och loggar dem på disken. Du kan använda det här läget för att felsöka vissa typer av problem, t. ex. om det inte går att förutsäga ett ankare. Aktivera endast diagnostikloggning för att återskapa ett speciellt problem. Inaktivera det sedan. Aktivera inte diagnostik när du kör apparna på vanligt sätt.

Under en support interaktion med Microsoft kan en Microsoft-representant fråga om du vill skicka ett diagnostiskt paket för ytterligare undersökning. I så fall kan du välja att aktivera diagnostik och återskapa problemet så att du kan skicka det diagnostiska paketet.

Om du skickar en diagnostisk logg till Microsoft utan föregående bekräftelse från en Microsoft-representant kommer överföringen att skickas utan svar.

I följande avsnitt visas hur du aktiverar diagnos läge och hur du skickar diagnostikloggar till Microsoft.

### <a name="enable-diagnostics-logging"></a>Aktivera diagnostikloggning

När du aktiverar en session för diagnostikloggning, har alla åtgärder i sessionen motsvarande diagnostikloggning i det lokala fil systemet. Under loggningen sparas bilder av miljön på disken.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Skicka diagnostik-paketet

Följande kodfragment visar hur du skickar ett diagnostiskt paket till Microsoft. Paketet innehåller avbildningar av den miljö som inhämtats av sessionen när du har aktiverat diagnostik.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Delar av ett diagnostik-paket
Diagnostikloggar kan innehålla följande information:

- **Nyckel bilds bilder**: avbildningar av miljön som fångats under sessionen medan diagnostik aktiverades.
- **Loggar**: logg händelser som registrerats av körnings miljön.
- **Metadata för session**: metadata som identifierar sessionen.
