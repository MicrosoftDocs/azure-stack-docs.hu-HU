---
title: A rendszerjogosultságú végpont használata Azure Stack központban
description: Megtudhatja, hogyan használhatja a Kiemelt végpontot (PEP) Azure Stack hub-ban operátorként.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 1/8/2020
ms.openlocfilehash: b82a776f67648ec882837dc44d51fe95b24aba4f
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661319"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>A rendszerjogosultságú végpont használata Azure Stack központban

Azure Stack hub-kezelőként a legtöbb napi felügyeleti feladathoz a felügyeleti portál, a PowerShell vagy a Azure Resource Manager API-k használhatók. Néhány kevésbé gyakori művelet esetében azonban a *Kiemelt jogosultságú végpontot* (PEP) kell használnia. A PEP egy előre konfigurált távoli PowerShell-konzol, amely elegendő képességet biztosít a szükséges feladatok elvégzéséhez. A végpont [PowerShell-JEA (elég felügyelet)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) használ, hogy csak korlátozott számú parancsmagot tegyen elérhetővé. A PEP eléréséhez és a parancsmagok korlátozott készletének meghívásához egy alacsony jogosultsági szintű fiókot kell használni. Nincs szükség rendszergazdai fiókra. A további biztonság érdekében a parancsfájlkezelés nem engedélyezett.

A következő feladatok elvégzéséhez használhatja a PEP-t:

- Alacsony szintű feladatok, például [diagnosztikai naplók gyűjtése](azure-stack-get-azurestacklog.md).
- Számos üzembe helyezést követő adatközpont-integrációs feladat az integrált rendszerekhez, például a tartománynévrendszer (DNS) továbbítóinak telepítése az üzembe helyezést követően, Microsoft Graph integráció, Active Directory összevonási szolgáltatások (AD FS) (AD FS) integráció, a tanúsítvány elforgatása stb.
- Az integrált rendszer részletes hibaelhárításához az ideiglenes, magas szintű hozzáférés támogatásával dolgozhat.

A PEP a PowerShell-munkamenetben végrehajtott összes műveletet (és a hozzá tartozó kimenetet) naplózza. Ez teljes átláthatóságot és teljes körű naplózást biztosít a műveletekhez. A naplófájlokat megtarthatja a jövőbeli ellenőrzésekhez.

> [!NOTE]
> A Azure Stack Development Kit (ASDK) alkalmazásban a PEP-ben elérhető parancsokat futtathatja közvetlenül a fejlesztői készlet gazdagépén található PowerShell-munkamenetből. Előfordulhat azonban, hogy tesztelni szeretné a PEP-t használó bizonyos műveleteket, például a naplózási gyűjteményt, mivel ez az egyetlen olyan módszer, amely bizonyos műveletek végrehajtásához használható egy integrált rendszer-környezetben.

## <a name="access-the-privileged-endpoint"></a>Hozzáférés a Kiemelt végponthoz

A PEP-t egy távoli PowerShell-munkameneten keresztül érheti el a virtuális gépen (VM), amely a PEP-et üzemelteti. A ASDK a virtuális gép neve **AzS-ERCS01**. Ha integrált rendszert használ, a PEP három példánya létezik, amelyek mindegyike egy virtuális gépen (*előtag*– ERCS01, *előtag*-ERCS02 vagy *előtag*-ERCS03) belül fut a különböző gazdagépeken a rugalmasság érdekében.

Mielőtt elkezdené ezt az eljárást egy integrált rendszeren, győződjön meg arról, hogy az IP-cím vagy a DNS használatával fér hozzá a PEP-hez. Azure Stack hub kezdeti telepítése után a PEP-t csak IP-címmel érheti el, mert a DNS-integráció még nincs beállítva. Az OEM hardvergyártó a **AzureStackStampDeploymentInfo** nevű JSON-fájlt fogja biztosítani, amely a PEP IP-címeket tartalmazza.

Az IP-címet az Azure Stack hub felügyeleti portálján is megtalálhatja. Nyissa meg a portált, `https://adminportal.local.azurestack.external`például:. Válassza a **régió-felügyeleti** > **Tulajdonságok**lehetőséget.

Az emelt szintű végpont futtatásakor be kell `en-US` állítania az aktuális kulturális környezet beállítását, máskülönben a test-AzureStack vagy a Get-AzureStackLog parancsmagok nem a várt módon fognak működni.

