---
title: Skapa en HTTPS-ingress med kluster Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och konfigurerar en NGINX ingress-kontrollant som använder vi kryptera vid automatisk generering av TLS-certifikat i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/27/2019
ms.author: iainfou
ms.openlocfilehash: ae1ef2c51fba9186eb75bfec421fbbb05baa4582
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956458"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Skapa en ingress-kontrollanten för HTTPS på Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Den här artikeln visar hur du distribuerar den [ingress-kontrollanten för NGINX] [ nginx-ingress] i ett kluster i Azure Kubernetes Service (AKS). Den [certifikathanterare] [ cert-manager] projektet används för att automatiskt generera och konfigurera [vi kryptera] [ lets-encrypt] certifikat. Slutligen körs två program i AKS-kluster som är tillgänglig via en IP-adress.

Du kan också:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Den här artikeln använder Helm för att installera NGINX ingress-kontrollant, certifikathanterare och en exempelwebbapp. Du måste ha Helm initieras i AKS-klustret och använda ett tjänstkonto för Tiller. Kontrollera att du använder den senaste versionen av Helm. Uppgradera anvisningar finns i den [Helm installera docs][helm-install]. Läs mer om att konfigurera och använda Helm [installera program med Helm i Azure Kubernetes Service (AKS)][use-helm].

Den här artikeln kräver också att du kör Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Skapa en ingress-kontrollant

Du kan skapa ingress-kontrollant med `Helm` installera *nginx-ingress*. För extra redundans två repliker av NGINX ingående kontrollenheterna distribueras med den `--set controller.replicaCount` parametern. Om du vill utnyttja alla fördelar med repliker av ingress-kontrollant, kontrollera att det finns fler än en nod i AKS-klustret.

Ingress-kontrollant måste också schemaläggas på en Linux-nod. Windows Server-noder (för närvarande i förhandsversion i AKS) bör inte köra ingress-kontrollant. En nod-väljare anges med hjälp av den `--set nodeSelector` parametern ska berätta för Kubernetes-Schemaläggaren för att köra ingress-kontrollanten för NGINX på en Linux-baserade nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingress-resurser med namnet *ingress-grundläggande*. Ange ett namnområde för din egen miljö efter behov. Om AKS-klustret inte RBAC aktiverat lägger du till `--set rbac.create=false` för Helm-kommandon.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set nodeSelector."beta.kubernetes.io/os"=linux
```

Under installationen skapas en Azure offentlig IP-adress för ingress-kontrollant. Den här offentliga IP-adressen är statisk för livslängden för ingress-kontrollant. Om du tar bort ingress-kontrollant förloras den offentliga IP-adresstilldelningen. Om du skapar sedan en ytterligare ingress-kontrollant, tilldelas en ny offentlig IP-adress. Om du vill behålla användningen av offentliga IP-adress kan du i stället [skapa en ingress-kontrollant med en statisk offentlig IP-adress][aks-ingress-static-tls].

Hämta den offentliga IP-adressen med den `kubectl get service` kommando. Det tar några minuter för IP-adress som ska tilldelas till tjänsten.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Inga inkommande regler har skapats ännu. Om du bläddrar till den offentliga IP-adressen visas NGINX ingående controller standard 404-sida.

## <a name="configure-a-dns-name"></a>Konfigurera ett DNS-namn

Konfigurera ett fullständigt domännamn för ingress controller IP-adress för HTTPS-certifikat ska fungera korrekt. Uppdatera följande skript med IP-adressen för ingress-kontrollanten och ett unikt namn som du vill använda för FQDN:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Ingress-kontrollant är nu tillgänglig via det fullständiga Domännamnet.

## <a name="install-cert-manager"></a>Installera certifikathanterare

Ingress-kontrollanten för NGINX stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS. Den här artikeln visar hur du använder [certifikathanterare][cert-manager], som tillhandahåller automatiska [kan kryptera] [ lets-encrypt] certifikat generation och hanteringsfunktioner.

> [!NOTE]
> Den här artikeln används den `staging` miljö för att kryptera vi. I distributioner av produktion, använda `letsencrypt-prod` och `https://acme-v02.api.letsencrypt.org/directory` i resursdefinitionerna och när du installerar Helm-diagrammet.

Använd följande för att installera certifikathanterare controller i ett kluster med RBAC-aktiverade, `helm install` kommando:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.7/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.7.0 \
  jetstack/cert-manager
