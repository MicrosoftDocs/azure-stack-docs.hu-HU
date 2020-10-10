---
title: Hálózati vezérlő telepítése a Windows PowerShell használatával
description: Útmutató a hálózati vezérlő üzembe helyezéséhez a Windows PowerShell használatával
author: v-dasis
ms.topic: how-to
ms.date: 09/22/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e217c8b3e2a67dafa121fe752b66af9f24f888a1
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899550"
---
# <a name="deploy-network-controller-using-windows-powershell"></a>Hálózati vezérlő telepítése a Windows PowerShell használatával

> A Azure Stack HCI 20H2; verzióra vonatkozik. Windows Server 2019

Ez a témakör azt ismerteti, hogyan lehet a Windows PowerShell használatával hálózati vezérlőt telepíteni egy vagy több Azure Stack HCI-fürtön futó virtuális gépre (VM). A hálózati vezérlő a szoftveresen definiált hálózatkezelés (SDN) egyik összetevője.

>[!NOTE]
>A hálózati vezérlőt a fürt létrehozása varázslóval is üzembe helyezheti a Windows felügyeleti központban. További információ: [Azure stack HCI-fürt létrehozása a Windows felügyeleti központban](create-cluster.md).

## <a name="using-windows-powershell"></a>A Windows PowerShell használata

Futtathatja a PowerShellt helyileg Távoli asztal (RDP) munkamenetben a gazdagépen, vagy távolról is futtathatja a PowerShellt egy felügyeleti számítógépről.

Ha felügyeleti számítógépről futtatja a PowerShellt, a `-Name` vagy a paramétert adja meg a felügyelt `-Cluster` kiszolgáló vagy fürt nevével. Emellett előfordulhat, hogy a kiszolgáló paraméterének használatakor meg kell adnia a teljes tartománynevet (FQDN) `-ComputerName` .

Szüksége lesz a Hyper-V és a feladatátvételi fürtszolgáltatás Távoli kiszolgálófelügyelet eszközei (RSAT) parancsmagjai és PowerShell-moduljaira is. Ha ezek még nem állnak rendelkezésre a PowerShell-munkamenetben a felügyeleti számítógépen, a következő paranccsal adhatja hozzá őket: `Add-WindowsFeature RSAT-Clustering-PowerShell` .

## <a name="install-the-network-controller-server-role"></a>A hálózati vezérlő kiszolgálói szerepkör telepítése

Ezzel az eljárással telepítheti a hálózati vezérlő kiszolgálói szerepkört egy virtuális gépre (VM).

>[!IMPORTANT]
>Ne telepítse a hálózati vezérlő kiszolgálói szerepkört a fizikai gazdagépeken. A hálózati vezérlő telepítéséhez telepítenie kell a hálózati vezérlő kiszolgálói szerepkört egy Hyper-v-gazdagépre telepített Hyper-V virtuális gépre. Miután telepítette a hálózati vezérlőt a virtuális gépeken három különböző Hyper-V gazdagépen, engedélyeznie kell a Hyper-V-gazdagépeket a szoftverek által meghatározott hálózatkezeléshez (SDN) a gazdagépek hálózati vezérlőhöz való hozzáadásával. Ezzel engedélyezi az SDN szoftver Load Balancer működését.

A művelet végrehajtásához a **rendszergazdák** csoport tagjának kell lennie, vagy ezzel egyenértékű jogosultsággal kell rendelkeznie.  

>[!NOTE]
>Ha a Windows PowerShell helyett a Kiszolgálókezelő eszközt szeretné használni a hálózati vezérlő telepítéséhez, tekintse meg [a hálózati vezérlő kiszolgálói szerepkör telepítése a Kiszolgálókezelő használatával](/windows-server/networking/sdn/technologies/network-controller/install-the-network-controller-server-role-using-server-manager) című témakört.

A hálózati vezérlő telepítéséhez írja be a következő parancsokat:

```powershell
Install-WindowsFeature -Name NetworkController -IncludeManagementTools
```

A hálózati vezérlő telepítéséhez újra kell indítani a számítógépet. Ehhez adja meg a következő parancsot:

