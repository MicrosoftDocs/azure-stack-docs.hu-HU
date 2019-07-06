---
title: A kiemelt jogosultságú végpont használata az Azure Stackben |} A Microsoft Docs
description: Bemutatja, hogyan használhatja a kiemelt végponthoz (EGP) az Azure Stackben (az Azure Stack-operátorokról).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: c9e796a4ece453c3cd74bbf9a2fb6996757a0b4e
ms.sourcegitcommit: 44f1bf6e0bfa85ee14819cad27c9b1de65d375df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596083"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>A kiemelt jogosultságú végpont használata az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Azure Stack-operátorként a legtöbb napi rendszerességű felügyeleti feladat ellátásához a felügyeleti portált, a PowerShellt vagy az Azure Resource Manager API-kat használhatja. Azonban az egyes kevésbé gyakori műveleteket kell használnia a *kiemelt végponthoz* (EGP). Az EGP egy előre konfigurált távoli PowerShell-konzolt, és éppen elegendő lehetőségekkel segíti elő a szükséges feladat elvégzését. A végpontok [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) elérhetővé a parancsmag csak korlátozott készletével. Az EGP eléréséhez, és meghívja a parancsmagok korlátozott készletét, egy alacsony jogosultsági szintű fiókot használja. Nem rendszergazdai fiókok szükség. A fokozott biztonság érdekében parancsfájlok nem engedélyezett.

Az EGP használhatja például a következő feladatok elvégzéséhez:

