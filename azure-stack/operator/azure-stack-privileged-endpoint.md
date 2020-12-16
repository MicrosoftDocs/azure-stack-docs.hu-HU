---
title: A rendszerjogosultságú végpont használata Azure Stack központban
description: Megtudhatja, hogyan használhatja a Kiemelt végpontot (PEP) Azure Stack hub-ban operátorként.
author: mattbriggs
ms.topic: article
ms.date: 04/28/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/28/2020
ms.custom: conteperfq4
ms.openlocfilehash: a20e7bf62cf65d1fd7c5ffb0f4f4ddc7313c55b2
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598249"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>A rendszerjogosultságú végpont használata Azure Stack központban

Azure Stack hub-kezelőként a legtöbb napi felügyeleti feladathoz a felügyeleti portál, a PowerShell vagy a Azure Resource Manager API-k használhatók. Néhány kevésbé gyakori művelet esetében azonban a *Kiemelt jogosultságú végpontot* (PEP) kell használnia. A PEP egy előre konfigurált távoli PowerShell-konzol, amely elegendő képességet biztosít a szükséges feladatok elvégzéséhez. A végpont [PowerShell-JEA (elég felügyelet)](/powershell/scripting/learn/remoting/jea/overview) használ, hogy csak korlátozott számú parancsmagot tegyen elérhetővé. A PEP eléréséhez és a parancsmagok korlátozott készletének meghívásához egy alacsony jogosultsági szintű fiókot kell használni. Nincs szükség rendszergazdai fiókra. A további biztonság érdekében a parancsfájlkezelés nem engedélyezett.

A következő feladatok elvégzéséhez használhatja a PEP-t:

- Alacsony szintű feladatok, például [diagnosztikai naplók gyűjtése](azure-stack-get-azurestacklog.md).
- Számos üzembe helyezést követő adatközpont-integrációs feladat az integrált rendszerekhez, például a tartománynévrendszer (DNS) továbbítóinak telepítése az üzembe helyezést követően, Microsoft Graph integráció, Active Directory összevonási szolgáltatások (AD FS) (AD FS) integráció, a tanúsítvány elforgatása stb.
- Az integrált rendszer részletes hibaelhárításához az ideiglenes, magas szintű hozzáférés támogatásával dolgozhat.

A PEP a PowerShell-munkamenetben végrehajtott összes műveletet (és a hozzá tartozó kimenetet) naplózza. Ez teljes átláthatóságot és teljes körű naplózást biztosít a műveletekhez. A naplófájlokat megtarthatja a jövőbeli ellenőrzésekhez.

> [!NOTE]
> A Azure Stack Development Kit (ASDK) alkalmazásban a PEP-ben elérhető parancsokat futtathatja közvetlenül a fejlesztői készlet gazdagépén található PowerShell-munkamenetből. Előfordulhat azonban, hogy tesztelni szeretné a PEP-t használó bizonyos műveleteket, például a naplózási gyűjteményt, mivel ez az egyetlen olyan módszer, amely bizonyos műveletek végrehajtásához használható egy integrált rendszer-környezetben.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

## <a name="access-the-privileged-endpoint"></a>Hozzáférés a Kiemelt végponthoz

A PEP-t egy távoli PowerShell-munkameneten keresztül érheti el a virtuális gépen (VM), amely a PEP-et üzemelteti. A ASDK a virtuális gép neve **AzS-ERCS01**. Ha integrált rendszert használ, a PEP három példánya létezik, amelyek mindegyike egy virtuális gépen (*előtag*– ERCS01, *előtag*-ERCS02 vagy *előtag*-ERCS03) belül fut a különböző gazdagépeken a rugalmasság érdekében.

Mielőtt elkezdené ezt az eljárást egy integrált rendszeren, győződjön meg arról, hogy az IP-cím vagy a DNS használatával fér hozzá a PEP-hez. Azure Stack hub kezdeti telepítése után a PEP-t csak IP-címmel érheti el, mert a DNS-integráció még nincs beállítva. Az OEM hardvergyártó a **AzureStackStampDeploymentInfo** nevű JSON-fájlt fogja biztosítani, amely a PEP IP-címeket tartalmazza.

Az IP-címet az Azure Stack hub felügyeleti portálján is megtalálhatja. Nyissa meg a portált, például: `https://adminportal.local.azurestack.external` . Válassza a **régió-felügyeleti**  >  **Tulajdonságok** lehetőséget.