```powershell
Restart-Computer
```

## <a name="configure-the-network-controller-cluster"></a>A hálózati vezérlő fürt konfigurálása

A hálózati vezérlő fürt magas rendelkezésre állást és méretezhetőséget biztosít a hálózati vezérlő alkalmazás számára, amelyet a fürt létrehozása után konfigurálhat, és amely a fürt tetején fut.

>[!NOTE]
>A következő részben ismertetett eljárásokat közvetlenül azon a virtuális gépen végezheti el, amelyen a hálózati vezérlőt telepítette, vagy a Windows felügyeleti központot futtató távoli számítógépről is elvégezheti az eljárásokat. Emellett az eljárás végrehajtásához a **rendszergazdák** csoport vagy ezzel egyenértékű csoport tagjának kell lennie. Ha a hálózati vezérlőt telepített számítógép vagy virtuális gép egy tartományhoz csatlakozik, a felhasználói fióknak a **tartományi felhasználók** csoport tagjának kell lennie.

A hálózati vezérlő fürtöt csomópont-objektum létrehozásával, majd a fürt konfigurálásával hozhatja létre.

### <a name="create-a-node-object"></a>Csomópont-objektum létrehozása

Létre kell hoznia egy csomópont-objektumot minden olyan virtuális géphez, amely a hálózati vezérlő fürtjének tagja.

Csomópont-objektum létrehozásához írja be a következő parancsot. Győződjön meg arról, hogy minden, a telepítéshez megfelelő paraméter értékeit használja.  

```powershell
New-NetworkControllerNodeObject -Name <string> -Server "ServerName" -FaultDomain "SiteName" -RestInterface "Name" [-NodeCertificate <X509Certificate2>]
```

A következő táblázat a parancs egyes paramétereinek leírásait tartalmazza `New-NetworkControllerNodeObject` .

|Paraméter|Leírás|
|-------------|---------------|
|Name (Név)|A **Name** paraméter adja meg a fürthöz hozzáadni kívánt kiszolgáló rövid nevét.|
|Kiszolgáló|A **kiszolgáló** paraméter adja meg a fürthöz hozzáadni kívánt kiszolgáló állomásnevét, teljes tartománynevét (FQDN) vagy IP-címét. Tartományhoz csatlakozó számítógépek esetén a teljes tartománynevet kötelező megadni.|
|FaultDomain|A **FaultDomain** paraméter határozza meg a fürthöz hozzáadni kívánt kiszolgáló meghibásodási tartományát. Ez a paraméter határozza meg azokat a kiszolgálókat, amelyek a fürthöz hozzáadott kiszolgálóval megegyező időpontban is előfordulhatnak. Ez a hiba a megosztott fizikai függőségek, például az energiaellátási és hálózati források miatt lehet. A tartalék tartományok jellemzően az ezekkel a megosztott függőségekkel kapcsolatos hierarchiákat jelképezik, és több kiszolgáló is valószínű, hogy a tartalék tartomány fájának egy magasabb pontján fog működni. A futtatókörnyezet során a hálózati vezérlő a fürtben található tartalék tartományokat veszi figyelembe, és megkísérli kiosztani a hálózati vezérlő szolgáltatásait, hogy azok külön tartalék tartományokban legyenek. Ez a folyamat segít biztosítani, hogy egy tartalék tartomány meghibásodása esetén a szolgáltatás és az állapota ne legyen biztonságban. A tartalék tartományok hierarchikus formátumban vannak megadva. Például: "FD:/DC1/Rack1/Host1", ahol a DC1 az adatközpont neve, a Rack1 a rack neve, a Host1 pedig annak a gazdagépnek a neve, ahol a csomópont elhelyezve van.|
|RestInterface|A **RestInterface** paraméter adja meg annak a csomópontnak a nevét, amelyen a reprezentációs állapot átvitele (REST) kommunikációja le van zárva. Ez a hálózati vezérlő felület fogadja a hálózat felügyeleti rétegének irányú API-kéréseit.|
|NodeCertificate|A **NodeCertificate** paraméter határozza meg azt a tanúsítványt, amelyet a hálózati vezérlő használ a számítógép-hitelesítéshez. A tanúsítványra akkor van szükség, ha tanúsítványalapú hitelesítést használ a fürtön belüli kommunikációhoz. a tanúsítvány a hálózati vezérlő szolgáltatások közötti adatforgalom titkosítására is használható. A tanúsítvány tulajdonosának nevének meg kell egyeznie a csomópont DNS-nevével.|

