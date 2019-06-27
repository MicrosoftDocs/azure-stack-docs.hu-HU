---
title: Az Azure Stack érvényesítési eszközzel |} A Microsoft Docs
description: Tudnivalók az Azure Stackben diagnosztikai naplófájlok összegyűjtése.
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
ms.openlocfilehash: 25280d1f5465776aedf2e3e0dfd84118e866a944
ms.sourcegitcommit: c9d11be7d27c73797bdf279d4fcabb7a22451541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2019
ms.locfileid: "67397277"
---
# <a name="validate-azure-stack-system-state"></a>Azure Stack rendszerállapotának ellenőrzése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack operátorait szerint képes arra, hogy meghatározza az egészségügyi és állapotát, a rendszer igény szerinti elengedhetetlen. Az Azure Stack érvényesítési eszköz (**Test-AzureStack**), amely lehetővé teszi egy PowerShell-parancsmag futtatása tesztek egy sorozatát hibák azonosítására, ha jelen van a rendszeren. Általában meg kell adnia az eszköz futtatásához a [kiemelt végpontját (EGP)](azure-stack-privileged-endpoint.md) amikor, forduljon a Microsoft támogatási (CSS) egy problémához. Rendszerszintű állapotát és állapotadatokat csak olyan mélységben CSS is gyűjthet és a részletes naplók elemzése, és összpontosítson a terület, ahol a hiba történt, és együttműködnek Önnel a probléma megoldásához.

## <a name="running-the-validation-tool-and-accessing-results"></a>A fürtérvényesítési eszköz futtatása és az eredmények elérése

Ahogy korábban is hangsúlyoztuk, a fürtérvényesítési eszköz futtatása az EGP-n keresztül. Minden teszt adja vissza egy **PASS/sikertelen** állapota a PowerShell-ablakban. Ezenkívül létrejön a HTML részletes jelentés ami később elérhetők során [naplót](azure-stack-diagnostics.md). Itt látható a teljes körű érvényesítési folyamat tesztelési röviden ismerteti: 

1. Hozzáférés a kiemelt végponthoz (EGP). A következő parancsokat egy EGP-munkamenetet létrehozni:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > -ComputerName AzS-ERCS01 használata az EGP-ASDK gazdaszámítógépen eléréséhez.

