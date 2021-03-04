---
title: Set-AksHciConfig
author: jessicaguan
description: A Set-AksHciConfig PowerShell-parancs frissíti az Azure Kubernetes Service Host konfigurációjának beállításait.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 80a6fc50bbfcaf028d6c810bbef7e2d6b4508cab
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873980"
---
# <a name="set-akshciconfig"></a>Set-AksHciConfig

## <a name="synopsis"></a>Áttekintés
Az Azure Kubernetes Service Host konfigurációs beállításainak megadása vagy frissítése.

## <a name="syntax"></a>Syntax

### <a name="set-configuration-for-host"></a>Gazdagép konfigurációjának beállítása
```powershell
Set-AksHciConfig [-imageDir <String>]
                 [-cloudConfigLocation <String>]
                 [-nodeConfigLocation <String>]
                 [-vnet <Virtual Network>]
                 [-controlPlaneVmSize <VmSize>]
                 [-sshPublicKey <String>]
                 [-macPoolStart <String>]
                 [-macPoolEnd <String>]       
                 [-proxyServerHTTP <String>]
                 [-proxyServerHTTPS <String>]
                 [-proxyServerNoProxy <String>]
                 [-proxyServerCertFile <String>]
                 [-proxyServerCredential <PSCredential>]
                 [-cloudServiceCidr <String>]
                 [-workingDir <String>]
                 [-version <String>]
                 [-nodeAgentPort <int>]
                 [-nodeAgentAuthorizerPort <int>]
                 [-clusterRoleName <String>]
                 [-cloudLocation <String>]
                 [-skipHostLimitChecks]
                 [-insecure]
                 [-skipUpdates]
                 [-forceDnsReplication]
                 [-enableDiagnosticData]   
```

## <a name="description"></a>Leírás
Adja meg az Azure Kubernetes Service Host konfigurációs beállításait. Ha 2-4-Azure Stack csomópontra vagy egy Windows Server 2019 Datacenter feladatátvevő fürtre végzi a üzembe helyezést, meg kell adnia a imageDir és a cloudConfigLocation paramétereket. Egyetlen csomópontos Windows Server 2019 Datacenter esetén az összes paraméter nem kötelező, és az alapértelmezett értékre van állítva. Az optimális teljesítmény érdekében azonban javasoljuk, hogy egy 2-4 csomópontot használjon Azure Stack HCI-fürt üzembe helyezéséhez.

## <a name="examples"></a>Példák

### <a name="to-deploy-on-a-2-4-node-cluster-with-dhcp-networking"></a>Üzembe helyezés egy 2-4 csomópontos fürtön DHCP-hálózatkezeléssel
```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
```

### <a name="to-deploy-with-a-virtual-ip-pool"></a>Üzembe helyezés virtuális IP-készlettel
```powershell
PS C:\> New-AksHciNetworkSetting -name newNetwork -subnetCidr 192.168.2.0/32 -defaultGateway 192.168.2.1 -subnetVSwitch External -vipPoolStart 192.168.2.20 -vipPoolEnd 192.168.2.80   
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -networkSettings newNetwork
```

### <a name="to-deploy-with-a-proxy-server"></a>Üzembe helyezés proxykiszolgáló használatával
```powershell
PS C:\> $credential = Get-Credential
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
```

## <a name="parameters"></a>Paraméterek