### <a name="configure-the-cluster"></a>A fürt konfigurálása

A fürt konfigurálásához írja be a következő parancsot. Győződjön meg arról, hogy minden, a telepítéshez megfelelő paraméter értékeit használja.

```powershell
Install-NetworkControllerCluster -Node "NetworkControllerNodeName" -ClusterAuthentication "ClusterAuthenticationType" [-ManagementSecurityGroup <string>][-DiagnosticLogLocation <string>][-LogLocationCredential <PSCredential>] [-CredentialEncryptionCertificate <X509Certificate2>][-Credential <PSCredential>][-CertificateThumbprint <String>] [-UseSSL][-ComputerName <string>][-LogSizeLimitInMBs<UInt32>] [-LogTimeLimitInDays<UInt32>]
```

A következő táblázat a parancs egyes paramétereinek leírásait tartalmazza `Install-NetworkControllerCluster` .
  
|Paraméter|Leírás|
|-------------|---------------|
|ClusterAuthentication|A **ClusterAuthentication** paraméter határozza meg a csomópontok közötti kommunikáció biztonságossá tételéhez használt hitelesítési típust, valamint a hálózati vezérlő szolgáltatások közötti adatforgalom titkosítására is használható. A támogatott értékek a következők: **Kerberos**, **X509** és **none**. A Kerberos-hitelesítés tartományi fiókokat használ, és csak akkor használható, ha a hálózati vezérlő csomópontjai tartományhoz csatlakoznak. Ha X509-alapú hitelesítést ad meg, meg kell adnia egy tanúsítványt a NetworkControllerNode objektumban. Emellett a parancs futtatása előtt manuálisan kell kiépíteni a tanúsítványt.|
|ManagementSecurityGroup|A **ManagementSecurityGroup** paraméter a felügyeleti parancsmagok távoli számítógépről való futtatására jogosult felhasználókat tartalmazó biztonsági csoport nevét adja meg. Ez csak akkor alkalmazható, ha a ClusterAuthentication Kerberos. Meg kell adnia egy tartományi biztonsági csoportot, és nem egy biztonsági csoportot a helyi számítógépen.|
|Csomópont|A **Node** paraméter a **New-NetworkControllerNodeObject** parancs használatával létrehozott hálózati vezérlő csomópontok listáját adja meg.|
|DiagnosticLogLocation|A **DiagnosticLogLocation** paraméter határozza meg azt a megosztási helyet, ahol a rendszer rendszeresen feltölti a diagnosztikai naplókat. Ha nem ad meg értéket ehhez a paraméterhez, a rendszer az egyes csomópontokon helyileg tárolja a naplókat. A naplókat a rendszer helyileg tárolja a%systemdrive%\Windows\tracing\SDNDiagnostics. mappában. A fürtök naplófájljai helyileg tárolódnak a%systemdrive%\ProgramData\Microsoft\Service Fabric\log\Traces.|
|LogLocationCredential|A **LogLocationCredential** paraméter adja meg azokat a hitelesítő adatokat, amelyek szükségesek a naplók tárolására szolgáló megosztási hely eléréséhez.|
|CredentialEncryptionCertificate|A **CredentialEncryptionCertificate** paraméter határozza meg azt a tanúsítványt, amelyet a hálózati vezérlő használ a hálózati vezérlő bináris fájljainak és a **LogLocationCredential**eléréséhez használt hitelesítő adatok titkosításához, ha meg van adva. A parancs futtatása előtt a tanúsítványt a hálózati vezérlő összes csomópontján kell kiépíteni, és ugyanazt a tanúsítványt az összes fürtcsomóponton regisztrálni kell. Ha ezt a paramétert használja a hálózati vezérlő bináris fájljainak és naplóinak a használatára, éles környezetekben ajánlott. A paraméter nélkül a hitelesítő adatok tárolása tiszta szövegként történik, és a jogosulatlan felhasználók nem használhatják azokat.|
|Hitelesítő adat|Ezt a paramétert csak akkor kell megadni, ha távoli számítógépről futtatja ezt a parancsot. A **hitelesítőadat** -paraméter egy olyan felhasználói fiókot határoz meg, amely jogosult a parancs futtatására a célszámítógépen.|
|CertificateThumbprint|Ezt a paramétert csak akkor kell megadni, ha távoli számítógépről futtatja ezt a parancsot. A **CertificateThumbprint** paraméter megadja egy olyan felhasználói fiók digitális nyilvánoskulcs-tanúsítványát (X509), amely engedéllyel rendelkezik a parancs célszámítógépen való futtatásához.|
|UseSSL|Ezt a paramétert csak akkor kell megadni, ha távoli számítógépről futtatja ezt a parancsot. A **UseSSL** paraméter a távoli számítógéppel létesített kapcsolat létesítéséhez használt SSL (SSL) protokollt adja meg. Alapértelmezés szerint a rendszer nem használja az SSL-t.|
|ComputerName|A **számítógépnév** paraméter határozza meg azt a hálózati vezérlő csomópontot, amelyen a parancs fut. Ha nem ad meg értéket ehhez a paraméterhez, a rendszer alapértelmezés szerint a helyi számítógépet használja.|
|LogSizeLimitInMBs|Ez a paraméter határozza meg a naplófájl maximális méretét MB-ban, amelyet a hálózati vezérlő tárolhat. A naplók körkörös módon vannak tárolva. Ha a DiagnosticLogLocation van megadva, a paraméter alapértelmezett értéke 40 GB. Ha a DiagnosticLogLocation nincs megadva, a rendszer a naplófájlokat a hálózati vezérlő csomópontjain tárolja, a paraméter alapértelmezett értéke pedig 15 GB.|
|LogTimeLimitInDays|Ez a paraméter határozza meg azt az időtartamot (nap), ameddig a rendszer a naplókat tárolja. A naplók körkörös módon vannak tárolva. A paraméter alapértelmezett értéke 3 nap.|