2. Miután az EGP, futtassa: 

   ```powershell
   Test-AzureStack
   ```

   Tekintse meg a [paraméter szempontok](azure-stack-diagnostic-test.md#parameter-considerations) és [megkülönbözteti a kis példákat](azure-stack-diagnostic-test.md#use-case-examples) szakaszokban további információkat.

3. Ha bármelyik teszteli, hogy a jelentés **sikertelen**futtassa `Get-AzureStackLog`. Az integrált rendszereken útmutatásért lásd: [Get-AzureStackLog futtathatók az Azure Stack integrált rendszerek](azure-stack-diagnostics.md#to-run-get-azurestacklog-on-azure-stack-integrated-systems), vagy nézze meg a ASDK [Get-AzureStackLog futtatása az Azure Stack Development Kit (ASDK) rendszeren](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

   A parancsmag a Test-AzureStack által létrehozott naplók gyűjt. Nem kell gyűjtését, vagy forduljon a CSS, ha a jelentés tesztek **WARN**.

4. Ha erre a fürtérvényesítési eszköz futtatása a CSS által, a CSS képviselő kérni fogja a naplókat a probléma elhárításához összegyűjtött.

## <a name="tests-available"></a>Rendelkezésre álló tesztek

A fürtérvényesítési eszköz lehetővé teszi a rendszerszintű tesztek egy sorozatát futtatná, és alapszintű cloud forgatókönyvek, amelyek egy elemzést az aktuális állapotát, és megalkotott problémákat a rendszerben.

### <a name="cloud-infrastructure-tests"></a>Felhőbeli infrastruktúra-tesztek

Kis hatású tesztekhez a infrastruktúra szintjén működik, és különféle rendszer összetevőit és funkciók információkat. Tesztek jelenleg a következő kategóriákba vannak csoportosítva:

| Vizsgált kategória                                        | -Argumentumot belefoglalása és - figyelmen kívül hagyása |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack ACS Summary                              | AzsAcsSummary                     |
| Az Azure Stack az Active Directory összegzése                 | AzsAdSummary                      |
| Az Azure Stack riasztási összegzése                            | AzsAlertSummary                   |
| Azure Stack Application Crash Summary                | AzsApplicationCrashSummary        |
| Az Azure Stack biztonsági másolatok megosztásáról kisegítő összegzése       | AzsBackupShareAccessibility       |
| Az Azure Stack BMC összegzése                              | AzsStampBMCSummary                |
| Azure Stack Cloud Hosting Infrastructure Summary     | AzsHostingInfraSummary            |
| Azure Stack Cloud Hosting Infrastructure Utilization | AzsHostingInfraUtilization        |
| Azure Stack Control Plane Summary                    | AzsControlPlane                   |
| Az Azure Stack Defender összegzése                         | AzsDefenderSummary                |
| Az Azure Stack futtató infrastruktúra belső vezérlőprogram összegzése  | AzsHostingInfraFWSummary          |
| Az Azure Stack-infrastruktúra-kapacitás                  | AzsInfraCapacity                  |
| Az Azure Stack-infrastruktúra teljesítmény               | AzsInfraPerformance               |
| Az Azure Stack infrastruktúra-szerepkör összefoglalása              | AzsInfraRoleSummary               |
| Az Azure Stack-portál és API-összefoglalót                   | AzsPortalAPISummary               |
| Az Azure Stack skálázási egység Virtuálisgép-eseményeket                     | AzsScaleUnitEvents                |
| Az Azure Stack skálázási egység Virtuálisgép-erőforrások                  | AzsScaleUnitResources             |
| Azure Stack Scenarios                                | AzsScenarios                      |
| Az Azure Stack SDN-alapú érvényesítés összegzése                   | AzsSDNValidation                  |
| Azure Stack Service Fabric Role Summary              | AzsSFRoleSummary                  |
| Az Azure Stack tárolási Adatsík                       | AzsStorageDataPlane               |
| Az Azure Stack tárolási szolgáltatások – összefoglalás                 | AzsStorageSvcsSummary             |
| Azure Stack SQL Store Summary                        | AzsStoreSummary                   |
| Az Azure Stack frissítés összegzése                           | AzsInfraUpdateSummary             |
| Az Azure Stack virtuális gép elhelyezése összegzése                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Felhőbeli forgatókönyv tesztek

A fenti infrastruktúra tesztek mellett funkciók ellenőrzéséhez infrastruktúra összetevői között felhőalapú forgatókönyv tesztek futtatására is rendelkezik. Ezek a tesztek futtatását, mert azok olyan erőforrások üzembe helyezésének felhőbeli rendszergazdai hitelesítő adatok szükségesek.

> [!NOTE]
> Jelenleg nem futtatható, az Active Directory összevonási szolgáltatásokban (AD FS) hitelesítő adatok használatával felhőalapú forgatókönyv teszteket. 

A fürtérvényesítési eszköz kell megvizsgálni, a következő felhőt helyzetekben:
- Erőforrás-csoport létrehozása   
- Csomag létrehozása              
- Ajánlat létrehozása            
- Tárfiók létrehozása   
- Virtuális gép létrehozása 
- A BLOB storage-művelet   
- Üzenetsor-tárolási művelet  
- TABLE storage művelet  

## <a name="parameter-considerations"></a>A paraméter kapcsolatos szempontok

- A paraméter **lista** használható összes elérhető vizsgálati kategóriák megjelenítéséhez.

- A paraméterek **Belefoglalás** és **figyelmen kívül hagyása** vagy kizárja a teszt kategóriák használható. A következő részben további információt az információkat a következő argumentumokkal használható.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Egy bérlő virtuális gép üzemel egy része a felhőbeli eset teszteli. Használhat **DoNotDeployTenantVm** , tiltsa le a szolgáltatást.

- Meg kell adnia a **ServiceAdminCredential** felhőalapú forgatókönyv tesztek futtatásához a paraméter a [megkülönbözteti a kis példákat](azure-stack-diagnostic-test.md#use-case-examples) szakaszban.

- **BackupSharePath** és **BackupShareCredential** használt történő tesztelésekor a biztonsági mentési infrastruktúra-beállításokat, ahogyan az a [megkülönbözteti a kis példákat](azure-stack-diagnostic-test.md#use-case-examples) szakaszban.

- **DetailedResults** pass/sikertelen/figyelmeztető információkat beolvasni minden teszt, valamint a teljes Futtatás is használható. Ha nincs megadva, **Test-AzureStack** adja vissza **$true** Ha nincsenek hibák, és **$false** van-e hibák.
- **TimeoutSeconds** beállítása egy adott időpont az egyes csoportokhoz végrehajtásához használható.

- A fürtérvényesítési eszköz is a PowerShell következő általános paramétereket támogatja: Részletes, hibakeresési, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable, és OutVariable. További információkért lásd: [általános paraméterek](https://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Kis példákat

### <a name="run-validation-without-cloud-scenarios"></a>Ellenőrzés nélkül felhőbeli forgatókönyvek futtatása

Futtassa az ellenőrzési eszközt nélkül a **ServiceAdminCredential** paraméter futó felhőalapú forgatókönyv tesztek kihagyása: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Futtassa az ellenőrzést a felhőbeli forgatókönyvek

A fürtérvényesítési eszköz való ellátása a **ServiceAdminCredentials** paraméter alapértelmezés szerint a felhőbeli eset tesztek fut: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Ha szeretné futtatni a csak felhőbeli forgatókönyvek a többi a tesztek futtatása nélkül, használhatja a **Belefoglalás** paraméter ehhez: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Felhő rendszergazdájának felhasználóneve az egyszerű felhasználónév formátumban kell megadni: serviceadmin@contoso.onmicrosoft.com (Az azure AD). Amikor a rendszer kéri, írja be a felhőalapú rendszergazdai fiók jelszavát.

### <a name="groups"></a>Csoportok

A kezelői élmény javítása érdekében egy **csoport** paraméter egyidejűleg több teszt kategóriák futtatásához engedélyezve van. Jelenleg nincsenek definiált 3 csoportok: **Alapértelmezett**, **UpdateReadiness** és **SecretRotationReadiness**.

- **Alapértelmezett**: Standard futtatását tekinthető **Test-AzureStack**. Ez a csoport alapértelmezés szerint fut, ha nincsenek más csoportok van kiválasztva.
- **UpdateReadiness**: Ellenőrzi, hogy ha a stamp frissítése is. Ha a **UpdateReadiness** csoport futtatja, figyelmeztetések jelennek meg a konzol kimenete a hibákat, és a frissítés meggátló kell tekinteni. A következő kategóriákban részét képezik a **UpdateReadiness** csoportot:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStoreSummary**

- **SecretRotationReadiness**: Ellenőrzi, hogy a blokk-e egy a melyik titkos Elforgatás is futtathatók. Ha a **SecretRotationReadiness** csoport futtatja, figyelmeztetések jelennek meg a konzol kimenete a hibákat, és a titkos Elforgatás meggátló kell figyelembe venni. A következő kategóriák a SecretRotationReadiness csoport részei:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>Példa gazdagépcsoportra paraméter

Az alábbi példakód lefuttat **Test-AzureStack** rendszer készültségének teszteléséhez frissítés vagy gyorsjavítás használatával telepítése előtt **csoport**. Frissítés vagy gyorsjavítás telepítése előtt el kell indítani **Test-AzureStack** az Azure Stack állapotának ellenőrzéséhez:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Azonban ha az Azure Stack 1811 kiadásnál régebbi verzió fut, használja a következő PowerShell-parancsok futtatásához **Test-AzureStack**:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Biztonsági mentési infrastruktúra-beállítások tesztelésére a fürtérvényesítési eszköz futtatása

*Mielőtt* konfigurálja az infrastruktúra biztonsági mentését, tesztelje a biztonsági mentési megosztás elérési útja és a hitelesítő adatok használatával a **AzsBackupShareAccessibility** teszteléséhez: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*Miután* biztonsági mentés konfigurálása, futtatása **AzsBackupShareAccessibility** ellenőrzése a megosztás érhető el a ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

A konfigurált biztonsági másolatok megosztásáról az új hitelesítő adatok ellenőrzéséhez futtassa: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>További lépések

Azure Stack-diagnosztikai eszközök és a probléma naplózása kapcsolatos további információkért lásd: [Azure Stack-diagnosztikai eszközök](azure-stack-diagnostics.md).

Hibaelhárítással kapcsolatos további tudnivalókért lásd: [hibáinak elhárítása a Microsoft Azure Stack](azure-stack-troubleshooting.md).