Az emelt szintű végpont futtatásakor be kell állítania az aktuális kulturális környezet beállítását `en-US` , ellenkező esetben a parancsmagok (például Test-AzureStack vagy Get-AzureStackLog) nem a várt módon fognak működni.

> [!NOTE]
> Biztonsági okokból szükség van arra, hogy csak a hardveres életciklus-gazdagépen futó megerősített virtuális gépről, vagy egy dedikált és biztonságos számítógépről, például egy emelt [szintű hozzáférési munkaállomásról](/windows-server/identity/securing-privileged-access/privileged-access-workstations)kapcsolódjon a PEP-hez. A hardveres életciklus-gazdagép eredeti konfigurációja nem módosítható az eredeti konfigurációjától (beleértve az új szoftverek telepítését), vagy a PEP-hez való kapcsolódáshoz használt konfigurációt.

1. Hozza létre a bizalmi kapcsolatot.

   - Egy integrált rendszeren futtassa a következő parancsot egy emelt szintű Windows PowerShell-munkamenetből, hogy hozzáadja a PEP-t megbízható gazdagépként a hardver életciklus-gazdagépén vagy a privilegizált elérésű munkaállomáson futó megerősített virtuális gépen.

      ```powershell  
      Set-Item WSMan:\localhost\Client\TrustedHosts -Value '<IP Address of Privileged Endpoint>' -Concatenate
      ```
      
   - Ha a ASDK futtatja, jelentkezzen be a fejlesztői csomag gazdagépére.

1. Nyisson meg egy Windows PowerShell-munkamenetet a hardveres életciklus-gazdagépen vagy az emelt szintű hozzáférési munkaállomáson futó megerősített virtuális gépen. Futtassa a következő parancsokat egy távoli munkamenet létrehozásához a PEP-t futtató virtuális gépen:
 
   - Integrált rendszeren:

      ```powershell  
      $cred = Get-Credential

      $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
      ```
    
      A `ComputerName` paraméter lehet a PEP-t futtató virtuális gépek egyikének IP-címe vagy DNS-neve.

      > [!NOTE]  
      > Azure Stack hub nem végez távoli hívást a PEP hitelesítő adatainak ellenőrzésekor. Egy helyileg tárolt RSA nyilvános kulcsra támaszkodik.

   - Ha a ASDK futtatja:

      ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
      ```
    
   Ha a rendszer kéri, használja a következő hitelesítő adatokat:
   
   -  **Felhasználónév**: adja meg a CloudAdmin-fiókot **&lt; *Azure stack hub-tartomány* &gt; \cloudadmin** formátumban. (A ASDK esetében a Felhasználónév a **azurestack\cloudadmin**)
   - **Password (jelszó**): adja meg ugyanazt a jelszót, amelyet a azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

   > [!NOTE]
   > Ha nem tud csatlakozni az ERCS-végponthoz, próbálkozzon újra egy másik ERCS virtuális gép IP-címével.

1. A kapcsolódást követően a rendszer a (z) **[*IP-cím vagy ERCS virtuális gép neve*]: PS>** vagy a (z) **[AZS-ercs01]: PS>** értékre vált a környezettől függően. Innen `Get-Command` a Futtatás gombra kattintva megtekintheti az elérhető parancsmagok listáját.

   A parancsmagok hivatkozását a következő helyen találja: [Azure stack hub privilegizált végpontjának referenciája](../reference/pep-2002/index.md)

   Ezen parancsmagok nagy része kizárólag az integrált rendszerkörnyezetekhez (például az adatközpont-integrációhoz kapcsolódó parancsmagokhoz) készült. A ASDK a következő parancsmagok lettek érvényesítve:

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="how-to-use-the-privileged-endpoint"></a>A Kiemelt végpont használata 

A fent említettek szerint a PEP egy [PowerShell-JEA](/powershell/scripting/learn/remoting/jea/overview) végpont. Erős biztonsági réteg biztosítása mellett a JEA-végpontok csökkentik az alapvető PowerShell-képességeket, például a parancsfájlok vagy a tabulátorok befejezését. Ha bármilyen típusú parancsfájl-műveletet próbál végrehajtani, a művelet sikertelen lesz a hiba **ScriptsNotAllowed**. Ez a hiba a várt viselkedés.

Ha például egy adott parancsmag paramétereinek listáját szeretné lekérni, futtassa a következő parancsot:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Azt is megteheti, hogy az [**import-PSSession**](/powershell/module/microsoft.powershell.utility/import-pssession?view=powershell-5.1) parancsmag használatával importálja az összes PEP-parancsmagot a helyi számítógép aktuális munkamenetére. A PEP-parancsmagok és-függvények mostantól elérhetők a helyi gépen, a TAB befejezéssel együtt, és általánosságban a parancsfájlok. A **[Get-Help](/powershell/module/microsoft.powershell.core/get-help)** modult is futtathatja a parancsmagra vonatkozó utasítások áttekintéséhez.

Ha a PEP-munkamenetet a helyi gépen szeretné importálni, hajtsa végre a következő lépéseket:

1. Hozza létre a bizalmi kapcsolatot.

   - Egy integrált rendszeren futtassa a következő parancsot egy emelt szintű Windows PowerShell-munkamenetből, hogy hozzáadja a PEP-t megbízható gazdagépként a hardver életciklus-gazdagépén vagy a privilegizált elérésű munkaállomáson futó megerősített virtuális gépen.

      ```powershell
      winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

   - Ha a ASDK futtatja, jelentkezzen be a fejlesztői csomag gazdagépére.