## <a name="configure-the-network-controller-application"></a>A hálózati vezérlő alkalmazás konfigurálása

A hálózati vezérlő alkalmazás konfigurálásához írja be a következő parancsot. Győződjön meg arról, hogy minden, a telepítéshez megfelelő paraméter értékeit használja.

```powershell
Install-NetworkController -Node <NetworkControllerNode[]> -ClientAuthentication <ClientAuthentication>  [-ClientCertificateThumbprint <string[]>]  [-ClientSecurityGroup <string>] -ServerCertificate <X509Certificate2> [-RESTIPAddress <String>] [-RESTName <String>] [-Credential <PSCredential>][-CertificateThumbprint <String> ] [-UseSSL]
```

A következő táblázat a parancs egyes paramétereinek leírásait tartalmazza `Install-NetworkController` .

|Paraméter|Leírás|
|-------------|---------------|
|Alkalmasnak|A **alkalmasnak** paraméter határozza meg a REST és a hálózati vezérlő közötti kommunikáció biztonságossá tételéhez használt hitelesítési típust. A támogatott értékek a következők: **Kerberos**, **X509** és **none**. A Kerberos-hitelesítés tartományi fiókokat használ, és csak akkor használható, ha a hálózati vezérlő csomópontjai tartományhoz csatlakoznak. Ha X509-alapú hitelesítést ad meg, meg kell adnia egy tanúsítványt a NetworkControllerNode objektumban. Emellett a parancs futtatása előtt manuálisan kell kiépíteni a tanúsítványt.|
|Csomópont|A **Node** paraméter a **New-NetworkControllerNodeObject** parancs használatával létrehozott hálózati vezérlő csomópontok listáját adja meg.|
|ClientCertificateThumbprint|Ezt a paramétert csak akkor kell megadni, ha tanúsítványalapú hitelesítést használ a hálózati vezérlő ügyfelei számára. A **ClientCertificateThumbprint** paraméter a irányú rétegben lévő ügyfeleknek beléptetett tanúsítvány ujjlenyomatát adja meg.|
|ServerCertificate|A **ServerCertificate** paraméter határozza meg azt a tanúsítványt, amelyet a hálózati vezérlő használ az ügyfelek identitásának igazolására. A kiszolgálói tanúsítványnak tartalmaznia kell a kiszolgálói hitelesítés célját a kibővített kulcshasználat bővítményekben, és az ügyfelek által megbízhatónak tartott HITELESÍTÉSSZOLGÁLTATÓ számára ki kell adni a hálózati vezérlőt.|
|RESTIPAddress|Nem kell megadnia a **RESTIPAddress** értékét a hálózati vezérlő egyetlen csomópontos üzembe helyezésével. Több csomópontos üzemelő példányok esetén a **RESTIPAddress** paraméter a REST végpont IP-címét adja meg CIDR jelöléssel. Például: 192.168.1.10/24. A **ServerCertificate** tulajdonos neve értékének fel kell oldania a **RESTIPAddress** paraméter értékét. Ezt a paramétert meg kell adni a több csomópontos hálózati vezérlő összes üzemelő példányához, ha az összes csomópont ugyanazon az alhálózaton van. Ha a csomópontok különböző alhálózatokon vannak, akkor a **RESTIPAddress**használata helyett a **előkészítésekor** paramétert kell használnia.|
|RestName|Nem kell megadnia a **előkészítésekor** értékét a hálózati vezérlő egyetlen csomópontos üzembe helyezésével. A **előkészítésekor** értékének csak akkor kell megadnia, ha több csomópontos üzemelő példányok rendelkeznek különböző alhálózatokon lévő csomópontokkal. Több csomópontos üzemelő példányok esetén a **előkészítésekor** paraméter a hálózati vezérlő FÜRTJÉNEK teljes tartománynevét adja meg.|
|ClientSecurityGroup|A **ClientSecurityGroup** paraméter azt a Active Directory biztonsági csoport nevét adja meg, amelynek tagjai a hálózati vezérlő ügyfelei. Ezt a paramétert csak akkor kell megadni, ha Kerberos-hitelesítést használ a **alkalmasnak**. A biztonsági csoportnak tartalmaznia kell azokat a fiókokat, amelyekről a REST API-k hozzáférnek, és létre kell hoznia a biztonsági csoportot, és hozzá kell adnia a tagokat a parancs futtatása előtt.|
|Hitelesítő adat|Ezt a paramétert csak akkor kell megadni, ha távoli számítógépről futtatja ezt a parancsot. A **hitelesítőadat** -paraméter egy olyan felhasználói fiókot határoz meg, amely jogosult a parancs futtatására a célszámítógépen.|
|CertificateThumbprint|Ezt a paramétert csak akkor kell megadni, ha távoli számítógépről futtatja ezt a parancsot. A **CertificateThumbprint** paraméter megadja egy olyan felhasználói fiók digitális nyilvánoskulcs-tanúsítványát (X509), amely engedéllyel rendelkezik a parancs célszámítógépen való futtatásához.|
|UseSSL|Ezt a paramétert csak akkor kell megadni, ha távoli számítógépről futtatja ezt a parancsot. A **UseSSL** paraméter a távoli számítógéppel létesített kapcsolat létesítéséhez használt SSL (SSL) protokollt adja meg. Alapértelmezés szerint a rendszer nem használja az SSL-t.|

