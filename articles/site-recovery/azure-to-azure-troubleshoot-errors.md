---
title: Azure Site Recovery felsökning för Azure till Azure-replikeringsproblem och fel | Microsoft Docs
description: Felsöka fel och problem vid replikering av virtuella datorer i Azure för haveriberedskap
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 1e0450554597d99aa99d6df51f22bfc90c0d92ad
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798568"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Felsöka problem med Azure till Azure VM-replikering

Den här artikeln beskriver vanliga problem i Azure Site Recovery när replikera och återställa virtuella Azure-datorer från en region till en annan region och förklarar hur du felsöker dem. Mer information om konfigurationer som stöds finns i den [stöd matrix för att replikera virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Lista över fel
- **[Problem med kvoten på Azure-resurs (felkod 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Betrodda rotcertifikat (felkod 151066)](#trusted-root-certificates-error-code-151066)**
- **[Utgående anslutning för Site Recovery (felkod 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problem med kvoten på Azure-resurs (felkod 150097)
Prenumerationen måste vara aktiverat för att skapa virtuella Azure-datorer i målregionen som du tänker använda som din region för disaster recovery. Din prenumeration bör också ha tillräckligt många aktiverat för att skapa virtuella datorer med specifik storlek. Som standard väljer Site Recovery samma storlek för den Virtuella måldatorn som virtuella datorn. Om matchande storleken är inte tillgängligt plockas närmaste möjliga storlek automatiskt. Om det finns ingen matchande storlek som stöder Virtuella källdatorkonfigurationen, visas det här felmeddelandet:

**Felkod** | **Möjliga orsaker** | **Rekommendationen**
--- | --- | ---
150097<br></br>**Meddelandet**: Att det gick inte aktivera replikering för den virtuella datorn VmName. | -Ditt prenumerations-ID kanske inte är aktiverat för att skapa virtuella datorer på målplatsen för regionen.</br></br>-Ditt prenumerations-ID kanske inte är aktiverat eller har inte tillräcklig kvot för att skapa specifika VM-storlekar på målplatsen för regionen.</br></br>– En lämplig VM-storlek som matchar källan VM NIC antalet (2) är inte att hitta målet för prenumerations-ID på målplatsen för regionen.| Kontakta [Azure faktureringshjälp](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att skapa virtuella datorer med storlekarna som krävs på målplatsen för din prenumeration. När den är aktiverad, försök igen.

### <a name="fix-the-problem"></a>Åtgärda problemet
Du kan kontakta [Azure faktureringshjälp](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att aktivera din prenumeration för att skapa virtuella datorer av nödvändiga storlekar på målplatsen.

Om målplatsen har en begränsning på kapacitet, inaktiverar du replikering och aktivera det på en annan plats där din prenumeration har tillräckligt stor kvot för att skapa virtuella datorer av nödvändiga storlekar.

## <a name="trusted-root-certificates-error-code-151066"></a>Betrodda rotcertifikat (felkod 151066)

Om alla de senaste betrodda rotcertifikaten inte finns på den virtuella datorn, kan ”aktivera replikering” jobbet misslyckas. Utan certifikat misslyckas autentisering och auktorisering av Site Recovery-tjänst-anrop från den virtuella datorn. Felmeddelandet för det misslyckade jobbet för ”Aktivera replikering” Site Recovery visas:

**Felkod** | **Möjlig orsak** | **Rekommendationer**
--- | --- | ---
151066<br></br>**Meddelandet**: Det gick inte att konfigurera site Recovery. | De begärda betrodda rotcertifikaten för auktorisering och autentisering inte finns på datorn. | – Se till att det finns betrodda rotcertifikat på datorn för en virtuell dator som kör operativsystemet Windows. Mer information finns i [konfigurera betrodda rotcertifikat och otillåtna certifikat](https://technet.microsoft.com/library/dn265983.aspx).<br></br>– För en virtuell dator som kör Linux-operativsystem, följer du anvisningarna för betrodda rotcertifikat som publicerats av distributören Linux operativsystemets version.

### <a name="fix-the-problem"></a>Åtgärda problemet
**Windows**

Installera alla de senaste Windows-uppdateringarna på den virtuella datorn så att alla betrodda rotcertifikat finns på datorn. Om du arbetar i en frånkopplad miljö, följer du Windows update standardprocessen i din organisation för att hämta certifikaten. Om certifikat som krävs inte finns på den virtuella datorn kan misslyckas anrop till Site Recovery-tjänsten av säkerhetsskäl.

Följ vanliga Windows update management eller certifikat management uppdateringsprocessen i din organisation att få de senaste rotcertifikaten och listan över återkallade certifikat uppdaterade certifikatet på de virtuella datorerna.

Kontrollera att problemet är löst genom att gå till login.microsoftonline.com från en webbläsare i den virtuella datorn.

**Linux**

Följ anvisningarna som tillhandahålls av Linux-distributören för att få de senaste betrodda rotcertifikaten och senaste listan över återkallade certifikat på den virtuella datorn.

Eftersom SuSE Linux använder symlinks för att underhålla en lista över certifikat, gör du följande:

1.  Logga in som en rotanvändare.

2.  Kör detta kommando för att ändra katalogen.

      ``# cd /etc/ssl/certs``

1. Kontrollera om Symantec CA: N rotcertifikatet finns.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Om Symantec CA: N rotcertifikatet inte finns, kör du följande kommando för att hämta filen. Kontrollera om några fel och följ rekommenderade åtgärden för nätverksfel.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Kontrollera om Baltimore rot-CA-certifikat finns.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Hämta certifikatet om Baltimore rot-CA-certifikat inte hittas.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Kontrollera om det finns DigiCert_Global_Root_CA-certifikat.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Om DigiCert_Global_Root_CA inte hittas, kör du följande kommandon för att hämta certifikatet.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Kör rehash skript för att uppdatera certifikatet ämne hashvärden för de nyligen hämtade certifikat.

    ``# c_rehash``

8.  Kontrollera om ämnet hashar som symlinks skapas för certifikaten.

    - Kommando

      ``# ls -l | grep Baltimore``

    - Resultat

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Kommando

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Resultat

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Kommando

      ``# ls -l | grep DigiCert_Global_Root``

    - Resultat

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Skapa en kopia av filen VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem med filename b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Skapa en kopia av filen Baltimore_CyberTrust_Root.pem med filename 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Skapa en kopia av filen DigiCert_Global_Root_CA.pem med filename 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Kontrollera om filerna finns.  

    - Kommando

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Resultat

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för Site Recovery-webbadresser eller IP-intervall (felkod: 151037 eller 151072)

För Site Recovery-replikering till arbete, utgående anslutning till specifika URL: er eller IP-intervall krävs från den virtuella datorn. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutningar, kan du står inför ett av de här problemen.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: Det gick inte att registrera Azure-dator med Site Recovery (151195) </br>
- **Möjlig orsak** </br>
  - Att går inte ansluta till Site Recovery-slutpunkter på grund av DNS-matchningsfel.
  - Detta visas oftare vid nytt skydd när du har misslyckats under den virtuella datorn men DNS-servern kan inte nås från regionen för Haveriberedskap.

- **Lösning**
   - Om du använder anpassade DNS Se till att är DNS-servern tillgänglig från regionen för Haveriberedskap. Kontrollera om du har en anpassad DNS går du till den virtuella datorn > Disaster Recovery-nätverket > DNS-servrar. Försök att öppna DNS-servern från den virtuella datorn. Om den inte är tillgänglig sedan när du gör det tillgängligt genom att antingen växling över DNS-server eller skapa verksamhetsspecifika plats mellan DR-nätverk och DNS.

    ![COM-fel](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguration av site Recovery misslyckades (151196)
- **Möjlig orsak** </br>
  - Att går inte ansluta till Office 365-autentisering och identitet IP4-slutpunkter.

- **Lösning**
  - Azure Site Recovery krävs åtkomst till Office 365-IP-intervall för autentisering.
    Om du använder Azure Network security group (NSG) regler/brandväggsproxy för att styra utgående nätverksanslutning på den virtuella datorn, kontrollerar du att du tillåter kommunikation till IP-intervall för O365. Skapa en [Azure Active Directory (AAD) tjänsttagg](../virtual-network/security-overview.md#service-tags) baserat NSG-regel för att tillåta åtkomst till alla IP-adresser för AAD
      - Om nya adresser läggs till Azure Active Directory (AAD) i framtiden, måste du skapa nya NSG-regler.

> [!NOTE]
> Om de virtuella datorerna bakom **Standard** intern belastningsutjämnare och det skulle inte ha åtkomst till O365 IP-adresser, dvs login.microsoftonline.com som standard. Antingen ändra det till **grundläggande** interna typ av belastningsutjämnare eller skapa ut bundna åtkomst enligt den [artikeln](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguration av site Recovery misslyckades (151197)
- **Möjlig orsak** </br>
  - Att går inte ansluta till Azure Site Recovery-Tjänsteslutpunkter.

- **Lösning**
  - Azure Site Recovery krävs för åtkomst till [Site Recovery IP-intervall](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) beroende på regionen. Kontrollera att som krävs för ip-intervall som är tillgängliga från den virtuella datorn.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: A2A-replikeringen misslyckades när nätverkstrafiken som går via en lokal proxyserver (151072)
- **Möjlig orsak** </br>
  - De anpassade proxyinställningarna är ogiltiga och Azure Site Recovery-Mobilitetstjänsten agenten identifieras inte automatiskt proxyinställningar från Internet Explorer


- **Lösning**
  1. Mobilitetstjänstagenten identifierar proxyinställningar från Internet Explorer på Windows och /etc/environment i Linux.
  2. Om du vill ställa in proxy endast för Azure Site Recovery-Mobilitetstjänsten kan du ange proxyinformationen i ProxyInfo.conf som finns på:</br>
     - ``/usr/local/InMage/config/`` på ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` på ***Windows***
  3. ProxyInfo.conf ska ha rätt proxyinställningar har följande INI-format.</br>
                *[proxy]*</br>
                *Adress =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Azure Site Recovery-Mobilitetstjänsten agent stöder endast ***oautentiserade proxyservrar***.


### <a name="fix-the-problem"></a>Åtgärda problemet
Att tillåta [de URL: erna](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller [krävs för IP-intervall](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), följer du stegen i den [vägledningsdokumentet nätverk om](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Hittades inte på datorn (felkod 150039)

En ny disk som är ansluten till den virtuella datorn måste initieras.

**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
150039<br></br>**Meddelandet**: Azure-datadisk (DiskName) (DiskURI) med logiska enhetsnummer (LUN) (LUNValue) har inte kopplats till en disk som rapporterades från den virtuella datorn har samma LUN-värde. | – En ny datadisk har anslutits till den virtuella datorn men den har inte initierats.</br></br>-Datadisken i den virtuella datorn rapporterar felaktigt det LUN-värde som disken anslöts med till den virtuella datorn.| Kontrollera att datadiskarna har initierats och försök sedan igen.</br></br>För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>För Linux: [Initiera en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Åtgärda problemet
Kontrollera att datadiskarna har initierats och försök sedan igen:

- För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- För Linux: [lägga till en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Kontakta supporten om problemet kvarstår.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>En eller flera diskar är tillgängliga för skydd (felkod 153039)
- **Möjlig orsak** </br>
  - Om en eller flera diskar har nyligen lagts till den virtuella datorn efter skyddet. 
  - Om en eller flera diskar initierades senare efter att skyddet av den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet
Du kan antingen välja att skydda diskarna eller Ignorera varningen göra felfri replikeringsstatusen för den virtuella datorn igen.</br>
1. Att skydda diskarna. Gå till replikerade objekt > virtuell dator > diskar > Klicka på oskyddad disk > Aktivera replikering.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. Att ignorera varningen. Gå till replikerat objekt > virtuell dator > Klicka på Stäng aviseringen under översiktsavsnittet.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Ta bort den virtuella datorn från valvet slutfördes med information (felkod 150225)
Vid tidpunkten för att skydda den virtuella datorn, skapar Azure Site Recovery vissa länkar på den virtuella källdatorn. När du ta bort skyddet eller inaktivera replikering, Azure Site Recovery att ta bort dessa länkar som en del av rensningsjobb. Om den virtuella datorn har en resurslås hämtar jobbet slutfördes med informationen. Meddelar att den virtuella datorn har tagits bort från Recovery services-valv men vissa av inaktuella länkarna gick inte att rensa från källdatorn.

Du kan ignorera varningen om du inte vill skydda den här virtuella datorn igen i framtiden. Men om du ska skydda den här virtuella datorn senare bör sedan du rensa länkarna enligt stegen nedan. 

**Om du inte gör rensningen sedan:**

1.  Under tiden för att aktivera replikering via Recovery services-valv, visas virtuell dator inte. 
2.  Om du försöker skydda den virtuella datorn via **virtuell dator > Inställningar > Disaster Recovery** att misslyckades med felet ”*replikering inte aktiveras på grund av befintliga inaktuella resurslänkar på den virtuella datorn*".


### <a name="fix-the-problem"></a>Åtgärda problemet

>[!NOTE]
>
>Azure Site Recovery inte ta bort den virtuella källdatorn eller påverka den på något sätt när du utför stegen nedan.
>

1. Ta bort låset från den virtuella datorn eller VM resursgrupp. Exempel: Nedan VM har namnet ”MoveDemo” resurslås som måste tas bort.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Ladda ned skriptet [ta bort inaktuella Azure Site Recovery configuration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Kör skriptet *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Ange prenumerationens namn-ID, resursgrupp för virtuell dator och virtuell dator som en parameter.
5. Om du blir tillfrågad Azure-autentiseringsuppgifter, ange som och kontrollera att skriptet hämtar körs utan fel. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>Det går inte att aktivera replikering på grund av befintliga inaktuella resurslänkar på den virtuella datorn (felkod 150226)

**Orsak: Virtuell dator har inaktuella konfigurationen kvar från tidigare Site Recovery-skydd**

Den inaktuella konfigurationen kan finnas kvar i en Azure-dator i följande fall:

- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och inaktivera replikering men **Virtuella källdatorn hade en resurslås**.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort valvet för Site Recovery utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort resursgruppen som innehåller Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

>[!NOTE]
>
>Azure Site Recovery inte ta bort den virtuella källdatorn eller påverka den på något sätt när du utför stegen nedan.


1. Ta bort låset från den virtuella datorn eller VM-resursgrupp, om det finns några. *Till exempel:* Nedan VM har namnet ”MoveDemo” resurslås som måste tas bort.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Ladda ned skriptet [ta bort inaktuella Azure Site Recovery configuration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Kör skriptet *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Ange prenumerationens namn-ID, resursgrupp för virtuell dator och virtuell dator som en parameter.
5. Om du blir tillfrågad Azure-autentiseringsuppgifter, ange som och kontrollera att skriptet hämtar körs utan fel.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Det går inte att se gruppen virtuell Azure-dator eller resurs för val av i ”Aktivera replikering”

 **Orsak 1:  Resursgruppens namn och det virtuella källdatorn finns på olika platser**
 
Azure Site Recovery för närvarande måste ett demonterat som käll-resursgrupp i regionen och virtuella datorer ska finnas på samma plats. Om detta inte är fallet skulle sedan du inte att hitta den virtuella datorn eller resursgrupp vid tidpunkten för skyddet. 

**Som en tillfällig lösning**, kan du aktivera replikering från den virtuella datorn i stället för Recovery services-valvet. Gå till Virtuella > Egenskaper > Haveriberedskap och aktivera replikering.

**Orsak 2: Resursgrupp ingår inte i den valda prenumerationen**

Du kanske inte att hitta resursgruppen vid tidpunkten för skydd om det inte är en del av den givna prenumerationen. Kontrollera att resursgruppen tillhör prenumerationen som används.

 **Orsak 3: Inaktuell konfiguration**
 
Om du inte ser den virtuella datorn som du vill aktivera för replikering, kan det på grund av en inaktuell konfiguration av Site Recovery bli virtuella Azure-datorn. Den inaktuella konfigurationen kan finnas kvar i en Azure-dator i följande fall:

- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort valvet för Site Recovery utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort resursgruppen som innehåller Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och inaktivera replikering, men den Virtuella källdatorn hade en resurslås.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
>
> Se till att uppdatera modulen ”” AzureRM.Resources ”” innan du använder den skriptet nedan. Azure Site Recovery inte ta bort den virtuella källdatorn eller påverka den på något sätt när du utför stegen nedan.
>

1. Ta bort låset från den virtuella datorn eller VM-resursgrupp, om det finns några. *Till exempel:* Nedan VM har namnet ”MoveDemo” resurslås som måste tas bort.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Ladda ned skriptet [ta bort inaktuell konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Kör skriptet *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Ange prenumerationens namn-ID, resursgrupp för virtuell dator och virtuell dator som en parameter.
5. Om du blir tillfrågad Azure-autentiseringsuppgifter, ange som och kontrollera att skriptet hämtar körs utan fel.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Det går inte att välja virtuell dator för skydd
 **Orsak 1:  Virtuell dator har vissa tillägg som installeras i tillståndet misslyckad eller svarar inte** <br>
 Gå till virtuella datorer > inställningen > tillägg och kontrollera om det finns några tillägg i ett felaktigt tillstånd. Avinstallera tillägget misslyckades och försök sedan skydda den virtuella datorn.<br>
 **Orsak 2:  [Virtuella datorns Etableringsstatus är inte giltig](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Virtuella datorns Etableringsstatus är inte giltigt (felkod 150019)

Om du vill aktivera replikering på den virtuella datorn, Etableringsstatus måste vara **lyckades**. Du kan kontrollera tillstånd för virtuell dator genom att följa stegen nedan.

1.  Välj den **Resursläsaren** från **alla tjänster** i Azure-portalen.
2.  Expandera den **prenumerationer** listan och välj din prenumeration.
3.  Expandera den **ResourceGroups** listan och välj resursgruppen för den virtuella datorn.
4.  Expandera den **resurser** listan och välj den virtuella datorn
5.  Kontrollera den **provisioningState** i Instansvy på höger sida.

### <a name="fix-the-problem"></a>Åtgärda problemet

- Om **provisioningState** är **misslyckades**, kontakta supporten med information om hur du felsöker.
- Om **provisioningState** är **uppdaterar**, ytterligare ett tillägg kunde komma distribueras. Kontrollera om det finns några pågående åtgärder på den virtuella datorn, vänta tills de slutförts och försök misslyckade Site Recovery **Aktivera replikering** jobbet.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Det går inte att välja mål virtuellt nätverk – fliken för val av nätverk är nedtonat.

**Orsak 1: Om den virtuella datorn är ansluten till ett nätverk som har redan mappats till ett Målnätverk.**
- Om den Virtuella källdatorn är en del av ett virtuellt nätverk och en annan virtuell dator från samma virtuella nätverk har redan mappats till ett nätverk i målresursgruppen, kommer sedan av nätverket standardvalet nedrullningsbara att inaktiveras.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Orsak 2: Om du tidigare har skyddat den virtuella datorn med hjälp av Azure Site Recovery och inaktiverat replikeringen.**
 - Inaktivera replikering för en virtuell dator tas inte bort mappning av nätverket. Det måste tas bort från det recovery service-valv där den virtuella datorn har skyddats. </br>
 Gå till recovery service-valv > Site Recovery-infrastruktur > nätverksmappning. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Målnätverket som konfigurerades vid installationen för disaster recovery kan ändras efter det första konfigurera när den virtuella datorn är skyddad. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Observera att om du ändrar nätverksmappning påverkas alla skyddade virtuella datorer som använder den specifika nätverksmappningen.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ fel i tjänsten Volume Shadow Copy (felkod 151025)

**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
151025<br></br>**Meddelandet**: Det gick inte att installera Site Recovery-tillägget | -, COM + System Application-tjänsten är inaktiverad.</br></br>-'Volume Shadow Copy-tjänsten är inaktiverad.| Ange ”COM + System Application” och ”Volume Shadow Copy-tjänster till automatiskt eller manuellt startläge.

### <a name="fix-the-problem"></a>Åtgärda problemet

Du kan öppna ”tjänster”-konsolen och se till att den ”COM + System Application” och ”Volume Shadow Copy” har angetts inte till ”Disabled” för ”starttyp”.
  ![COM-fel](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Stöds inte hanterade diskens storlek (felkod 150172)


**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
150172<br></br>**Meddelandet**: Att det gick inte aktivera skydd för den virtuella datorn eftersom den innehåller (DiskName) med storlek (Diskstorlek) som är mindre än minimikraven storlek på 1 024 MB. | -Disken är mindre än maxstorleken på 1 024 MB| Kontrollera att de diskar som är inom storleksintervallet som stöds och försök igen.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Aktivera skydd kunde inte utföras eftersom enhetsnamn som nämns i GRUB-konfigurationen istället för UUID (felkod 151126)

**Möjlig orsak:** </br>
GRUB-konfigurationsfilerna (”/ boot/grub/menu.lst” ”, / boot/grub/grub.cfg” ”, / boot/grub2/grub.cfg” eller ”/ etc/standard/grub”) kan innehålla värdet för parametrarna **rot** och **återuppta** som den namn på verkliga enheter istället för UUID. Site Recovery mandat UUID-metod som namn på enheter som kan ändras över omstart av den virtuella datorn när virtuell dator inte kanske kommer upp med samma namn på redundans, vilket resulterar i problem. Exempel: </br>


- Följande rad är GRUB-fil **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- Följande rad är GRUB-fil **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **rot = / dev/sda2** **återuppta = / dev/sda1** stänker = tyst crashkernel = 256M-:128M showopts vga = 0x314*

Om du upptäcker fetstil strängen ovan, innehåller GRUB faktiska enhetsnamn för parametrar ”rot” och ”återuppta” istället för UUID.

**Så här åtgärdar du:**<br>
Enhetsnamn ska ersättas med motsvarande UUID.<br>


1. Hitta UUID för enheten genom att köra kommandot ”blkid \<enhetens namn >”. Exempel:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=\<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again

## Enable protection failed as device mentioned in the GRUB configuration doesn't exist(error code 151124)
**Possible Cause:** </br>
The GRUB configuration files ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") may contain the parameters "rd.lvm.lv" or "rd_LVM_LV" to indicate the LVM device that should be discovered at the time of booting. If these LVM devices doesn't exist, then the protected system itself will not boot and stuck in the boot process. Even the same will be observed with the failover VM. Below are few examples:

Few examples: </br>

1. The following line is from the GRUB file **"/boot/grub2/grub.cfg"** on RHEL7. </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/etc/default/grub"** on RHEL7 </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/boot/grub/menu.lst"** on RHEL6 </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".<br>

**How to Fix:**<br>

If the LVM device doesn't exist, fix either by creating it or remove the parameter for the same from the GRUB configuration files and then retry the enable protection. </br>

## Site Recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  Site Recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  


## Protection couldn't be enabled as replica managed disk 'diskname-replica' already exists without expected tags in the target resource group( error code 150161

**Cause**: It can occur if the  virtual machine was protected earlier in the past and during disabling the replication, replica disk was not cleaned due to some reason.</br>
**How to fix:**
Delete the mentioned replica disk in the error message and restart the failed protection job again.

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
