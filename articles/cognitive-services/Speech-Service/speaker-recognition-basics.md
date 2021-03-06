---
title: Grunderna – tal service för Talarigenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal-SDK för att besvara frågan, "vem som talar". I den här artikeln får du lära dig om vanliga design mönster för att arbeta med både högtalar verifiering och identifiering.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/05/2020
ms.author: trbye
ms.openlocfilehash: b8c38847e3228c54091b84218a5d72c79e3e41a8
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632319"
---
# <a name="learn-the-basics-of-speaker-recognition"></a>Lär dig grunderna för Talarigenkänning

I den här artikeln får du lära dig grundläggande design mönster för Talarigenkänning med hjälp av tal-SDK, inklusive:

* Text beroende och text oberoende verifiering
* Högtalar identifiering för att identifiera ett röst exempel bland en grupp röster
* Ta bort röst profiler

En övergripande överblick över tal igenkännings koncept finns i [översikts](speaker-recognition-overview.md) artikeln.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](get-started.md).

> [!IMPORTANT]
> Talarigenkänning stöds för närvarande *bara* i Azure tal-resurser som skapats i `westus` regionen.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Använd följande instruktioner, beroende på plattform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln inkluderar du följande `using` instruktioner överst i skriptet.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) . I det här exemplet skapar du en [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) med en prenumerations nyckel och en region. Du kan också skapa en grundläggande exempel kod som du kan använda för resten av den här artikeln, som du ändrar för olika anpassningar.

Observera att regionen är inställt på `westus` , eftersom det är den enda region som stöds för tjänsten.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Text beroende verifiering