### <a name="-imagedir"></a>-imageDir
Annak a könyvtárnak az elérési útja, amelyen az Azure Kubernetes szolgáltatása Azure Stack HCI-ben tárolja a VHD-lemezképeit. Az alapértelmezett érték `%systemdrive%\AksHciImageStore` az egycsomópontos üzemelő példányok esetében. Több csomópontos üzemelő példányok esetén ezt a paramétert meg kell adni. Az elérési útnak egy megosztott tárolási elérési útra kell mutatnia, például  `C:\ClusterStorage\Volume2\ImageStore`   vagy egy SMB-megosztásra, például:  `\\FileShare\ImageStore` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\AksHciImageStore
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudconfiglocation"></a>-cloudConfigLocation
Az a hely, ahol a felhőalapú ügynök tárolni fogja a konfigurációját. Az alapértelmezett érték `%systemdrive%\wssdcloudagent` az egycsomópontos üzemelő példányok esetében. A hely lehet ugyanaz, mint a-imageDir fenti elérési útja.Több csomópontos üzemelő példányok esetén ezt a paramétert meg kell adni. Az elérési útnak egy megosztott tárolási elérési útra kell mutatnia, például  `C:\ClusterStorage\Volume2\ImageStore`   vagy egy SMB-megosztásra, például:  `\\FileShare\ImageStore` . A helynek egy magasan elérhető megosztáson kell lennie, hogy a tárterület mindig elérhető legyen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\wssdcloudagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeconfiglocation"></a>-nodeConfigLocation
Az a hely, ahol a csomópont-ügynökök tárolni fogják a konfigurációt. Minden csomóponthoz tartozik egy csomópont-ügynök, így annak konfigurációja a helyi. Ennek a helynek helyi elérési útnak kell lennie. Alapértelmezés szerint `%systemdrive%\programdata\wssdagent` az összes üzemelő példánynál.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\programdata\wssdagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>– vnet
A New-AksHciNetworkSetting paranccsal létrehozott AksHciNetworkSetting objektum neve.
```yaml
Type: VirtualNetwork
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
A vezérlő síkja számára létrehozandó virtuális gép mérete. Az alapértelmezett érték Standard_A4_V2. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-sshpublickey"></a>– sshPublicKey
Egy nyilvános SSH-kulcsfájl elérési útja. A nyilvános kulcs használatával bejelentkezhet az Azure Kubernetes szolgáltatás által létrehozott bármelyik virtuális gépre Azure Stack HCI-telepítésre. Ha saját nyilvános SSH-kulcsával rendelkezik, itt adhatja át a helyét. Ha nincs megadva kulcs, a rendszer a. pub alatt keres egyet `%systemdrive%\akshci\.ssh\akshci_rsa` . Ha a fájl nem létezik, a rendszer létrehoz és használ egy SSH-kulcspárt a fenti helyen.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolstart"></a>-macPoolStart
Ezzel a beállítással megadhatja az Azure Kubernetes Service Host VM-hez használni kívánt MAC-készlet MAC-címe kezdetét. A MAC-címek szintaxisa megköveteli, hogy az első bájt legkisebb jelentős részének mindig 0 legyen, és az első bájtnak mindig páros számnak kell lennie (azaz 00, 02, 04, 06...). Egy tipikus MAC-címnek A következőképpen nézhet ki: 02:1E: 2B: 78:00:00. A MAC-készletek használata hosszú élettartamú központi telepítésekhez, hogy a hozzárendelt MAC-címek konzisztensek legyenek. Ez akkor hasznos, ha követelmény, hogy a virtuális gépek egyedi MAC-címmel rendelkezzenek. Az alapértelmezett érték a none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolend"></a>-macPoolEnd
Ezzel a beállítással adható meg az Azure Kubernetes Service Host virtuális géphez használni kívánt MAC-készlet MAC-címe. A MAC-címek szintaxisa megköveteli, hogy az első bájt legkisebb jelentős részének mindig 0 legyen, és az első bájtnak mindig páros számnak kell lennie (azaz 00, 02, 04, 06...). A-macPoolEnd átadott címnek első bájtjának meg kell egyeznie a-macPoolStart-ként átadott címnek az első bájttal. A MAC-készletek használata hosszú élettartamú központi telepítésekhez, hogy a hozzárendelt MAC-címek konzisztensek legyenek. Ez akkor hasznos, ha követelmény, hogy a virtuális gépek egyedi MAC-címmel rendelkezzenek. Az alapértelmezett érték a none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttp"></a>-proxyServerHTTP
Ez egy proxykiszolgáló URI-t biztosít, amelyet az összes olyan összetevőnek használnia kell, amelynek HTTP-végpontokat kell elérnie. Az URI formátuma tartalmazza az URI-sémát, a kiszolgálónevet és a portot (azaz: https://server.com:8888) . Az alapértelmezett érték a none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttps"></a>-proxyServerHTTPS
Ez egy proxykiszolgáló URI-t biztosít, amelyet az összes olyan összetevőnek használnia kell, amelyeknek a HTTPS-végpontokat kell elérniük. Az URI formátuma tartalmazza az URI-sémát, a kiszolgálónevet és a portot (azaz: https://server.com:8888) . Az alapértelmezett érték a none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservernoproxy"></a>-proxyServerNoProxy
Ez egy vesszővel tagolt címtartomány, amely mentesül a proxy alól. Az alapértelmezett érték a localhost, 127.0.0.1,. SVC, 10.96.0.0/12, 10.244.0.0/16. Ez kizárja a localhost forgalmat (localhost, 127.0.0.1), a belső Kubernetes-szolgáltatás forgalmát (. SVC), a Kubernetes szolgáltatás CIDR (10.96.0.0/12) és a Kubernetes POD CIDR (10.244.0.0/16) a proxykiszolgálót. Ezzel a paraméterrel további alhálózati tartományokat vagy nevekre vonatkozó kivételeket is hozzáadhat. **A paraméter beállításai nagyon fontosak, mert ha nem megfelelően van konfigurálva, előfordulhat, hogy a belső Kubernetes-fürt forgalmát váratlanul átirányítja a proxyhoz. Ez különféle hibákhoz vezethet a hálózati kommunikációban.**

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
A proxykiszolgáló hitelesítéséhez használt tanúsítvány.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:
 
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercredential"></a>-proxyServerCredential
Ez a Felhasználónév és a jelszó megadásával hitelesíti magát a HTTP/HTTPS-proxy kiszolgálókon. A (z `Get-Credential` ) segítségével létrehozhatja a paraméternek átadandó PSCredential objektumot. Az alapértelmezett érték a none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudservicecidr"></a>-cloudServiceCidr
Ezzel megadhatja a MOC CloudAgent szolgáltatáshoz hozzárendelni kívánt statikus IP/hálózati előtagot. Ezt az értéket a CIDR formátum használatával kell megadni. (Például: 192.168.1.2/16). Érdemes megadnia ezt annak biztosítására, hogy a hálózat minden fontos adata mindig elérhető legyen, mivel az IP-cím nem változik. Az alapértelmezett érték a none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-workingdir"></a>-workingDir
Ez egy munkakönyvtár a kis fájlok tárolására használt modul számára. Alapértelmezés szerint a `%PROGRAMFILES%\AksHci`   és a nem módosítható a legtöbb üzemelő példány esetében.Az alapértelmezett beállítás módosítása nem ajánlott.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %PROGRAMFILES%\AksHci
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-version"></a>– verzió
Az Azure Kubernetes szolgáltatás azon verziója, Azure Stack HCI-re, amelyet telepíteni kíván. Az alapértelmezett érték a legújabb verzió. Az alapértelmezett beállítás módosítása nem ajánlott.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Latest version
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentport"></a>-nodeAgentPort
Annak a TCP/IP-portnak a száma, amelyre a csomópont-ügynököknek figyelniük kell. Az alapértelmezett érték a 45000.Az alapértelmezett beállítás módosítása nem ajánlott.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45000
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentauthorizerport"></a>-nodeAgentAuthorizerPort
A csomópont-ügynökök által az engedélyezési porthoz használandó TCP/IP-portszám. Az alapértelmezett érték a 45001. Az alapértelmezett beállítás módosítása nem ajánlott. 

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45001
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clusterrolename"></a>-clusterRoleName
Ez határozza meg a felhőalapú ügynök általános szolgáltatásként történő létrehozásakor használandó nevet a fürtön belül. Ez az alapértelmezett érték egy egyedi név, amely egy CA-előtaggal és egy GUID utótaggal rendelkezik (például: "CA-9e6eb299-bc0b-4f00-9fd7-942843820c26"). Az alapértelmezett beállítás módosítása nem ajánlott.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: A unique name with a prefix of ca- and a guid suffix
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudlocation"></a>-cloudLocation
Ez a paraméter egy egyéni Microsoft által üzemeltetett Felhőbeli hely nevét adja meg. Az alapértelmezett név: "MocLocation". Az alapértelmezett beállítás módosítása nem ajánlott.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: MocLocation
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skiphostlimitchecks"></a>-skipHostLimitChecks
Megkéri a parancsfájlt, hogy hagyja ki a szükséges ellenőrzéseket, hogy megerősítse a memóriát és a lemezterületet, mielőtt engedélyezi a telepítés folytatását. Ezt a beállítást nem ajánlott használni.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-insecure"></a>– nem biztonságos
Üzembe helyezi az Azure Kubernetes szolgáltatást olyan Azure Stack HCI-összetevőkön, mint a felhőalapú ügynök és a csomópont-ügynök (ek) nem biztonságos módban (nincs TLS-alapú biztonságos kapcsolat).Nem javasoljuk a nem biztonságos üzemmód használatát éles környezetekben.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipupdates"></a>-skipUpdates
Akkor használja ezt a jelzőt, ha ki szeretné hagyni az elérhető frissítéseket. Ezt a beállítást nem ajánlott használni.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-forcednsreplication"></a>-forceDnsReplication
A DNS-replikáció néhány rendszeren akár egy órát is igénybe vehet. Ennek hatására az üzemelő példány lassú lesz. Ha ezt a problémát tapasztalja, látni fogja, hogy a Install-AksHci egy hurokban lesznek beragadva. A probléma megkezdéséhez próbálja meg használni ezt a jelzőt. A-forceDnsReplication jelző nem garantált javítás. Ha a jelző mögötti logika nem sikerül, a rendszer elrejti a hibát, és a parancs úgy fog futni, mintha a jelző nem lett megadva.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablediagnosticdata"></a>-enableDiagnosticData'

Ezzel a jelzővel beleegyezik, hogy elküldje a szükséges diagnosztikai fájlokat a Microsoftnak. Ez akkor hasznos, ha ki szeretné hagyni az `Set-AksHciConfig` egyéb módon megjelenő hozzájárulási kérést. `Install-AksHci` sikertelen lesz, ha nem fogadja el a hozzájárulási kérést, amikor megadja `Set-AksHciConfig` azt.