> [!NOTE]
> Biztonsági okokból szükség van arra, hogy csak a hardveres életciklus-gazdagépen futó megerősített virtuális gépről, vagy egy dedikált és biztonságos számítógépről, például egy emelt [szintű hozzáférési munkaállomásról](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)kapcsolódjon a PEP-hez. A hardveres életciklus-gazdagép eredeti konfigurációja nem módosítható az eredeti konfigurációjától (beleértve az új szoftverek telepítését), vagy a PEP-hez való kapcsolódáshoz használt konfigurációt.

1. Hozza létre a bizalmi kapcsolatot.

      - Egy integrált rendszeren futtassa a következő parancsot egy emelt szintű Windows PowerShell-munkamenetből, hogy hozzáadja a PEP-t megbízható gazdagépként a hardver életciklus-gazdagépén vagy a privilegizált elérésű munkaállomáson futó megerősített virtuális gépen.

      ```powershell  
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

      - Ha a ASDK futtatja, jelentkezzen be a fejlesztői csomag gazdagépére.

2. Nyisson meg egy Windows PowerShell-munkamenetet a hardveres életciklus-gazdagépen vagy az emelt szintű hozzáférési munkaállomáson futó megerősített virtuális gépen. Futtassa a következő parancsokat egy távoli munkamenet létrehozásához a PEP-t futtató virtuális gépen:
 
  - Integrált rendszeren:

    ```powershell  
    $cred = Get-Credential

    $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
    Enter-PSSession $pep
    ```
    
    A `ComputerName` paraméter lehet a PEP-t futtató virtuális gépek egyikének IP-címe vagy DNS-neve.

    > [!NOTE]  
    >Azure Stack hub nem végez távoli hívást a PEP hitelesítő adatainak ellenőrzésekor. Egy helyileg tárolt RSA nyilvános kulcsra támaszkodik.

   - Ha a ASDK futtatja:

     ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
     ```
    
   - Ha a rendszer kéri, használja a következő hitelesítő adatokat:
   
       - **Felhasználónév**: adja meg a CloudAdmin-fiókot ** &lt; *Azure stack hub-tartomány*&gt;\cloudadmin**formátumban. (A ASDK esetében a Felhasználónév a **azurestack\cloudadmin**.)
  
        - **Password (jelszó**): adja meg ugyanazt a jelszót, amelyet a azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

      > [!NOTE]
      > Ha nem tud csatlakozni az ERCS-végponthoz, próbálkozzon újra egy másik ERCS virtuális gép IP-címével.

3. A kapcsolódást követően a rendszer a (z) **[*IP-cím vagy ERCS virtuális gép neve*]: PS>** vagy a (z) **[AZS-ercs01]: PS>** értékre vált a környezettől függően. Innen a Futtatás `Get-Command` gombra kattintva megtekintheti az elérhető parancsmagok listáját.

   Ezen parancsmagok nagy része kizárólag az integrált rendszerkörnyezetekhez (például az adatközpont-integrációhoz kapcsolódó parancsmagokhoz) készült. A ASDK a következő parancsmagok lettek érvényesítve:

   - Gazdagép törlése
   - Bezárás – PrivilegedEndpoint
   - Kilépés – PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Mérték – objektum
   - Új – CloudAdminUser
   - Alapértelmezett
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Leállítás – AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tippek a Kiemelt végpont használatához 

A fent említettek szerint a PEP egy [PowerShell-JEA](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) végpont. Erős biztonsági réteg biztosítása mellett a JEA-végpontok csökkentik az alapvető PowerShell-képességeket, például a parancsfájlok vagy a tabulátorok befejezését. Ha bármilyen típusú parancsfájl-műveletet próbál végrehajtani, a művelet sikertelen lesz a hiba **ScriptsNotAllowed**. Ez a hiba a várt viselkedés.

Ha például egy adott parancsmag paramétereinek listáját szeretné lekérni, futtassa a következő parancsot:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Azt is megteheti, hogy az [import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) parancsmag használatával importálja az összes PEP-parancsmagot a helyi számítógép aktuális munkamenetére. Ezzel a PEP-parancsmagok és-függvények mostantól elérhetők a helyi gépen, a TAB befejezésével együtt, és általánosságban a parancsfájlok.

Ha a PEP-munkamenetet a helyi gépen szeretné importálni, hajtsa végre a következő lépéseket:

