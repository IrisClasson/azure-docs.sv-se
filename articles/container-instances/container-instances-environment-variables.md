---
title: Ange miljövariabler i Azure Container Instances
description: Lär dig hur du ställer in miljövariabler i de behållare som du kör i Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 4a4b19338d96094f28b4f4bedd8042723f67f10a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66149157"
---
# <a name="set-environment-variables-in-container-instances"></a>Ange miljövariabler i container instances

Ställa in miljövariabler i container instances kan du tillhandahålla dynamisk konfiguration av program eller skript som körs av behållaren. Detta liknar den `--env` kommandoradsargument till `docker run`. 

Ange miljövariabler i en behållare genom att ange dem när du skapar en behållarinstans. Den här artikeln visar exempel på att ställa in miljövariabler när du startar en behållare med den [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example), och [Azure-portalen](#azure-portal-example). 

Exempel: Om du kör Microsofts [aci wordcount] [ aci-wordcount] behållaravbildningen, du kan ändra sitt beteende genom att ange följande miljövariabler:

*NumWords*: Antalet ord som skickas till STDOUT.

*MinLength*: Minsta antalet tecken i ett ord för att det ska räknas. En hög siffra ignorerar vanliga ord som ”av” och ”den”.

Om du vill skicka hemligheter som miljövariabler kan Azure Container Instances stöder [säkra värden](#secure-values) för både Windows och Linux-behållare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Exempel på Azure CLI

Se standardutdata från den [aci wordcount] [ aci-wordcount] behållare, kör först med den här [az container skapa] [ az-container-create] kommando (Nej miljövariabler anges):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Om du vill ändra utdata, starta en andra behållare med den `--environment-variables` argumentet som har lagts till, att ange värden för den *NumWords* och *MinLength* variabler. (Det här exemplet förutsätter att du kör CLI i ett Bash-gränssnittet eller Azure Cloud Shell. Om du använder Windows-kommandotolk, ange variablerna med dubbla citattecken, till exempel `--environment-variables "NumWords"="5" "MinLength"="8"`.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

När båda behållare status visas som *Uppsagd* (Använd [az container show] [ az-container-show] att kontrollera tillstånd), visa loggar med [az behållarloggarna] [ az-container-logs] att se utdata.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Utdata från behållarna som visar hur du har ändrat behållaren andra skript beteende genom att ange miljövariabler.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Exempel på Azure PowerShell

Ange miljövariabler i PowerShell liknar CLI, men använder den `-EnvironmentVariable` kommandoradsargument.

Starta först den [aci wordcount] [ aci-wordcount] behållare med standardkonfigurationen med den här [New-AzContainerGroup] [ new-Azcontainergroup] kommando:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Kör nu följande [New-AzContainerGroup] [ new-Azcontainergroup] kommando. Den här anger den *NumWords* och *MinLength* miljövariabler när du har fyllt en matrisvariabel `envVars`:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

När båda behållare tillstånd är *Uppsagd* (använda [Get-AzContainerInstanceLog] [ azure-instance-log] att kontrollera tillstånd), hämta loggar för med den [ Get-AzContainerInstanceLog] [ azure-instance-log] kommando.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Utdata för varje behållare visar hur du har ändrat det skriptet som körs av behållaren genom att ange miljövariabler.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Exempel på Azure portal

Om du vill ange miljövariabler när du startar en behållare i Azure-portalen, kan du ange dem i den **Avancerat** när du skapar behållaren.

1. På den **Avancerat** ställer du in den **omstartsprincip** till *vid fel*
2. Under **miljövariabler**, ange `NumWords` med värdet `5` för den första variabeln, och ange `MinLength` med värdet `8` för den andra variabeln. 
1. Välj **granska + skapa** att verifiera och sedan distribuerar du behållaren.

![Portal-sida som visar miljö variabeln aktivera-knappen och textrutor][portal-env-vars-01]

Visa behållarens loggar under **inställningar** Välj **behållare**, sedan **loggar**. Liknar utdata visas i den tidigare CLI och PowerShell avsnitt ser du hur skriptets beteendet har ändrats av miljövariablerna. Endast fem ord visas var och en med en minsta längd på åtta tecken.

![Portalen med loggutdata för behållaren][portal-env-vars-02]

## <a name="secure-values"></a>Säkra värden

Objekt med säkra värden är avsedda att lagra känslig information, t.ex. lösenord eller nycklar för ditt program. Med säkra värden för miljövariabler är både säkrare och mer flexibelt än att inkludera den i din behållaravbildning. Ett annat alternativ är att använda hemliga volymer, som beskrivs i [montera hemlighetsvolymen i Azure Container Instances](container-instances-volume-secret.md).

Miljövariabler med säkra värden inte visas i behållaregenskaperna--deras värden kan nås från i behållaren. Till exempel visas egenskaper för behållare i Azure portal eller Azure CLI visas endast en säker variabelns namn, inte dess värde.

Ange en säker miljövariabel genom att ange den `secureValue` egenskap i stället för vanliga `value` för variabeltypen. De två variabler som definieras i följande YAML visar två variabeltyper.

### <a name="yaml-deployment"></a>YAML-distribution

Skapa en `secure-env.yaml` fil med följande kodavsnitt.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Kör följande kommando för att distribuera behållargrupp med YAML (justera resursgruppens namn efter behov):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Kontrollera miljövariabler

Kör den [az container show] [ az-container-show] kommando för att fråga din behållare miljövariabler:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON-svar visas både i osäkert miljövariabeln nyckel och värde, men bara namnet på variabeln säker miljö:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Med den [az container exec] [ az-container-exec] kommando, vilket gör det möjligt att köra ett kommando från en behållare som körs, du kan kontrollera att miljövariabeln säker har ställts in. Kör följande kommando för att starta en interaktiv bash-session i behållaren:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

När du har öppnat ett interaktivt gränssnitt i behållaren kan du komma åt den `SECRET` variabelns värde:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, till exempel en stor datauppsättning med flera behållare för batchbearbetning kan dra nytta av anpassade miljövariabler vid körning. Mer information om hur du kör uppgiftsbaserade behållare finns i [köra behållarbaserade uppgifter med principer för omstart](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
