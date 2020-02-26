---
title: Självstudie – Skapa en utvecklings pipeline i Azure med Jenkins
description: Självstudier – I den här självstudiekursen lär du dig hur du skapar en virtuell Jenkins-dator i Azure som hämtar data från GitHub vid varje kodincheckning och skapar en ny Docker-container för att köra din app.
keywords: Jenkins, Azure, DevOps, pipeline, cicd, Docker
ms.topic: tutorial
ms.date: 03/27/2017
ms.openlocfilehash: 2560d03282b2b3c8193a0b8c2a7a9f7c4036e75a
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77606449"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Självstudier: Skapa en infrastruktur för utveckling på en virtuell Linux-dator i Azure med Jenkins, GitHub och Docker

Om du vill automatisera versionen och testfasen i programutvecklingen kan du använda en pipeline för kontinuerlig integrering och distribution (CI/CD). I den här självstudien skapar du en CI/CD-pipeline på en virtuell Azure-dator och lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator i Jenkins
> * Installera och konfigurera Jenkins
> * Skapa webhookintegrering mellan GitHub och Jenkins
> * Skapa och utlösa Jenkins-skapade jobb från GitHub-skrivningar
> * Skapa en Docker-avbildning för appen
> * Verifiera att GitHub-incheckningar skapar ny Docker-avbildning och att uppdateringar kör appen

I den här självstudien används CLI i [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), som uppdateras kontinuerligt till den senaste versionen. Om du vill öppna Cloud Shell väljer du **testa den** överst i ett kodblock.

