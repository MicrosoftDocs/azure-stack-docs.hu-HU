---
title: Frissítések figyelése Azure Stack központban a privilegizált végpont használatával
description: Megtudhatja, hogyan használhatja a Kiemelt végpontot a Azure Stack hub integrált rendszerek frissítési állapotának figyelésére.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 943f391d709f772ec3ed5aa0c99bd738f5de679a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "78368124"
---
# <a name="monitor-updates-in-azure-stack-hub-using-the-privileged-endpoint"></a>Frissítések figyelése Azure Stack központban a privilegizált végpont használatával

Az emelt [szintű végpont](azure-stack-privileged-endpoint.md) használatával figyelheti az Azure stack hub frissítési futtatásának állapotát. A rendszerjogosultságú végpontot is használhatja a sikertelen frissítés futtatásának folytatásához az utolsó sikeres lépés után, ha az Azure Stack hub-portál elérhetetlenné válik. Az Azure Stack hub portál használata ajánlott módszer a frissítések Azure Stack hub-ban való kezeléséhez.

A következő új PowerShell-parancsmagok szerepelnek az Update Managementhez Azure Stack hub integrált rendszerek 1710-es frissítésében.

| Parancsmag  | Leírás  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | A jelenleg futó, befejezett vagy sikertelen frissítés állapotát adja vissza. A frissítési művelet magas szintű állapotát és egy olyan XML-dokumentumot biztosít, amely az aktuális lépést és a megfelelő állapotot is leírja. |
| `Resume-AzureStackUpdate` | Egy sikertelen frissítés folytatása azon a ponton, ahol a művelet sikertelen volt. Bizonyos esetekben előfordulhat, hogy a frissítés folytatása előtt el kell végeznie a kockázatcsökkentő lépéseket.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Ellenőrizze, hogy elérhetők-e a parancsmagok
Mivel a parancsmagok a Azure Stack hub 1710-es frissítési csomagjához tartoznak, a 1710 frissítési folyamatnak egy bizonyos pontra kell jutnia ahhoz, hogy elérhető legyen a figyelési funkció. A parancsmagok jellemzően akkor érhetők el, ha a felügyeleti portálon lévő állapot azt jelzi, hogy az 1710-es frissítés a **Storage-gazdagépek újraindítása** lépéssel történik. A parancsmag frissítése a lépés végrehajtása során következik be **: a 2,6-es lépés – a PrivilegedEndpoint engedélyezési**listájának frissítése.

Azt is meghatározhatja, hogy a parancsmagok programozott módon elérhetők-e a parancsok a Kiemelt végpontról történő lekérdezésével. A lekérdezés végrehajtásához futtassa a következő parancsokat a hardver életciklus-gazdagépén vagy egy emelt szintű hozzáférési munkaállomáson. Győződjön meg arról is, hogy a Kiemelt végpont megbízható gazdagép. További információ: [a privilegizált végpont elérésének](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)1. lépése.

1. Hozzon létre egy PowerShell-munkamenetet az Azure Stack hub-környezetben található bármelyik ERCS virtuális gépen (*előtag*: ERCS01, *prefix*-ERCS02 vagy *előtag*-ERCS03). Cserélje le az *előtagot* a környezetre jellemző virtuálisgép-előtagi karakterlánccal.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Amikor a rendszer kéri a hitelesítő adatokat &lt;, használja az *Azure stack hub tartományi*&gt;\cloudadmin-fiókot vagy egy olyan fiókot, amely tagja a CloudAdmins csoportnak. A CloudAdmin fiók esetében adja meg ugyanazt a jelszót, amelyet a Azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

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
   Például:
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

A következő parancsok futtatásával hozzon létre egy PowerShell-munkamenetet a Azure Stack hub-környezetben található egyik ERCS virtuális gépen (*előtag*: ERCS01, *előtag*-ERCS02 vagy *előtag*-ERCS03), és rendeljen hozzá egy munkamenet-változót.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Amikor a rendszer kéri a hitelesítő adatokat &lt;, használja az *Azure stack hub tartományi*&gt;\cloudadmin-fiókot vagy egy olyan fiókot, amely tagja a CloudAdmins csoportnak. A CloudAdmin fiók esetében adja meg ugyanazt a jelszót, amelyet a Azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

### <a name="get-high-level-status-of-the-current-update-run"></a>Az aktuális frissítési Futtatás magas szintű állapotának beolvasása

Az aktuális frissítési kísérlet magas szintű állapotának lekéréséhez futtassa a következő parancsokat:

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

A lehetséges értékek a következők:

- Fut
- Befejezve
- Sikertelen 
- Megszakítva

Ezeket a parancsokat többször is futtathatja a legfrissebb állapot megjelenítéséhez. Nem kell újból létrehoznia egy kapcsolatot az ismételt vizsgálathoz.

### <a name="get-the-full-update-run-status-with-details"></a>A teljes frissítés futtatási állapotának beolvasása részletekkel

A teljes frissítés futtatásának összegzése XML-karakterláncként szerezhető be. Megírhatja a karakterláncot egy fájlba a vizsgálathoz, vagy átalakíthatja egy XML-dokumentumba, és a PowerShell használatával elemezheti azt. A következő parancs elemzi az XML-t, hogy beolvassa a jelenleg futó lépések hierarchikus listáját:

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

A Kiemelt végpont az Azure Stack hub-környezetben található összes ERCS virtuális gépen elérhető. Mivel a kapcsolódás nem egy magasan elérhető végpontra történik, időnként megszakítások, figyelmeztetés vagy hibaüzenetek merülhetnek fel. Ezek az üzenetek azt jelezhetik, hogy a munkamenet le lett választva, vagy hiba történt az ECE szolgáltatással való kommunikáció során. Ez várt működés. Próbálja megismételni a műveletet néhány perc múlva, vagy hozzon létre egy új emelt szintű végponti munkamenetet az egyik másik ERCS virtuális gépen.

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése Azure Stack központban](azure-stack-updates.md)


