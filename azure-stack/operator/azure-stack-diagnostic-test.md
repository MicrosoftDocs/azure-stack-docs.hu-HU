---
title: A Azure Stack ellenőrző eszköz használata | Microsoft Docs
description: Diagnosztikai naplófájlok gyűjtése a Azure Stack-ben.
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
ms.openlocfilehash: 43179dfaed48385c901fcf4ad7684d225e36b3df
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493794"
---
# <a name="validate-azure-stack-system-state"></a>Azure Stack rendszerállapotának ellenőrzése

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack-operátorként a rendszer állapotának igény szerinti meghatározása elengedhetetlen. A Azure Stack Validation Tool (**test-AzureStack**) egy PowerShell-parancsmag, amely lehetővé teszi, hogy a rendszer teszteket futtasson a rendszeren a hibák azonosítása érdekében, ha van ilyen. Jellemzően arra fogják kérni, hogy ezt az eszközt a [kiemelt végponton (PEP)](azure-stack-privileged-endpoint.md) futtassa, amikor egy hiba miatt kapcsolatba lép a Microsoft ügyfélszolgálattal és támogatási csapatával (CSS). Az egész rendszerre kiterjedő állapotinformációk segítségével a CSS képes begyűjteni és elemezni a részletes naplókat, a hiba előfordulási helyére összpontosíthat, és Önnel együttműködve megoldhatja a problémát.

## <a name="running-the-validation-tool-and-accessing-results"></a>Az érvényesítési eszköz futtatása és az eredmények elérése

Amint azt korábban már említettük, az ellenőrző eszköz a PEP-n keresztül fut. Minden teszt a PowerShell-ablakban a **Pass/Fail** állapotot adja vissza. Itt látható a végpontok közötti érvényesítés tesztelési folyamatának vázlata: 

1. Hozzáférés az emelt szintű végponthoz (PEP). A következő parancsok futtatásával hozzon létre egy PEP-munkamenetet:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Ha a PEP-t egy ASDK futtató számítógépen szeretné elérni, használja a AzS-ERCS01 for-számítógépnév szolgáltatást.

