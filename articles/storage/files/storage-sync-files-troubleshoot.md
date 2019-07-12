---
title: Felsök Azure File Sync | Microsoft Docs
description: Felsök vanliga problem med Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 68d0f4f85bc8879191784f038c74fafc40c422b7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604677"
---
# <a name="troubleshoot-azure-file-sync"></a>Felsök Azure File Sync
Använd Azure File Sync för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln är utformad för att hjälpa dig att felsöka och lösa problem som kan uppstå med Azure File Sync-distributionen. Vi beskriver också hur du samlar in viktiga loggar från systemet om det krävs en mer ingående undersökning av problemet. Om du inte ser svar på din fråga, kan du kontakta oss via följande kanaler (i ständigt växande ordning):

1. [Azure Storage-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft Support. Att skapa en ny supportbegäran i Azure-portalen på den **hjälpa** fliken den **hjälp + support** och välj sedan **ny supportbegäran**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Jag har problem med Azure File Sync på Min server (sync, cloud lagringsnivåer, etc.). Ta bort och återskapa min serverslutpunkt
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Agentregistreringen för installation och server
<a id="agent-installation-failures"></a>**Felsöka fel vid installation av agent**  
Om Azure File Sync-agentinstallationen misslyckas vid en upphöjd kommandotolk kör du följande kommando för att aktivera loggning under agentinstallationen:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Granska installer.log om du vill ta reda på orsaken till att installationen misslyckades.

<a id="agent-installation-on-DC"></a>**Skyddsagenten kan inte installeras på Active Directory-domänkontrollant**  
Du kan stöta på problem där sync-agenten kan inte installeras om du försöker installera sync-agenten på en Active Directory-domänkontrollant där rollägare PDC är på en Windows Server 2008 R2 eller under OS-version.

Lös genom att överföra rollen PDC till en annan domänkontrollant som kör Windows Server 2012 R2 eller senare, installera synkronisering.

<a id="server-registration-missing"></a>**Servern visas under registrerade servrar i Azure portal**  
Om en server inte visas **registrerade servrar** för en tjänst för Lagringssynkronisering:
1. Logga in på den server som du vill registrera.
2. Öppna Utforskaren och gå sedan till installationskatalogen för Storage Sync-agenten (standardsökvägen är C:\Program Files\Azure\StorageSyncAgent). 
3. Kör ServerRegistration.exe och slutför guiden för att registrera servern med en Lagringssynkroniseringstjänst.

<a id="server-already-registered"></a>**Registrera servern visas följande meddelande under installationen av Azure File Sync-agenten: ”Den här servern är redan registrerad”** 

![En skärmbild av dialogrutan registrera servern med felmeddelandet ”servern är redan registrerad” meddelande](media/storage-sync-files-troubleshoot/server-registration-1.png)

Det här meddelandet visas om servern tidigare har registrerats med en Lagringssynkroniseringstjänst. För att avregistrera servern från den aktuella Storage Sync-tjänsten och registrera sedan med en ny tjänst för Lagringssynkronisering, slutför du stegen som beskrivs i [avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Om servern inte visas **registrerade servrar** i Storage Sync-tjänsten på den server som du vill avregistrera, kör du följande PowerShell-kommandon:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Om servern är en del av ett kluster, kan du använda den valfria *återställning StorageSyncServer - CleanClusterRegistration* parametern du också ta bort kluster-registreringen.

<a id="web-site-not-trusted"></a>**När jag registrera en server visas ett stort antal ”webbplats inte betrott”-svar. Varför?**  
Det här problemet uppstår när den **förbättrad säkerhet i Internet Explorer** principen är aktiverad under registreringen av servern. Mer information om hur du inaktiverar korrekt i **förbättrad säkerhet i Internet Explorer** princip, se [förbereda Windows Server som ska användas med Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) och [så här distribuerar du Azure File Synkronisera](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Synkronisera grupphantering
<a id="cloud-endpoint-using-share"></a>**Molnslutpunkten misslyckas med felet: ”Angiven Azure FileShare används redan av en annan CloudEndpoint”**  
Det här problemet uppstår om Azure-filresursen används redan av en annan slutpunkt i molnet. 

Om du ser det här meddelandet och Azure-filresursen är för närvarande inte används av en slutpunkt i molnet, gör du följande för att ta bort Azure File Sync-metadata i Azure-filresursen:

> [!Warning]  
> Tar bort metadata i en Azure-filresurs som för närvarande används av en slutpunkt i molnet gör Azure File Sync-åtgärder misslyckas. 

1. Gå till din Azure-filresurs i Azure-portalen.  
2. Högerklicka på Azure-filresursen och välj sedan **redigera metadata**.
3. Högerklicka på **SyncService**, och välj sedan **ta bort**.

<a id="cloud-endpoint-authfailed"></a>**Molnslutpunkten misslyckas med felet: ”AuthorizationFailed”**  
Det här problemet uppstår om ditt konto inte har behörighet att skapa en slutpunkt i molnet. 

Om du vill skapa en slutpunkt i molnet, måste ditt användarkonto ha följande behörigheter i Microsoft Authorization:  
* Läs: Hämta rolldefinition
* Skriva: Skapa eller uppdatera anpassad rolldefinition
* Läs: Hämta rolltilldelning
* Skriva: Skapa rolltilldelning

Följande inbyggda roller har behörigheterna som krävs Microsoft Authorization:  
* Ägare
* Administratör för användaråtkomst

Att fastställa om din användarroll konto har behörigheterna som krävs:  
1. I Azure-portalen väljer du **resursgrupper**.
2. Välj den resursgrupp där lagringskontot finns och välj sedan **åtkomstkontroll (IAM)** .
3. Välj den **rolltilldelningar** fliken.
4. Välj den **rollen** (till exempel ägare eller deltagare) för ditt konto.
5. I den **Resursprovidern** väljer **Microsoft Authorization**. 
    * **Rolltilldelning** bör ha **Läs** och **skriva** behörigheter.
    * **Rolldefinitionen** bör ha **Läs** och **skriva** behörigheter.

<a id="server-endpoint-createjobfailed"></a>**Serverslutpunkten misslyckas med felet: "MgmtServerJobFailed" (Error code: -2134375898)**  
Det här problemet uppstår om sökvägen till serverns slutpunkt finns på systemvolymen och molnet lagringsnivåer är aktiverad. Molnet lagringsnivåer stöds inte på systemvolymen. Inaktivera molnlagringsnivåer när du skapar Serverslutpunkten för att skapa en serverslutpunkt på systemvolymen.

<a id="server-endpoint-deletejobexpired"></a>**Borttagningen av Serverslutpunkten misslyckas med felet: "MgmtServerJobExpired"**                 
Det här problemet uppstår om servern är offline eller inte har någon nätverksanslutning. Om servern är inte längre tillgänglig, avregistrera servern i portalen som tar bort server-slutpunkter. Om du vill ta bort serverslutpunkter, följer du stegen som beskrivs i [avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Det går inte att öppna egenskapssidan för server-slutpunkt eller uppdatera principer för lagringsnivåer för moln**  
Det här problemet kan inträffa om en management-åtgärd på Serverslutpunkten misslyckas. Om egenskapssidan för slutpunkten inte öppnas i Azure-portalen, kan uppdaterar serverslutpunkt med hjälp av PowerShell-kommandon från servern åtgärda problemet. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Serverslutpunkten har en hälsostatus ”ingen aktivitet” eller ”väntande” och Servertillstånd på bladet registrerade servrar är ”visas som offline”**  

Det här problemet kan inträffa om Övervakaren för synkronisering av lagring-processen körs inte eller servern kan inte kommunicera med Azure File Sync-tjänsten på grund av en proxy eller brandvägg.

Åtgärda problemet genom att följa dessa steg:

1. Öppna Aktivitetshanteraren på servern och kontrollera att Storage Sync Monitor-processen (AzureStorageSyncMonitor.exe) körs. Om processen inte körs provar du först att starta om servern. Om du startar om servern inte löser problemet, uppgradera till den senaste Azure File Sync [agentversion](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes).
2. Kontrollera inställningarna för brandväggen och proxyservern är korrekt konfigurerade:
    - Om servern finns bakom en brandvägg kontrollerar du att port 443 för utgående trafik tillåts. Om brandväggen begränsar trafik till specifika domäner, kontrollerar du de domäner som anges i brandväggen [dokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) är tillgängliga.
    - Om servern finns bakom en proxyserver kan du konfigurera datoromfattande eller appspecifika proxyinställningarna genom att följa stegen i proxyn [dokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy).

<a id="endpoint-noactivity-sync"></a>**Serverslutpunkten har en hälsostatus ”ingen aktivitet” och Servertillstånd på bladet registrerade servrar är ”Online”**  

Server hälsostatus för slutpunkter för ”ingen aktivitet” innebär Serverslutpunkten inte har loggat synkroniseringsaktivitet under de senaste två timmarna.

En serverslutpunkt kan inte logga synkronisering av följande skäl:

- Agentversion 4.3.0.0 eller äldre är installerad och att servern har en aktiv VSS-synkroniseringssessionen (SnapshotSync). När en VSS-synkroniseringssessionen är aktiv för en serverslutpunkt kan inte andra serverslutpunkter på samma volym starta start synkroniseringssession tills VSS-synkroniseringssessionen har slutförts. Lös problemet genom att installera agentversion 5.0.2.0 eller senare som har stöd för flera serverslutpunkter synkronisera på en volym när en VSS synkroniseras session är aktiv.

    Du kan kontrollera den aktuella synkroniseringsaktivitet på en server [hur jag för att övervaka förloppet för aktuella synkroniseringssession?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

- Servern har nått det maximala antalet samtidiga synkroniseringssessioner. 
    - Agent-version 4.x och senare: Filgränsen varierar beroende på tillgängliga systemresurser.
    - Agentversion 3.x: 2 active sync-sessioner per processor eller högst 8 active sync-sessioner per server.

> [!Note]  
> Om servertillståndet på bladet registrerade servrar är ”visas Offline”, kan du utföra stegen i den [Serverslutpunkten har en hälsostatus ”ingen aktivitet” eller ”väntande” och Servertillstånd på bladet registrerade servrar är ”visas som offline” ](#server-endpoint-noactivity) avsnittet.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Om jag har skapat en fil direkt i min Azure-filresurs via SMB eller via portalen, hur lång tid tar det för den fil som ska synkroniseras till servrar i synkroniseringsgruppen?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Serverhälsa-slutpunkten är i ett väntande tillstånd under flera timmar**  
Det här problemet förväntas om du skapar en molnslutpunkt och använder en Azure-filresurs som innehåller data. Ändra uppräkning jobbet som söker efter ändringar i Azure-filresursen måste slutföras innan filer kan synkroniseras mellan molnet och servern slutpunkter. Tid att slutföra jobbet är beroende av storleken på namnområdet i Azure-filresursen. Serverhälsa för slutpunkten bör uppdatera när ändringen uppräkning jobbet har slutförts.

### <a id="broken-sync"></a>Hur övervakar hälsan för synkronisering?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Inom varje synkroniseringsgrupp kan du granska nedåt i dess enskilda serverslutpunkter att se status för senaste slutförda synkroniseringssessioner. En grön Health-kolumn och filer synkroniseras inte värdet 0 anger att synkroniseringen fungerar som förväntat. Om detta inte är fallet nedan finns en lista över vanliga synkroniseringsfel och hur du hanterar filer som inte synkroniseras. 

![En skärmbild av Azure-portalen](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Server](#tab/server)
Gå till serverns telemetri loggarna, vilket kan vara finns i Loggboken på `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. Händelsen 9102 motsvarar en slutförd sync-session. Leta efter den senaste händelsen med ID 9102 för den senaste statusen för synkroniseringen. SyncDirection visar om det här sessionen var en överföring eller hämtning. Om HResult 0, lyckades synkroniseringssessionen. Ett noll HResult innebär att det uppstod ett fel under synkronisering; nedan finns en lista över vanliga fel. Det innebär att vissa filer eller mappar inte synkroniserades korrekt om PerItemErrorCount är större än 0. Det är möjligt att ha HResult 0 men en PerItemErrorCount som är större än 0.

Nedan visas ett exempel på en lyckad överföring. Av utrymmesskäl visas endast vissa av värdena i varje 9102 händelse nedan. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Omvänt kan se ladda upp ut så här:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Ibland synkroniseringssessioner misslyckas övergripande eller har en noll PerItemErrorCount men fortfarande göra vidare framsteg med vissa filer synkroniseras har. Detta kan ses i kopplat * fälten (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes), som visar hur mycket av sessionen lyckas. Om du ser flera synkroniseringssessioner i en rad som misslyckas men har en ökande kopplat * antal bör du ge tid att försöka igen innan du öppnar ett supportärende.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hur övervakar förloppet för aktuella synkroniseringssession?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Gå till Serverslutpunkten i fråga inom din synkroniseringsgruppen och titta på avsnittet synkroniseringsaktivitet för att visa antalet filer som laddas upp eller ned i den aktuella synkroniseringssessionen. Observera att denna status ska fördröjas med cirka 5 minuter och om din synkroniseringssessionen är tillräckligt liten för att slutföras inom denna period, den kanske inte rapporteras i portalen. 

# <a name="servertabserver"></a>[Server](#tab/server)
Titta på det senaste 9302 i telemetrin i händelseloggen på servern (i Loggboken, gå till program och tjänster Logs\Microsoft\FileSync\Agent\Telemetry). Den här händelsen anger tillståndet för den aktuella sync-sessionen. TotalItemCount anger hur många filer är att synkronisera AppliedItemCount antalet filer som har synkroniserats hittills och PerItemErrorCount antalet filer som inte kan synkronisera (se nedan att hantera detta).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Hur vet jag om Mina servrar är synkroniserade med varandra?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Kontrollera att för varje server i en viss synkroniseringsgrupp:
- Tidsstämplar för den senaste försök synkroniseringen för både överföring och hämtning är de senaste.
- Statusen är grön för både överföring och hämtning.
- Fältet synkroniseringsaktivitet visar mycket få eller inga filer återstår att synkronisera.
- Fältet inte synkroniserar filer är 0 för både överföring och hämtning.

# <a name="servertabserver"></a>[Server](#tab/server)
Titta på de slutförda synkroniseringssessioner som markerats av 9102 händelser i händelseloggen för telemetri för varje server (i Loggboken, går du till `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. På en server, som du vill kontrollera att den senaste ladda upp och ladda ned sessioner som har slutförts. Om du vill göra detta måste du kontrollera att HResult och PerItemErrorCount är 0 för både uppladdning och nedladdning (fältet SyncDirection anger om en viss session är en överföring eller hämtning session). Observera att om du inte ser en nyligen utförda synkroniseringssessionen är det sannolikt synkroniseringssession pågår, som kan förväntas om du bara läggs till eller ändras av en stor mängd data.
2. När en server är helt uppdaterade med molnet och har inga ändringar synkroniseras i båda riktningarna, visas tom synkroniseringssessioner. Dessa anges genom att ladda upp och hämta händelser i vilken alla de synkronisering * fälten (SyncFileCount, SyncDirCount, SyncTombstoneCount och SyncSizeBytes) är noll, vilket innebär att det inte fanns något att synkronisera. Observera att dessa tom synkroniseringssessioner inte upptäcks på hög dataomsättning servrar eftersom det finns alltid något nytt ska synkroniseras. Om det finns ingen synkroniseringsaktivitet, bör de inträffar var 30: e minut. 
3. Om alla servrar är uppdaterade med molnet, vilket innebär att deras senaste uppladdning och nedladdning sessioner är tom synkroniseringssessioner, så kan du säga med rimlig säkerhet att systemet som helhet är synkroniserade. 
    
Observera att om du har gjort ändringar direkt i din Azure-filresurs, Azure File Sync inte identifierar ändringen förrän ändringen uppräkning körningar som sker en gång per dygn. Det är möjligt att en server kommer säger att det är uppdaterade med molnet och den saknar i själva verket ändringar som görs direkt i Azure-filresursen. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hur ser jag om det finns specifika filer och mappar som inte synkroniseras?
Om din PerItemErrorCount på servern eller filer synkroniseras inte räknas i portalen för alla angivna synkroniseringssessionen som innebär att vissa objekt inte kan synkronisera med är större än 0. Filer och mappar kan ha egenskaper som hindrar dem från att synkronisera. Följande egenskaper kan vara beständig och kräver en explicit åtgärd återuppta synkronisering, till exempel ta bort tecken som inte stöds från filens eller mappens namn. De kan även vara tillfälligt, vilket innebär att filen eller mappen kommer automatiskt att återuppta synkroniseringen; filer med öppna referenser återupptas exempelvis automatiskt synkronisering när filen stängs. När Azure File Sync-motorn upptäcker sådana problem, skapas en fellogg som kan parsas för att visa de objekt som just nu inte ska synkroniseras korrekt.

Om du vill se de här felen, kör den **FileSyncErrorsReport.ps1** PowerShell-skript (finns i installationskatalogen för agenten för Azure File Sync-agenten) att identifiera filer som misslyckades med att synkronisera på grund av öppna referenser som inte stöds tecken, eller andra problem. Fältet ItemPath anger platsen för filen i förhållande till synkronisering för rotkatalogen. Se en lista över vanliga synkroniseringsfel nedan reparationsstegen.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Felsöka per synkroniseringsfel för filen eller katalogen
**ItemResults log - per-item synkroniseringsfel**  

| HRESULT | HRESULT (decimal) | Felsträng | Problem | Åtgärd |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Fil- eller katalogändring kan inte synkroniseras än eftersom en beroende mapp inte har synkroniserats ännu. Det här objektet synkroniseras när de beroende ändringarna har synkroniserats. | Ingen åtgärd krävs. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Namnet på filen eller katalogen är ogiltig. | Byt namn på filen eller katalogen i fråga. Se [hantering av tecken som inte stöds](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) för mer information. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | En fil kan inte synkroniseras eftersom den inte används. Filen kommer att synkroniseras när den inte längre används. | Ingen åtgärd krävs. Azure File Sync skapas en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | En fil har ändrats, men ändringen har ännu inte identifierats av synkronisering. Återställer synkronisera om den här ändringen har identifierats. | Ingen åtgärd krävs. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Filen kan inte synkroniseras eftersom Azure file share gränsen har nåtts. | För att lösa problemet, se [du nått lagringsgränsen för Azure file-resursen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) avsnittet i felsökningsguiden. |
| 0x80070005 | -2147024891 | E_ACCESSDENIED | Det här felet kan inträffa av följande skäl: filen är krypterad med en lösning som inte stöds (till exempel NTFS EFS), filen har ett väntetillstånd eller filen finns på en skrivskyddad replikering DFS-R-mapp | Om filen är krypterad med en lösning för stöds inte dekryptera filen och använder en stöds encryption-lösningen. En lista över supportlösningar finns i [krypteringslösningar](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) avsnitt i Planeringsguiden. Om filen finns i en delete väntetillstånd, kommer filen tas bort när alla öppna filreferenser stängs. Om filen finns på en skrivskyddad replikering DFS-R-mapp, stöder inte serverslutpunkter för DFS-R skrivskyddad replikering mappar i Azure Files Sync. Se [Planeringsguiden](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) för mer information.
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | En fil kan inte synkroniseras eftersom den inte används. Filen kommer att synkroniseras när den inte längre används. | Ingen åtgärd krävs. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | En fil ändrades under synkroniseringen, så den behöver för att synkronisera igen. | Ingen åtgärd krävs. |

#### <a name="handling-unsupported-characters"></a>Hantering av stöds inte tecken
Om den **FileSyncErrorsReport.ps1** PowerShell-skript visar fel på grund av tecken som inte stöds (felkod 0x8007007b), bör du ta bort eller byta namn på tecknen vid fel från respektive filnamnen. PowerShell kommer sannolikt att skriva ut dessa tecken som frågetecken eller tom rektanglar eftersom de flesta av dessa tecken har ingen standard visuell kodning. Den [Evaluation Tool](storage-sync-files-planning.md#evaluation-cmdlet) kan användas för att identifiera tecken som inte stöds.

Tabellen nedan innehåller alla Azure File Sync inte har stöd för Unicode-tecken.

| Teckenuppsättning | Antal tecken |
|---------------|-----------------|
| <ul><li>0x0000009D (osc operativsystemkommando)</li><li>0x00000090 (domänkontrollanter enheten kontroll sträng)</li><li>0x0000008F (ss3 enda SKIFT tre)</li><li>0x00000081 (hög oktetten förinställning)</li><li>0x0000007F (del ta bort)</li><li>0x0000008D (ri omvänd radmatning)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (arabiska presentation formulär-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (Special) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (noncharacter)</li><li>0x0002FFFE - 0x0002FFFF = 2 (noncharacter)</li><li>0x0003FFFE - 0x0003FFFF = 2 (noncharacter)</li><li>0x0004FFFE - 0x0004FFFF = 2 (noncharacter)</li><li>0x0005FFFE - 0x0005FFFF = 2 (noncharacter)</li><li>0x0006FFFE - 0x0006FFFF = 2 (noncharacter)</li><li>0x0007FFFE - 0x0007FFFF = 2 (noncharacter)</li><li>0x0008FFFE - 0x0008FFFF = 2 (noncharacter)</li><li>0x0009FFFE - 0x0009FFFF = 2 (noncharacter)</li><li>0x000AFFFE - 0x000AFFFF = 2 (noncharacter)</li><li>0x000BFFFE - 0x000BFFFF = 2 (noncharacter)</li><li>0x000CFFFE - 0x000CFFFF = 2 (noncharacter)</li><li>0x000DFFFE - 0x000DFFFF = 2 (noncharacter)</li><li>0x000EFFFE - 0x000EFFFF = 2 (odefinierat)</li><li>0x000FFFFE - 0x000FFFFF = 2 (kompletterande privat bruk område)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Vanliga synkroniseringsfel
<a id="-2147023673"></a>**Synkroniseringssessionen avbröts.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimal)** | -2147023673 | 
| **Felsträng** | ERROR_CANCELLED |
| **Reparation krävs** | Nej |

Synkroniseringssessioner kan misslyckas av olika anledningar, inklusive den servern att startas om eller uppdateras, VSS-ögonblicksbilder och så vidare. Även om det här felet verkar det kräver uppföljning, är det säkert att ignorera det här felet, såvida inte den finns kvar under en period på flera timmar.

<a id="-2147012889"></a>**Det gick inte att upprätta en anslutning till tjänsten.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimal)** | -2147012889 | 
| **Felsträng** | WININET_E_NAME_NOT_RESOLVED |
| **Reparation krävs** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Användarbegäran begränsades av tjänsten.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimal)** | -2134376372 |
| **Felsträng** | ECS_E_USER_REQUEST_THROTTLED |
| **Reparation krävs** | Nej |

Ingen åtgärd krävs. servern kommer att försöka igen. Om felet kvarstår under längre tid än ett par timmar, skapa en supportbegäran.

<a id="-2134364043"></a>**Synkronisering är blockerat tills identifiering av ändring av har slutförts efter återställning**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimal)** | -2134364043 |
| **Felsträng** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Reparation krävs** | Nej |

Ingen åtgärd krävs. När en fil eller en fil delar (molnslutpunkt) har återställts med Azure Backup, synkronisering är blockerat tills identifiering av ändring av har slutförts på Azure-filresursen. Identifiering av ändring av körs omedelbart när återställningen är klar och varaktighet baserat på antalet filer i filresursen.

<a id="-2134364065"></a>**Synkronisering kan inte komma åt Azure-filresursen som anges i molnslutpunkten.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimal)** | -2134364065 |
| **Felsträng** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **Reparation krävs** | Ja |

Det här felet beror på att Azure File Sync-agenten inte kan komma åt Azure-filresurs, vilket kan bero på att Azure-filresursen eller storage-konto som är värd för den inte längre finns. Du kan felsöka det här felet genom att utföra följande steg:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att lagringskontot inte innehåller några Nätverksregler.](#troubleshoot-network-rules)
3. [Kontrollera Azure-filresursen finns.](#troubleshoot-azure-file-share)
4. [Kontrollera Azure File Sync har åtkomst till lagringskontot.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Det gick inte att matcha namnet på lagringskontot används.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimal)** | -2134364064 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Reparation krävs** | Ja |

1. Kontrollera att du kan matcha DNS-namn för lagring från servern.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
3. [Kontrollera att lagringskontot inte innehåller några Nätverksregler.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**Synkroniseringen misslyckades på grund av ett problem med sync-databasen.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimal)** | -1906441138 |
| **Felsträng** | JET_errWriteConflict |
| **Reparation krävs** | Ja |

Det här felet uppstår när det finns ett problem med den interna databasen som används av Azure File Sync. När det här problemet uppstår, skapa en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2134364053"></a>**Azure File Sync-agent-version som installerats på servern stöds inte.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimal)** | -2134364053 |
| **Felsträng** | ECS_E_AGENT_VERSION_BLOCKED |
| **Reparation krävs** | Ja |

Det här felet uppstår om Azure File Sync-agent-version som installerats på servern inte stöds. Lös problemet, [uppgradera]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) till en [agentversion som stöds]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Du har nått lagringsgränsen för Azure file-resursen.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimal)** | -2134351810 |
| **Felsträng** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Reparation krävs** | Ja |

Det här felet uppstår när lagringsgränsen för Azure file-resursen har nåtts, vilket kan inträffa om en kvot tillämpas för en Azure-filresurs eller om användningen överskrider gränserna för en Azure-filresurs. Mer information finns i den [aktuella gränser för en Azure-filresurs](storage-files-scale-targets.md).

1. Gå till synkroniseringsgruppen inom Storage Sync-tjänsten.
2. Välj molnslutpunkten i synkroniseringsgruppen.
3. Observera Azure filresursens namn i fönstret öppnade.
4. Välj det länkade storage-kontot. Om den här länken inte har refererade storage-konto tagits bort.

    ![En skärmbild som visar fönstret cloud endpoint detaljerad med en länk till lagringskontot.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Välj **filer** att visa en lista över filresurser.
6. Klicka på de tre punkterna i slutet av raden för Azure-filresursen som refereras av molnslutpunkten.
7. Kontrollera att den **användning** är lägre än den **kvot**. Obs Om inte en annan kvot har angetts, kvoten matchar den [maximal storlek på Azure-filresursen](storage-files-scale-targets.md).

    ![En skärmbild av Azure filresursegenskaperna.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Om resursen är full och en kvot har inte angetts, är ett sätt för att åtgärda det här problemet att göra sin egen serverslutpunkt i sina egna separata synkroniseringsgrupper varje undermapp i den aktuella server-slutpunkten. Det här sättet synkroniseras varje undermapp till enskilda Azure-filresurser.

<a id="-2134351824"></a>**Det går inte att hitta Azure-filresursen.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimal)** | -2134351824 |
| **Felsträng** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Reparation krävs** | Ja |

Det här felet uppstår när Azure-filresursen inte är tillgänglig. Felsökning av:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera Azure-filresursen finns.](#troubleshoot-azure-file-share)

Om Azure-filresursen har tagits bort, måste du skapa en ny filresurs och sedan återskapa synkroniseringsgruppen. 

<a id="-2134364042"></a>**Synkroniseringen har pausats medan Azure-prenumerationen har inaktiverats.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimal)** | -2134364042 |
| **Felsträng** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Reparation krävs** | Ja |

Det här felet uppstår när Azure-prenumerationen har inaktiverats. Synkronisering kommer vara reenabled när Azure-prenumerationen har återställts. Se [Varför är min Azure-prenumeration inaktiverad och hur jag återaktivera den?](../../billing/billing-subscription-become-disable.md) för mer information.

<a id="-2134364052"></a>**Lagringskontot har en brandvägg eller virtuella datornätverk som har konfigurerats.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (decimal)** | -2134364052 |
| **Felsträng** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Reparation krävs** | Ja |

Det här felet uppstår när Azure-filresursen är otillgänglig på grund av en brandvägg för storage-konto eller eftersom lagringskontot tillhör ett virtuellt nätverk. Azure File Sync ännu har inte stöd för den här funktionen. Felsökning av:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att lagringskontot inte innehåller några Nätverksregler.](#troubleshoot-network-rules)

Ta bort dessa regler för att åtgärda problemet. 

<a id="-2134375911"></a>**Synkroniseringen misslyckades på grund av ett problem med sync-databasen.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimal)** | -2134375911 |
| **Felsträng** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Reparation krävs** | Nej |

Det här felet som vanligtvis löser sig själv och som kan inträffa om det finns:

* Ett stort antal filändringar på servrar i synkroniseringsgruppen.
* Ett stort antal fel på enskilda filer och kataloger.

Om felet kvarstår under längre tid än några få timmar, skapa en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2146762487"></a>**Det gick inte att upprätta en säker anslutning på servern. Molntjänsten tog emot ett oväntat certifikat.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimal)** | -2146762487 |
| **Felsträng** | CERT_E_UNTRUSTEDROOT |
| **Reparation krävs** | Ja |

Det här felet kan inträffa om organisationen använder en avslutande SSL-proxy eller om skadliga kan störa trafiken mellan din server och Azure File Sync-tjänsten. Om du är säker på att detta är förväntat (eftersom organisationen använder en SSL avslutande proxy) du hoppa över certifikatverifiering med en åsidosättning med registret.

1. Skapa registervärdet SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Starta om synkroniseringstjänsten på den registrerade servern.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Genom att ange det här registervärdet godtar Azure File Sync-agenten ett lokalt betrodda SSL-certifikat när data överförs mellan servern och Molntjänsten.

<a id="-2147012894"></a>**Det gick inte att upprätta en anslutning till tjänsten.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimal)** | -2147012894 |
| **Felsträng** | WININET_E_TIMEOUT |
| **Reparation krävs** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synkroniseringen misslyckades på grund av ett problem med autentisering.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimal)** | -2134375680 |
| **Felsträng** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Reparation krävs** | Ja |