Om du väljer att installera och använda CLI:t lokalt för den här självstudien måste du köra Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Skapa Jenkins-instans
I en tidigare självstudie om [hur du anpassar en virtuell Linux-dator vid den första starten](../virtual-machines/linux/tutorial-automate-vm-deployment.md) lärde du dig att automatisera VM-anpassning med cloud-init. I den här självstudien används en cloud-init-fil för att installera Jenkins och Docker på en virtuell dator. Jenkins är en populär automatiseringsserver med öppen källkod som integreras sömlöst med Azure för att tillåta kontinuerlig integrering (CI) och kontinuerlig leverans (CD). Fler självstudier om hur du använder Jenkins finns i [Jenkins i Azure-hubben](https://docs.microsoft.com/azure/jenkins/).

I ditt nuvarande gränssnitt skapar du en fil med namnet *cloud-init-jenkins.txt* och klistrar in följande konfiguration. Skapa till exempel inte filen i Cloud Shell på din lokala dator. Ange `sensible-editor cloud-init-jenkins.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Innan du kan skapa en virtuell dator skapar du en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroupJenkins* på platsen *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Skapa nu en virtuell dator med [az vm create](/cli/azure/vm). Använd parametern `--custom-data` för att skicka in din cloud-init-konfigurationsfil. Ange den fullständiga sökvägen till *cloud-init-jenkins.txt* om du har sparat filen utanför din aktuella arbetskatalog.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Det tar några minuter att skapa och konfigurera den virtuella datorn.

För att webbtrafik ska kunna nå din virtuella dator använder du [az vm open-port](/cli/azure/vm) för att öppna port *8080* för Jenkins-trafik och port *1337* för Node.js-appen som används för att köra en exempelapp:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Konfigurera Jenkins
Hämta den offentliga IP-adressen på den virtuella datorn för att komma åt din Jenkins-instans:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Av säkerhetsskäl måste du ange det ursprungliga administratörslösenordet som finns lagrat i en textfil i den virtuella datorn för att starta installationen av Jenkins. Använd den offentliga IP-adressen du hämtade i föregående steg för att använda SSH till den virtuella datorn:

```bash
ssh azureuser@<publicIps>
```

Verifiera att Jenkins körs med hjälp av kommandot `service`:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Visa `initialAdminPassword` för Jenkins-installationen och kopiera den:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Om filen ännu inte är tillgänglig väntar du ett par minuter till för att cloud-init ska slutföra installationen av Jenkins och Docker.

Öppna nu en webbläsare och gå till `http://<publicIps>:8080`. Slutför den initiala Jenkins-konfigurationen så här:

- Välj **Välj plugin-program att installera**
- Sök efter *GitHub* i textrutan längst upp. Markera kryssrutan för *GitHub* och välj sedan **installera**
- Skapa den första administratörsanvändaren. Ange ett användarnamn som **admin** och ange ditt eget säkra lösenord. Skriv slutligen ett fullständigt namn och e-postadress.
- Välj **Spara och Slutför**
- När Jenkins är klar, väljer du **Börja använda Jenkins**
  - Om en tom sida visas i din webbläsare när du börjar använda Jenkins, starta om Jenkins-tjänsten. Från din SSH-session skriver du `sudo service jenkins restart` och uppdatera därefter webbläsaren.
- Logga in på Jenkins med det användarnamn och lösenord som du skapade, om det behövs.


## <a name="create-github-webhook"></a>Skapa GitHub-webhook
Om du vill konfigurera integreringen med GitHub öppnar du [exempelappen Node.js Hello World](https://github.com/Azure-Samples/nodejs-docs-hello-world) från exempellagringsplatsen i Azure. Om du vill förgrena lagringsplatsen till ditt eget GitHub-konto väljer du knappen **Fork** (Förgrening) i det övre högra hörnet.

Skapa en webhook i förgreningen du har skapat:

- Välj **Inställningar** och sedan **Webhooks** på vänster sida.
- Välj **Lägg till webhook** och skriv *Jenkins* i filterrutan.
- För **Payload URL** (Webbadress för nyttolast) anger du `http://<publicIps>:8080/github-webhook/`. Se till att ta med ”/” i slutet
- För **Innehållstyp** väljer du *application/x-www-form-urlencoded*.
- För **Which events would you like to trigger this webhook?** (Vilka händelser vill du ska utlösa denna webhook?) väljer du *Just the push event* (Bara push-händelsen).
- Markera **Aktiv**.
- Klicka på **Lägg till webhook**.

![Lägga till GitHub-webhook till din förgrenade lagringsplats](media/tutorial-jenkins-github-docker-cicd/github-webhook.png)


## <a name="create-jenkins-job"></a>Skapa Jenkins-jobb
Om du vill att Jenkins ska svara på en händelse i GitHub, som att checka in kod, skapar du ett Jenkins-jobb. Använd URL:er för din egen GitHub-förgrening.

På Jenkins-webbplatsens startsida väljer du **Skapa nya jobb**:

- Ange *HelloWorld* som jobbnamn. Välj **Freestyle project** (Freestyle-projekt) och välj **OK**.
- I avsnittet **Allmänt** väljer du **GitHub-projekt** och anger URL:en till din förgrenade lagringsplats, som *https://github.com/cynthn/nodejs-docs-hello-world*
- I avsnittet **Källkodshantering** väljer du **Git** och anger *.git*-URL:en till din förgrenade lagringsplats, som *https://github.com/cynthn/nodejs-docs-hello-world.git*
- I avsnittet **Build Triggers** (Bygg utlösare) väljer du **GitHub hook trigger for GITscm polling** (GitHub-hookutlösare för GITScm-avsökning).
- I avsnittet **Skapa** väljer du **Add build step** (Lägg till byggsteg). Välj **Execute shell** (Kör gränssnitt) och ange `echo "Test"` i kommandofönstret.
- Välj **Spara** längst ned i jobbfönstret.


## <a name="test-github-integration"></a>Testa GitHub-integrering
Om du vill testa GitHub-integreringen med Jenkins gör du en ändring i förgreningen. 

När du är tillbaka i GitHub-webbgränssnittet väljer du din förgrenade lagringsplats och sedan filen **index.js**. Välj pennikonen för att redigera filen så att det står följande på rad 6:

```javascript
response.end("Hello World!");
```

Om du vill genomföra ändringarna markerar du knappen för att **spara ändringarna** längst ned.

I Jenkins startar en ny version i avsnittet med **versionshistorik** längst ned till vänster på jobbsidan. Välj länken för versionsnummer och välj **konsolens utdata** till vänster. Du kan visa åtgärderna Jenkins vidtar medan koden hämtas från GitHub och byggåtgärden matar ut meddelandet `Test` till konsolen. Varje gång ett genomförande görs i GitHub anropar webhooken Jenkins och utlöser en ny version.


## <a name="define-docker-build-image"></a>Definiera Docker-avbildning
För att du ska kunna se när Node.js-appen körs baserat på dina GitHub-genomföranden ska vi skapa en Docker-avbildning för att köra appen. Avbildningen skapas från en Dockerfile som definierar hur du ska konfigurera containern som kör appen. 

Från SSH-anslutningen till din virtuella dator ändrar du till Jenkins-arbetsytans katalog som är uppkallad efter jobbet du skapade i föregående steg. I det här exemplet var namnet *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Skapa en fil i den här arbetsytans katalog med `sudo sensible-editor Dockerfile` och klistra in följande innehåll. Se till att hela Docker-filen kopieras korrekt, särskilt den första raden:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Denna Dockerfile använder Node.js-basavbildningen med Alpine Linux, gör port 1337 som Hello World-appen körs på tillgänglig och kopierar appfilerna och initierar den sedan.


## <a name="create-jenkins-build-rules"></a>Skapa build-regler för Jenkins
Tidigare skapade du en grundläggande Jenkins-byggregel som matar ut ett meddelande till konsolen. Nu ska vi skapa ett byggsteg för att använda vår Dockerfile och köra appen.

Välj det jobb som du skapade i föregående steg när du är tillbaka i din Jenkins-instans. Välj **Konfigurera** på vänster sida och bläddra ned till avsnittet **Skapa**:

- Ta bort ditt befintliga byggsteg `echo "Test"`. Markera det röda krysset i det övre högra hörnet i den befintliga byggstegsrutan.
- Välj **Add build step** (Lägg till byggsteg) och sedan **Execute shell** (Kör gränssnitt)
- I rutan **Kommando** anger du följande Docker-kommandon och väljer sedan **Spara**:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Docker-byggstegen skapar en avbildning och taggar den med Jenkins-versionsnumret så du kan underhålla en historik för avbildningarna. Eventuella befintliga containrar som kör appen stoppas och tas sedan bort. En ny container startas sedan med avbildningen och kör Node.js-appen baserat på de senaste genomförandena i GitHub.


## <a name="test-your-pipeline"></a>Testa din pipeline
Om du vill se hela pipelinen när den är igång redigerar du filen *index.js* på den förgrenade GitHub-lagringsplatsen igen och väljer alternativet för att **spara ändringen**. Ett nytt jobb startar i Jenkins baserat på webhooken för GitHub. Det tar några sekunder att skapa Docker-avbildningen och starta appen i en ny container.

Om det behövs hämtar du den offentliga IP-adressen för den virtuella datorn på nytt:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Öppna en webbläsare och ange `http://<publicIps>:1337`. Din Node.js-app visas och återspeglar de senaste incheckningarna i GitHub-förgreningen enligt följande:

![Köra Node.js-app](media/tutorial-jenkins-github-docker-cicd/running-nodejs-app.png)

Nu ska du göra ytterligare en redigering i filen *index.js* i GitHub och genomföra ändringen. Vänta några sekunder för att jobbet ska slutföras i Jenkins. Uppdatera sedan webbläsaren för att se den uppdaterade versionen av din app som körs i en ny container så här:

![Köra Node.js-app efter ytterligare ett GitHub-genomförande](media/tutorial-jenkins-github-docker-cicd/another-running-nodejs-app.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudien har du konfigurerat GitHub för att köra ett Jenkins-byggjobb på varje kodgenomförande och sedan distribuerat en Docker-container för att testa appen. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator i Jenkins
> * Installera och konfigurera Jenkins
> * Skapa webhookintegrering mellan GitHub och Jenkins
> * Skapa och utlösa Jenkins-skapade jobb från GitHub-skrivningar
> * Skapa en Docker-avbildning för appen
> * Verifiera att GitHub-incheckningar skapar ny Docker-avbildning och att uppdateringar kör appen

Gå vidare till nästa självstudie om du vill lära dig mer om hur du integrerar Jenkins med Azure DevOps Services.

> [!div class="nextstepaction"]
> [Distribuera appar med tjänsterna Jenkins och Azure DevOps](tutorial-build-deploy-jenkins.md)