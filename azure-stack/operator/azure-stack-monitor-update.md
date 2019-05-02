---
title: Frissítések az Azure Stack használatával a privilegizált végpont monitorozása |} A Microsoft Docs
description: Ismerje meg, hogyan használható a kiemelt végponthoz Azure Stack integrált rendszerek esetében a frissítés állapotának figyelése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 3ee71fe48b73c6edd2b0fcb8b0dc83cdf5d77910
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985077"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Frissítések az Azure Stack használatával a privilegizált végpont monitorozása

*Vonatkozik: Az Azure Stack integrált rendszerek*

Használhatja a [kiemelt végponthoz](azure-stack-privileged-endpoint.md) előrehaladtát, az Azure Stackkel való frissítés futtassa, és az utolsó sikeres lépésből futtassa sikertelen frissítés folytatásához kell az Azure Stack portálon elérhetetlenné válik.  Az Azure Stack portal használata az ajánlott módszer az Azure Stack frissítéseinek kezelésére.

A következő új PowerShell-parancsmagok az update management megtalálhatók az Azure Stack integrált rendszerek 1710-es frissítés.

| Parancsmag  | Leírás  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | A jelenleg futó, befejezett, vagy a sikertelen frissítés állapotát adja vissza. A magas szintű állapotának a frissítési művelet, és a egy XML-dokumentum, amely leírja az aktuális lépés és a megfelelő állapotot is tartalmazza. |
| `Resume-AzureStackUpdate` | Folytatja a sikertelen frissítés esetén a pontnál, amelyen nem sikerült. Bizonyos esetekben előfordulhat, a frissítés folytatása előtt kárenyhítési lépések végrehajtásához.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Ellenőrizze, hogy a parancsmagok érhetők el
A parancsmagok nem ismeri az Azure Stack az 1710-es frissítési csomag, mert az 1710-es frissítési folyamat van szüksége egy bizonyos ponton elérése előtt a figyelési funkció érhető el. A parancsmagok jellemzően érhető el, ha a felügyeleti portálon az állapot azt jelzi, hogy az 1710-es frissítés a **indítsa újra a Storage-gazdagépek** . lépés. Pontosabban, a parancsmag frissítése során történik **. lépés: 2.6 – a frissítés PrivilegedEndpoint engedélyezési lista lépés futtatása**.

Azt is meghatározhatja, hogy a parancsmagok elérhetők-e programozott módon a parancslistából, a kiemelt jogosultságú végpontról lekérdezésével. Ehhez futtassa az alábbi parancsokat a hardver életciklus gazdagépen vagy egy emelt szintű hozzáférési szintű munkaállomásokat. Ügyeljen arra, hogy a kiemelt jogosultságú végpont nem megbízható gazdagép. További információkért lásd: 1. lépés a [az emelt szintű végpontot](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Hozzon létre egy PowerShell-munkamenetet a ERCS virtuális gépek az Azure Stack-környezet valamelyik (*előtag*-ERCS01, *előtag*-ERCS02, vagy *előtag*-ERCS03). Cserélje le *előtag* az a virtuális gép címelőtag-karakterlánca, amely adott környezetre jellemző.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Amikor a rendszer kéri a hitelesítő adatokat, használja a &lt; *Azure Stack-tartományhoz*&gt;\cloudadmin fiókot vagy egy fiókot, amely a CloudAdmins csoport tagja. A CloudAdmin fiókot adja meg ugyanazt a jelszót a Azurestack tartományi rendszergazdai fiók a telepítés során megadott.

2. A kiemelt jogosultságú végpont elérhető parancsok teljes listájának beolvasása. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Határozza meg, ha a kiemelt jogosultságú végpont frissítve lett.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. A Microsoft.AzureStack.UpdateManagement modulhoz parancsok listáját.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Példa:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Az update management-parancsmagok használata

> [!NOTE]
> A hardver életciklus gazdagépen vagy egy Privileged Access Workstation, futtassa a következő parancsokat. Ügyeljen arra, hogy a kiemelt jogosultságú végpont nem megbízható gazdagép. További információkért lásd: 1. lépés a [az emelt szintű végpontot](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>A kiemelt végponthoz csatlakozik, és rendelje hozzá a munkamenet-változó

Futtassa az alábbi parancsokat a PowerShell-munkamenetet létrehozni bármely ERCS virtuális gépe az Azure Stack környezettel (*előtag*-ERCS01, *előtag*-ERCS02, vagy *előtag*-ERCS03), és a egy munkamenet-változó hozzárendelni.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Amikor a rendszer kéri a hitelesítő adatokat, használja a &lt; *Azure Stack-tartományhoz*&gt;\cloudadmin fiókot vagy egy fiókot, amely a CloudAdmins csoport tagja. A CloudAdmin fiókot adja meg ugyanazt a jelszót a Azurestack tartományi rendszergazdai fiók a telepítés során megadott.

### <a name="get-high-level-status-of-the-current-update-run"></a>A jelenlegi frissítés Futtatás magas szintű állapotának beolvasása 

A jelenlegi frissítés Futtatás magas szintű állapotának lekéréséhez futtassa a következő parancsokat: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

A lehetséges értékek:

- Fut
- Befejezve
- Meghiúsult 
- Megszakítva

Ezeket a parancsokat ismételten a legfrissebb állapotának megtekintéséhez futtathatja. Ne kelljen újra kapcsolatot létesíteni.

### <a name="get-the-full-update-run-status-with-details"></a>A teljes frissítés futtatási állapota a részletek beolvasása 

A teljes frissítési menetet összefoglaló XML-karakterláncként kérheti le. Vizsgálatához, fájlba írása a karakterlánc vagy átalakíthatja egy XML-dokumentum, és elemezni, a PowerShell használatával. A következő parancs elemzi az XML-fájl aktuálisan futó lépéseket hierarchikus listáját.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

A következő példában a legfelső szintű lépés (felhő frissítése) tartalmaz, egy gyermek terv frissítéséhez, és indítsa újra a storage-gazdagépeket. Ez azt mutatja, hogy a tárolási gazdagépek indítsa újra a tervet a gazdagépet a Blob Storage szolgáltatás frissítése folyamatban van.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="resume-a-failed-update-operation"></a>A sikertelen frissítés esetén a művelet folytatása

Ha a frissítés sikertelen, folytathatja a frissítés futtatása, ahol abbahagyta.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Hibaelhárítás

A kiemelt végponthoz ERCS virtuális gépekre az Azure Stack környezetben érhető el. Mivel a kapcsolat nem jön létre egy magas rendelkezésre állású végpontra, alkalmi megszakítások, figyelmeztető vagy hibaüzeneteket tapasztalhat. Ezek az üzenetek azt jelezheti, hogy a munkamenet meg lett szakítva, illetve, hogy hiba történt a Dokumentumokat szolgáltatással való kommunikáció. Ez várt működés. Próbálja megismételni a műveletet néhány perc múlva, vagy a kiemelt végponthoz új munkamenetet létrehozni egy másik ERCS virtuális gépet. 

## <a name="next-steps"></a>További lépések

- [Az Azure Stack frissítéseinek felügyelete](azure-stack-updates.md) 