Det här felet kan orsakas av:

- Servertid är felaktig
- Borttagningen av Serverslutpunkten misslyckades
- Certifikatet som används för autentisering har upphört att gälla. 
    Utför följande steg för att kontrollera om certifikatet har upphört att gälla:  
    1. Öppna snapin-modulen MMC certifikat, Välj datorkontot och navigera till \Personal\Certificates certifikat (lokal dator).
    2. Kontrollera om certifikatet för klientautentisering har upphört att gälla.

Utför följande steg för att lösa problemet om servertiden stämmer:

1. Verifiera Azure File Sync-agenten version 4.0.1.0 eller senare är installerat.
2. Kör följande PowerShell-kommandon på servern:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Den volym som Serverslutpunkten har för lite ledigt diskutrymme.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimal)** | -1906441711 |
| **Felsträng** | JET_errLogDiskFull |
| **Reparation krävs** | Ja |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimal)** | -2134375654 |
| **Felsträng** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Reparation krävs** | Ja |

Det här felet beror på att volymen har blivit full. Det här felet uppstår ofta eftersom filer utanför Serverslutpunkten använder upp utrymme på volymen. Frigör utrymme på volymen genom att lägga till ytterligare serverslutpunkter, flytta filer till en annan volym, eller öka storleken på volymen Serverslutpunkten är på.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Tjänsten är ännu inte redo att synkronisera med den här Serverslutpunkten.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimal)** | -2134364145 |
| **Felsträng** | ECS_E_REPLICA_NOT_READY |
| **Reparation krävs** | Nej |