1. Hozza létre a bizalmi kapcsolatot.

    - Egy integrált rendszeren futtassa a következő parancsot egy emelt szintű Windows PowerShell-munkamenetből, hogy hozzáadja a PEP-t megbízható gazdagépként a hardver életciklus-gazdagépén vagy a privilegizált elérésű munkaállomáson futó megerősített virtuális gépen.

    ```powershell
    winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
    ```

    - Ha a ASDK futtatja, jelentkezzen be a fejlesztői csomag gazdagépére.

2. Nyisson meg egy Windows PowerShell-munkamenetet a hardveres életciklus-gazdagépen vagy az emelt szintű hozzáférési munkaállomáson futó megerősített virtuális gépen. Futtassa a következő parancsokat egy távoli munkamenet létrehozásához a PEP-t futtató virtuális gépen:

    - Integrált rendszeren:
    
      ```powershell  
        $cred = Get-Credential
      
        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```
    
      A `ComputerName` paraméter lehet a PEP-t futtató virtuális gépek egyikének IP-címe vagy DNS-neve.

    - Ha a ASDK futtatja:
     
        ```powershell  
          $cred = Get-Credential
    
          $session = New-PSSession -ComputerName azs-ercs01 `
             -ConfigurationName PrivilegedEndpoint -Credential $cred
        ```

     Ha a rendszer kéri, használja a következő hitelesítő adatokat:

     - **Felhasználónév**: adja meg a CloudAdmin-fiókot ** &lt; *Azure stack hub-tartomány*&gt;\cloudadmin**formátumban. (A ASDK esetében a Felhasználónév a **azurestack\cloudadmin**.)
     - **Password (jelszó**): adja meg ugyanazt a jelszót, amelyet a azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

3. Importálja a PEP-munkamenetet a helyi gépre:

    ```powershell 
      Import-PSSession $session
    ```

4. Most már használhatja a TAB-Complete és a parancsfájlokat a szokásos módon a helyi PowerShell-munkamenetben a PEP összes funkciójának és parancsmagjának használatával anélkül, hogy az Azure Stack hub biztonsági helyzetének csökkenését kellene elvégeznie. Jó munkát!

## <a name="close-the-privileged-endpoint-session"></a>Az emelt szintű végpont munkamenetének lezárása

 Ahogy azt korábban említettük, a PEP a PowerShell-munkamenetben elvégezhető összes műveletet (és a hozzá tartozó kimenetet) naplózza. A-munkamenetet a `Close-PrivilegedEndpoint` parancsmag használatával kell lezárva. Ez a parancsmag helyesen zárja le a végpontot, és átviszi a naplófájlokat egy külső fájlmegosztást a megőrzéshez.

A végpont-munkamenet lezárása:

1. Hozzon létre egy külső fájlmegosztást, amelyet a PEP is elérhet. A fejlesztői csomag környezetében egyszerűen létrehozhat egy fájlmegosztást a fejlesztői készlet gazdagépén.
2. Futtassa a következő parancsmagot:

  ```powershell  
     Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
  ```

   A parancsmag a következő táblázatban szereplő paramétereket használja:

   | Paraméter | Leírás | Típus | Kötelező |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | A külső fájlmegosztás elérési útja "fileshareIP\sharefoldername"-ként definiálva | Sztring | Igen|
   | *Hitelesítőadat* | A fájlmegosztás eléréséhez szükséges hitelesítő adatok | SecureString |   Igen |


Miután az átirat naplófájljai sikeresen át lettek küldve a fájlmegosztásba, automatikusan törlődnek a PEP-ből. 

> [!NOTE]
> Ha a-parancsmagok `Exit-PSSession` `Exit`használatával, vagy csak a PowerShell-konzol bezárásával zárta be a PEP-munkamenetet, a rendszer nem továbbítja a fájlokat a fájlmegosztás számára. A PEP-ben maradnak. Amikor legközelebb futtatja `Close-PrivilegedEndpoint` és belefoglal egy fájlmegosztást, az előző munkamenet (ek) ből származó átiratok is át lesznek továbbítva. Ne használja `Exit-PSSession` vagy `Exit` ne zárjuk be a PEP-munkamenetet; használja `Close-PrivilegedEndpoint` helyette.


## <a name="next-steps"></a>További lépések

[Azure Stack hub diagnosztikai eszközök](azure-stack-diagnostic-log-collection-overview-tzl.md)