1. Nyisson meg egy Windows PowerShell-munkamenetet a hardveres életciklus-gazdagépen vagy az emelt szintű hozzáférési munkaállomáson futó megerősített virtuális gépen. Futtassa a következő parancsokat egy távoli munkamenet létrehozásához a PEP-t futtató virtuális gépen:

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

   - **Felhasználónév**: adja meg a CloudAdmin-fiókot **&lt; *Azure stack hub-tartomány* &gt; \cloudadmin** formátumban. (A ASDK esetében a Felhasználónév a **azurestack\cloudadmin**.)
      
   - **Password (jelszó**): adja meg ugyanazt a jelszót, amelyet a azurestack tartományi rendszergazdai fiók telepítésekor adott meg.

1. Importálja a PEP-munkamenetet a helyi gépre:

   ```powershell 
   Import-PSSession $session
   ```

1. Most már használhatja a TAB-Complete és a parancsfájlokat a szokásos módon a helyi PowerShell-munkamenetben a PEP összes funkciójának és parancsmagjának használatával anélkül, hogy az Azure Stack hub biztonsági helyzetének csökkenését kellene elvégeznie. Jó munkát!


## <a name="close-the-privileged-endpoint-session"></a>Az emelt szintű végpont munkamenetének lezárása

Ahogy azt korábban említettük, a PEP a PowerShell-munkamenetben elvégezhető összes műveletet (és a hozzá tartozó kimenetet) naplózza. A-munkamenetet a parancsmag használatával kell lezárva  `Close-PrivilegedEndpoint` . Ez a parancsmag helyesen zárja le a végpontot, és átviszi a naplófájlokat egy külső fájlmegosztást a megőrzéshez.

A végpont-munkamenet lezárása:

1. Hozzon létre egy külső fájlmegosztást, amelyet a PEP is elérhet. A fejlesztői csomag környezetében egyszerűen létrehozhat egy fájlmegosztást a fejlesztői készlet gazdagépén.
1. Futtassa a következő parancsmagot:

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
> Ha a-parancsmagok használatával, vagy csak a PowerShell-konzol bezárásával zárta be a PEP-munkamenetet `Exit-PSSession` `Exit` , a rendszer nem továbbítja a fájlokat a fájlmegosztás számára. A PEP-ben maradnak. Amikor legközelebb futtatja `Close-PrivilegedEndpoint` és belefoglal egy fájlmegosztást, az előző munkamenet (ek) ből származó átiratok is át lesznek továbbítva. Ne használja `Exit-PSSession` vagy ne `Exit` zárjuk be a PEP-munkamenetet; Ehelyett használja a `Close-PrivilegedEndpoint` parancsot.

## <a name="unlocking-the-privileged-endpoint-for-support-scenarios"></a>A rendszerjogosultságú végpont zárolásának feloldása támogatási forgatókönyvek esetén

A támogatási forgatókönyvek során előfordulhat, hogy a Microsoft támogatási szakemberének a Azure Stack hub-infrastruktúra belső hálózatához való hozzáféréshez meg kell adnia a privilegizált Endpoint PowerShell-munkamenetet. Ezt a folyamatot néha nem hivatalosan "az üveg megtörése" vagy "a PEP feloldása" kifejezésnek nevezzük. A PEP-munkamenet-jogosultságszint-emelési folyamat egy két lépés, két személy, két szervezet hitelesítési folyamata. A feloldási eljárást a Azure Stack hub-kezelő kezdeményezi, amely mindig megőrzi a környezete irányítását. A kezelő hozzáfér a PEP-hez, és végrehajtja a következő parancsmagot:
 
 ```powershell  
      Get-SupportSessionToken
 ```

