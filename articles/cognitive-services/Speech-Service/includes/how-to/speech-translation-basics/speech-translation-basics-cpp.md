---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 9b5d1421c36fa5d0568985b7792715533d2540ee
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035516"
---
## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Beroende på din plattform följer du anvisningarna under Hämta avsnittet om <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">tal-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i artikeln _om tal SDK_ .

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln inkluderar du följande `#include` och- `using` instruktioner överst i C++-kod filen.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Känsliga data och miljövariabler

Exempel käll koden i den här artikeln är beroende av miljövariabler för lagring av känsliga data, till exempel nyckel och region för tal resurs prenumerationen. Kod filen i C++ innehåller två sträng värden som tilldelas från värd datorns miljövariabler, nämligen `SPEECH__SUBSCRIPTION__KEY` och `SPEECH__SERVICE__REGION` . Båda dessa fält är i klass omfånget, vilket gör dem tillgängliga inom metod kroppar av klassen. Mer information om miljövariabler finns i [miljövariabler och program konfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
```

## <a name="create-a-speech-translation-configuration"></a>Skapa en konfiguration för tal Översättning

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechTranslationConfig`][config] . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!TIP]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

Det finns några sätt som du kan initiera en [`SpeechTranslationConfig`][config] :

* Med en prenumeration: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

Låt oss ta en titt på hur en [`SpeechTranslationConfig`][config] har skapats med hjälp av en nyckel och region. Se sidan [region support](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regions-ID.

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal översättning anger indata (eller käll språket). Låt oss ta en titt på hur du ändrar indatamängds språk till italienska. Interagera med [`SpeechTranslationConfig`][config] instansen i koden och anropar `SetSpeechRecognitionLanguage` metoden.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

[`SpeechRecognitionLanguage`][recognitionlang]Egenskapen förväntar sig en språk språks format sträng. Du kan ange valfritt värde i kolumnen **språk** i listan [över språk som stöds.](../../../language-support.md)

## <a name="add-translation-language"></a>Lägg till översättnings språk

En annan vanlig uppgift för tal översättning är att ange språk för mål översättning, minst ett krävs men multiplar stöds. Följande kodfragment anger både franska och tyska som översättnings språk mål.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Med varje anrop till [`AddTargetLanguage`][addlang] anges ett nytt mål översättnings språk. Med andra ord, när tal identifieras från käll språket, är varje mål översättning tillgänglig som en del av den resulterande översättnings åtgärden.

## <a name="initialize-a-translation-recognizer"></a>Initiera en översättnings tolk

När du har skapat en [`SpeechTranslationConfig`][config] är nästa steg att initiera en [`TranslationRecognizer`][recognizer] . När du initierar en måste [`TranslationRecognizer`][recognizer] du skicka den `translationConfig` . Konfigurationsobjektet ger de autentiseringsuppgifter som krävs av tal tjänsten för att verifiera din begäran.

Om du känner igen tal med hjälp av enhetens standard mikrofon [`TranslationRecognizer`][recognizer] ska du se vad som ska se ut så här:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Om du vill ange enheten för ljud inspelning måste du skapa en [`AudioConfig`][audioconfig] och ange `audioConfig` parametern när du initierar [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Lär dig hur du hämtar enhets-ID: t för din enhet för ljud inspelning](../../../how-to-select-audio-input-devices.md).

Först ska du referera till `AudioConfig` objektet på följande sätt:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Om du vill ange en ljudfil i stället för att använda en mikrofon måste du ändå ange en `audioConfig` . Men när du skapar en [`AudioConfig`][audioconfig] , i stället för att anropa `FromDefaultMicrophoneInput` , kommer du `FromWavFileInput` att anropa och skicka `filename` parametern.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Översätt tal

För att översätta tal är tal-SDK: n beroende av en mikrofon eller en inspelning av ljud filen. Tal igenkänning inträffar före tal översättning. När alla objekt har initierats anropar du funktionen recognize-Once och hämtar resultatet.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Mer information om tal till text finns i [grunderna i tal igenkänning](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Syntetisera översättningar

Efter en lyckad tal igenkänning och översättning innehåller resultatet alla översättningar i en ord lista. [`Translations`][translations]Ord listans nyckel är mål översättnings språket och värdet är den översatta texten. Identifierat tal kan översättas och sedan syntetiseras på ett annat språk (tal-till-tal).

### <a name="event-based-synthesis"></a>Händelse-baserad syntes

`TranslationRecognizer`Objektet visar en `Synthesizing` händelse. Händelsen utlöses flera gånger och ger en mekanism för att hämta det syntetiserade ljudet från resultatet av översättnings igenkänningen. Om du översätter till flera språk, se [manuell syntes](#manual-synthesis). Ange syntes rösten genom att tilldela en [`SetVoiceName`][voicename] och ange en händelse hanterare för `Synthesizing` händelsen, Hämta ljudet. I följande exempel sparas det översatta ljudet som en *. wav* -fil.

> [!IMPORTANT]
> Den händelsebaserade syntesen fungerar bara med en enda översättning, Lägg **inte** till flera mål översättnings språk. Dessutom bör det [`SetVoiceName`][voicename] vara samma språk som mål översättnings språket, till exempel, `"de"` kan mappas till `"de-DE-Hedda"` .

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Manuell syntes

[`Translations`][translations]Ord listan kan användas för att syntetisera ljud från översättnings texten. Iterera igenom varje översättning och syntetisera översättningen. När du skapar en `SpeechSynthesizer` instans `SpeechConfig` måste dess [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] egenskap ha angetts till önskad röst för objektet. I följande exempel översätts till fem språk och varje översättning syntetiseras sedan till en ljudfil på motsvarande neurala-språk.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Mer information om tal syntes finns i [grunderna för tal syntes](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
