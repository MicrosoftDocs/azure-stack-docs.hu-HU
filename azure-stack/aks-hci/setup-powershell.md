---
title: Gyors útmutató egy Azure Kubernetes-szolgáltatás gazdagépének beállításához Azure Stack HCI-ben a Windows PowerShell használatával
description: Ismerje meg, hogyan állíthat be egy Azure Kubernetes Service hostt Azure Stack HCI-ben a Windows PowerShell használatával
author: jessicaguan
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: jeguan
ms.openlocfilehash: b4b128c5d51d7f916e0936102224283dd77a971d
ms.sourcegitcommit: 849be7ebd02a1e54e8d0ec59736c9917c67e309e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2020
ms.locfileid: "91134661"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Gyors útmutató: Azure Kubernetes Service Host beállítása Azure Stack HCI-ben a PowerShell használatával

> A következőkre vonatkozik: Azure Stack HCI

Ebből a rövid útmutatóból megtudhatja, hogyan állíthat be egy Azure Kubernetes Service-gazdagépet Azure Stack HCI-ben a PowerShell használatával. A Windows felügyeleti központ helyett a Windows [felügyeleti központ beállítása](setup.md)című témakörben talál további információt.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik egy 2-4-es csomóponttal Azure Stack HCI-fürttel, vagy egyetlen csomópont Azure Stack HCI-vel. **Javasoljuk, hogy egy 2-4 csomópontot Azure Stack HCI-fürtöt.** Ha nem, kövesse az [itt](./system-requirements.md)található utasításokat.