A parancsmag egy nagyon hosszú alfanumerikus karakterláncot ad vissza a támogatási munkamenet-kérelem tokenje számára. Az operátor ezután átadja a kérelem jogkivonatát a Microsoft támogatási szakemberének a választott médiumon keresztül (pl. csevegés, e-mail). A Microsoft támogatási szakembere a kérelem tokenjét használja, ha érvényes, a támogatási munkamenet engedélyezési jogkivonatát, majd visszaküldi a Azure Stack hub-kezelőnek. Ugyanebben a PEP PowerShell-munkamenetben az operátor a következő parancsmag bemenetként továbbítja az engedélyezési jogkivonatot:

```powershell  
      unlock-supportsession
      cmdlet Unlock-SupportSession at command pipeline position 1
      Supply values for the following parameters:
      ResponseToken:
 ```

Ha az engedélyezési jogkivonat érvényes, a PEP PowerShell-munkamenet megemelhető azáltal, hogy teljes körű rendszergazdai képességeket biztosít, és teljes mértékben elérhetővé teszi az infrastruktúrát. 

> [!NOTE]
> Egy emelt szintű PEP-munkamenetben végrehajtott összes műveletet és parancsmagot a Microsoft támogatási szakemberének szigorú felügyelete alatt kell végrehajtani. Ennek elmulasztása súlyos állásidőt, adatvesztést okozhat, és az Azure Stack hub-környezet teljes újratelepítését teheti szükségessé.

A támogatási munkamenet leállítása után nagyon fontos, hogy a fenti szakaszban leírtak szerint zárja vissza a emelt szintű PEP-munkamenetet a **PrivilegedEndpoint** parancsmag használatával. Az egyik PEP-munkamenet véget ér, a feloldási jogkivonat már nem érvényes, és nem használható fel újra a PEP-munkamenet ismételt feloldására.
Egy emelt szintű PEP-munkamenet 8 óra elteltével érvényes, ami után a magasabb szintű PEP-munkamenet automatikusan vissza fog térni egy normál PEP-munkamenetbe.

## <a name="content-of-the-privileged-endpoint-tokens"></a>Az emelt szintű Endpoint tokenek tartalma

A PEP-támogatás munkamenet-kérelem és engedélyezési jogkivonatok kihasználják a titkosítást a hozzáférés biztosításához, és biztosítják, hogy csak a jogosult jogkivonatok tudják feloldani a PEP-munkamenetet A jogkivonatok úgy vannak kialakítva, hogy kriptográfiai garanciát biztosítsanak arra, hogy a válasz tokent csak a kérelem tokenjét létrehozó PEP-munkamenet fogadja el. A PEP-tokenek nem tartalmaznak olyan információt, amely egyedileg azonosít egy Azure Stack hub-környezetet vagy egy ügyfelet. Teljesen névtelenek. Az egyes tokenek tartalmának részleteit az alábbi táblázat ismerteti.
 
### <a name="support-session-request-token"></a>Támogatási munkamenet-kérelem tokenje

A PEP-támogatás munkamenet-kérelmének tokenje három objektumból áll:

- Véletlenszerűen generált munkamenet-azonosító.
- Önaláírt tanúsítvány, amely egy egyszeri nyilvános/titkos kulcspár létrehozása céljából lett létrehozva. A tanúsítvány nem tartalmaz semmilyen információt a környezetről.
- Egy időbélyegző, amely a kérelem jogkivonatának lejáratát jelzi.

Ezután a rendszer az Azure-felhő nyilvános kulcsával titkosítja a kérelem jogkivonatát, amelyhez az Azure Stack hub-környezet regisztrálva van.
 
### <a name="support-session-authorization-response-token"></a>A munkamenet-engedélyezési válasz jogkivonatának támogatása

A PEP-támogatás hitelesítési válaszának tokenje két objektumból áll:

- A kérelem tokenből kinyert véletlenszerűen generált munkamenet-azonosító.
- Egy időbélyegző, amely a válasz token lejáratát jelzi.
      
Ezután a rendszer titkosítja a válasz tokent a kérelem jogkivonatában található önaláírt tanúsítvánnyal. Az önaláírt tanúsítványt visszautasította az Azure-felhőhöz társított titkos kulccsal, amelyhez az Azure Stack hub-környezet regisztrálva van.


## <a name="next-steps"></a>További lépések

- [Azure Stack hub diagnosztikai eszközök](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)
- [Azure Stack hub privilegizált végpontjának referenciája](../reference/pep-2002/index.md)
