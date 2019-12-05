---
title: A rendszerállapot ellenőrzése a Azure Stack ellenőrzési eszköz használatával | Microsoft Docs
description: Megtudhatja, hogyan ellenőrizheti a rendszerállapotot a Azure Stack ellenőrzési eszköz használatával.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 06/26/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 98732c3eb5933e1fd6d7ce42d726d3f5019c97eb
ms.sourcegitcommit: 53f7daf295783a30feb284d4c48c30c6936557c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74830963"
---
# <a name="validate-azure-stack-system-state"></a>Azure Stack rendszerállapotának ellenőrzése

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack kezelőként elengedhetetlen, hogy az igény szerinti rendszer állapotát és állapotát meg lehessen határozni. A Azure Stack Validation Tool (**test-AzureStack**) egy PowerShell-parancsmag, amely lehetővé teszi, hogy a rendszer teszteket futtasson a rendszeren a hibák azonosítása érdekében, ha van ilyen. A rendszer általában arra kéri, hogy ezt az eszközt a [privilegizált végponton (PEP)](azure-stack-privileged-endpoint.md) keresztül futtassa, amikor probléma lép fel a Microsoft Customer Services ügyfélszolgálatával (CSS). A rendszerszintű állapot-és állapotadatok alapján a CSS összegyűjtheti és elemezheti a részletes naplókat, koncentrálhat arra a területre, ahol a hiba bekövetkezett, és együttműködik Önnel a probléma megoldásához.

## <a name="running-the-validation-tool-and-accessing-results"></a>Az érvényesítési eszköz futtatása és az eredmények elérése

A fentiekben leírtak szerint az ellenőrző eszköz a PEP-n keresztül fut. Minden teszt a PowerShell-ablakban a **Pass/Fail** állapotot adja vissza. Itt látható a végpontok közötti érvényesítés tesztelési folyamatának vázlata:

1. Hozzáférés a PEP-hez. A következő parancsok futtatásával hozzon létre egy PEP-munkamenetet:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > A PEP Azure Stack Development Kit (ASDK) gazdagépen való eléréséhez használja a következőt: AzS-ERCS01 for-számítógépnév.

