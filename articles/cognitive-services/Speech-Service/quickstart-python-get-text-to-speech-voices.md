---
title: 'Snabbstart: Lista text till tal-röster, python-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du får en fullständig lista över standard-och neurala-röster för en region/slut punkt med python. Listan returneras som JSON och röst tillgängligheten varierar beroende på region.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: ac96c3ce3924b8b2fe834e2b350e95ce23c52e1f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559346"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Snabbstart: Hämta listan med text till tal-röster med python

I den här snabb starten får du lära dig hur du får en fullständig lista över standard-och neurala-röster för en region/slut punkt med python. Listan returneras som JSON och röst tillgängligheten varierar beroende på region. En lista över regioner som stöds finns i [regioner](regions.md).

Den här snabb starten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Speech Services-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](get-started.md) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* Python 2.7.x eller 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) eller valfritt redigeringsprogram
* En Azure-prenumerations nyckel för tal tjänsterna

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan det här kodavsnittet till projektet i en fil med namnet `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests`.

Begär Anden används för HTTP-begäranden till tjänsten för text till tal.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ange prenumerationsnyckeln och skapa en uppmaning för text till tal

I nästa avsnitt ska du skapa metoder för att hantera auktorisering, anropa text till tal-API och verifiera svaret. Vi börjar med att skapa en klass. Det här är där vi lägger in vår metoder för tokenutbytet och anropa text till tal-API.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Den `subscription_key` är den unika nyckeln från Azure-portalen.

## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Den här slut punkten kräver en åtkomsttoken för autentisering. Om du vill få en åtkomsttoken, krävs ett utbyte. Det här exemplet utbyter din prenumerations nyckel för tal tjänster för en `issueToken` åtkomsttoken med hjälp av slut punkten.

Det här exemplet förutsätter att din röst tjänst prenumeration är i regionen USA, västra. Om du använder en annan region måste du uppdatera värdet för `fetch_token_url`. En fullständig lista finns i [regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopiera den här koden i den `GetVoices` klass:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Mer information om autentisering finns i [autentisera med en åtkomsttoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Gör en begäran och spara svaret

Här skapar du begäran och sparar listan med returnerade röster. Först behöver du ange den `base_url` och `path`. Det här exemplet förutsätter att du använder slutpunkten som USA, västra. Om din resurs har registrerats till en annan region, kontrollera att du uppdaterar den `base_url`. Mer information finns i avsnittet om [tal Services-regioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Lägg sedan till de rubriker som krävs för begäran. Slutligen ska du göra en begäran till tjänsten. Om begäran lyckas och en status kod för 200 returneras, skrivs svaret till filen.

Kopiera den här koden i den `GetVoices` klass:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Färdigställa allt

Nästan klart. Det sista steget är att skapa en instans av klassen och anropa dina funktioner.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Kör exempelappen

Då är det dags att köra exemplet. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Se också

* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Skapa anpassade rösttyper](how-to-customize-voice-font.md)
* [Post voice-exempel för att skapa en anpassad röst](record-custom-voice-samples.md)
