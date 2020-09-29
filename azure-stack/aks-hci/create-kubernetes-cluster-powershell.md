---
title: Gyors útmutató Kubernetes-fürtök létrehozásához Azure Stack HCI-ben a Windows PowerShell használatával
description: Megtudhatja, hogyan hozhat létre Kubernetes-fürtöket Azure Stack HCI-ben a Windows PowerShell használatával
author: jessicaguan
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: jeguan
ms.openlocfilehash: 35d527b56a2429676d343ba8098fc6821835fb00
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948882"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Gyors útmutató: Kubernetes-fürtök létrehozása Azure Stack HCI-ben a Windows PowerShell használatával

> A következőkre vonatkozik: Azure Stack HCI

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Kubernetes-fürtöt a Windows PowerShell használatával Azure Stack HCI-ben. Ha ehelyett a Windows felügyeleti központot szeretné használni, olvassa el az [Azure Kubernetes szolgáltatás beállítása Azure stack HCI-ben a Windows felügyeleti központ használatával](setup.md)című témakört.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, győződjön meg róla, hogy:

- 2-4 csomóponttal Azure Stack HCI-fürtöt vagy egyetlen csomópontot Azure Stack a HCI-t. **Javasoljuk, hogy egy 2-4 csomópontot Azure Stack HCI-fürtöt.** Ha nem, kövesse az [itt](./system-requirements.md)létrehozott útmutatást.
- Rendelkeznie kell egy Azure Stack Kubernetes-gazdagép beállításával. Ha nem, kövesse az [itt](./setup-powershell.md)beállított útmutatást.

## <a name="step-1-create-a-kubernetes-cluster"></a>1. lépés: Kubernetes-fürt létrehozása

Az Azure Kubernetes Service Host telepítése után készen áll egy Kubernetes-fürt üzembe helyezésére.

Nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő parancsot.

   ```powershell
   New-AksHciCluster -clusterName
                    [-kubernetesVersion]
                    [-controlPlaneNodeCount]
                    [-linuxNodeCount]
                    [-windowsNodeCount]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-linuxNodeVmSize]
                    [-windowsNodeVmSize]
   ```

### <a name="required-parameters"></a>Szükséges paraméterek

`-clusterName`

A Kubernetes-fürt alfanumerikus neve.

### <a name="optional-parameters"></a>Választható paraméterek

`-kubernetesVersion`

A telepíteni kívánt Kubernetes verziója. Az alapértelmezett érték a v 1.18.6. Az elérhető verziók listájának lekéréséhez futtassa a parancsot `Get-AksHciKubernetesVersion` .

`-controlPlaneNodeCount`

A vezérlő síkja csomópontjainak száma. Az alapértelmezett érték 1.

`-linuxNodeCount`

A Kubernetes-fürtben található Linux-csomópontok száma. Az alapértelmezett érték 1.

`-windowsNodeCount`

A Kubernetes-fürtben lévő Windows-csomópontok száma. Az alapértelmezett érték a 0.

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
Set-AksHciClusterNodeCount –clusterName
                           -controlPlaneNodeCount
```

A munkavégző csomópontok méretezéséhez futtassa a következő parancsot.

```powershell
Set-AksHciClusterNodeCount –clusterName
                           -linuxNodeCount
                           -windowsNodeCount
```

A vezérlési sík csomópontjait és a munkavégző csomópontokat egymástól függetlenül kell méretezni.

## <a name="step-3-upgrade-kubernetes-version"></a>3. lépés: a Kubernetes verziójának frissítése

A jelenleg futtatott Kubernetes-verzió megtekintéséhez futtassa a következő parancsot.

```powershell
Get-AksHciKubernetesVersion
```

A következő Kubernetes-verzióra való frissítéshez futtassa a következő parancsot.

```powershell
Update-AksHciCluster -clusterName
```

Ha Windows-csomópontokat kíván használni, a minimálisan szükséges verzió a v 1.1.8.6.

## <a name="step-4-access-your-clusters-using-kubectl"></a>4. lépés: a fürtök elérése a kubectl használatával

Az Azure Kubernetes Service Host vagy a Kubernetes-fürt kubectl használatával való eléréséhez futtassa a következő parancsot. Ez a megadott fürt kubeconfig-fájlját fogja használni a kubectl alapértelmezett kubeconfig-fájljához.

```powershell
Set-AksHciKubeConfig -clusterName
```

## <a name="delete-a-kubernetes-cluster"></a>Kubernetes-fürt törlése

Ha törölnie kell egy Kubernetes-fürtöt, futtassa a következő parancsot.

```powershell
Remove-AksHciCluster -clusterName
```

## <a name="get-logs"></a>Naplók lekérése

Az összes hüvelyből származó naplók lekéréséhez futtassa a következő parancsot. Ez a parancs létrehoz egy nevű kimeneti tömörített mappát `akshcilogs` az elérési úton `C:\wssd\akshcilogs` .

```powershell
Get-AksHciLogs
```

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre, méretezheti és frissíthet egy Kubernetes-fürtöt a PowerShell használatával.

## <a name="next-steps"></a>Következő lépések

- [Kösse össze a fürtöket az Azure arc Kubernetes](./connect-to-arc.md).
- [Linux-alkalmazás üzembe helyezése a Kubernetes-fürtön](./deploy-linux-application.md).
- [Helyezzen üzembe egy Windows-alkalmazást a Kubernetes-fürtön](./deploy-windows-application.md).