2. A PEP futtatása után futtassa a következőket:

   ```powershell
   Test-AzureStack
   ```

   További információ: [paraméterekkel kapcsolatos szempontok](azure-stack-diagnostic-test.md#parameter-considerations) és [példák használata](azure-stack-diagnostic-test.md#use-case-examples).

3. Ha a tesztek jelentése **sikertelen**, futtassa a `Get-AzureStackLog`. Az integrált rendszerekre vonatkozó utasításokért lásd: [Get-AzureStackLog futtatása Azure stack integrált rendszereken](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)vagy a ASDK: a [Get-AzureStackLog futtatása ASDK rendszeren](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

   A parancsmag a test-AzureStack által generált naplókat gyűjti. Javasoljuk, hogy ne gyűjtsön naplókat, és ne vegye fel a kapcsolatot a CSS-sel, ha a tesztek **figyelmeztetnek**

4. Ha arra utasítja az ellenőrző eszközt, hogy a CSS-t futtatja, a CSS-képviselő az összegyűjtött naplókat fogja kérni a probléma megoldásának folytatásához.

## <a name="tests-available"></a>Elérhető tesztek

Az ellenőrző eszköz lehetővé teszi a rendszerszintű tesztek és az alapszintű Felhőbeli forgatókönyvek futtatását, amelyek betekintést nyújtanak az aktuális állapotba, így kijavíthatja a rendszerével kapcsolatos problémákat.

### <a name="cloud-infrastructure-tests"></a>Felhőalapú infrastruktúra-tesztek

Ezek az alacsony hatású tesztek infrastrukturális szinten működnek, és információkat biztosítanak a különböző rendszerösszetevőkről és függvényekről. Jelenleg a tesztek a következő kategóriákba vannak csoportosítva:

| Teszt kategóriája                                        | Argumentum – belefoglalási és-Mellőzés |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack ACS összegzése                              | AzsAcsSummary                     |
| Azure Stack Active Directory Összefoglaló                 | AzsAdSummary                      |
| Azure Stack riasztások összegzése                            | AzsAlertSummary                   |
| Azure Stack alkalmazás összeomlásának összegzése                | AzsApplicationCrashSummary        |
| Azure Stack biztonsági mentési megosztás kisegítő lehetőségeinek összefoglalása       | AzsBackupShareAccessibility       |
| Azure Stack BMC összegzése                              | AzsStampBMCSummary                |
| Azure Stack Cloud hosting infrastruktúra összegzése     | AzsHostingInfraSummary            |
| Azure Stack Cloud hosting infrastruktúra kihasználtsága | AzsHostingInfraUtilization        |
| Azure Stack vezérlő síkja – összefoglalás                    | AzsControlPlane                   |
| Azure Stack Defender összegzése                         | AzsDefenderSummary                |
| Azure Stack üzemeltetési infrastruktúra belső vezérlőprogram-összefoglalója  | AzsHostingInfraFWSummary          |
| Azure Stack infrastruktúra kapacitása                  | AzsInfraCapacity                  |
| Infrastruktúra-teljesítmény Azure Stack               | AzsInfraPerformance               |
| Infrastruktúra-szerepkör összefoglalása Azure Stack              | AzsInfraRoleSummary               |
| Azure Stack Network infra                            | AzsNetworkInfra                   |
| Azure Stack portál és API-összefoglalás                   | AzsPortalAPISummary               |
| Azure Stack méretezési egység VM-eseményei                     | AzsScaleUnitEvents                |
| Azure Stack méretezési egység virtuálisgép-erőforrásai                  | AzsScaleUnitResources             |
| Azure Stack forgatókönyvek                                | AzsScenarios                      |
| Azure Stack SDN-érvényesítés összegzése                   | AzsSDNValidation                  |
| Azure Stack Service Fabric szerepkör összefoglalása              | AzsSFRoleSummary                  |
| Azure Stack Storage-adatsíkon                       | AzsStorageDataPlane               |
| Azure Stack Storage szolgáltatások összegzése                 | AzsStorageSvcsSummary             |
| Azure Stack SQL-tároló összefoglalása                        | AzsStoreSummary                   |
| Azure Stack frissítés összegzése                           | AzsInfraUpdateSummary             |
| Azure Stack virtuális gép elhelyezésének összegzése                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Felhőbeli forgatókönyvek tesztelése

A fenti infrastruktúra-tesztek mellett a felhőalapú forgatókönyvek tesztelését is futtathatja az infrastruktúra-összetevők funkcióinak ellenőrzéséhez. A tesztek futtatásához Felhőbeli rendszergazdai hitelesítő adatokra van szükség, mivel ezek az erőforrások üzembe helyezését foglalják magukban.

> [!NOTE]
> Jelenleg nem futtathat felhőalapú forgatókönyv-teszteket Active Directory összevont szolgáltatások (AD FS) hitelesítő adataival.

Az ellenőrzési eszköz a következő Felhőbeli forgatókönyveket teszteli:
- Erőforráscsoport létrehozása
- Terv létrehozása
- Ajánlat létrehozása
- Storage-fiók létrehozása
- Virtuális gép létrehozása (VM)
- BLOB Storage-művelet
- Várólista tárolási művelete
- Tábla tárolási művelete

## <a name="parameter-considerations"></a>Paraméterekkel kapcsolatos szempontok

- A paraméterek **listája** az összes rendelkezésre álló tesztelési kategória megjelenítésére használható.

- A paraméterek **közé tartozik** és **figyelmen kívül hagyható** a tesztelési kategóriák belefoglalása vagy kizárása is. További információt ezekről az argumentumokról a következő szakaszban talál.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- A bérlői virtuális gép üzembe helyezése a felhőalapú forgatókönyv-tesztek részeként történik. A virtuális gép központi telepítésének letiltásához használja a **DoNotDeployTenantVm** .

- A **ServiceAdminCredential** paramétert kell megadnia a Felhőbeli forgatókönyvek teszteléséhez a [használati példák](azure-stack-diagnostic-test.md#use-case-examples) című szakaszban leírtak szerint.

- A **BackupSharePath** és a **BackupShareCredential** az infrastruktúra biztonsági mentési beállításainak tesztelésekor használatosak, ahogy azt a [használati példák](azure-stack-diagnostic-test.md#use-case-examples) szakaszban is látható.

- A **DetailedResults** az egyes tesztekhez tartozó Pass/Fail/Warning információ beszerzésére, valamint a teljes futtatásra is használható. Ha nincs megadva, a **test-AzureStack** visszaadja **$true** , ha nincsenek hibák, és **$false** , ha hibák történtek.
- A **TimeoutSeconds** egy adott idő megadására is használható az egyes csoportok befejezéséhez.

- Az érvényesítési eszköz az általános PowerShell-paramétereket is támogatja: verbose, debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, kimenet, PipelineVariable és túlváltozó. További információ: [About Common Parameters](https://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Példák használata

### <a name="run-validation-without-cloud-scenarios"></a>Ellenőrzés futtatása Felhőbeli forgatókönyvek nélkül

Futtassa az ellenőrzési eszközt anélkül, hogy a **ServiceAdminCredential** paraméterrel kihagyja a felhőben futó forgatókönyvek tesztjét: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Ellenőrzés futtatása Felhőbeli forgatókönyvekkel

Az ellenőrző eszköz a **ServiceAdminCredentials** paraméterrel való ellátása alapértelmezés szerint a felhőalapú forgatókönyvek teszteit futtatja: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Ha csak Felhőbeli forgatókönyveket szeretne futtatni a tesztek további részeinek futtatása nélkül, a **Belefoglalás** paraméterrel is elvégezheti a következőket: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

A Felhőbeli rendszergazda felhasználónevet a következő UPN-formátumba kell beírni: serviceadmin@contoso.onmicrosoft.com (Azure AD). Ha a rendszer kéri, írja be a jelszót a Felhőbeli rendszergazdai fiókba.

### <a name="groups"></a>Csoportok

A kezelői élmény javítása érdekében a **Group** paraméter engedélyezve lett több tesztelési kategória egyidejű futtatásához. Jelenleg három csoport van definiálva: **default**, **UpdateReadiness**és **SecretRotationReadiness**.

- **Alapértelmezett**: a **test-AzureStack**standard futtatásának tekintendő. Alapértelmezés szerint ez a csoport akkor fut, ha nincs más csoport kiválasztva.
- **UpdateReadiness**: megtekintheti, hogy a Azure stack-példány frissíthető-e. A **UpdateReadiness** csoport futtatásakor a figyelmeztetések hibákként jelennek meg a konzol kimenetében, és a frissítéshez blokkoló kell tekinteni őket. Az 1910-es verziótól kezdve a következő kategóriák a **UpdateReadiness** csoport részét képezik: Azure stack

  - **AzsInfraFileValidation**
  - **AzsActionPlanStatus**
  - **AzsStampBMCSummary**

- **SecretRotationReadiness**: Ellenőrizze, hogy a Azure stack példány olyan állapotban van-e, amelyben a titkos elforgatás futtatható. Ha a **SecretRotationReadiness** csoport fut, a figyelmeztetések hibákként jelennek meg a konzol kimenetében, és a titkos elforgatáshoz blokkoló kell tekinteni őket. A következő kategóriák a SecretRotationReadiness csoport részét képezik:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>Példa a Group paraméterre

Az alábbi példa a **test-AzureStack** tesztelésével teszteli a rendszer készültségét, mielőtt telepítené egy frissítést vagy gyorsjavítást a **csoport**használatával. A frissítés vagy gyorsjavítás telepítésének megkezdése előtt futtassa a **test-AzureStack** eszközt a Azure stack állapotának ellenőrzéséhez:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Ha a Azure Stack a 1811 előtti verziót futtatja, akkor a következő PowerShell-parancsokkal futtassa a **test-AzureStack**:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Ellenőrzési eszköz futtatása az infrastruktúra biztonsági mentési beállításainak teszteléséhez

Az infrastruktúra biztonsági mentésének konfigurálása *előtt* tesztelheti a biztonsági mentési megosztás elérési útját és a hitelesítő adatokat az **AzsBackupShareAccessibility** teszt használatával:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential $using:backupcred
  ```

A biztonsági mentés konfigurálása *után* a **AzsBackupShareAccessibility** futtatásával ellenőrizheti, hogy a megosztás elérhető-e a ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Ha új hitelesítő adatokat szeretne tesztelni a beállított biztonsági mentési megosztással, futtassa a következőt: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```

### <a name="run-validation-tool-to-test-network-infrastructure"></a>Ellenőrző eszköz futtatása a hálózati infrastruktúra teszteléséhez

Ez a teszt ellenőrzi a hálózati infrastruktúra azon kapcsolatát, amely megkerüli a Azure Stack szoftver által meghatározott hálózatot (SDN). Egy nyilvános VIP kapcsolatát mutatja be a konfigurált DNS-továbbítókkal, az NTP-kiszolgálókkal és a hitelesítési végpontokkal. Ez magában foglalja az Azure-hoz való kapcsolódást is, ha az Azure AD-t identitás-szolgáltatóként vagy az összevont kiszolgálóként használja AD FS identitás-szolgáltatóként

Adja meg a Debug paramétert a parancs részletes kimenetének lekéréséhez:

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni Azure Stack diagnosztikai eszközökről és a naplózási problémákról, tekintse meg a [Azure stack diagnosztikai eszközök](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)című témakört.

A hibaelhárítással kapcsolatos további tudnivalókért tekintse meg a [Microsoft Azure stack hibaelhárítást](azure-stack-troubleshooting.md)ismertető témakört.
