---
title: Gyors útmutató egy Azure Kubernetes-szolgáltatás gazdagépének beállításához Azure Stack HCI-ben a Windows PowerShell használatával
description: Ismerje meg, hogyan állíthat be egy Azure Kubernetes Service hostt Azure Stack HCI-ben a Windows PowerShell használatával
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 4211ec50ef0ea24ffb55f14791101c5d266ede2e
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612556"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Gyors útmutató: Azure Kubernetes Service Host beállítása Azure Stack HCI-ben a PowerShell használatával

> A következőkre vonatkozik: Azure Stack HCI, Windows Server 2019 Datacenter

Ebből a rövid útmutatóból megtudhatja, hogyan állíthat be egy Azure Kubernetes Service-gazdagépet a PowerShell használatával. A Windows felügyeleti központ helyett használja a [Windows felügyeleti központ beállítása](setup.md)című témakört.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik a következők valamelyikével:
 - 2-4 Node Azure Stack HCI-fürt
 - Windows Server 2019 Datacenter feladatátvevő fürt
 - Egyetlen csomópontos Windows Server 2019 Datacenter 
 
Mielőtt elkezdené, győződjön meg arról, hogy teljesítette a [rendszerkövetelmények](.\system-requirements.md) lapon szereplő összes előfeltételt. 
**Javasoljuk, hogy egy 2-4 csomópontot Azure Stack HCI-fürtöt.** Ha nem rendelkezik a fentiekkel, kövesse az [Azure stack HCI regisztrációs oldalon](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)található utasításokat.    

   > [!IMPORTANT]
   > Az Azure Kubernetes szolgáltatás Azure Stack HCI-ben való eltávolításakor lásd: az [Azure Kubernetes szolgáltatás eltávolítása Azure stack HCI](#remove-azure-kubernetes-service-on-azure-stack-hci) -ben, és körültekintően kövesse az utasításokat. 

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>1. lépés: töltse le és telepítse a AksHci PowerShell-modult

Töltse le az alkalmazást `AKS-HCI-Public-Preview-Dec-2020` az [Azure Kubernetes szolgáltatásból Azure stack HCI regisztrációs oldalon](https://aka.ms/AKS-HCI-Evaluate). A zip-fájl `AksHci.Powershell.zip` tartalmazza a PowerShell-modult.

Ha korábban telepítette az Azure Kubernetes szolgáltatást a Azure Stack HCI-be a PowerShell vagy a Windows felügyeleti központ használatával, két telepítési folyamat van az új PowerShell-modulhoz:
 - Végezze el a PowerShell-modul tiszta telepítését, így a tiszta rendszerrel indul, és a korábban üzembe helyezett számítási feladatok törlődnek. A tiszta telepítés végrehajtásához lépjen a 1,1. lépésre.
 - Frissítse a PowerShell-modult, ha meg szeretné őrizni a rendszerét és a munkaterheléseit. A PowerShell-modul frissítéséhez lépjen a 1,2. lépésre.

### <a name="step-11-clean-install-of-the-akshci-powershell-module"></a>1,1. lépés: a AksHci PowerShell-modul tiszta telepítése

A folytatás előtt futtassa a következő parancsot.
   ```powershell
   Uninstall-AksHci
   ```

**Az összes PowerShell-ablak lezárása.** Törölje az elérési úton található AksHci, AksHci. UI, MOC és MSK8sDownloadAgent meglévő könyvtárait `%systemdrive%\program files\windowspowershell\modules` . A meglévő könyvtárak törlése után kibonthatja az új zip-fájl tartalmát. Győződjön meg arról, hogy a zip-fájlt a megfelelő helyen () szeretné kibontani `%systemdrive%\program files\windowspowershell\modules` . Ezután futtassa a következő parancsokat.

   ```powershell
   Import-Module AksHci
   ```

Zárjunk be minden PowerShell-ablakot újra, majd nyissa meg újra a felügyeleti munkamenetet, és folytassa a 1,3-es lépéssel a frissített PowerShell-modul érvényesítése

### <a name="step-12-upgrade-the-akshci-powershell-module"></a>1,2. lépés: a AksHci PowerShell-modul frissítése

**Az összes PowerShell-ablak lezárása.** Törölje az elérési úton található AksHci, AksHci. UI, MOC és MSK8sDownloadAgent meglévő könyvtárait `%systemdrive%\program files\windowspowershell\modules` . A könyvtárak eltávolítása után kibonthatja az új zip-fájl tartalmát. Győződjön meg arról, hogy a zip-fájlt a megfelelő helyen () szeretné kibontani `%systemdrive%\program files\windowspowershell\modules` . Ezután futtassa a következő parancsokat.

   ```powershell
   Import-Module AksHci
   ```
  
A fenti parancsok futtatása után zárjunk be minden PowerShell-ablakot, majd nyissa meg újra a felügyeleti munkamenetet a PowerShell-modul frissítésének ellenőrzéséhez az alábbiakban részletezett módon, majd futtassa a `Update-AksHci` parancsot a dokumentum későbbi részében leírtak szerint.

## <a name="step-13-validate-upgraded-powershell-module"></a>1,3. lépés: a frissített PowerShell-modul ellenőrzése

**Zárjunk be minden PowerShell-ablakot** , és nyissa meg újra az új felügyeleti munkamenetet, és ellenőrizze, hogy rendelkezik-e a PowerShell-modul legújabb verziójával.  

   ```powershell
   Get-Command -Module AksHci
   ```
 
**Kimeneti**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.12     AksHci
Function        Get-AksHciCluster                                  0.2.12     AksHci
Function        Get-AksHciConfig                                   0.2.12     AksHci
Function        Get-AksHciCredential                               0.2.12     AksHci
Function        Get-AksHciKubernetesVersion                        0.2.12     AksHci
Function        Get-AksHciLogs                                     0.2.12     AksHci
Function        Get-AksHciUpdates                                  0.2.12     AksHci
Function        Get-AksHciVersion                                  0.2.12     AksHci
Function        Get-AksHciVmSize                                   0.2.12     AksHci
Function        Install-AksHci                                     0.2.12     AksHci
Function        Install-AksHciAdAuth                               0.2.12     AksHci
Function        Install-AksHciArcOnboarding                        0.2.12     AksHci
Function        New-AksHciCluster                                  0.2.12     AksHci
Function        Remove-AksHciCluster                               0.2.12     AksHci
Function        Restart-AksHci                                     0.2.12     AksHci
Function        Set-AksHciClusterNodeCount                         0.2.12     AksHci
Function        Set-AksHciConfig                                   0.2.12     AksHci
Function        Uninstall-AksHci                                   0.2.12     AksHci
Function        Uninstall-AksHciAdAuth                             0.2.12     AksHci
Function        Uninstall-AksHciArcOnboarding                      0.2.12     AksHci
Function        Update-AksHci                                      0.2.12     AksHci
Function        Update-AksHciCluster                               0.2.12     AksHci
```

## <a name="step-2-prepare-your-machines-for-deployment"></a>2. lépés: a gép (ek) előkészítése az üzembe helyezéshez

Futtasson ellenőrzéseket minden fizikai csomóponton, és ellenőrizze, hogy az összes követelmény teljesül-e az Azure Kubernetes Service Azure Stack HCI-on való telepítéséhez.

Nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot.

   ```powershell
   Initialize-AksHciNode
   ```

Az ellenőrzések befejezése után a "kész" szöveg jelenik meg zöld színnel.

## <a name="step-3-configure-your-deployment"></a>3. lépés: az üzemelő példány konfigurálása

Adja meg az Azure Kubernetes Service Host konfigurációs beállításait. **Ha 2-4-Azure Stack csomópontra telepíti a HCI-fürtöt vagy egy Windows Server 2019 Datacenter feladatátvevő fürtöt, meg kell adnia a `imageDir` és a `cloudConfigLocation` paramétereket.** Egyetlen csomópontos Windows Server 2019 Datacenter esetén az összes paraméter nem kötelező, és az alapértelmezett értékre van állítva. Az optimális teljesítmény érdekében azonban javasoljuk, hogy **egy 2-4 csomópontot használjon Azure stack HCI-fürt üzembe helyezéséhez.**

Konfigurálja az üzemelő példányt a következő paranccsal.

   ```powershell
   Set-AksHciConfig [-imageDir <String>]
                    [-cloudConfigLocation <String>]
                    [-nodeConfigLocation <String>]
                    [-vnetName <String>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-sshPublicKey <String>]
                    [-vipPoolStartIp <String>]
                    [-vipPoolEndIp <String>]
                    [-macPoolStart <String>]
                    [-macPoolEnd <String>]
                    [-vlanID <int>]
                    [-kvaLoadBalancerType {unstacked_haproxy, stacked_kube_vip}]
                    [-kvaControlPlaneEndpoint <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerNoProxy <String>]
                    [-proxyServerCredential <PSCredential>]
                    [-cloudServiceCidr <String>]
                    [-workingDir <String>]
                    [-version <String>]
                    [-vnetType <String>]
                    [-nodeAgentPort <int>]
                    [-nodeAgentAuthorizerPort <int>]
                    [-clusterRoleName <String>]
                    [-cloudLocation <String>]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="example"></a>Példa

Üzembe helyezés egy 2-4 csomópontos fürtön DHCP hálózatkezeléssel:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
   ```

Üzembe helyezés virtuális IP-készlettel:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vipPoolStartIp 10.0.0.20 -vipPoolEndIp 10.0.0.80
   ```

Üzembe helyezés a terheléselosztó használatával `stacked_kube_vip` :

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -kvaLoadBalancerType stacked_kube_vip -kvaControlPlaneEndpoint 10.0.1.10
   ```

Üzembe helyezés proxykiszolgáló használatával:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
   ```  

### <a name="optional-parameters"></a>Választható paraméterek

`-imageDir`

Annak a könyvtárnak az elérési útja, amelyen az Azure Kubernetes szolgáltatása Azure Stack HCI-ben tárolja a VHD-lemezképeit. Az alapértelmezett érték `%systemdrive%\AksHciImageStore` az egycsomópontos üzemelő példányok esetében. *Több csomópontos üzemelő példányok esetén ezt a paramétert meg kell adni*. Az elérési útnak egy megosztott tárolási elérési útra kell mutatnia, például  `C:\ClusterStorage\Volume2\ImageStore`   vagy egy SMB-megosztásra, például:  `\\FileShare\ImageStore` .

`-cloudConfigLocation`

Az a hely, ahol a felhőalapú ügynök tárolni fogja a konfigurációját. Az alapértelmezett érték `%systemdrive%\wssdcloudagent` az egycsomópontos üzemelő példányok esetében. A hely lehet ugyanaz, mint a fenti elérési út  `-imageDir` .*Több csomópontos üzemelő példányok esetén ezt a paramétert meg kell adni*. Az elérési útnak egy megosztott tárolási elérési útra kell mutatnia, például  `C:\ClusterStorage\Volume2\ImageStore`   vagy egy SMB-megosztásra, például:  `\\FileShare\ImageStore` . A helynek egy magasan elérhető megosztáson kell lennie, hogy a tárterület mindig elérhető legyen.

`-nodeConfigLocation`

Az a hely, ahol a csomópont-ügynökök tárolni fogják a konfigurációt. Minden csomóponthoz tartozik egy csomópont-ügynök, így annak konfigurációja a helyi. Ennek a helynek helyi elérési útnak kell lennie. Alapértelmezés szerint `%systemdrive%\programdata\wssdagent` az összes üzemelő példánynál.

`-vnetName`

Annak a virtuális kapcsolónak a neve, amelyhez a virtuális gépeket hozzá szeretné kapcsolni. Ha már rendelkezik külső kapcsolóval a gazdagépen, akkor ide kell adnia a kapcsoló nevét. A kapcsoló akkor jön létre, ha nem létezik.Az alapértelmezett érték a "külső" név.  

`-controlPlaneVmSize`

A vezérlő síkja számára létrehozandó virtuális gép mérete. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

`-loadBalancerVmSize`

A Load Balancer virtuális gépek számára létrehozandó virtuális gép mérete. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

`-sshPublicKey`

Egy nyilvános SSH-kulcsfájl elérési útja. A nyilvános kulcs használatával bejelentkezhet az Azure Kubernetes szolgáltatás által létrehozott bármelyik virtuális gépre Azure Stack HCI-telepítésre. Ha saját nyilvános SSH-kulcsával rendelkezik, itt adhatja át a helyét. Ha nincs megadva kulcs, a rendszer a következőt keresi: `%systemdrive%\akshci\.ssh\akshci_rsa.pub` . Ha a fájl nem létezik, a rendszer létrehoz és használ egy SSH-kulcspárt a fenti helyen.

`-vipPoolStartIp`

Ha VIP-készleteket használ az üzemelő példányhoz, ez a paraméter határozza meg a készlet hálózati kezdetét. A hosszú élettartamú üzemelő példányokhoz VIP-készleteket kell használnia annak biztosítására, hogy az IP-címek készlete konzisztens maradjon. Ez akkor hasznos, ha olyan számítási feladatokkal rendelkezik, amelyeknek mindig elérhetőnek kell lenniük. Az alapértelmezett érték a none.

`-vipPoolEndIp`

Ha VIP-készleteket használ az üzemelő példányhoz, ez a paraméter határozza meg a készlet hálózatának végét. A hosszú élettartamú üzemelő példányokhoz VIP-készleteket kell használnia annak biztosítására, hogy az IP-címek készlete konzisztens maradjon. Ez akkor hasznos, ha olyan számítási feladatokkal rendelkezik, amelyeknek mindig elérhetőnek kell lenniük. Az alapértelmezett érték a none.

`-macPoolStart` 

Ezzel a beállítással megadhatja az Azure Kubernetes Service Host VM-hez használni kívánt MAC-készlet MAC-címe kezdetét. A MAC-címek szintaxisa megköveteli, hogy az első bájt legkisebb jelentős részének mindig 0 legyen, és az első bájtnak mindig páros számnak kell lennie (azaz 00, 02, 04, 06...). Egy tipikus MAC-címnek A következőképpen nézhet ki: 02:1E: 2B: 78:00:00. A MAC-készletek használata hosszú élettartamú központi telepítésekhez, hogy a hozzárendelt MAC-címek konzisztensek legyenek. Ez akkor hasznos, ha követelmény, hogy a virtuális gépek egyedi MAC-címmel rendelkezzenek. Az alapértelmezett érték a none.

`-macPoolEnd`

Ezzel a beállítással adható meg az Azure Kubernetes Service Host virtuális géphez használni kívánt MAC-készlet MAC-címe. A MAC-címek szintaxisa megköveteli, hogy az első bájt legkisebb jelentős részének mindig 0 legyen, és az első bájtnak mindig páros számnak kell lennie (azaz 00, 02, 04, 06...). Az átadott címnek első bájtjának meg kell `-macPoolEnd` egyeznie a-ként átadott címek első bájtjának értékével `-macPoolStart` . A MAC-készletek használata hosszú élettartamú központi telepítésekhez, hogy a hozzárendelt MAC-címek konzisztensek legyenek. Ez akkor hasznos, ha követelmény, hogy a virtuális gépek egyedi MAC-címmel rendelkezzenek. Az alapértelmezett érték a none.

`-vlandID`

Ez a hálózati VLAN-azonosító megadására használható. Az Azure Kubernetes Service Host és a Kubernetes cluster VM hálózati adapterek a megadott VLAN-AZONOSÍTÓval lesznek címkézve. Ezt akkor kell használni, ha van egy adott VLAN-azonosító, amelyet meg kell adni a megfelelő kapcsolat beszerzéséhez. Az alapértelmezett érték a none.

`-kvaLoadBalancerType`

Ez a következőt veszi igénybe: `unstacked_haproxy` vagy `stacked_kube_vip` . `unstacked_haproxy` az alapértelmezett beállítás, ha egy különálló terheléselosztó virtuális gép üzembe helyezése az Azure Kubernetes Service Host API Server-végpontjának HAProxy történik. `stacked_kube_vip`Az Azure Kubernetes Service Host [Kubevip](https://kube-vip.io/)terheléselosztó-megoldás. Lehetővé teszi, hogy statikus IP-címet határozzon meg a gazdagépen a vezérlési sík csomópontjain lévő lebegőpontos IP-címeken, hogy az API-kiszolgáló az IP-n keresztül is elérhető legyen. Ha ezt a beállítást választja, meg kell adnia a statikus IP-címet a `kvaControlPlaneEndpoint` paraméterben, és nincs telepítve külön terheléselosztó virtuális gép.

`stacked_kube_vip` IP-címet igényel, és a memória, a processzor és a központi telepítés idejének megtakarításával több erőforrás-barát. Ha nem rendelkezik lebegőpontos IP-címmel, használja a következőt: `unstacked_haproxy` . Az utóbbi beállításhoz egy terheléselosztó virtuális gép szükséges. 

`-kvaControlPlaneEndpoint`

Ez határozza meg az Azure Kubernetes Service Host API-kiszolgáló címéhez használt statikus IP-címet, ha a `kvaLoadBalancerType` paraméter értéke `stacked_kube_vip` . Ha `stacked_kube_vip` a használatban van, ezt a paramétert meg kell adni.

`-proxyServerHTTP`

Ez egy proxykiszolgáló URI-t biztosít, amelyet az összes olyan összetevőnek használnia kell, amelynek HTTP-végpontokat kell elérnie. Az URI formátuma tartalmazza az URI-sémát, a kiszolgálónevet és a portot (azaz: https://server.com:8888) . Az alapértelmezett érték a none.

`-proxyServerHTTPS`

Ez egy proxykiszolgáló URI-t biztosít, amelyet az összes olyan összetevőnek használnia kell, amelyeknek a HTTPS-végpontokat kell elérniük. Az URI formátuma tartalmazza az URI-sémát, a kiszolgálónevet és a portot (azaz: https://server.com:8888) . Az alapértelmezett érték a none.

`-proxyServerNoProxy`

Ez egy vesszővel tagolt címtartomány, amely mentesül a proxy alól. Az alapértelmezett érték `localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16`. Ez kizárja a localhost forgalmat (localhost, 127.0.0.1), a belső Kubernetes-szolgáltatás forgalmát (. SVC), a Kubernetes szolgáltatás CIDR (10.96.0.0/12) és a Kubernetes POD CIDR (10.244.0.0/16) a proxykiszolgálót. Ezzel a paraméterrel további alhálózati tartományokat vagy nevekre vonatkozó kivételeket is hozzáadhat. **A paraméter beállításai nagyon fontosak, mert ha nem megfelelően van konfigurálva, előfordulhat, hogy a belső Kubernetes-fürt forgalmát váratlanul átirányítja a proxyhoz. Ez különféle hibákhoz vezethet a hálózati kommunikációban.**


`-proxyServerCredential`

Ez a Felhasználónév és a jelszó megadásával hitelesíti magát a HTTP/HTTPS-proxy kiszolgálókon. A (z `Get-Credential` ) segítségével létrehozható egy objektum, amelyet `PSCredential` át lehet adni ehhez a paraméterhez. Az alapértelmezett érték a none.

`-cloudServiceCidr`

Ezzel megadhatja a MOC CloudAgent szolgáltatáshoz hozzárendelni kívánt statikus IP/hálózati előtagot. Ezt az értéket a CIDR formátum használatával kell megadni. (Például: 192.168.1.2/16). Érdemes megadnia ezt annak biztosítására, hogy a hálózat minden fontos adata mindig elérhető legyen, mivel az IP-cím nem változik. Az alapértelmezett érték a none.

`-workingDir`

Ez egy munkakönyvtár a kis fájlok tárolására használt modul számára. Alapértelmezés szerint a `%PROGRAMFILES%\AksHci`   és a nem módosítható a legtöbb üzemelő példány esetében.Az alapértelmezett beállítás módosítása nem ajánlott. 

`-version`

Az Azure Kubernetes szolgáltatás azon verziója, Azure Stack HCI-re, amelyet telepíteni kíván. Az alapértelmezett érték a legújabb verzió. Az alapértelmezett beállítás módosítása nem ajánlott.

`-vnetType`

A kapcsolódáshoz vagy létrehozáshoz használandó virtuális kapcsoló típusa. Ez az alapértelmezett érték a "külső" kapcsoló típusa. Az alapértelmezett beállítás módosítása nem ajánlott.

`-nodeAgentPort`

Annak a TCP/IP-portnak a száma, amelyre a csomópont-ügynököknek figyelniük kell. Az alapértelmezett érték a 45000.Az alapértelmezett beállítás módosítása nem ajánlott. 

`-nodeAgentAuthorizerPort`

A csomópont-ügynökök által az engedélyezési porthoz használandó TCP/IP-portszám. Az alapértelmezett érték a 45001. Az alapértelmezett beállítás módosítása nem ajánlott.  

`-clusterRoleName`

Ez határozza meg a felhőalapú ügynök általános szolgáltatásként történő létrehozásakor használandó nevet a fürtön belül. Ez az alapértelmezett érték egy egyedi név, amely egy CA-előtaggal és egy GUID utótaggal rendelkezik (például: "CA-9e6eb299-bc0b-4f00-9fd7-942843820c26"). Az alapértelmezett beállítás módosítása nem ajánlott.

`-cloudLocation` 

Ez a paraméter egy egyéni Microsoft által üzemeltetett Felhőbeli hely nevét adja meg. Az alapértelmezett név: "MocLocation". Az alapértelmezett beállítás módosítása nem ajánlott.

`-skipHostLimitChecks`

Megkéri a parancsfájlt, hogy hagyja ki a szükséges ellenőrzéseket, hogy megerősítse a memóriát és a lemezterületet, mielőtt engedélyezi a telepítés folytatását. Ezt a beállítást nem ajánlott használni.

`-insecure`

Üzembe helyezi az Azure Kubernetes szolgáltatást olyan Azure Stack HCI-összetevőkön, mint a felhőalapú ügynök és a csomópont-ügynök (ek) nem biztonságos módban (nincs TLS-alapú biztonságos kapcsolat).Nem javasoljuk a nem biztonságos üzemmód használatát éles környezetekben.

`-skipUpdates`

Akkor használja ezt a jelzőt, ha ki szeretné hagyni az elérhető frissítéseket. Ezt a beállítást nem ajánlott használni.

`-forceDnsReplication`

A DNS-replikáció néhány rendszeren akár egy órát is igénybe vehet. Ennek hatására az üzemelő példány lassú lesz. Ha ezt a problémát tapasztalja, látni fogja, hogy a Install-AksHci egy hurokban lesznek beragadva. A probléma megkezdéséhez próbálja meg használni ezt a jelzőt. A `-forceDnsReplication` jelző nem garantált javítás. Ha a jelző mögötti logika nem sikerül, a rendszer elrejti a hibát, és a parancs úgy fog futni, mintha a jelző nem lett megadva. 

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Az Azure Kubernetes szolgáltatás alaphelyzetbe állítása Azure Stack HCI-konfiguráción

Az Azure Kubernetes szolgáltatás Azure Stack HCI-konfiguráción való alaphelyzetbe állításához futtassa a következő parancsokat. Ha ezt a parancsot saját maga futtatja, alaphelyzetbe állítja a konfigurációt az alapértelmezett értékekre.

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>4. lépés: új központi telepítés indítása

A telepítés konfigurálása után el kell indítania az üzembe helyezést. Ezzel telepíti az Azure Kubernetes szolgáltatást Azure Stack HCI-ügynökökre/-szolgáltatásokra és az Azure Kubernetes Service-gazdagépre.

Az üzembe helyezés megkezdéséhez futtassa a következő parancsot.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Az üzembe helyezett Azure Kubernetes Service-gazdagép ellenőrzése

Az Azure Kubernetes Service Host üzembe helyezésének biztosításához futtassa a következő parancsot. A Kubernetes-fürtöket ugyanezen parancs használatával is lekérheti a telepítés után.

```powershell
Get-AksHciCluster
```

**Kimeneti**
```

Name            : clustergroup-management
Version         : v1.18.8
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>5. lépés: a fürtök elérése a kubectl használatával

Az Azure Kubernetes Service Host vagy a Kubernetes-fürt kubectl használatával való eléréséhez futtassa a következő parancsot. Ez a megadott fürt kubeconfig-fájlját fogja használni a kubectl alapértelmezett kubeconfig-fájljához.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Példa

```powershell
Get-AksHciCredential -clusterName clustergroup-management
```

### <a name="required-parameters"></a>Kötelező paraméterek

`clusterName`

A fürt neve.

### <a name="optional-parameters"></a>Opcionális paraméterek

`outputLocation`

Az a hely, ahová le szeretné tölteni a kubeconfig. Az alapértelmezett szint a `%USERPROFILE%\.kube`.

## <a name="get-logs"></a>Naplók lekérése

Az összes hüvelyből származó naplók lekéréséhez futtassa a következő parancsot. Ez a parancs létrehoz egy nevű kimeneti tömörített mappát `akshcilogs` az elérési úton `C:\wssd\akshcilogs` .

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes Service legújabb verziójának frissítése Azure Stack HCI-on

Az Azure Kubernetes Service legújabb verziójának frissítéséhez Azure Stack HCI-ben futtassa a következő parancsot. A frissítés parancs csak akkor működik, ha telepítette a TOT kiadását. Nem fog működni az októberi kiadásnál régebbi verziókban. Ez a frissítési parancs frissíti az Azure Kubernetes Service hostt és a helyszíni Microsoft által üzemeltetett felhőalapú platformot. Ebben az előzetes kiadásban a Kubernetes és az AK-OS gazdagép operációsrendszer-verziója továbbra is változatlan marad. Ez a parancs nem frissíti a meglévő munkaterhelés-fürtöket. Az AK-gazdagép frissítése után létrehozott új munkaterhelés-fürtök eltérhetnek a meglévő munkaterhelés-fürtöktől a Windows-csomópont operációs rendszerének verziószáma és a Kubernetes verziója alapján.

   ```powershell
   Update-AksHci
   ```
   
Javasoljuk, hogy a munkaterhelési fürtöket azonnal frissítse a felügyeleti fürt frissítése után, hogy megakadályozza a Windows Server operációsrendszer-verziók nem támogatott Windows-csomópontokkal történő futtatását a Kubernetes-fürtökben. A munkaterhelés-fürt frissítéséhez látogasson el [a munkaterhelés-fürt frissítése](create-kubernetes-cluster-powershell.md)lehetőségre.

## <a name="restart-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes szolgáltatás újraindítása Azure Stack HCI-ben

Az Azure Kubernetes szolgáltatás újraindítása Azure Stack HCI-ben a rendszer eltávolítja az összes Kubernetes-fürtöt, ha van ilyen, és az Azure Kubernetes Service Host. Emellett eltávolítja az Azure Kubernetes szolgáltatást Azure Stack HCI-ügynökökön és-szolgáltatásokon a csomópontokból. Ezután visszakerül az eredeti telepítési folyamat lépéseire, amíg a gazdagép újra nem jön létre. Az Azure Kubernetes szolgáltatás Azure Stack HCI-konfiguráción keresztül, amelyet `Set-AksHciConfig` a és a letöltött VHDX-lemezképek is megőrzik.

A következő parancs futtatásával indítsa újra az Azure Kubernetes szolgáltatást a Azure Stack HCI-ben, a konfigurációs beállításokkal.

```powershell
Restart-AksHci
```

## <a name="reset-configuration-settings-and-reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Konfigurációs beállítások alaphelyzetbe állítása és az Azure Kubernetes szolgáltatás újratelepítése Azure Stack HCI-re

Ha az Azure Kubernetes szolgáltatást a különböző konfigurációs beállításokkal rendelkező Azure Stack HCI-re szeretné telepíteni, először futtassa a következő parancsot.

```powershell
Uninstall-AksHci
```

A fenti parancs futtatása után a konfigurációs beállításokat a következő parancs használatával módosíthatja. A paraméterek változatlanok maradnak a 3. lépésben leírtak szerint. Ha a parancsot megadott paraméterek nélkül futtatja, a paraméterek az alapértelmezett értékekre lesznek visszaállítva.

```powershell
Set-AksHciConfig
```

Miután módosította a konfigurációt a kívánt beállításokra, futtassa a következő parancsot a Azure Stack Kubernetes újratelepítéséhez Azure Stack HCI-ben.

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes szolgáltatás eltávolítása Azure Stack HCI-re

Az Azure Kubernetes Service Azure Stack HCI-ben való eltávolításához futtassa a következő parancsot. **Ha a PowerShell használatával távolít el egy Windows felügyeleti központú központi telepítést, a parancsot a jelzővel kell futtatnia `-Force` .**

```powershell
Uninstall-AksHci
```

A fenti parancs futtatása után a parancs futtatásával `Install-AksHci` telepítheti az Azure Kubernetes Service hostt ugyanazzal a konfigurációval, mint korábban. Ha módosítani szeretné a konfigurációt, futtassa `Set-AksHciConfig` a parancsot az install parancs futtatása előtt a kívánt módosításokkal.

Ha nem szeretné megőrizni a régi konfigurációt, futtassa a következő parancsot.

```powershell
Uninstall-AksHci -Force
```

Ha a PowerShell-parancsok olyan fürtön futnak, ahol a Windows felügyeleti központot korábban már telepítették, a PowerShell-modul ellenőrzi a Windows felügyeleti központ konfigurációs fájljának létezését. A Windows felügyeleti központ minden csomóponton elhelyezi a Windows felügyeleti központ konfigurációs fájlját. **Ha az Eltávolítás parancsot használja, és térjen vissza a Windows felügyeleti központba, futtassa a fenti eltávolítási parancsot a `-Force` jelzővel. Ha ez nem történik meg, a PowerShell és a Windows felügyeleti központ nem lesz szinkronban.**

## <a name="next-steps"></a>További lépések

- [Kubernetes-fürt létrehozása az alkalmazásaihoz](create-kubernetes-cluster-powershell.md)
