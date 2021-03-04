---
title: Gyors útmutató egy Azure Kubernetes-szolgáltatás gazdagépének beállításához Azure Stack HCI-ben a Windows PowerShell használatával
description: Ismerje meg, hogyan állíthat be egy Azure Kubernetes Service hostt Azure Stack HCI-ben a Windows PowerShell használatával
author: jessicaguan
ms.topic: quickstart
ms.date: 02/12/2021
ms.author: jeguan
ms.openlocfilehash: f14f3550e7a6d51ffd51a521bc6f155186b4c0c0
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839725"
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

Töltse le az alkalmazást `AKS-HCI-Public-Preview-Feb-2021` az [Azure Kubernetes szolgáltatásból Azure stack HCI regisztrációs oldalon](https://aka.ms/AKS-HCI-Evaluate). A zip-fájl `AksHci.Powershell.zip` tartalmazza a PowerShell-modult.

**Az összes PowerShell-ablak lezárása.** Törölje az elérési úton található AksHci, AksHci. Day2, kVA, MOC és MSK8sDownloadAgent összes meglévő könyvtárát `%systemdrive%\program files\windowspowershell\modules` . Ha ez megtörtént, kibonthatja az új zip-fájl tartalmát. Győződjön meg arról, hogy a zip-fájlt a megfelelő helyen () szeretné kibontani `%systemdrive%\program files\windowspowershell\modules` .

   ```powershell
   Import-Module AksHci
   ```

**Zárjunk be minden PowerShell-ablakot** , és nyissa meg újra az új felügyeleti munkamenetet, és ellenőrizze, hogy rendelkezik-e a PowerShell-modul legújabb verziójával.
  
   ```powershell
   Get-Command -Module AksHci
   ```

 **Kimeneti**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.19     akshci
Function        Get-AksHciCluster                                  0.2.19     akshci
Function        Get-AksHciClusterUpgrades                          0.2.19     akshci
Function        Get-AksHciConfig                                   0.2.19     akshci
Function        Get-AksHciCredential                               0.2.19     akshci
Function        Get-AksHciEventLog                                 0.2.19     akshci
Function        Get-AksHciKubernetesVersion                        0.2.19     akshci
Function        Get-AksHciLogs                                     0.2.19     akshci
Function        Get-AksHciUpdates                                  0.2.19     akshci
Function        Get-AksHciVersion                                  0.2.19     akshci
Function        Get-AksHciVmSize                                   0.2.19     akshci
Function        Install-AksHci                                     0.2.19     akshci
Function        Install-AksHciAdAuth                               0.2.19     akshci
Function        Install-AksHciArcOnboarding                        0.2.19     akshci
Function        New-AksHciCluster                                  0.2.19     akshci
Function        New-AksHciNetworkSetting                           0.2.19     akshci
Function        Remove-AksHciCluster                               0.2.19     akshci
Function        Restart-AksHci                                     0.2.19     akshci
Function        Set-AksHciClusterNodeCount                         0.2.19     akshci
Function        Set-AksHciConfig                                   0.2.19     akshci
Function        Uninstall-AksHci                                   0.2.19     akshci
Function        Uninstall-AksHciAdAuth                             0.2.19     akshci
Function        Uninstall-AksHciArcOnboarding                      0.2.19     akshci
Function        Update-AksHci                                      0.2.19     akshci
Function        Update-AksHciCluster                               0.2.19     akshci
```

A fenti parancs futtatása után zárjunk be minden PowerShell-ablakot, majd nyissa meg újra a felügyeleti munkamenetet, és futtassa a parancsokat a következő lépésekben.

### <a name="update-your-clusters-to-the-latest-aks-hci-version"></a>A fürtök frissítése a legújabb AK-HCI-verzióra

Ha meglévő központi telepítéssel rendelkezik, és szeretné frissíteni, végezze el ezt a lépést. Ha nem rendelkezik meglévő központi telepítéssel, ugorja át ezt a lépést, és folytassa a 2. lépéssel. Ha van üzembe helyezése, de a következő AKSHCI-verzió tiszta telepítését szeretné futtatni, futtassa a parancsot, `Uninstall-AksHci` és folytassa a 2. lépéssel.

Az Azure Kubernetes Service legújabb verziójának frissítéséhez Azure Stack HCI-ben futtassa az [Update-akshci](./update-akshci.md) parancsot. Ez a parancs frissíti az Azure Kubernetes Service hostt és a helyszíni Microsoft által üzemeltetett felhőalapú platformot. Ez a parancs nem frissíti a Kubernetes és a Windows Node operációsrendszer-verzióit a meglévő munkaterhelés-fürtökben. Az AK-gazdagép frissítése után létrehozott új munkaterhelés-fürtök különböznek a Windows-csomópont operációsrendszer-verziójának és Kubernetes-verziójának meglévő munkaterhelés-fürtjétől.

```powershell
Update-AksHci
```

Javasoljuk, hogy a munkaterhelési fürtöket azonnal frissítse a felügyeleti fürt frissítése után, hogy megakadályozza a Windows Server operációsrendszer-verziók nem támogatott Windows-csomópontokkal történő futtatását a Kubernetes-fürtökben. Ha a munkaterhelés-fürtök nem támogatott verziójúak, akkor továbbra is támogatottak lesznek, de nem fog tudni új csomópontokat létrehozni. A munkaterhelés-fürt frissítéséhez látogasson el [a munkaterhelés-fürt frissítése](create-kubernetes-cluster-powershell.md)lehetőségre.

## <a name="step-2-prepare-your-machines-for-deployment"></a>2. lépés: a gép (ek) előkészítése az üzembe helyezéshez

Futtasson ellenőrzéseket minden fizikai csomóponton, és ellenőrizze, hogy az összes követelmény teljesül-e az Azure Kubernetes Service Azure Stack HCI-on való telepítéséhez.
Nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot.

```powershell
Initialize-AksHciNode
```

Az ellenőrzések befejezése után a "kész" szöveg jelenik meg zöld színnel.

## <a name="step-3-create-a-virtual-network"></a>3. lépés: virtuális hálózat létrehozása

Az elérhető külső kapcsolók nevének lekéréséhez futtassa a következő parancsot:

```powershell
Get-VMSwitch
```

Külső kapcsolót kell használnia.

Példa a kimenetre:
```output
Name SwitchType NetAdapterInterfaceDescription
---- ---------- ------------------------------
extSwitch External Mellanox ConnectX-3 Pro Ethernet Adapter
```

Ha virtuális hálózatot szeretne létrehozni a központi telepítésben lévő csomópontokhoz, hozzon létre egy környezeti változót a [New-akshcinetworksetting PowerShell-](.\new-akshcinetworksetting.md) paranccsal. Ezt később a statikus IP-címet használó központi telepítés konfigurálására fogjuk használni. Ha az AK-alapú telepítést DHCP-vel szeretné konfigurálni, tekintse meg a [New-akshcinetworksetting](.\new-akshcinetworksetting.md) példákat.

```powershell
#static IP
$vnet = New-AksHciNetworkSetting -vnetName "extSwitch" -k8sNodeIpPoolStart "172.16.10.0" -k8sNodeIpPoolEnd "172.16.10.255" -vipPoolStart "172.16.255.0" -vipPoolEnd
"172.16.255.254" -ipAddressPrefix "172.16.0.0/16" -gateway "172.16.0.1" -dnsServers "172.16.0.1"
```

> [!NOTE]
> Az ebben a példában szereplő parancsban megadott értékeket testre kell szabni a környezetben.

## <a name="step-4-configure-your-deployment"></a>4. lépés: az üzemelő példány konfigurálása

Állítsa be az Azure Kubernetes Service Host konfigurációs beállításait a [set-akshciconfig](./set-akshciconfig.md) parancs használatával. **Ha 2-4-Azure Stack csomópontra telepíti a HCI-fürtöt vagy egy Windows Server 2019 Datacenter feladatátvevő fürtöt, meg kell adnia a `imageDir` és a `cloudConfigLocation` paramétereket.** Ha alaphelyzetbe szeretné állítani a konfiguráció részleteit, futtassa újra a parancsot új paraméterekkel.

Konfigurálja az üzemelő példányt a következő paranccsal.

```powershell
Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16" 
```

> [!NOTE]
> Az ebben a `-cloudservicecidr` példában szereplő parancsban megadott értéket testre kell szabni a környezet számára.

A paraméterrel hozzájárulhat a `-enableDiagnosticData` szükséges diagnosztikai adatszolgáltatások Microsoftnak való elküldéséhez. Ha ez a paraméter nincs használatban, a rendszer a futtatáskor kéri a diagnosztikai adatkérések engedélyezését `Set-AksHciConfig` . `Install-AksHci` sikertelen lesz, ha nem fogadja el a hozzájárulási kérést, amikor megadja `Set-AksHciConfig` azt.

## <a name="step-5-start-a-new-deployment"></a>5. lépés: új központi telepítés indítása

A telepítés konfigurálása után el kell indítania azt. Ezzel telepíti az Azure Kubernetes szolgáltatást Azure Stack HCI-ügynökökre/-szolgáltatásokra és az Azure Kubernetes Service-gazdagépre.

Az üzembe helyezés megkezdéséhez futtassa a következő parancsot.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Az üzembe helyezett Azure Kubernetes Service-gazdagép ellenőrzése

Az Azure Kubernetes Service Host üzembe helyezésének biztosításához futtassa a [Get-akshcicluster](./get-akshcicluster.md) parancsot. A Kubernetes-fürtöket ugyanezen parancs használatával is lekérheti a telepítés után.

```powershell
Get-AksHciCluster
```

**Kimeneti**
```

Name            : clustergroup-management
Version         : v1.18.10
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-6-access-your-clusters-using-kubectl"></a>6. lépés: a fürtök elérése a kubectl használatával

Az Azure Kubernetes Service-gazdagép a használatával való eléréséhez `kubectl` futtassa a [Get-akshcicredential](./get-akshcicredential.md) parancsot. Ez a megadott fürt _kubeconfig_ -fájlját fogja használni alapértelmezett _kubeconfig_ -fájlként `kubectl` . Ezzel a paranccsal más Kubernetes-fürtöket is elérhet a telepítése után.

```powershell
Get-AksHciCredential -name clustergroup-management
```

## <a name="get-logs"></a>Naplók lekérése

Az összes hüvelyből származó naplók lekéréséhez futtassa a [Get-akshcilogs](./get-akshcilogs) parancsot. Ez a parancs létrehoz egy nevű kimeneti tömörített mappát `akshcilogs` az elérési úton `c:\%workingdirectory%\%AKS HCI release number%\%filename%` (például: `c:\AksHci\0.9.6.0\akshcilogs.zip` ).

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes Service legújabb verziójának frissítése Azure Stack HCI-on

Az Azure Kubernetes Service legújabb verziójának frissítéséhez Azure Stack HCI-ben futtassa az [Update-akshci](./update-akshci.md) parancsot. A frissítés parancs csak akkor működik, ha telepítette a TOT kiadását vagy újabb verzióját. Nem fog működni az októberi kiadásnál régebbi verziókban. Ez a frissítési parancs frissíti az Azure Kubernetes Service hostt és a helyszíni Microsoft által üzemeltetett felhőalapú platformot. Ez a parancs nem frissíti a meglévő munkaterhelés-fürtöket. Az AK-gazdagép frissítése után létrehozott új munkaterhelés-fürtök különböznek a Windows-csomópont operációsrendszer-verziójának és Kubernetes-verziójának meglévő munkaterhelés-fürtjétől.

```powershell
Update-AksHci
```
   
Javasoljuk, hogy a munkaterhelési fürtöket azonnal frissítse a felügyeleti fürt frissítése után, hogy megakadályozza a Windows Server operációsrendszer-verziók nem támogatott Windows-csomópontokkal történő futtatását a Kubernetes-fürtökben. Ha a munkaterhelés-fürtök nem támogatott verziójúak, akkor továbbra is támogatottak lesznek, de nem fog tudni új csomópontokat létrehozni. A munkaterhelés-fürt frissítéséhez látogasson el [a munkaterhelés-fürt frissítése](create-kubernetes-cluster-powershell.md)lehetőségre.

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

A fenti parancs futtatása után a konfigurációs beállításokat a következő parancs használatával módosíthatja. A paraméterek változatlanok maradnak a 3. lépésben leírtak szerint. 

```powershell
Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16" 
```

Miután módosította a konfigurációt a kívánt beállításokra, futtassa a következő parancsot a Azure Stack Kubernetes újratelepítéséhez Azure Stack HCI-ben.

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes szolgáltatás eltávolítása Azure Stack HCI-re


Az Azure Kubernetes Service Azure Stack HCI-ben való eltávolításához futtassa a következő parancsot. Ezzel a paranccsal a régi konfigurációt távolítja el, és újra kell futtatnia az `Set-AksHciConfig` újratelepítést követően.

```powershell
Uninstall-AksHci
```

Ha meg szeretné őrizni a régi konfigurációt, futtassa a következő parancsot.

```powershell
Uninstall-AksHci -SkipConfigCleanup
```

## <a name="next-steps"></a>Következő lépések

- [Kubernetes-fürt létrehozása az alkalmazásaihoz](create-kubernetes-cluster-powershell.md)
