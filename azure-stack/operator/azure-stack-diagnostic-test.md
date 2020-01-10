---
title: A rendszerállapot ellenőrzéséhez használja a Azure Stack hub érvényesítési eszközét | Microsoft Docs
description: Megtudhatja, hogyan ellenőrizheti a rendszerállapotot az Azure Stack hub érvényesítési eszközével.
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
ms.openlocfilehash: f362fb5dfc47dca23bf7076ecfe0d347a9c789d0
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817397"
---
# <a name="validate-azure-stack-hub-system-state"></a>Azure Stack hub rendszerállapotának ellenőrzése

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

Azure Stack hub-kezelőként elengedhetetlen az igény szerinti rendszer állapotának és állapotának megállapítása. Az Azure Stack hub Validation Tool (**test-AzureStack**) egy PowerShell-parancsmag, amely lehetővé teszi, hogy teszteket futtasson a rendszeren a hibák azonosítására, ha van ilyen. A rendszer általában arra kéri, hogy ezt az eszközt a [privilegizált végponton (PEP)](azure-stack-privileged-endpoint.md) keresztül futtassa, amikor probléma lép fel a Microsoft Customer Services ügyfélszolgálatával (CSS). A rendszerszintű állapot-és állapotadatok alapján a CSS összegyűjtheti és elemezheti a részletes naplókat, koncentrálhat arra a területre, ahol a hiba bekövetkezett, és együttműködik Önnel a probléma megoldásához.

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