A hálózati vezérlő alkalmazás konfigurációjának befejezése után a hálózati vezérlő üzembe helyezése befejeződött.

## <a name="network-controller-deployment-validation"></a>Hálózati vezérlő üzembe helyezésének ellenőrzése

A hálózati vezérlő üzembe helyezésének ellenőrzéséhez adjon hozzá egy hitelesítő adatot a hálózati vezérlőhöz, majd kérje le a hitelesítő adatokat.

Ha a Kerberost használja `ClientAuthentication` típusként, a művelet végrehajtásához a létrehozott **ClientSecurityGroup** csoport tagjának kell lennie.

1. Ha az ügyfélszámítógépen a Kerberost használja `ClientAuthentication` típusként, jelentkezzen be egy olyan felhasználói fiókkal, amely tagja a **ClientSecurityGroup** csoportnak.

1. A PowerShell-ablakba írja be következő parancsokat: Győződjön meg arról, hogy minden, a telepítéshez megfelelő paraméter értékeit használja.

    ```powershell
    $cred=New-Object Microsoft.Windows.Networkcontroller.credentialproperties
    $cred.type="usernamepassword"
    $cred.username="admin"
    $cred.value="abcd"

    New-NetworkControllerCredential -ConnectionUri "https://networkcontroller"-Properties $cred -ResourceId "cred1"
    ```

