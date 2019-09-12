---
title: A rendszerjogosultságú végpont használata a Azure Stackban | Microsoft Docs
description: Azt mutatja be, hogyan használható a Kiemelt végpont (PEP) a Azure Stack (Azure Stack operátor esetében).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 09/03/2019
ms.openlocfilehash: a278a918100619953b2b7eb9b288236625968187
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902625"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>A rendszerjogosultságú végpont használata Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack-operátorként a legtöbb napi rendszerességű felügyeleti feladat ellátásához a felügyeleti portált, a PowerShellt vagy az Azure Resource Manager API-kat használhatja. Néhány kevésbé gyakori művelet esetében azonban a *Kiemelt jogosultságú végpontot* (PEP) kell használnia. A PEP egy előre konfigurált távoli PowerShell-konzol, amely elegendő képességet biztosít a szükséges feladatok elvégzéséhez. A végpont [PowerShell-JEA (elég felügyelet)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) használ, hogy csak korlátozott számú parancsmagot tegyen elérhetővé. A PEP eléréséhez és a parancsmagok korlátozott készletének meghívásához egy alacsony jogosultsági szintű fiókot kell használni. Nincs szükség rendszergazdai fiókra. A további biztonság érdekében a parancsfájlok futtatása nem engedélyezett.

A PEP használatával olyan feladatokat hajthat végre, mint például a következők:

- Alacsony szintű feladatok végrehajtásához, például a [diagnosztikai naplók összegyűjtéséhez](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep).
- Számos üzembe helyezés utáni adatközpont-integrációs feladat elvégzéséhez az integrált rendszerekhez, például a tartománynévrendszer (DNS) továbbítóinak az üzembe helyezést követően történő hozzáadásával, Microsoft Graph integráció, a Active Directory összevonási szolgáltatások (AD FS) (AD FS) integrációjának beállításával tanúsítvány elforgatása stb.
- Az integrált rendszer részletes hibaelhárításához az ideiglenes, magas szintű hozzáférés támogatásával dolgozhat.

A PEP a PowerShell-munkamenetben végrehajtott összes műveletet (és a hozzá tartozó kimenetet) naplózza. Ez teljes átláthatóságot és teljes körű naplózást biztosít a műveletekhez. Ezeket a naplófájlokat megtarthatja a jövőbeli naplózáshoz.

> [!NOTE]
> A Azure Stack Development Kit (ASDK) alkalmazásban a PEP-ben elérhető parancsokat futtathatja közvetlenül a fejlesztői készlet gazdagépén található PowerShell-munkamenetből. Előfordulhat azonban, hogy tesztelni szeretné a PEP-t használó bizonyos műveleteket, például a naplózási gyűjteményt, mivel ez az egyetlen olyan módszer, amely bizonyos műveletek végrehajtásához használható egy integrált rendszer-környezetben.

## <a name="access-the-privileged-endpoint"></a>Hozzáférés a Kiemelt végponthoz

A PEP-t egy távoli PowerShell-munkameneten keresztül érheti el a virtuális gépen, amelyen a PEP fut. A ASDK a virtuális gép neve **AzS-ERCS01**. Ha integrált rendszert használ, a PEP három példánya van, amelyek mindegyike egy virtuális gépen (*előtag*: ERCS01, *előtag*-ERCS02 vagy *előtag*-ERCS03) fut a különböző gazdagépeken a rugalmasság érdekében. 

Mielőtt elkezdené ezt az eljárást egy integrált rendszeren, győződjön meg arról, hogy az IP-cím vagy a DNS használatával fér hozzá a PEP-hez. A Azure Stack kezdeti telepítése után a PEP-t csak IP-címmel érheti el, mivel a DNS-integráció még nincs beállítva. Az OEM hardvergyártó a **AzureStackStampDeploymentInfo** nevű JSON-fájlt fogja biztosítani, amely a PEP IP-címeket tartalmazza.


> [!NOTE]
> Biztonsági okokból szükség van arra, hogy csak olyan megerősített virtuális gépről kapcsolódjon a PEP-hez, amely a hardver életciklus-állomásán fut, vagy egy dedikált, biztonságos számítógépről, például egy emelt [szintű hozzáférésű munkaállomásról](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). A hardveres életciklus-állomás eredeti konfigurációja nem módosítható az eredeti konfigurációjától, beleértve az új szoftverek telepítését, és nem használható a PEP-hez való kapcsolódásra.

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

       Enter-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     A `ComputerName` paraméter lehet a PEP-t futtató virtuális gépek egyikének IP-címe vagy DNS-neve. 

     >[!NOTE]
     >A Azure Stack nem végez távoli hívást a PEP hitelesítő adatának érvényesítésekor. Egy helyileg tárolt RSA nyilvános kulcsra támaszkodik.
     
   - Ha a ASDK futtatja:
     
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     Ha a rendszer kéri, használja a következő hitelesítő adatokat:

     - **Felhasználónév**: A CloudAdmin fiókjának  **&lt; *Azure stack tartományi*&gt;\cloudadmin**formátumban kell megadnia. (A ASDK esetében a Felhasználónév a **azurestack\cloudadmin**.)
     - **Jelszó**: Adja meg ugyanazt a jelszót, amelyet a Azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

     > [!NOTE]
     > Ha nem tud csatlakozni az ERCS-végponthoz, próbálkozzon újra egy másik ERCS virtuális gép IP-címével.