- Az alsó szintű feladatok végrehajtásához, például [diagnosztikai naplók gyűjtésére](azure-stack-diagnostics.md#log-collection-tool).
- Integrált rendszerek, például a tartománynévrendszer (DNS) a továbbítók beállítása a Microsoft Graph-integráció, az Active Directory összevonási szolgáltatások (AD FS) integrációt, üzembe helyezés után több adatközpont üzembe helyezés utáni integrációs feladatok végrehajtásához tanúsítvány rotációja, és így tovább.
- A részletes hibakeresés integrált rendszer ideiglenes, magas szintű hozzáférést szerezni a támogatási dolgozhat.

Az EGP naplózza minden művelet (és a megfelelő kimeneti), amelyeket elvégezhet a PowerShell-munkamenetben. Ez biztosítja, teljes átláthatóságot és a teljes naplózási műveletek. Ezek a naplófájlok a jövőbeni ellenőrzésekhez őrizheti meg.

> [!NOTE]
> Az az Azure Stack Development Kit (ASDK), futtathatja az EGP elérhető parancsai közül néhányat, közvetlenül a PowerShell-munkamenetet a development kit gazdagépen. Azonban érdemes néhány művelet használatával az EGP, például a naplógyűjtés, teszteléséhez, mivel ez az egyetlen módszer érhető el, hogy bizonyos műveletek elvégzéséhez integrált rendszerek környezetben.

## <a name="access-the-privileged-endpoint"></a>Hozzáférés a kiemelt végponthoz

Az EGP keresztül egy távoli PowerShell-munkamenetet a virtuális gépen, amelyen az EGP érhetők el. A ASDK, a virtuális gép neve **AzS-ERCS01**. Integrált rendszer használja, hogy vannak-e három példányban EGP, minden futó virtuális gépen belüli (*előtag*-ERCS01, *előtag*-ERCS02, vagy *előtag*- ERCS03) rugalmasság a különböző gazdagépeken. 

Mielőtt elkezdené integrált rendszer ezt az eljárást, győződjön meg arról, elérheti az EGP IP-címe vagy DNS-en keresztül. Után a kezdeti üzembe helyezhető Azure Stacket érheti el az EGP csak az IP-cím, mert a DNS-integráció van még nem állította be. OEM hardvergyártójához biztosít Önnek egy JSON-fájlt **AzureStackStampDeploymentInfo** , amely tartalmazza a EGP IP-címeket.


> [!NOTE]
> Biztonsági okokból szükséges, hogy csatlakozni az EGP csak egy megerősített virtuális gép futó, a hardver életciklus gazdagép felett, vagy egy dedikált, biztonságos számítógépre, mint például egy [Privileged Access Workstation](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). Az EGP való kapcsolódáshoz használandó és nem kell az eredeti konfigurációt a hardver életciklus-gazdagép nem lehet módosítani az eredeti konfigurációnak, beleértve az új szoftverek telepítése.

1. A megbízhatósági kapcsolatot hoz létre.

    - Az integrált rendszereken, futtassa a következő parancsot egy rendszergazda jogú Windows PowerShell-munkamenetben az EGP a megerősített virtuális gépen, a hardver életciklus gazdagép-vagy a Privileged Access Workstation megbízható gazdagépként hozzáadni.

      ```powershell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Ha a ASDK futtatja, jelentkezzen be a development kit gazdagépre.

2. A megerősített futó virtuális géphez a hardver életciklus-gazdagép vagy a Privileged Access Workstation nyisson meg egy Windows PowerShell-munkamenetet. Futtassa a következő parancsok futtatásával hozzon létre egy távoli munkamenetet a virtuális gépen, amelyen az EGP:
 
   - Az integrált rendszereken:
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     A `ComputerName` paraméter lehet, az IP-cím vagy egy virtuális gépet, amelyen az EGP DNS-nevét. 
   - Ha a ASDK futtatja:
     
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Amikor a rendszer kéri, használja a következő hitelesítő adatok:

     - **Felhasználónév**: Adja meg a CloudAdmin fiók formátumban  **&lt; *Azure Stack-tartományhoz*&gt;\cloudadmin**. (ASDK, az a felhasználónév az **azurestack\cloudadmin**.)
     - **Jelszó**: Adja meg ugyanazt a jelszót a Azurestack tartományi rendszergazdai fiók a telepítés során megadott.

     > [!NOTE]
     > Ha Ön nem lehet kapcsolódni a ERCS végpont, próbálja meg első és második szintén egy ERCS virtuális Gépet, amelyhez Ön még nem már csatlakozni próbált, IP-címét a lépést.

3. A csatlakozás után a rendszer kéri változik **[*IP-cím vagy ERCS virtuális gép neve*]: PS >** vagy **[azs-ercs01]: PS >** , attól függően, a környezetben. Futtatás innen `Get-Command` elérhető parancsmagjainak listájának megtekintéséhez.

   Ezek a parancsmagok számos szánt csak integrált rendszer környezetekben (például az Adatközpont-integrációval kapcsolatos parancsmagok). Az a ASDK ellenőrzése a következő parancsmagokat:

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Kilépés-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Mérték-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tippek a kiemelt végponthoz 

Ahogy említettük, az EGP van egy [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) végpont. Egy erős biztonsági réteget biztosít, miközben a JEA-végpont csökkenti az alapszintű PowerShell funkciói, például parancsfájlok vagy lap befejezését. Ha bármilyen típusú parancsprogram-művelet, a művelet sikertelen, és a **ScriptsNotAllowed**. Ez az elvárt működés.

Így például a paraméterek listája egy adott parancsmag, futtassa a következő parancsot:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Másik lehetőségként használhatja a [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) parancsmag a EGP-parancsmagok importálásához az aktuális munkamenet a helyi gépen. Ezzel a módszerrel minden parancsmag és funkció az EGP, mostantól elérhetők a helyi gépén, kiegészítés és, más együtt általában scripting. 

Importálja a EGP-munkamenetet a helyi gépén, tegye a következőket:

1. A megbízhatósági kapcsolatot hoz létre.

    -A egy integrált rendszer, a következő parancsot egy rendszergazda jogú Windows PowerShell-munkamenetben az EGP a megerősített virtuális gépen, a hardver életciklus gazdagép-vagy a Privileged Access Workstation megbízható gazdagépként hozzáadni a.

      ```powershell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - Ha a ASDK futtatja, jelentkezzen be a development kit gazdagépre.

2. A megerősített futó virtuális géphez a hardver életciklus-gazdagép vagy a Privileged Access Workstation nyisson meg egy Windows PowerShell-munkamenetet. Futtassa a következő parancsok futtatásával hozzon létre egy távoli munkamenetet a virtuális gépen, amelyen az EGP:
 
   - Az integrált rendszereken:
     ```powershell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     A `ComputerName` paraméter lehet, az IP-cím vagy egy virtuális gépet, amelyen az EGP DNS-nevét. 
   - Ha a ASDK futtatja:
     
     ```powershell
      $cred = Get-Credential

      $session = New-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Amikor a rendszer kéri, használja a következő hitelesítő adatok:

     - **Felhasználónév**: Adja meg a CloudAdmin fiók formátumban  **&lt; *Azure Stack-tartományhoz*&gt;\cloudadmin**. (ASDK, az a felhasználónév az **azurestack\cloudadmin**.)
     - **Jelszó**: Adja meg ugyanazt a jelszót a Azurestack tartományi rendszergazdai fiók a telepítés során megadott.

3. A EGP munkamenet importálja a helyi gépen
    ```powershell 
        Import-PSSession $session
    ```
4. Most-kiegészítés használata, és hajtsa végre a parancsfájl-kezelési a szokásos módon a helyi PowerShell-munkamenetet a functions és az EGP-parancsmagok a nélkül csökkentése az Azure Stack biztonsági állapotát. Jó munkát!


## <a name="close-the-privileged-endpoint-session"></a>A kiemelt végponthoz munkamenet bezárása

 Ahogy korábban említettük, az EGP naplózza minden művelet (és a megfelelő kimeneti), amelyeket elvégezhet a PowerShell-munkamenetben. Zárja be a munkamenet használatával a `Close-PrivilegedEndpoint` parancsmagot. Ez a parancsmag megfelelően bezárja a végpontot, és egy külső fájlmegosztás megőrzésének adja át a rendszernapló fájljaiban.

A végpont munkamenet lezárásához:

1. Hozzon létre egy külső fájlmegosztás által az EGP elérhető. Fejlesztői csomag környezetben egyszerűen létrehozhatja egy fájlmegosztást a development kit gazdagépen.
2. A parancsmag futtatása 
    ```powershell
    Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
    ```
ahol

| Paraméter | Leírás | Típus | Kötelező |
|---------|---------|---------|---------|
| *TranscriptsPathDestination* | a "fileshareIP\sharefoldername" definiálva külső fájlmegosztás elérési útja | String | igen|
| *Hitelesítő adatok* | a fájlmegosztás eléréséhez szükséges hitelesítő adatok | SecureString |  igen |


A fájlmegosztás sikeresen átkerülnek a szöveges naplófájlok, miután azok még automatikusan törli a az EGP. 

> [!NOTE]
> Ha a parancsmagok használatával zárja be a EGP munkamenet `Exit-PSSession` vagy `Exit`, vagy bezárhatja a PowerShell-konzolt, a szöveges naplók átadása nem történik meg azt a fájlmegosztást. Az EGP maradnak. A következő futtatásakor `Close-PrivilegedEndpoint` egy fájlmegosztást, és a szöveges naplók az előző munkamenet is fogja átvinni. Ne használjon `Exit-PSSession` vagy `Exit` gombra kattintva zárja be a EGP munkamenethez használni `Close-PrivilegedEndpoint` helyette.


## <a name="next-steps"></a>További lépések

[Az Azure Stack-diagnosztikai eszközök](azure-stack-diagnostics.md)