1. A hálózati vezérlőhöz hozzáadott hitelesítő adat lekéréséhez írja be a következő parancsot. Győződjön meg arról, hogy minden, a telepítéshez megfelelő paraméter értékeit használja.

    ```powershell
    Get-NetworkControllerCredential -ConnectionUri https://networkcontroller -ResourceId cred1  
    ```

1. Tekintse át a parancs kimenetét, amelynek az alábbi példában szereplő kimenethez hasonlóan kell szerepelnie.

    ```powershell
    Tags                   :
    ResourceRef     : /credentials/cred1
    CreatedTime    : 1/1/0001 12:00:00 AM
    InstanceId        : e16ffe62-a701-4d31-915e-7234d4bc5a18
    Etag                  : W/"1ec59631-607f-4d3e-ac78-94b0822f3a9d"
    ResourceMetadata :
    ResourceId       : cred1
    Properties       : Microsoft.Windows.NetworkController.CredentialProperties
    ```

    > [!NOTE]
    > A parancs futtatásakor `Get-NetworkControllerCredential` hozzárendelheti a parancs kimenetét egy változóhoz a pont operátor használatával a hitelesítő adatok tulajdonságainak listázásához. Például: `$cred.Properties`.

## <a name="additional-powershell-commands-for-network-controller"></a>További PowerShell-parancsok a hálózati vezérlőhöz

A hálózati vezérlő üzembe helyezése után a PowerShell-parancsok használatával kezelheti és módosíthatja a központi telepítést. A következőkben néhány olyan módosítást hajt végre, amelyeket az üzembe helyezése során végezhet el.

- A hálózati vezérlő csomópont-, fürt-és Alkalmazásbeállítások módosítása

- A hálózati vezérlő fürt és az alkalmazás eltávolítása

- A hálózati vezérlő fürtcsomópontok kezelése, beleértve a csomópontok hozzáadását, eltávolítását, engedélyezését és letiltását.

A következő táblázat a feladatok végrehajtásához használható PowerShell-parancsok szintaxisát tartalmazza.