3. A kapcsolódás után a rendszer az [ ***IP-cím vagy a ERCS virtuális gép neve*] értékre vált. PS >** **vagy [AZS-ercs01]: PS >** , a környezettől függően. Innen a Futtatás `Get-Command` gombra kattintva megtekintheti az elérhető parancsmagok listáját.

   Ezen parancsmagok nagy része kizárólag az integrált rendszerkörnyezetekhez (például az adatközpont-integrációhoz kapcsolódó parancsmagokhoz) készült. A ASDK a következő parancsmagok lettek érvényesítve:

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Kilépés – PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Mérték – objektum
   - New-CloudAdminUser
   - Alapértelmezett
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tippek a Kiemelt végpont használatához 

A fent említettek szerint a PEP egy [PowerShell-JEA](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) végpont. Erős biztonsági réteg biztosítása mellett a JEA-végpontok csökkentik az alapvető PowerShell-képességeket, például a parancsfájlok vagy a tabulátorok befejezését. Ha bármilyen típusú parancsfájl-műveletet próbál végrehajtani, a művelet sikertelen lesz a hiba **ScriptsNotAllowed**. Ez a várt viselkedés.

Így például egy adott parancsmag paramétereinek listájának lekéréséhez futtassa a következő parancsot:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Azt is megteheti, hogy az [import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) parancsmag használatával importálja az összes PEP-parancsmagot a helyi számítógép aktuális munkamenetére. Ezzel a PEP-parancsmagok és-függvények mostantól elérhetők a helyi gépen, a TAB befejezésével együtt, és általánosságban a parancsfájlok. 

Ha a PEP-munkamenetet a helyi gépen szeretné importálni, hajtsa végre a következő lépéseket:

1. Hozza létre a bizalmi kapcsolatot.

    – Egy integrált rendszeren futtassa a következő parancsot egy emelt szintű Windows PowerShell-munkamenetből, hogy a PEP-t megbízható gazdagépként adja hozzá a hardver életciklus-gazdagépén vagy a privilegizált elérésű munkaállomáson futó megerősített virtuális gépen.

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

     - **Felhasználónév**: A CloudAdmin fiókjának  **&lt; *Azure stack tartományi*&gt;\cloudadmin**formátumban kell megadnia. (A ASDK esetében a Felhasználónév a **azurestack\cloudadmin**.)
     - **Jelszó**: Adja meg ugyanazt a jelszót, amelyet a Azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

3. A PEP-munkamenet importálása a helyi gépre
     ```powershell 
        Import-PSSession $session
   ```
4. Most már használhatja a TAB-Complete és a parancsfájlokat a szokásos módon a helyi PowerShell-munkamenetben a PEP összes funkciójának és parancsmagjának használatával anélkül, hogy a Azure Stack biztonsági állapotát kellene csökkentenie. Jó munkát!


## <a name="close-the-privileged-endpoint-session"></a>Az emelt szintű végpont munkamenetének lezárása

 Ahogy azt korábban említettük, a PEP a PowerShell-munkamenetben végrehajtott összes műveletet (és a hozzá tartozó kimenetet) naplózza. A-munkamenetet a `Close-PrivilegedEndpoint` parancsmag használatával kell lezárva. Ez a parancsmag helyesen zárja le a végpontot, és átviszi a naplófájlokat egy külső fájlmegosztást a megőrzéshez.

A végpont-munkamenet lezárása:

1. Hozzon létre egy külső fájlmegosztást, amelyet a PEP is elérhet. A fejlesztői csomag környezetében egyszerűen létrehozhat egy fájlmegosztást a fejlesztői készlet gazdagépén.
2. Futtassa a következő parancsmagot: 
     ```powershell
     Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
     ```
   amely a következő táblázatban szereplő paramétereket használja.

   | Paraméter | Leírás | Type | Kötelező |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | a külső fájlmegosztás elérési útja "fileshareIP\sharefoldername"-ként definiálva | Sztring | igen|
   | *Hitelesítő adatok* | a fájlmegosztás eléréséhez szükséges hitelesítő adatok | SecureString |   igen |


Miután az átirat naplófájljai sikeresen át lettek küldve a fájlmegosztásba, automatikusan törlődnek a PEP-ből. 

> [!NOTE]
> Ha a-parancsmagok `Exit-PSSession` `Exit`használatával, vagy csak a PowerShell-konzol bezárásával zárta be a PEP-munkamenetet, a rendszer nem továbbítja a fájlokat a fájlmegosztás számára. A PEP-ben maradnak. Amikor legközelebb futtatja `Close-PrivilegedEndpoint` és belefoglal egy fájlmegosztást, az előző munkamenet (ek) ből származó átiratok is át lesznek továbbítva. Ne használja `Exit-PSSession` a- `Exit` t vagy a-t a PEP `Close-PrivilegedEndpoint` -munkamenet bezárásához; használja helyette.


## <a name="next-steps"></a>További lépések

[Diagnosztikai eszközök Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)