Det här felet beror på att det finns ändringar på Azure-filresursen direkt och ändra identifiering pågår. Synkronisera påbörjas när identifiering av ändring av har slutförts.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synkroniseringen misslyckades på grund av problem med många enskilda filer.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimal)** | -2134364145 |
| **Felsträng** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Reparation krävs** | Ja |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimal)** | -2134375908 |
| **Felsträng** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Reparation krävs** | Ja |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimal)** | -2134375853 |
| **Felsträng** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Reparation krävs** | Ja |

I fall där det finns många per fil synkroniseringsfel, synkroniseringssessioner får börja misslyckas. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure File Sync skapas en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser.

<a id="-2134376423"></a>**Synkroniseringen misslyckades på grund av ett problem med sökvägen till serverns slutpunkt.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimal)** | -2134376423 |
| **Felsträng** | ECS_E_SYNC_INVALID_PATH |
| **Reparation krävs** | Ja |

Se till att sökvägen finns, finns på en lokal NTFS-volym och är inte en referenspunkt eller en befintlig server-slutpunkt.

<a id="-2134375817"></a>**Synkroniseringen misslyckades eftersom versionen av filter-drivrutinen inte är kompatibel med agentversionen**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimal)** | -2134375817 |
| **Felsträng** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Reparation krävs** | Ja |

