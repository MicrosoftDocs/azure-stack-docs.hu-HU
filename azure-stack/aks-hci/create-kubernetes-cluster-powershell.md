---
title: Gyors útmutató Kubernetes-fürtök létrehozásához Azure Stack HCI-ben a Windows PowerShell használatával
description: Megtudhatja, hogyan hozhat létre Kubernetes-fürtöket Azure Stack HCI-ben a Windows PowerShell használatával
author: jessicaguan
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: jeguan
ms.openlocfilehash: 38d65798d2f30377f3160484c9a618730cda80ea
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557140"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Gyors útmutató: Kubernetes-fürtök létrehozása Azure Stack HCI-ben a Windows PowerShell használatával

> A következőkre vonatkozik: Azure Stack HCI, AK Runtime a Windows Server 2019 Datacenter rendszeren

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Kubernetes-fürtöt a Windows PowerShell használatával Azure Stack HCI-ben. Ezután megtudhatja, hogyan méretezheti a Kubernetes-fürtöt, és hogyan frissítheti a fürt Kubernetes verzióját. Ha ehelyett a Windows felügyeleti központot szeretné használni, olvassa el az [Azure Kubernetes szolgáltatás beállítása Azure stack HCI-ben a Windows felügyeleti központ használatával](setup.md)című témakört.

## <a name="before-you-begin"></a>Előkészületek

 - Győződjön meg arról, hogy rendelkezik egy Azure Stack Kubernetes-gazdagép beállításával. Ha nem, tekintse [meg a rövid útmutató: Azure Kubernetes Service Host beállítása a Azure stack HCI-ben a PowerShell használatával](./setup-powershell.md)című témakört.
 - Győződjön meg arról, hogy telepítve van a legújabb Aks-Hci PowerShell-modul. Ha nem, olvassa el [a AksHci PowerShell-modul letöltése és telepítése](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module)című témakört.

## <a name="step-1-create-a-kubernetes-cluster"></a>1. lépés: Kubernetes-fürt létrehozása

Az Azure Kubernetes Service Host telepítése után készen áll egy Kubernetes-fürt üzembe helyezésére.

Nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot.

   ```powershell
   New-AksHciCluster -clusterName <String>
                    [-kubernetesVersion <String>]
                    [-controlPlaneNodeCount <int>]
                    [-linuxNodeCount <int>]
                    [-windowsNodeCount <int>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-linuxNodeVmSize <VmSize>]
                    [-windowsNodeVmSize <VmSize>]
   ```

### <a name="example"></a>Példa

   ```powershell
   New-AksHciCluster -clusterName mynewcluster -kubernetesVersion v1.18.8 -controlPlaneNodeCount 1 -linuxNodeCount 1 -windowsNodeCount 0 
   ```

### <a name="required-parameters"></a>Szükséges paraméterek

`-clusterName`

A Kubernetes-fürt alfanumerikus neve.

### <a name="optional-parameters"></a>Választható paraméterek

`-kubernetesVersion`

A telepíteni kívánt Kubernetes verziója. Az alapértelmezett érték a v 1.18.8. Az elérhető verziók listájának lekéréséhez futtassa a parancsot `Get-AksHciKubernetesVersion` .

`-controlPlaneNodeCount`

A vezérlő síkja csomópontjainak száma. Az alapértelmezett érték 1.

`-linuxNodeCount`

A Kubernetes-fürtben található Linux-csomópontok száma. Az alapértelmezett érték 1.

`-windowsNodeCount`

A Kubernetes-fürtben lévő Windows-csomópontok száma. Az alapértelmezett érték a 0. Windows-csomópontokat csak akkor telepíthet, ha a Kubernetes v 1.18.8 futtatja.

`-controlPlaneVmSize`

A vezérlési sík virtuális gép mérete. Az alapértelmezett érték Standard_A2_v2. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

`-loadBalancerVmSize`

A terheléselosztó virtuális gépe mérete. Az alapértelmezett érték Standard_A2_V2. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

`-linuxNodeVmSize`

A Linux-csomópont virtuális gépe mérete. Az alapértelmezett érték Standard_K8S3_v1. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

`-windowsNodeVmSize`

A Windows-csomópont virtuális gépe mérete. Az alapértelmezett érték Standard_K8S3_v1. A rendelkezésre álló virtuálisgép-méretek listájának lekéréséhez futtassa a parancsot `Get-AksHciVmSize` .

### <a name="check-your-deployed-clusters"></a>A telepített fürtök keresése

A telepített Azure Kubernetes Service Host-és Kubernetes-fürtök listáját a következő parancs futtatásával kérheti le.

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>2. lépés: Kubernetes-fürt skálázása

Ha fel-vagy leskálázást szeretne készíteni a fürtön, módosíthatja a vezérlési sík csomópontjainak, a linuxos feldolgozó csomópontoknak vagy a Windows-munkavégző csomópontoknak a számát.

