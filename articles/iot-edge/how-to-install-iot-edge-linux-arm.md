---
title: Installera Azure IoT Edge på Linux ARM32 | Microsoft Docs
description: Azure IoT Edge instruktioner för installation på Linux på ARM32 enheter, till exempel en Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.openlocfilehash: f7004edf2bab0e22d4d1e4c1200d6e8b8ef729b3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485950"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installera Azure IoT Edge-körningen på Linux (ARM32v7/armhf)

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet. 

Läs mer om hur IoT Edge-körningen fungerar och vilka komponenter som ingår i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller steg för att installera Azure IoT Edge-körningen på en Linux ARM32v7/armhf IoT Edge-enhet. De här stegen skulle till exempel fungerar för Raspberry Pi-enheter. En lista över operativsystem som stöds ARM32 finns i [Azure IoT Edge stöds system](support.md#operating-systems). 

>[!NOTE]
>Paket i databaser för Linux-programvara är gäller under licensvillkor som finns i varje paket (/ usr/dela/docs/*paketnamn*). Läs licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

## <a name="install-the-latest-version"></a>Installera den senaste versionen

Använd följande avsnitt för att installera den senaste versionen av tjänsten Azure IoT Edge på Linux ARM-enheter. 

### <a name="install-the-container-runtime"></a>Installera runtime behållare

Azure IoT Edge förlitar sig på en [OCI-kompatibla](https://www.opencontainers.org/) behållare runtime. I produktionsscenarier rekommenderas du använder den [Moby-baserade](https://mobyproject.org/) motor som anges nedan. Det är den enda behållare motorn officiellt stöds med Azure IoT Edge. Docker CE/EE-behållaravbildningar är kompatibla med Moby-baserade-runtime.

Följande kommandon installera både Moby-baserad motor och kommandoradsgränssnittet (CLI). CLI är användbart för utveckling men valfria för Produktionsdistribution.

```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f
```

### <a name="install-the-iot-edge-security-daemon"></a>Installera Daemon för IoT Edge-säkerhet

Den **IoT Edge security daemon** tillhandahåller och underhåller säkerhetsstandarder på IoT Edge-enhet. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge-körningen. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

När IoT Edge har installerats uppmanas du att uppdatera konfigurationsfilen i utdata. Följ stegen i den [konfigurera Azure IoT Edge security daemon](#configure-the-azure-iot-edge-security-daemon) avsnitt för att avsluta etablering av enheten. 

## <a name="install-a-specific-version"></a>Installera en specifik version

Om du vill installera en specifik version av Azure IoT Edge kan du rikta komponentfiler direkt från IoT Edge GitHub-lagringsplatsen. Använd samma `curl` kommandon som visas i föregående avsnitt för att få alla IoT Edge-komponenterna på din enhet: Moby-motorn och CLI, libiothsm och slutligen daemonen IoT Edge-säkerhet. Den enda skillnaden är att du ersätter den **aka.ms** URL: er med länkar direkt som pekar på versionen av varje komponent som du vill använda.

Navigera till den [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases), och hitta den utgivna versionen som du vill använda. Expandera den **tillgångar** för versionen och välja de filer som matchar din IoT-Edge enhetens arkitektur. Varje IoT Edge-versionen innehåller **iotedge** och **libiothsm** filer. Inte alla versioner innehåller **moby-engine** eller **moby cli**. Om du inte redan har Moby container-motorn installerad, titta igenom äldre versioner tills du hittar en som innehåller Moby-komponenter. 

När IoT Edge har installerats uppmanas du att uppdatera konfigurationsfilen i utdata. Följ stegen i nästa avsnitt för att avsluta etablering av enheten. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurera Azure IoT Edge security daemon

Konfigurera IoT Edge-körningen för att länka den fysiska enheten med en enhetsidentitet som finns i Azure IoT hub. 

Daemonen kan konfigureras med hjälp av konfigurationsfilen på `/etc/iotedge/config.yaml`. Filen är skrivskyddad som standard så att du kan behöva förhöjd behörighet att redigera den.

En enda IoT Edge-enhet kan etableras manuellt med hjälp av en sträng för anslutningar av enhet som tillhandahålls av IoT Hub. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter för att etablera. Beroende på föredrar etablering, väljer du lämplig installationsskriptet. 

### <a name="option-1-manual-provisioning"></a>Alternativ 1: Manuell etablering

Om du vill etablera en enhet manuellt, måste du ange den med en [enhetsanslutningssträngen](how-to-register-device-portal.md) att du kan skapa genom att registrera en ny IoT Edge-enhet i IoT hub.

Öppna konfigurationsfilen. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Gå till avsnittet etablering av filen och ta bort den **manuell** Etableringsläge. Uppdatera värdet för **device_connection_string** med anslutningssträngen från din IoT Edge-enhet.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# provisioning: 
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   registration_id: "{registration_id}"
```

Spara och stäng filen. 

`CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Alternativ 2: Automatisk etablering

Att automatiskt etablera en enhet [konfigurera Device Provisioning-tjänsten och hämta din enhet registrerings-ID](how-to-auto-provision-simulated-device-linux.md). Automatisk etablering fungerar bara med enheter som har ett chip för Trusted Platform Module (TPM). Till exempel levereras Raspberry Pi enheter inte med TPM som standard. 

Öppna konfigurationsfilen. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Gå till avsnittet etablering av filen och ta bort den **dps** Etableringsläge. Uppdaterar du värdet för **scope_id** och **registration_id** med värden från IoT Hub Device Provisioning-tjänsten och IoT Edge-enhet med TPM. 

```yaml
# provisioning:
#   source: "manual"
#   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
provisioning: 
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "{scope_id}"
  registration_id: "{registration_id}"
```

Spara och stäng filen. 

`CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifiera installationen

Om du har använt den **manuell konfiguration** stegen i föregående avsnitt, IoT Edge-körningen ska vara har etablerad och körs på din enhet. Eller, om du har använt den **automatisk konfiguration** stegen, måste du utföra några ytterligare steg så att körningen kan registrera din enhet med IoT-hubben för din räkning. Nästa steg, se [skapa och etablera en simulerad TPM IoT Edge-enhet på en Linux-dator](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Du kan kontrollera status för en IoT Edge-Daemon med hjälp av:

```bash
systemctl status iotedge
```

Kontrollera daemon loggar med:

```bash
journalctl -u iotedge --no-pager --no-full
```

Och listan körs moduler med:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Användbara tips och råd

Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du har installerat en runtime, logga ut från datorn och logga in för att uppdatera dina behörigheter automatiskt. Tills dess kan du använda **sudo** framför alla `iotedge` kommandona.

På resursen begränsad enheter, vi rekommenderar starkt att du ställer in den *OptimizeForPerformance* miljövariabeln till *FALSKT* enligt anvisningarna i den [felsökningsguide ](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Om ditt nätverk som har en proxyserver, följer du stegen i [konfigurerar IoT Edge-enheten att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge-installationen från din Linux-enhet kan använda följande kommandon från kommandoraden. 

Ta bort IoT Edge-körningen. 

```bash
sudo apt-get remove --purge iotedge
```

När IoT Edge-körningen tas bort, stoppas den behållare som det skapats men finns kvar på enheten. Visa alla behållare för att se vilka som finns kvar. 

```bash
sudo docker ps -a
```

Ta bort behållarna från enheten, till exempel två körningsbehållarna. 

```bash
sudo docker rm -f <container name>
```

Ta bort behållaren runtime slutligen från din enhet. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med IoT Edge-körningen installeras korrekt, ser du den [felsökning](troubleshoot.md#stability-issues-on-resource-constrained-devices) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).