```

Mer information om certifikathanterare konfigurationen finns i den [certifikathanterare projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Skapa en utfärdare för CA-kluster

Innan certifikat kan utfärdas, certifikathanterare kräver en [utfärdare] [ cert-manager-issuer] eller [ClusterIssuer] [ cert-manager-cluster-issuer] resurs. De här resurserna för Kubernetes är identiska i funktionen, men `Issuer` fungerar i ett enda namnområde och `ClusterIssuer` fungerar över alla namnområden. Mer information finns i den [certifikathanterare utfärdare] [ cert-manager-issuer] dokumentation.

Skapa en kluster-utfärdare som `cluster-issuer.yaml`, med hjälp av följande exempel manifestet. Uppdatera den e-postadressen med en giltig adress från din organisation:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Skapa utfärdaren genom att använda den `kubectl apply -f cluster-issuer.yaml` kommando.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Köra demo-program

Ingress-kontrollanten och en lösning för hantering av certifikat har konfigurerats. Nu ska vi köra två demonstrera program i AKS-klustret. I det här exemplet används Helm för att distribuera två instanser av en enkel ”Hello world”-program.

Innan du kan installera exempel Helm-diagram, lägger du till lagringsplatsen Azure-exempel i miljön Helm på följande sätt:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Skapa det första demonstrationsprogrammet från ett Helm-diagram med följande kommando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Nu installera en andra instans av demoprogrammet. För den andra instansen anger du ett nytt namn så att de två programmen är visuellt åtskilda. Du kan även ange ett unikt namn:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Skapa en inkommande väg

Båda programmen körs nu på ett Kubernetes-kluster, men de är konfigurerade med en tjänst av typen `ClusterIP`. Programmen är därför inte tillgänglig från internet. Skapa en Kubernetes ingress-resurs så att de blir allmänt tillgänglig. Ingress-resursen konfigurerar regler som vidarebefordrar trafik till en av de två programmen.

I följande exempel trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras till den `ingress-demo` service. Uppdatera den *värdar* och *värden* till DNS-namn som du skapade i föregående steg.

Skapa en fil med namnet `hello-world-ingress.yaml` och kopiera följande YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Skapa den ingående resursen med hjälp av den `kubectl apply -f hello-world-ingress.yaml` kommando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Skapa ett certifikatobjekt

Därefter måste du skapa en resurs för certifikatet. Certifikatresursen definierar det önskade X.509-certifikatet. Mer information finns i [certifikathanterare certifikat][cert-manager-certificates].

Certifikathanterare har förmodligen skapas automatiskt ett certifikatobjekt med ingångs-shim, det distribueras automatiskt med certifikathanterare sedan v0.2.2. Mer information finns i den [ingress-shim dokumentation][ingress-shim].

Kontrollera att certifikatet har skapats genom att använda den `kubectl describe certificate tls-secret --namespace ingress-basic` kommando.

Om certifikatet har utfärdats, visas utdata som liknar följande:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Om du vill skapa en ytterligare certifikat-resurs kan göra du det med följande exempel manifestet. Uppdatera den *dnsNames* och *domäner* till DNS-namn som du skapade i föregående steg. Om du använder en interna ingress-kontrollant, ange det interna DNS-namnet för din tjänst.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret-staging
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Använd för att skapa certifikatresursen, den `kubectl apply -f certificates.yaml` kommando.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret-staging created
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfiguration

Öppna en webbläsare till FQDN för din Kubernetes ingress-kontrollant, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Eftersom de här exemplen använder `letsencrypt-staging`, utfärdade SSL-certifikatet inte är betrodd av webbläsaren. Godkänn varningen att fortsätta till programmet. Certifikatinformationen visar detta *Fake LE mellanliggande X1* certifikat utfärdas av vi kryptera. Den här falska certifikat anger `cert-manager` bearbetade förfrågan korrekt och tog emot ett certifikat från providern:

![Nu ska vi kryptera mellanlagrings-certifikat](media/ingress/staging-certificate.png)

När du ändrar vi kryptera att använda `prod` snarare än `staging`, ett betrott certifikat som utfärdats av vi kryptera används, enligt följande exempel:

![Nu ska vi kryptera certifikat](media/ingress/certificate.png)

Demoprogrammet visas i webbläsaren:

![Exempel på en](media/ingress/app-one.png)

Lägg nu till den */hello-world-two* sökvägen till det fullständiga Domännamnet, till exempel *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Andra demoprogrammet med anpassade rubriken visas:

![Exempel på två](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Rensa resurser

Den här artikeln används Helm för att installera komponenter för ingress, certifikat och exempelappar. När du distribuerar ett Helm-diagram, skapas ett antal Kubernetes-resurser. Dessa resurser inkluderar poddar, distributioner och tjänster. Om du vill rensa resurserna du antingen ta bort hela exemplet namnområde eller enskilda resurser.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Ta bort namnområdet exemplet och alla resurser

Ta bort namnområdet hela exemplet genom att använda den `kubectl delete` kommandot och ange namnet på ditt namnområde. Alla resurser i namnområdet tas bort.

```console
kubectl delete namespace ingress-basic
```

Ta sedan bort Helm-lagringsplatsen för AKS hello world-app:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Ta bort resurser individuellt

Du kan också är en mer detaljerad metod att ta bort enskilda resurserna som du skapade. Ta först bort certificate-resurser:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Nu lista Helm-versioner med den `helm list` kommando. Leta efter diagram med namnet *nginx-ingress*, *certifikathanterare*, och *aks-helloworld*, vilket visas i följande Exempelutdata:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Wed Mar  6 19:37:43 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      kube-system
loitering-waterbuffalo  1           Wed Mar  6 20:25:01 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
flabby-deer             1           Wed Mar  6 20:27:54 2019    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Wed Mar  6 20:27:59 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Ta bort versionerna med de `helm delete` kommando. I följande exempel tar bort NGINX ingående distribution och Certifikathanteraren två AKS hello world exempelapparna.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ta sedan bort Helm-lagringsplatsen för AKS hello world-app:

```console
helm repo remove azure-samples
```

Ta bort de själva namnområde. Använd den `kubectl delete` kommandot och ange namnet på ditt namnområde:

```console
kubectl delete namespace ingress-basic
```

Slutligen tar du bort den inkommande väg som dirigeras trafiken till exempelapparna:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln ingår vissa externa komponenter till AKS. Mer information om dessa komponenter finns på följande sidor i projektet:

- [Helm CLI][helm-cli]
- [Ingress-kontrollanten för NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Du kan också:

- [Skapa en grundläggande ingress-kontrollant med extern nätverksanslutning][aks-ingress-basic]
- [Aktivera HTTP-programmet routning tillägg][aks-http-app-routing]
- [Skapa en ingress-kontrollant som använder ett privat internt nätverk och IP-adress][aks-ingress-internal]
- [Skapa en ingress-kontrollant som använder en egen TLS-certifikat][aks-ingress-own-tls]
- [Skapa en ingress-kontrollant som använder vi kryptera för att automatiskt generera TLS-certifikat med en statisk offentlig IP-adress][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
