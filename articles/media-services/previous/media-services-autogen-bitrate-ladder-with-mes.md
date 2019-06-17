---
title: Använd Azure Media Encoder Standard för att Autogenerera en bithastighetsstege | Microsoft Docs
description: Det här avsnittet visar hur du använder Media Encoder Standard (MES) för att Autogenerera en bithastighetsstege baserat på inkommande upplösning och bithastighet. Inkommande upplösning och bithastighet kommer aldrig att överskridas. Till exempel om indata är 720p på 3 Mbit/s, utdata kommer förblir 720p i bästa och börjar avgifterna lägre än 3 Mbit/s.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: bbaf4d490fcebb4cd741a9b83ffc5d7e85699755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61224352"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Använd Azure Media Encoder Standard för att generera en bithastighetsstege automatiskt  

## <a name="overview"></a>Översikt

Den här artikeln visar hur du använder Media Encoder Standard (MES) för att Autogenerera en bithastighetsstege (bithastighet upplösning par) baserat på inkommande upplösning och bithastighet. Automatiskt genererade förinställningen kan inte överstiga inkommande upplösning och bithastighet. Till exempel om indata är 720p med 3 Mbit/s, utdata förblir 720p i bästa och börjar avgifterna lägre än 3 Mbit/s.

### <a name="encoding-for-streaming-only"></a>Kodning för direktuppspelning endast

Om din avsikt är att koda källvideon endast för direktuppspelning, bör du använda den ”Adaptiv direktuppspelning” förinställda när du skapar ett kodningsjobb. När du använder den **Adaptiv direktuppspelning** bitrate MES-kodare kommer Intelligent gräns om en bithastighetsstege. Men kommer du inte att styra kodningen kostnader, eftersom tjänsten avgör hur många lager för att använda och med vilken upplösning. Du kan se exempel på utdata lager som produceras av MES till följd av kodning med den **Adaptiv direktuppspelning** förinställda i slutet av den här artikeln. Utdata tillgång innehåller MP4-filer där ljud och video är inte överlagrad.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kodning för strömning och progressiv nedladdning

Om din avsikt är att koda källvideon för direktuppspelning samt att producera MP4-filer för progressiv nedladdning, bör du använda den ”Content anpassningsbar flera bithastigheter MP4” förinställda när du skapar ett kodningsjobb. När du använder den **innehåll anpassningsbar flera bithastigheter MP4** bitrate MES-kodaren gäller samma kodning logik som ovan, men nu utdatatillgången innehåller MP4-filer där det är ljud och video överlagrad. Du kan använda någon av dessa MP4-filer (till exempel högsta bithastighet version) som en fil för progressiv nedladdning.

## <a id="encoding_with_dotnet"></a>Encoding med Media Services .NET SDK

I följande kodexempel använder Media Services .NET SDK för att utföra följande uppgifter:

- Skapa ett kodningsjobb.
- Hämta en referens till Media Encoder Standard-kodaren.
- Lägg till ett kodningsjobb i jobbet och ange om du vill använda den **Adaptiv direktuppspelning** förinställda. 
- Skapa en utdata-tillgång som innehåller den kodade tillgången.
- Lägg till en händelsehanterare för att kontrollera jobbförloppet för.
- Skicka in jobbet.

#### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Exempel

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a id="output"></a>Utdata

Det här avsnittet visar tre exempel på utdata lager som produceras av MES till följd av kodning med den **Adaptiv direktuppspelning** förinställda. 

### <a name="example-1"></a>Exempel 1
Källan med höjd ”1080” och ramhastighet ”29.970” ger 6 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exempel 2
Källan med höjd ”720” och ramhastighet ”23.970” producerar 5 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exempel 3
Källan med höjd ”360” och ramhastighet ”29.970” ger 3 video lager:

|Lager|Höjd|Bredd|Bitrate(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Media Services-kodning – översikt](media-services-encode-asset.md)