Det här felet beror på att Molnlagringsnivå filter (StorageSync.sys) drivrutinsversionen lästs in inte är kompatibel med tjänsten Storage Sync-agenten (FileSyncSvc). Starta om servern för att slutföra installationen om Azure File Sync-agenten har uppgraderats. Om felet kvarstår kan du avinstallera agenten, starta om servern och installera om Azure File Sync-agenten.

<a id="-2134376373"></a>**Tjänsten är inte tillgänglig för tillfället.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimal)** | -2134376373 |
| **Felsträng** | ECS_E_SERVICE_UNAVAILABLE |
| **Reparation krävs** | Nej |

Det här felet beror på att Azure File Sync-tjänsten är inte tillgänglig. Det här felet kommer Lös automatiskt när Azure File Sync-tjänsten eftersom tillgänglig igen.

<a id="-2134375922"></a>**Synkroniseringen misslyckades på grund av ett tillfälligt problem med av synkroniseringsdatabasen.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimal)** | -2134375922 |
| **Felsträng** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Reparation krävs** | Nej |

Det här felet uppstår på grund av ett internt problem med av synkroniseringsdatabasen. Det här felet kommer Lös automatiskt när Azure File Sync när synkronisering försöker igen. Om felet kvarstår för en utöka period, skapa en supportbegäran och vi kontaktar dig för att hjälpa dig att lösa problemet.