3. Ha a tesztek jelentése **sikertelen**, futtassa a `Get-AzureStackLog`. Az integrált rendszerekre vonatkozó utasításokért lásd: [Get-AzureStackLog futtatása Azure stack hub integrált rendszereken](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)vagy a ASDK: a [Get-AzureStackLog futtatása ASDK rendszeren](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

   A parancsmag a test-AzureStack által generált naplókat gyűjti. Javasoljuk, hogy ne gyűjtsön naplókat, és ne vegye fel a kapcsolatot a CSS-sel, ha a tesztek **figyelmeztetnek**

4. Ha arra utasítja az ellenőrző eszközt, hogy a CSS-t futtatja, a CSS-képviselő az összegyűjtött naplókat fogja kérni a probléma megoldásának folytatásához.

## <a name="tests-available"></a>Elérhető tesztek

Az ellenőrző eszköz lehetővé teszi a rendszerszintű tesztek és az alapszintű Felhőbeli forgatókönyvek futtatását, amelyek betekintést nyújtanak az aktuális állapotba, így kijavíthatja a rendszerével kapcsolatos problémákat.

### <a name="cloud-infrastructure-tests"></a>Felhőalapú infrastruktúra-tesztek

Ezek az alacsony hatású tesztek infrastrukturális szinten működnek, és információkat biztosítanak a különböző rendszerösszetevőkről és függvényekről. Jelenleg a tesztek a következő kategóriákba vannak csoportosítva:

| Teszt kategóriája                                        | Argumentum – belefoglalási és-Mellőzés |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack hub ACS összegzése                              | AzsAcsSummary                     |
| Azure Stack hub Active Directory Összefoglaló                 | AzsAdSummary                      |
| Azure Stack hub riasztások összegzése                            | AzsAlertSummary                   |
| Azure Stack hub-alkalmazás összeomlásának összegzése                | AzsApplicationCrashSummary        |
| Azure Stack hub biztonsági mentési megosztás kisegítő információk összegzése       | AzsBackupShareAccessibility       |
| Azure Stack hub BMC – összefoglalás                              | AzsStampBMCSummary                |
| Azure Stack hub Cloud üzemeltetési infrastruktúra összegzése     | AzsHostingInfraSummary            |
| Azure Stack hub Cloud hosting infrastruktúra kihasználtsága | AzsHostingInfraUtilization        |
| Azure Stack hub vezérlőelem síkja – összefoglalás                    | AzsControlPlane                   |
| Azure Stack hub Defender összegzése                         | AzsDefenderSummary                |
| Az infrastruktúra belső vezérlőprogram-összefoglalását üzemeltető Azure Stack hub  | AzsHostingInfraFWSummary          |
| Azure Stack hub infrastruktúra kapacitása                  | AzsInfraCapacity                  |
| Azure Stack hub-infrastruktúra teljesítménye               | AzsInfraPerformance               |
| Azure Stack hub infrastruktúra-szerepkör összefoglalása              | AzsInfraRoleSummary               |
| Azure Stack hub Network infra                            | AzsNetworkInfra                   |
| Azure Stack hub-portál és API-összefoglalás                   | AzsPortalAPISummary               |
| Azure Stack hub méretezési egység VM-eseményei                     | AzsScaleUnitEvents                |
| Azure Stack hub méretezési egység VM-erőforrásai                  | AzsScaleUnitResources             |
| Azure Stack hub-forgatókönyvek                                | AzsScenarios                      |
| Azure Stack hub SDN érvényesítési összegzése                   | AzsSDNValidation                  |
| Azure Stack hub Service Fabric szerepkör összefoglalása              | AzsSFRoleSummary                  |
| Azure Stack hub Storage-adatsíkon                       | AzsStorageDataPlane               |
| Azure Stack hub Storage Services – összefoglalás                 | AzsStorageSvcsSummary             |
| Azure Stack hub SQL Store-összefoglalás                        | AzsStoreSummary                   |
| Azure Stack hub frissítésének összegzése                           | AzsInfraUpdateSummary             |
| Azure Stack hub virtuális gép elhelyezésének összegzése                     | AzsVmPlacement                    |

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
- **UpdateReadiness**: megtekintheti, hogy az Azure stack hub-példány frissíthető-e. A **UpdateReadiness** csoport futtatásakor a figyelmeztetések hibákként jelennek meg a konzol kimenetében, és a frissítéshez blokkoló kell tekinteni őket. A Azure Stack hub 1910-es verziójától kezdve a következő kategóriák a **UpdateReadiness** -csoport részét képezik:

  - **AzsInfraFileValidation**
  - **AzsActionPlanStatus**
  - **AzsStampBMCSummary**

- **SecretRotationReadiness**: Ellenőrizze, hogy az Azure stack hub-példány olyan állapotban van-e, amelyben a titkos elforgatás futtatható. Ha a **SecretRotationReadiness** csoport fut, a figyelmeztetések hibákként jelennek meg a konzol kimenetében, és a titkos elforgatáshoz blokkoló kell tekinteni őket. A következő kategóriák a SecretRotationReadiness csoport részét képezik:

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

Az alábbi példa a **test-AzureStack** tesztelésével teszteli a rendszer készültségét, mielőtt telepítené egy frissítést vagy gyorsjavítást a **csoport**használatával. A frissítés vagy gyorsjavítás telepítésének megkezdése előtt futtassa a **test-AzureStack** eszközt az Azure stack hub állapotának ellenőrzéséhez:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Ha az Azure Stack hub 1811 előtti verziót futtat, használja a következő PowerShell-parancsokat a **test-AzureStack**futtatásához:

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

Ez a teszt ellenőrzi a hálózati infrastruktúra azon kapcsolatát, amely megkerüli az Azure Stack hub szoftver által meghatározott hálózatot (SDN). Egy nyilvános VIP kapcsolatát mutatja be a konfigurált DNS-továbbítókkal, az NTP-kiszolgálókkal és a hitelesítési végpontokkal. Ez magában foglalja az Azure-hoz való kapcsolódást is, ha az Azure AD-t identitás-szolgáltatóként vagy az összevont kiszolgálóként használja AD FS identitás-szolgáltatóként

Adja meg a Debug paramétert a parancs részletes kimenetének lekéréséhez:

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure Stack hub diagnosztikai eszközeiről és a naplózási problémákról, tekintse meg a [Azure stack hub diagnosztikai eszközök](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)című témakört.

További információ a hibaelhárításról: [Microsoft Azure stack hub hibaelhárítása](azure-stack-troubleshooting.md).