Emellett meg kell győződnie arról, hogy telepítve van a AksHci PowerShell-modul. Az [itt](https://aka.ms/AKS-HCI-Evaluate) található letöltési csomag a modult egy zip-fájlban fogja tartalmazni. Győződjön meg arról, hogy a zip-fájlt a megfelelő helyen () szeretné kibontani `%systemdrive%\program files\windowspowershell\modules` , majd futtassa a következő parancsot egy PowerShell felügyeleti ablakban.

   ```powershell
   Import-Module AksHci
   ```

A fenti parancs futtatása után zárjunk be minden PowerShell-ablakot, majd nyissa meg újra a felügyeleti munkamenetet, és futtassa a parancsokat a következő lépésekben.

## <a name="step-1-prepare-your-machines-for-deployment"></a>1. lépés: a gép (ek) előkészítése az üzembe helyezéshez

Először minden fizikai csomóponton futtatunk ellenőrzéseket, hogy ellenőrizze, hogy az összes követelmény teljesül-e az Azure Kubernetes Service Azure Stack HCI-re való telepítéséhez.

Nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot.

   ```powershell
   Initialize-AksHciNode
   ```

Az ellenőrzések befejezése után a "kész" szöveg jelenik meg zöld színnel.

## <a name="step-2-configure-your-deployment"></a>2. lépés: az üzemelő példány konfigurálása

Adja meg az Azure Kubernetes Service Host konfigurációs beállításait. **Egy 2-4-es csomóponthoz Azure Stack HCI-fürthöz meg kell adnia a `MultiNode` `-deploymentType` , a `wssdImageDir` és a `cloudConfigLocation` paramétereket.** Egy 1 csomópontos Azure Stack HCI-fürthöz az összes paraméter nem kötelező, és az alapértelmezett értékre van állítva. Az optimális teljesítmény érdekében azonban javasoljuk, hogy **egy 2-4 csomópontot használjon Azure stack HCI-fürt üzembe helyezéséhez.**

Konfigurálja az üzemelő példányt a következő paranccsal.

   ```powershell
   Set-AksHciConfig [-deploymentType {SingleNode, MultiNode}]
                    [-wssdImageDir]
                    [-cloudConfigLocation]
                    [-nodeConfigLocation]
                    [-vnetName]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-sshPublicKey]
                    [-vipPoolStartIp]
                    [-vipPoolEndIp]
                    [-macPoolStart]
                    [-macPoolEnd]
                    [-wssdDir]
                    [-akshciVersion]
                    [-vnetType]
                    [-nodeAgentPort]
                    [-nodeAgentAuthorizerPort]
                    [-clusterRoleName]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="optional-parameters"></a>Választható paraméterek

`-deploymentType`

A központi telepítési típus. Elfogadott értékek: SingleNode, többcsomópontos. Az alapértelmezett érték a SingleNode.

`-wssdImageDir`

Annak a könyvtárnak az elérési útja, amelyen az Azure Kubernetes szolgáltatása Azure Stack HCI-ben tárolja a VHD-lemezképeit. Az alapértelmezett érték `%systemdrive%\wssdimagestore` az egycsomópontos üzemelő példányok esetében. *Több csomópontos üzemelő példányok esetén ezt a paramétert meg kell adni*. Az elérési útnak egy megosztott tárolási elérési útra kell mutatnia, például  `C:\ClusterStorage\Volume2\ImageStore`   vagy egy SMB-megosztásra, például:  `\\FileShare\ImageStore` .

`-cloudConfigLocation`

Az a hely, ahol a felhőalapú ügynök tárolni fogja a konfigurációját. Az alapértelmezett érték `%systemdrive%\wssdimagestore` az egycsomópontos üzemelő példányok esetében. A hely lehet ugyanaz, mint a fenti elérési út  `-wssdImageDir` .*Több csomópontos üzemelő példányok esetén ezt a paramétert meg kell adni*.

`-nodeConfigLocation`

Az a hely, ahol a csomópont-ügynökök tárolni fogják a konfigurációt. Ennek a helyi elérési útnak kell lennie.

`-vnetName`

Annak a virtuális kapcsolónak a neve, amelyhez a virtuális gépeket hozzá szeretné kapcsolni. Az alapértelmezett érték a "külső" név. A kapcsoló akkor jön létre, ha nem létezik.  

`-controlPlaneVmSize`

A vezérlő síkja számára létrehozandó virtuális gép mérete. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

`-loadBalancerVmSize`

A Load Balancer virtuális gépek számára létrehozandó virtuális gép mérete. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

`-sshPublicKey`

Egy nyilvános SSH-kulcsfájl elérési útja. A nyilvános kulcs használatával bejelentkezhet az Azure Kubernetes szolgáltatás által létrehozott bármelyik virtuális gépre Azure Stack HCI-telepítésre. Ha nincs megadva kulcs, a rendszer a következőt keresi:  `%systemdrive%\Users\<username>\.ssh\id_rsa.pub` . Ha a fájl nem létezik, a rendszer létrehoz és használ egy SSH-kulcspárt a fenti helyen.  

`-vipPoolStartIp`

Ha VIP-készleteket használ az üzemelő példányhoz, ez a paraméter határozza meg a készlet hálózati kezdetét. Az alapértelmezett érték a none.

`-vipPoolEndIp`

Ha VIP-készleteket használ az üzemelő példányhoz, ez a paraméter határozza meg a készlet hálózatának végét. Az alapértelmezett érték a none.

`-macPoolStart`

Ezzel a beállítással megadhatja az Azure Kubernetes Service Host VM-hez használni kívánt MAC-készlet MAC-címe kezdetét. A MAC-címek szintaxisa megköveteli, hogy az első bájt legkisebb jelentős részének mindig 0 legyen, és az első bájtnak mindig páros számnak kell lennie (például 00, 02, 04, 06...). Egy tipikus MAC-címnek A következőképpen nézhet ki: 02:1E: 2B: 78:00:00. Az alapértelmezett érték a none.

`-macPoolEnd`

Ezzel a beállítással adható meg az Azure Kubernetes Service Host virtuális géphez használni kívánt MAC-készlet MAC-címe. A MAC-címek szintaxisa megköveteli, hogy az első bájt legkisebb jelentős részének mindig 0 legyen, és az első bájtnak mindig páros számnak kell lennie (például 00, 02, 04, 06...). Az átadott címnek első bájtjának meg kell `-macPoolEnd` egyeznie a-ként átadott címek első bájtjának értékével `-macPoolStart` . Az alapértelmezett érték a none.

`-wssdDir`

Ez egy munkakönyvtár a kis fájlok tárolására használt modul számára. Alapértelmezés szerint a `%PROGRAMFILES%\AksHci`   és a nem módosítható a legtöbb üzemelő példány esetében.  

`-akshciVersion`

Az Azure Kubernetes szolgáltatás azon verziója, Azure Stack HCI-re, amelyet telepíteni kíván. Az alapértelmezett érték a legújabb verzió.

`-vnetType`

A kapcsolódáshoz vagy létrehozáshoz használandó virtuális kapcsoló típusa. Ez az alapértelmezett érték a "külső" kapcsoló típusa.

`-nodeAgentPort`

Annak a TCP/IP-portnak a száma, amelyet a nodeagents figyelni kell. Az alapértelmezett érték a 45000.  

`-nodeAgentAuthorizerPort`

A TCP/IP-portszám, amelyet a nodeagents az engedélyezési porthoz kell használniuk. Az alapértelmezett érték a 45001.  

`-clusterRoleName`

Ez határozza meg a cloudagent a fürtön belüli általános szolgáltatásként való létrehozásakor használandó nevet. Ez az alapértelmezett érték egy egyedi név, amely a CA-előtaggal és egy GUID utótaggal rendelkezik (például: "CA-9e6eb299-bc0b-4f00-9fd7-942843820c26")

`-skipHostLimitChecks`

Megkéri a parancsfájlt, hogy hagyja ki a szükséges ellenőrzéseket, hogy megerősítse a memóriát és a lemezterületet, mielőtt engedélyezi a telepítés folytatását.

`-insecure`

Üzembe helyezi az Azure Kubernetes szolgáltatást olyan Azure Stack HCI-összetevőkön, mint a cloudagent és a nodeagent (k) nem biztonságos módban (nincs TLS biztonságos kapcsolat).Éles környezetben nem ajánlott a nem biztonságos üzemmód használata.

`-skipUpdates`

Akkor használja ezt a jelzőt, ha ki szeretné hagyni az elérhető frissítéseket.

`-forceDnsReplication`

A DNS-replikáció néhány rendszeren akár egy órát is igénybe vehet. Ennek hatására az üzemelő példány lassú lesz. Ha ezt a problémát tapasztalja, látni fogja, hogy az install-AksHci egy hurokban fog megakadni. A probléma megkezdéséhez próbálja meg használni ezt a jelzőt. A `-forceDnsReplication` jelző nem garantált javítás. Ha a jelző mögötti logika nem sikerül, a rendszer elrejti a hibát, és a parancs úgy fog futni, mintha a jelző nem lett megadva.

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Az Azure Kubernetes szolgáltatás alaphelyzetbe állítása Azure Stack HCI-konfiguráción

Az Azure Kubernetes szolgáltatás Azure Stack HCI-konfiguráción való alaphelyzetbe állításához futtassa a következő parancsot. Ha ezt a parancsot saját maga futtatja, alaphelyzetbe állítja a konfigurációt az alapértelmezett értékekre.

```powershell
Set-AksHciConfig
```

## <a name="step-3-start-a-new-deployment"></a>3. lépés: új központi telepítés indítása

A telepítés konfigurálása után el kell indítania az üzembe helyezést. Ezzel telepíti az Azure Kubernetes szolgáltatást Azure Stack HCI-ügynökökre/-szolgáltatásokra és az Azure Kubernetes Service-gazdagépre.

Az üzembe helyezés megkezdéséhez futtassa a következő parancsot.

```powershell
Install-AksHci
```

### <a name="check-your-deployed-clusters"></a>A telepített fürtök keresése

A telepített Azure Kubernetes Service-gazdagépek listájának lekéréséhez futtassa a következő parancsot. A Kubernetes-fürtöket ugyanezen parancs használatával is lekérheti a telepítés után.

```powershell
Get-AksHciCluster
```

## <a name="step-4-access-your-clusters-using-kubectl"></a>4. lépés: a fürtök elérése a kubectl használatával

Az Azure Kubernetes Service Host vagy a Kubernetes-fürt kubectl használatával való eléréséhez futtassa a következő parancsot. Ez a megadott fürt kubeconfig-fájlját fogja használni a kubectl alapértelmezett kubeconfig-fájljához.

```powershell
Set-AksHciKubeConfig -clusterName
```

## <a name="get-logs"></a>Naplók lekérése

Az összes hüvelyből származó naplók lekéréséhez futtassa a következő parancsot. Ez a parancs létrehoz egy nevű kimeneti tömörített mappát `akshcilogs` az elérési úton `C:\wssd\akshcilogs` .

```powershell
Get-AksHciLogs
```

## <a name="reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes szolgáltatás újratelepítése Azure Stack HCI-re

Ha az Azure Kubernetes Service-t Azure Stack HCI-re telepíti, akkor az összes Kubernetes-fürtöt, és az Azure Kubernetes Service hostt is eltávolítja. Emellett eltávolítja az Azure Kubernetes szolgáltatást Azure Stack HCI-ügynökökön és-szolgáltatásokon a csomópontokból. Ezután visszakerül az eredeti telepítési folyamat lépéseire, amíg a gazdagép újra nem jön létre. Az Azure Kubernetes szolgáltatás Azure Stack HCI-konfiguráción keresztül, amelyet `Set-AksHciConfig` a és a letöltött VHDX-lemezképek is megőrzik.

Ha újra szeretné telepíteni az Azure Kubernetes szolgáltatást Azure Stack HCI-re, futtassa a következő parancsot.

```powershell
Restart-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes szolgáltatás eltávolítása Azure Stack HCI-re

Az Azure Kubernetes Service Azure Stack HCI-ben való eltávolításához futtassa a következő parancsot.

```powershell
Uninstall-AksHci
```

## <a name="next-steps"></a>Következő lépések

- [Kubernetes-fürt létrehozása az alkalmazásaihoz](create-kubernetes-cluster-powershell.md)