A vezérlési sík csomópontjainak méretezéséhez futtassa a következő parancsot.

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -controlPlaneNodeCount <int>
```

A munkavégző csomópontok méretezéséhez futtassa a következő parancsot.

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

A vezérlési sík csomópontjait és a munkavégző csomópontokat egymástól függetlenül kell méretezni.

### <a name="example"></a>Példa

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -controlPlaneNodeCount 3
```

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -linuxNodeCount 2 -windowsNodeCount 2 
```

## <a name="step-3-upgrade-kubernetes-version"></a>3. lépés: a Kubernetes verziójának frissítése

Az elérhető Kubernetes-verziók listájának megtekintéséhez futtassa a következő parancsot.

```powershell
Get-AksHciKubernetesVersion
```

A következő Kubernetes-verzióra való frissítéshez futtassa a következő parancsot.

```powershell
Update-AksHciCluster -clusterName <String>
                     [-patch]
```
Minden Kubernetes-verzióhoz tartozik egy fő kiadás, egy alverzió és egy javítás verziója. Például a v 1.18.6, 1 a fő kiadás, a 18 a másodlagos verzió, a 6 pedig a javítás verziója. Az idő múlásával az AK-HCI az egyik főverziót, három kisebb kiadást és két javítást is támogat, a teljes 6 támogatott verzióhoz képest. Ebben az előzetes kiadásban azonban összesen 4 kiadást támogatunk: v 1.16.10, v 1.16.15, v 1.17.11, v 1.18.8. 

Ha a paramétert a `patch` futás közben adja hozzá `Update-AksHciCluster` , a parancs a másodlagos verzió következő javítási verziójára (ha van) frissíti. Ha a parancsot a paraméter nélkül futtatja `patch` , az alapértelmezett frissítési élmény a következő alverzió. Ennek elvégzéséhez a következő táblázat tartalmazza az összes lehetséges frissítést:

| Aktuális kiadás           | A Kubernetes frissítése javítás nélkül         | Kubernetes frissített verziója – javítás
| ---------------------------- | ------------ | -------------------------------- |
| v 1.16.10           |     v 1.17.11      | v 1.16.15
| v 1.16.15            | v 1.17.11 | helyi bővítmény frissítése
| v 1.17.11           |  v 1.18.8          | helyi bővítmény frissítése
| v 1.18.8             | helyi bővítmény frissítése   | helyi bővítmény frissítése

A helyi bővítmény frissítése frissíti az összes olyan Kubernetes-bővítményt, mint például a CSI, amelyet az AK-HCI kezel. Ez a frissítés nem változtatja meg a csomópont operációs rendszerének verzióját. Emellett nem módosítja a Kubernetes verzióját.

### <a name="example---upgrade-kubernetes-version-to-the-next-minor-version"></a>Példa – a Kubernetes verziójának frissítése a következő alverzióra

```powershell
Update-AksHciCluster -clusterName mynewcluster
```

### <a name="example---upgrade-kubernetes-version-to-the-next-patch-version"></a>Példa – a Kubernetes verziójának frissítése a következő javítási verzióra

```powershell
Update-AksHciCluster -clusterName mynewcluster -patch
```


## <a name="step-4-access-your-clusters-using-kubectl"></a>4. lépés: a fürtök elérése a kubectl használatával

Ha a Kubernetes-fürtöket a kubectl használatával szeretné elérni, futtassa a következő parancsot. Ez a megadott fürt kubeconfig-fájlját fogja használni a kubectl alapértelmezett kubeconfig-fájljához.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Példa

```powershell
Get-AksHciCredential -clusterName mynewcluster
```

### <a name="required-parameters"></a>Kötelező paraméterek

`clusterName`

A fürt neve.

### <a name="optional-parameters"></a>Opcionális paraméterek

`outputLocation`

A hely, ahová a kubeconfig letöltötte. Az alapértelmezett szint a `%USERPROFILE%\.kube`.

## <a name="delete-a-kubernetes-cluster"></a>Kubernetes-fürt törlése

Ha törölnie kell egy Kubernetes-fürtöt, futtassa a következő parancsot.

```powershell
Remove-AksHciCluster -clusterName
```

### <a name="example"></a>Példa

```powershell
Remove-AksHciCluster -clusterName mynewcluster
```

## <a name="get-logs"></a>Naplók lekérése

Az összes hüvelyből származó naplók lekéréséhez futtassa a következő parancsot. Ez a parancs létrehoz egy nevű kimeneti tömörített mappát `akshcilogs` az elérési úton `C:\wssd\akshcilogs` .

```powershell
Get-AksHciLogs
```

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre, méretezheti és frissíthet egy fürt Kubernetes verzióját a PowerShell használatával.

## <a name="next-steps"></a>További lépések

- [Kösse össze a fürtöket az Azure arc Kubernetes](./connect-to-arc.md).
- [Linux-alkalmazás üzembe helyezése a Kubernetes-fürtön](./deploy-linux-application.md).
- [Helyezzen üzembe egy Windows-alkalmazást a Kubernetes-fürtön](./deploy-windows-application.md).