|Feladat|Parancs|Syntax|
|--------|-------|----------|
|A hálózati vezérlő fürtjének beállításainak módosítása|Set-NetworkControllerCluster|`Set-NetworkControllerCluster [-ManagementSecurityGroup <string>][-Credential <PSCredential>] [-computerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Hálózati vezérlő alkalmazás beállításainak módosítása|Set-NetworkController|`Set-NetworkController [-ClientAuthentication <ClientAuthentication>] [-Credential <PSCredential>] [-ClientCertificateThumbprint <string[]>] [-ClientSecurityGroup <string>] [-ServerCertificate <X509Certificate2>] [-RestIPAddress <String>] [-ComputerName <String>][-CertificateThumbprint <String> ] [-UseSSL]`
|Hálózati vezérlő csomópont-beállításainak módosítása|Set-NetworkControllerNode|`Set-NetworkControllerNode -Name <string> > [-RestInterface <string>] [-NodeCertificate <X509Certificate2>] [-Credential <PSCredential>] [-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Hálózati vezérlő diagnosztikai beállításainak módosítása|Set-NetworkControllerDiagnostic|`Set-NetworkControllerDiagnostic [-LogScope <string>] [-DiagnosticLogLocation <string>] [-LogLocationCredential <PSCredential>] [-UseLocalLogLocation] >] [-LogLevel <loglevel>][-LogSizeLimitInMBs <uint32>] [-LogTimeLimitInDays <uint32>] [-Credential <PSCredential>] [-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|A hálózati vezérlő alkalmazás eltávolítása|Uninstall-NetworkController|`Uninstall-NetworkController [-Credential <PSCredential>][-ComputerName <string>] [-CertificateThumbprint <String> ] [-UseSSL]`
|A hálózati vezérlő fürt eltávolítása|Uninstall-NetworkControllerCluster|`Uninstall-NetworkControllerCluster [-Credential <PSCredential>][-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Csomópont hozzáadása a hálózati vezérlő fürthöz|Add-NetworkControllerNode|`Add-NetworkControllerNode -FaultDomain <String> -Name <String> -RestInterface <String> -Server <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-Force] [-NodeCertificate <X509Certificate2> ] [-PassThru] [-UseSsl]`
|Hálózati vezérlő fürt csomópontjának letiltása|Disable-NetworkControllerNode|`Disable-NetworkControllerNode -Name <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-PassThru] [-UseSsl]`
|Hálózati vezérlő fürt csomópontjának engedélyezése|Enable-NetworkControllerNode|`Enable-NetworkControllerNode -Name <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-PassThru] [-UseSsl]`
|Hálózati vezérlő csomópontjának eltávolítása fürtből|Remove-NetworkControllerNode|`Remove-NetworkControllerNode [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-Force] [-Name <String> ] [-PassThru] [-UseSsl]`

További információ: a Windows PowerShell dokumentációja a hálózati vezérlőhöz: [NetworkController](/powershell/module/networkcontroller/?view=win10-ps).

## <a name="sample-network-controller-configuration-script"></a>Példa hálózati vezérlő konfigurációs parancsfájlra

A következő minta konfigurációs szkript bemutatja, hogyan hozhat létre több csomópontos hálózati vezérlő fürtöt, és hogyan telepítheti a hálózati vezérlő alkalmazást. Emellett a `$cert` változó kiválasztja a tanúsítványokat a helyi számítógép tanúsítványtárolójában, amely megfelel a tulajdonos neve "networkController.contoso.com" karakterláncának.

```powershell
$a = New-NetworkControllerNodeObject -Name "Node1" -Server "NCNode1.contoso.com" -FaultDomain "fd:/rack1/host1" -RestInterface Internal
$b = New-NetworkControllerNodeObject -Name "Node2" -Server "NCNode2.contoso.com" -FaultDomain "fd:/rack1/host2" -RestInterface Internal
$c = New-NetworkControllerNodeObject -Name "Node3" -Server "NCNode3.contoso.com" -FaultDomain "fd:/rack1/host3" -RestInterface Internal

$cert= get-item Cert:\LocalMachine\My | get-ChildItem | where {$_.Subject -imatch "networkController.contoso.com" }

Install-NetworkControllerCluster -Node @($a,$b,$c)  -ClusterAuthentication Kerberos -DiagnosticLogLocation \\share\Diagnostics - ManagementSecurityGroup Contoso\NCManagementAdmins -CredentialEncryptionCertificate $cert  
Install-NetworkController -Node @($a,$b,$c) -ClientAuthentication Kerberos -ClientSecurityGroup Contoso\NCRESTClients -ServerCertificate $cert -RestIpAddress 10.0.0.1/24
```

## <a name="next-steps"></a>Következő lépések

Ha nem a Kerberost használja a hálózati vezérlő üzembe helyezéséhez, telepítenie kell a tanúsítványokat. További információ: [üzembe helyezés utáni lépések a hálózati vezérlőhöz](/windows-server/networking/sdn/technologies/network-controller/post-deploy-steps-nc).