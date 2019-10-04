---
title: Frissítések figyelése Azure Stack a privilegizált végpont használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Kiemelt végpontot a Azure Stack integrált rendszerek frissítési állapotának figyelésére.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 7cfbba830b91d5dba8935cce20a2cdc0e65e49de
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909161"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Frissítések figyelése Azure Stack a privilegizált végpont használatával

*Vonatkozik: Integrált rendszerek Azure Stack*

Az emelt [szintű végpont](azure-stack-privileged-endpoint.md) segítségével figyelheti egy Azure stack frissítési futtatásának állapotát, és folytathatja a sikertelen frissítés futtatását az utolsó sikeres lépéssel, ha a Azure stack-portál elérhetetlenné válik.  A Azure Stack portál használata ajánlott módszer a frissítések Azure Stack-ben való kezeléséhez.

Az Update Management következő új PowerShell-parancsmagjai a 1710-es frissítés részét képezik Azure Stack integrált rendszerek esetében.

| A parancsmag  | Leírás  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | A jelenleg futó, befejezett vagy sikertelen frissítés állapotát adja vissza. Megadja a frissítési művelet magas szintű állapotát, valamint egy XML-dokumentumot, amely az aktuális lépést és a megfelelő állapotot is leírja. |
| `Resume-AzureStackUpdate` | Egy sikertelen frissítés folytatása azon a ponton, ahol a művelet sikertelen volt. Bizonyos esetekben előfordulhat, hogy a frissítés folytatása előtt el kell végeznie a kockázatcsökkentő lépéseket.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Ellenőrizze, hogy elérhetők-e a parancsmagok
Mivel a parancsmagok a Azure Stack 1710 frissítési csomagja újdonságai, a 1710 frissítési folyamatnak egy bizonyos ponthoz kell jutnia ahhoz, hogy elérhető legyen a figyelési funkció. A parancsmagok jellemzően akkor érhetők el, ha a felügyeleti portálon lévő állapot azt jelzi, hogy az 1710-es frissítés a **Storage** -gazdagépek újraindítása lépéssel történik. Pontosabban a parancsmag frissítése a lépés **során következik be: Az 2,6-es lépés futtatása –** PrivilegedEndpoint-engedélyezési lista frissítése.

Azt is meghatározhatja, hogy a parancsmagok programozott módon elérhetők-e a parancsok a Kiemelt végpontról történő lekérdezésével. Ehhez futtassa a következő parancsokat a hardver életciklus-gazdagépén vagy egy emelt szintű hozzáférési munkaállomáson. Győződjön meg arról is, hogy a Kiemelt végpont megbízható gazdagép. További információ: [a privilegizált végpont elérésének](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)1. lépése. 

1. Hozzon létre egy PowerShell-munkamenetet a Azure Stack-környezetben található egyik ERCS virtuális gépen (*előtag*: ERCS01, *prefix*-ERCS02 vagy *előtag*-ERCS03). Cserélje le az előtagot a környezetre jellemző virtuálisgép-előtagi karakterlánccal.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Ha a rendszer hitelesítő adatokat kér, &lt;használja a *Azure stack tartományi*&gt;\cloudadmin fiókot, vagy egy olyan fiókot, amely tagja a CloudAdmins csoportnak. A CloudAdmin fiók esetében adja meg ugyanazt a jelszót, amelyet a Azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

2. A privilegizált végponton elérhető parancsok teljes listájának beolvasása. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Annak megállapítása, hogy a Kiemelt végpont frissítve lett-e.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. A Microsoft. AzureStack. UpdateManagement modulhoz tartozó parancsok listázása.

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

## <a name="use-the-update-management-cmdlets"></a>Az Update Management-parancsmagok használata

> [!NOTE]
> Futtassa az alábbi parancsokat a hardver életciklus-gazdagépről vagy egy emelt szintű hozzáférési munkaállomásról. Győződjön meg arról is, hogy a Kiemelt végpont megbízható gazdagép. További információ: [a privilegizált végpont elérésének](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)1. lépése.

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Kapcsolódás a privilegizált végponthoz és a munkamenet-változó társítása

Futtassa a következő parancsokat egy PowerShell-munkamenet létrehozásához a Azure Stack-környezetben található egyik ERCS virtuális gépen (*előtag*: ERCS01, *előtag*-ERCS02 vagy *előtag*-ERCS03), és rendeljen hozzá egy munkamenet-változót.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Ha a rendszer hitelesítő adatokat kér, &lt;használja a *Azure stack tartományi*&gt;\cloudadmin fiókot, vagy egy olyan fiókot, amely tagja a CloudAdmins csoportnak. A CloudAdmin fiók esetében adja meg ugyanazt a jelszót, amelyet a Azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

### <a name="get-high-level-status-of-the-current-update-run"></a>Az aktuális frissítési Futtatás magas szintű állapotának beolvasása 

Az aktuális frissítési kísérlet magas szintű állapotának lekéréséhez futtassa a következő parancsokat: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

A lehetséges értékek:

- Fut
- Befejeződött
- Meghiúsult 
- Megszakítva

Ezeket a parancsokat többször is futtathatja a legfrissebb állapot megjelenítéséhez. Nem kell újból létrehoznia egy kapcsolatot az ismételt vizsgálathoz.

### <a name="get-the-full-update-run-status-with-details"></a>A teljes frissítés futtatási állapotának beolvasása részletekkel 

A teljes frissítés futtatásának összegzése XML-karakterláncként szerezhető be. Megírhatja a karakterláncot egy fájlba a vizsgálathoz, vagy átalakíthatja egy XML-dokumentumba, és a PowerShell használatával elemezheti azt. A következő parancs elemzi az XML-t, hogy beolvassa a jelenleg futó lépések hierarchikus listáját.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

A következő példában a legfelső szintű lépés (a felhő frissítése) egy gyermek tervet tartalmaz a tároló-gazdagépek frissítéséhez és újraindításához. Azt mutatja, hogy az újraindítási tárolók terve frissíti a Blob Storage szolgáltatást az egyik gazdagépen.

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

### <a name="resume-a-failed-update-operation"></a>Sikertelen frissítési művelet folytatása

Ha a frissítés sikertelen, akkor folytathatja a frissítés futtatását, ahol a szolgáltatás abbahagyta.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Hibaelhárítás

A Kiemelt végpont a Azure Stack környezetben található összes ERCS virtuális gépen elérhető. Mivel a kapcsolatok nem egy magasan elérhető végpontra mutatnak, időnként megszakítások, figyelmeztetés vagy hibaüzenetek merülhetnek fel. Ezek az üzenetek azt jelezhetik, hogy a munkamenet le lett választva, vagy hiba történt az ECE szolgáltatással való kommunikáció során. Ez várt működés. Próbálja megismételni a műveletet néhány perc múlva, vagy hozzon létre egy új emelt szintű végponti munkamenetet az egyik másik ERCS virtuális gépen. 

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése Azure Stack](azure-stack-updates.md) 