### <a name="common-troubleshooting-steps"></a>Vanliga åtgärder för felsökning
<a id="troubleshoot-storage-account"></a>**Kontrollera att lagringskontot finns.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Gå till synkroniseringsgruppen inom Storage Sync-tjänsten.
2. Välj molnslutpunkten i synkroniseringsgruppen.
3. Observera Azure filresursens namn i fönstret öppnade.
4. Välj det länkade storage-kontot. Om den här länken inte har refererade storage-konto tagits bort.
    ![En skärmbild som visar fönstret cloud endpoint detaljerad med en länk till lagringskontot.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-network-rules"></a>**Kontrollera att lagringskontot inte innehåller några Nätverksregler.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. En gång i storage-konto, väljer **brandväggar och virtuella nätverk** på vänster sida av storage-konto.
2. I storage-konto i **tillåta åtkomst från alla nätverk** alternativknappen måste väljas.
    ![En skärmbild som visar storage-konto brandväggs- och regler inaktiveras.](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    throw [System.Exception]::new("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Kontrollera Azure-filresursen finns.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Klicka på **översikt** på den vänstra innehållsförteckningen att återgå till sidan för huvudsakliga lagringskontot.
2. Välj **filer** att visa en lista över filresurser.
3. Kontrollera filresursen som refereras av molnslutpunkten visas i listan över filresurser (du bör ha antecknat detta i steg 1 ovan).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Kontrollera Azure File Sync har åtkomst till lagringskontot.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Klicka på **åtkomstkontroll (IAM)** på den vänstra innehållsförteckningen.
1. Klicka på den **rolltilldelningar** fliken i listan med användare och program (*tjänsthuvudnamn*) som har åtkomst till ditt lagringskonto.
1. Kontrollera **Hybrid Filsynkroniseringstjänstens** visas i listan med de **läsare och dataåtkomst** roll. 

    ![En skärmbild av Hybrid Filsynkroniseringstjänstens tjänstens huvudnamn i fliken access control i storage-konto](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Om **Hybrid Filsynkroniseringstjänstens** inte visas i listan, utför följande steg:

    - Klicka på **Lägg till**.
    - I den **rollen** väljer **läsare och dataåtkomst**.
    - I den **Välj** skriver **Hybrid Filsynkroniseringstjänstens**, markerar du rollen och klickar på **spara**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hur jag för att förhindra användare från att skapa filer som innehåller tecken som inte stöds på servern?
Du kan använda [File Server Resource Manager (FSRM) filgaller](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) filer med tecken som inte stöds i sina namn skapas på servern. Du kan behöva göra detta med hjälp av PowerShell som de flesta av otillåtna tecken inte är utskrivbara och så att du behöver att omvandla sina hexadecimala synpunkter som tecken först.

Först skapa en FSRM-filen med den [cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Det här exemplet definierar gruppen så att den innehåller endast två av otillåtna tecken, men du kan innehålla valfritt antal tecken som krävs i din grupp.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

När du har definierat en FSRM-filgrupp kan skapa du en FSRM-filgallring med hjälp av cmdleten New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Observera att filgaller bara kan användas till blockerar skapandet av tecken som inte stöds av Azure File Sync. Om filgaller används i andra scenarier, sync försöker kontinuerligt ladda ned filer från Azure-filresursen till servern och kommer att blockeras på grund av filgallret, vilket ger hög datatrafik. 

## <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Det finns två sökvägar för fel i molnet lagringsnivåer:

- Filer kan det hända att tier, vilket innebär att Azure File Sync har inte försöker nivån en fil till Azure Files.
- Filer kan det hända att återkallar, vilket innebär att Azure File Sync-filsystemsfilter (StorageSync.sys) går inte att hämta data när en användare försöker få åtkomst till en fil som har varit nivåindelade.

Det finns två huvudsakliga typer av fel som kan ske via antingen fel väg:

- Molnet lagringsfel
    - *Problem med tillfälliga lagringen tjänsters tillgänglighet*. Mer information finns i den [serviceavtal (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Otillgängligt Azure-filresurs*. Det här felet inträffar vanligtvis när du tar bort Azure-filresursen när det är fortfarande en slutpunkt i en synkroniseringsgrupp.
    - *Otillgängligt lagringskonto*. Det här felet inträffar vanligtvis när du tar bort lagringskontot medan det fortfarande har en Azure-filresurs som är en slutpunkt i en synkroniseringsgrupp. 
- Serverfel 
  - *Azure File Sync system filfilter (StorageSync.sys) har inte lästs in*. Azure File Sync-filsystemsfilter måste läsas in för att kunna svara för att lagringsnivåer/återkallande begäranden. Filtret inte hämtas kan bero på flera orsaker, men den vanligaste orsaken är att en administratör tas bort från minnet det manuellt. Azure File Sync-filsystemsfilter måste läsas in på hela tiden för Azure File Sync ska fungera korrekt.
  - *Saknas, skadad eller på annat sätt bruten referenspunkt*. En referenspunkt är en särskild datastruktur för en fil som består av två delar:
    1. En tagg för referenspunkter som anger att operativsystemet filsystemsfilter (StorageSync.sys) för Azure File Sync kan behöva utföra en åtgärd på i/o i filen. 
    2. Referensdata som anger att filsystemet filtrera URI för filen på associerade molnslutpunkten (Azure-filresurs). 
        
       Det vanligaste sättet som kan vara skadade en referenspunkt är om en administratör försöker ändra taggen eller dess data. 
  - *Problem med nätverksanslutningen*. För att kunna nivå eller återställa en fil, måste servern ha Internetanslutning.

I följande avsnitt visar hur du felsöker problem med lagringsnivåer-molnet och avgöra om ett problem är ett problem för lagring av molnet eller ett serverfel.

<a id="monitor-tiering-activity"></a>**Hur övervakar lagringsnivåer på en server**  
Om du vill övervaka lagringsnivåer på en server, använder du händelse-ID 9003 9016 och 9029 i händelseloggen telemetri (finns under program och Services\Microsoft\FileSync\Agent i Loggboken).

- Händelse-ID 9003 innehåller felfördelningen för en serverslutpunkt. Till exempel Total Felräkning ErrorCode, osv. Observera att en händelse loggas per felkod.
- Händelse-ID 9016 innehåller eftersläpningar resultat för en volym. Till exempel ledigt utrymme i procent är avbildad antalet filer i sessionen, antal filer kunde inte ghost osv.
- Händelse-ID 9029 innehåller eftersläpningar sessionsinformation för en serverslutpunkt. Till exempel antalet filer i sessionen, antal filer nivåer i sessionen, antalet filer som redan har nivåindelade, osv.

<a id="monitor-recall-activity"></a>**Så här övervakar du återställningar på en server**  
Du övervakar återställningar på en server med händelse-ID 9005 9006, 9009 och 9059 i händelseloggen telemetri (finns under program och Services\Microsoft\FileSync\Agent i Loggboken).

- Händelse-ID 9005 ger återkallande tillförlitligheten hos en serverslutpunkt. Till exempel Total unika filer som används, totalt antal unika filer med misslyckade åtkomst osv.
- Händelse-ID 9006 ger återkallande felfördelningen för en serverslutpunkt. Exempel: Totalt antal misslyckade begäranden, ErrorCode, osv. Observera att en händelse loggas per felkod.
- Händelse-ID 9009 ger återkallande sessionsinformation för en serverslutpunkt. Till exempel DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, osv.
- Händelse-ID 9059 ger återkallande av programdistribution för en serverslutpunkt. Till exempel ShareId, programnamn och TotalEgressNetworkBytes.

<a id="files-fail-tiering"></a>**Felsökning av filer som misslyckas till tier**  
Om filerna inte nivå till Azure Files:

1. Granska telemetri, drift- och diagnostikdata händelseloggar, finns under program och Services\Microsoft\FileSync\Agent i Loggboken. 
   1. Kontrollera att filerna finns i Azure-filresursen.

      > [!NOTE]
      > En fil måste synkroniseras till en Azure-filresurs innan den kan vara nivåindelad.

   2. Kontrollera att servern är ansluten till internet. 
   3. Kontrollera Azure File Sync-filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
       - Vid en upphöjd kommandotolk kör `fltmc`. Kontrollera att de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas.

> [!NOTE]
> En händelse-ID 9003 loggas en gång i timmen i händelseloggen telemetri om en fil inte kan nivå (en händelse loggas per felkod). Drift- och diagnostikloggar till event ska användas om ytterligare information behövs för att diagnostisera problem.

<a id="files-fail-recall"></a>**Felsökning av filer som det gick inte att återkallas**  
Om filerna inte kan återställas:
1. Granska telemetri, drift- och diagnostikdata händelseloggar, finns under program och Services\Microsoft\FileSync\Agent i Loggboken.
    1. Kontrollera att filerna finns i Azure-filresursen.
    2. Kontrollera att servern är ansluten till internet. 
    3. Öppna snapin-modulen tjänster i MMC och kontrollera att tjänsten Storage Sync-agenten (FileSyncSvc) körs.
    4. Kontrollera Azure File Sync-filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
        - Vid en upphöjd kommandotolk kör `fltmc`. Kontrollera att de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas.

> [!NOTE]
> En händelse-ID 9006 loggas en gång i timmen i händelseloggen telemetri om det inte går att återställa en fil (en händelse loggas per felkod). Drift- och diagnostikloggar till event ska användas om ytterligare information behövs för att diagnostisera problem.

<a id="files-unexpectedly-recalled"></a>**Felsöka filer som oväntat återställs på en server**  
Antivirusprogram, säkerhetskopiering och andra program som läser stort antal filer leda till oönskade konsumenternas om de inte respekterar attributet hoppa över offline och hoppar över att läsa innehållet i filerna. Genom att hoppa över filer för produkter som stöder det här alternativet kan oönskad återkallning undvikas i samband med aktiviteter som virusgenomsökningar eller säkerhetskopieringsjobb.

Kontakta programleverantören för information om hur du konfigurerar deras lösning så att offlinefiler ignoreras.

Oönskade konsumenternas kan uppstå i andra scenarier, t.ex. när du försöker nå filer i Utforskaren. Om en mapp med filer på molnnivå öppnas i Utforskaren på servern kan det leda till oväntad återkallning. Det är ännu mer troligt om en antiviruslösning är aktiverad på servern.

> [!NOTE]
>Använd Event ID 9059 i händelseloggen telemetri för att avgöra vilka program som orsakar konsumenternas. Den här händelsen ger återkallande av programdistribution för en serverslutpunkt och loggas en gång i timmen.

## <a name="general-troubleshooting"></a>Allmän felsökning
Om du stöter på problem med Azure File Sync på en server kan starta genom att utföra följande steg:
1. Granska telemetri, händelseloggarna operativa och diagnostik i Loggboken.
    - Synkronisera, molnlagring och återkallande ärenden loggas i telemetri, diagnostik och driftssäkerhet händelseloggarna under program och Services\Microsoft\FileSync\Agent.
    - Problem med att hantera en server (till exempel konfigurationsinställningar) loggas i händelseloggarna operativa och diagnostiska under program- och Services\Microsoft\FileSync\Management.
2. Verifiera Azure File Sync-tjänsten körs på servern:
    - Öppna snapin-modulen tjänster i MMC och kontrollera att tjänsten Storage Sync-agenten (FileSyncSvc) körs.
3. Kontrollera Azure File Sync-filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
    - Vid en upphöjd kommandotolk kör `fltmc`. Kontrollera att de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas.

Om problemet kvarstår kör du verktyget AFSDiag:
1. Skapa en katalog som där AFSDiag utdata ska sparas (till exempel C:\Output).
    > [!NOTE]
    >AFSDiag tar bort allt innehåll i katalogen innan du samla in loggar. Ange en plats som inte innehåller data.
2. Öppna en upphöjd PowerShell-kommandotolk och kör sedan följande kommandon (tryck på RETUR efter varje kommando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Spårningsnivån för Azure File Sync kernel-läge, ange **1** (om inget annat anges, att skapa mer utförlig spårningar), och tryck sedan på RETUR.
4. Spårningsnivån för Azure File Sync användaren läge ange **1** (om inget annat anges, att skapa mer utförlig spårningar), och tryck sedan på RETUR.
5. Återskapa problemet. När du är klar anger **D**.
6. En .zip-fil som innehåller loggar och spårningsfiler sparas i den katalogen som du har angett.

## <a name="see-also"></a>Se också
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
- [Vanliga och frågor svar om Azure Files](storage-files-faq.md)
- [Felsöka Azure Files-problem i Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsöka Azure Files-problem i Linux](storage-troubleshoot-linux-file-connection-problems.md)