Talarverifiering är en bekräftelse på att en talare matchar en känd eller **registrerad** röst. Det första steget är att **Registrera** en röst profil, så att tjänsten har något att jämföra framtida röst exempel mot. I det här exemplet registrerar du profilen med en **text beroende** strategi, som kräver att en speciell pass fras används för både registrering och verifiering. En lista över de pass fraser som stöds finns i [referens dokumenten](https://docs.microsoft.com/rest/api/speakerrecognition/) .

Börja med att skapa följande funktion i din- `Program` klass för att registrera en röst profil.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

I den här funktionen `await client.CreateProfileAsync()` är det som faktiskt skapar den nya röst profilen. När den har skapats anger du hur du vill att ljud exempel ska matas in med hjälp av `AudioConfig.FromDefaultMicrophoneInput()` i det här exemplet för att avbilda ljud från din standardenhet för indata. Sedan registrerar du ljud exempel i en `while` slinga som spårar antalet återstående prover och krävs för registrering. I varje iteration `client.EnrollProfileAsync(profile, audioInput)` kommer du att uppmanas att prata med pass frasen till din mikrofon och lägga till exemplet i röst profilen.

När registreringen är klar anropar du `await SpeakerVerify(config, profile, profileMapping)` för att verifiera mot den profil som du nyss skapade. Lägg till en annan funktion som ska definieras `SpeakerVerify` .

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

I den här funktionen skickar du det `VoiceProfile` objekt som du nyss skapade för att initiera en modell att verifiera mot. Sedan `await speakerRecognizer.RecognizeOnceAsync(model)` uppmanas du att prata med pass frasen igen, men den här gången kommer den att verifieras mot din röst profil och returnerar en likhets poäng på mellan 0,0 – 1,0. `result`Objektet returnerar också `Accept` eller `Reject` , baserat på om pass fras matchningen ska matchas eller inte.

Ändra sedan `Main()` funktionen så att den anropar de nya funktionerna som du skapade. Observera också att du skapar en `Dictionary<string, string>` för att skicka en att skicka med-referens via dina funktions anrop. Orsaken till detta är att tjänsten inte tillåter lagring av ett människo läsbart namn med ett skapat `VoiceProfile` , och lagrar bara ett ID-nummer i sekretess syfte. I `VerificationEnroll` funktionen lägger du till en post med det nya ID: t, tillsammans med ett text namn i den här ord listan. I program utvecklings scenarier där du behöver visa ett människo läsbart namn **måste du lagra mappningen någonstans, tjänsten kan inte lagra den**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Kör skriptet och du uppmanas att tala om frasen *min röst är mitt Passport, verifiera mig* tre gånger för registrering och en ytterligare tid för verifiering. Resultatet som returneras är likhets poängen, som du kan använda för att skapa egna anpassade tröskelvärden för verifiering.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Text oberoende verifiering

I motsats till **text beroende** verifiering, **text oberoende** verifiering:

* Kräver ingen viss pass-fras för att bli talas, allt kan ses över
* Kräver inte tre ljud exempel *, men kräver* 20 sekunders total ljud

Gör några enkla ändringar i din `VerificationEnroll` funktion för att växla till **text oberoende** verifiering. Först ändrar du verifierings typen till `VoiceProfileType.TextIndependentVerification` . Ändra sedan `while` slingan till Track `result.RemainingEnrollmentsSpeechLength` , som kommer fortsätta att uppmana dig att tala tills 20 sekunder ljud har fångats.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Kör programmet igen och tala om vad som helst under verifierings fasen eftersom det inte krävs någon pass fras. Återigen returneras likhets poängen.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Talaridentifiering

Talaridentifiering används för att bestämma **vem som** talar från en specifik grupp med registrerade röster. Processen liknar **text oberoende verifiering**, med den största skillnaden att kunna verifiera mot flera röst profiler samtidigt, i stället för att verifiera mot en enskild profil.

Skapa en funktion `IdentificationEnroll` för att registrera flera röst profiler. Registrerings processen för varje profil är samma som registrerings processen för **text oberoende verifiering**och kräver 20 sekunders ljud för varje profil. Den här funktionen accepterar en lista med strängar `profileNames` och skapar en ny röst profil för varje namn i listan. Funktionen returnerar en lista med `VoiceProfile` objekt som du kan använda i nästa funktion för att identifiera en talare.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Skapa `SpeakerIdentification` en identifierings förfrågan genom att skapa följande funktion. Den största skillnaden i den här funktionen jämfört med en begäran om att **Verifiera en talare** är att använda `SpeakerIdentificationModel.FromProfiles()` , vilket accepterar en lista med `VoiceProfile` objekt. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similiar voice profile is {profileMapping[result.ProfileId]} with similiarity score {result.Score}");
}
```

Ändra `Main()` funktionen till följande. Du skapar en lista med strängar `profileNames` som du skickar till din `IdentificationEnroll()` funktion. Då uppmanas du att skapa en ny röst profil för varje namn i listan, så du kan lägga till fler namn för att skapa ytterligare profiler för vänner eller kollegor.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Kör skriptet och uppmanas att tala om att registrera röst exempel för den första profilen. När registreringen är klar uppmanas du att upprepa den här processen för varje namn i listan `profileNames` . När varje registrering är färdig uppmanas du att låta **någon** tala och tjänsten försöker identifiera personen från dina registrerade röst profiler.

Det här exemplet returnerar bara den närmaste matchningen och det är likhets poängen, men du kan få det fullständiga svaret som innehåller de fem främsta likhets poängen genom att lägga till `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` i din `SpeakerIdentification` funktion.

## <a name="changing-audio-input-type"></a>Ändrar typ av ljud inspelning

I exemplen i den här artikeln används standard mikrofonen för enheten som indata för ljud exempel. I scenarier där du behöver använda ljudfiler i stället för mikrofon indata kan du dock bara ändra en instans av till för att `AudioConfig.FromDefaultMicrophoneInput()` `AudioConfig.FromWavFileInput(path/to/your/file.wav)` Växla till en fil indata. Du kan också använda blandade indata med en mikrofon för registrering och filer för verifiering, till exempel.

## <a name="deleting-voice-profile-enrollments"></a>Ta bort röst profil registreringar

Om du vill ta bort en registrerad profil använder du `DeleteProfileAsync()` funktionen på `VoiceProfileClient` objektet. Följande exempel funktion visar hur du tar bort en röst profil från ett känt röst profil-ID.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om klasser och funktioner finns i [referens dokumentationen](https://docs.microsoft.com/rest/api/speakerrecognition/) för Talarigenkänning.

* Se [C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) -och [C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) -exempel på GitHub.