2. Ha a PEP-et futtatja, futtassa a következőket: 

   ```powershell
   Test-AzureStack
   ```

   További információért tekintse meg a [paraméterekkel kapcsolatos szempontokat](azure-stack-diagnostic-test.md#parameter-considerations) , és [használjon példákat](azure-stack-diagnostic-test.md#use-case-examples) .

3. Ha bármilyen teszt jelentés **sikertelen**, futtassa `Get-AzureStackLog`a parancsot. Az integrált rendszerekre vonatkozó utasításokért lásd: [Get-AzureStackLog futtatása Azure stack integrált rendszereken](azure-stack-configure-on-demand-diagnostic-log-collection.md#to-run-get-azurestacklog-on-azure-stack-integrated-systems)vagy a ASDK: a [Get-AzureStackLog futtatása Azure stack Development Kit (ASDK) rendszeren](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

   A parancsmag a test-AzureStack által generált naplókat gyűjti. Ne gyűjtsön naplókat, vagy forduljon a CSS-hez, ha a tesztek jelentés **figyelmeztet**.

4. Ha arra utasította, hogy a CSS-sel futtassa az ellenőrző eszközt, a CSS-képviselő az összegyűjtött naplókat fogja kérni a probléma megoldásának folytatásához.

## <a name="tests-available"></a>Elérhető tesztek

Az ellenőrző eszköz lehetővé teszi a rendszerszintű tesztek és az alapszintű felhőalapú forgatókönyvek sorozatának futtatását, amelyek betekintést nyújtanak az aktuális állapotba, és megerősítik a rendszer hibáit.

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

A fenti infrastruktúra-tesztek mellett lehetősége van arra is, hogy felhőalapú forgatókönyv-teszteket futtasson az infrastruktúra-összetevők funkcióinak ellenőrzéséhez. A tesztek futtatásához Felhőbeli rendszergazdai hitelesítő adatok szükségesek, mivel ezek az erőforrások üzembe helyezésével járnak.

> [!NOTE]
> Jelenleg nem futtathat felhőalapú forgatókönyv-teszteket Active Directory összevont szolgáltatások (AD FS) hitelesítő adataival. 

Az ellenőrzési eszköz a következő Felhőbeli forgatókönyveket teszteli:
- Erőforráscsoport létrehozása   
- Terv létrehozása              
- Ajánlat létrehozása            
- Storage-fiók létrehozása   
- Virtuális gépek létrehozása 
- BLOB Storage-művelet   
- Várólista tárolási művelete  
- Tábla tárolási művelete  

## <a name="parameter-considerations"></a>Paraméterekkel kapcsolatos szempontok

- A paraméterek **listája** az összes rendelkezésre álló tesztelési kategória megjelenítésére használható.

- A paraméterek **közé tartozik** és **figyelmen kívül hagyható** a tesztelési kategóriák belefoglalása vagy kizárása is. Az argumentumokkal használandó információkkal kapcsolatos további információkért tekintse meg a következő szakaszt.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- A bérlői virtuális gép üzembe helyezése egy felhőalapú forgatókönyv-teszt részeként történik. A **DoNotDeployTenantVm** használatával letilthatja ezt.

- A **ServiceAdminCredential** paramétert kell megadnia a Felhőbeli forgatókönyvek teszteléséhez a [használati példák](azure-stack-diagnostic-test.md#use-case-examples) című szakaszban leírtak szerint.

- A **BackupSharePath** és a **BackupShareCredential** az infrastruktúra biztonsági mentési beállításainak tesztelésekor használatosak, ahogy azt a [használati példák](azure-stack-diagnostic-test.md#use-case-examples) szakaszban is látható.

- A **DetailedResults** az egyes tesztekhez tartozó Pass/Fail/Warning információ beszerzésére, valamint a teljes futtatásra is használható. Ha nincs megadva, a **test-AzureStack** visszaadja **$true** , ha nincsenek hibák, és **$false** , ha hibák történtek.
- A **TimeoutSeconds** egy adott idő megadására is használható az egyes csoportok befejezéséhez.

- Az ellenőrző eszköz a gyakori PowerShell-paramétereket is támogatja: Részletes, hibakeresés, ErrorAction, ErrorVariable, WarningAction, WarningVariable, kiegyenlítő, PipelineVariable és túlváltozó. További információ: [About Common Parameters](https://go.microsoft.com/fwlink/?LinkID=113216).  

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

A Felhőbeli rendszergazda felhasználónevet az UPN formátumba kell írni: serviceadmin@contoso.onmicrosoft.com(Azure AD). Ha a rendszer kéri, írja be a jelszót a Felhőbeli rendszergazdai fiókba.

### <a name="groups"></a>Csoportok

A kezelői élmény javítása érdekében a **Group** paraméter engedélyezve lett több tesztelési kategória egyidejű futtatásához. Jelenleg 3 csoport van definiálva: **Alapértelmezett**, **UpdateReadiness** és **SecretRotationReadiness**.

- **Alapértelmezett**: A **teszt-AzureStack**normál futtatásának számít. Alapértelmezés szerint ez a csoport akkor fut, ha nincs más csoport kiválasztva.
- **UpdateReadiness**: Ellenőrizze, hogy a bélyegző frissíthető-e. Ha a **UpdateReadiness** csoport fut, a figyelmeztetések hibákként jelennek meg a konzol kimenetében, és blokkoló kell tekinteni őket a frissítéshez. A következő kategóriák a **UpdateReadiness** csoport részét képezik:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStoreSummary**

- **SecretRotationReadiness**: Ellenőrizze, hogy a bélyegző olyan-e, amelyben a titkos elforgatás futtatható. A **SecretRotationReadiness** csoport futtatásakor a figyelmeztetések hibákként jelennek meg a konzol kimenetében, és a titkos elforgatáshoz blokkoló kell tekinteni őket. A következő kategóriák a SecretRotationReadiness csoport részét képezik:

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

Ha azonban a Azure Stack 1811-es verziót futtat, akkor a következő PowerShell-parancsokkal futtassa a **test-AzureStack**:

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



## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni Azure Stack diagnosztikai eszközökről és a naplózási problémákról, tekintse meg a [Azure stack diagnosztikai eszközök](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)című témakört.

A hibaelhárítással kapcsolatos további tudnivalókért tekintse meg a [Microsoft Azure stack hibaelhárítást](azure-stack-troubleshooting.md)ismertető témakört.
