---
title: Gyors útmutató Kubernetes-fürtök létrehozásához Azure Stack HCI-ben a Windows PowerShell használatával
description: Megtudhatja, hogyan hozhat létre Kubernetes-fürtöket Azure Stack HCI-ben a Windows PowerShell használatával
author: jessicaguan
ms.topic: quickstart
ms.date: 2/10/2021
ms.author: jeguan
ms.openlocfilehash: 68539a77907bb0c5eef4809882c562525b93e983
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116733"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Gyors útmutató: Kubernetes-fürtök létrehozása Azure Stack HCI-ben a Windows PowerShell használatával

> A következőkre vonatkozik: Azure Stack HCI, AK Runtime a Windows Server 2019 Datacenter rendszeren

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Kubernetes-fürtöt Azure Stack HCI-ben a Windows PowerShell használatával. Ezután megtudhatja, hogyan méretezheti a Kubernetes-fürtöt, és hogyan frissítheti a fürt Kubernetes verzióját. Ha ehelyett a Windows felügyeleti központot szeretné használni, olvassa el az [Azure Kubernetes szolgáltatás beállítása Azure stack HCI-ben a Windows felügyeleti központ használatával](setup.md)című témakört.

## <a name="before-you-begin"></a>Előkészületek

 - Győződjön meg arról, hogy rendelkezik egy Azure Stack Kubernetes-gazdagép beállításával. Ha nem, tekintse [meg a rövid útmutató: Azure Kubernetes Service Host beállítása a Azure stack HCI-ben a PowerShell használatával](./setup-powershell.md)című témakört.
 - Győződjön meg arról, hogy telepítve van a legújabb Aks-Hci PowerShell-modul. Ha nem, olvassa el [a AksHci PowerShell-modul letöltése és telepítése](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module)című témakört.

## <a name="step-1-create-a-kubernetes-cluster"></a>1. lépés: Kubernetes-fürt létrehozása

Az Azure Kubernetes Service Host telepítése után készen áll egy Kubernetes-fürt üzembe helyezésére.

Nyissa meg a PowerShellt rendszergazdaként, és futtassa a következő [New-AksHciCluster](./new-akshcicluster.md) parancsot.

```powershell
New-AksHciCluster -name mycluster
```

### <a name="check-your-deployed-clusters"></a>A telepített fürtök keresése

A telepített Azure Kubernetes Service Host-és Kubernetes-fürtök listáját a következő parancs futtatásával kérheti le.

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>2. lépés: Kubernetes-fürt skálázása

Ha fel-vagy leskálázást szeretne készíteni a fürtön, a [set-AksHciClusterNodeCount](./set-akshciclusternodecount.md) parancs használatával módosíthatja a vezérlési sík csomópontjainak, a Linux-feldolgozó csomópontoknak vagy a Windows-munkavégző csomópontoknak a számát.

A vezérlési sík csomópontjainak méretezéséhez futtassa a következő parancsot.

```powershell
Set-AksHciClusterNodeCount –name mycluster -controlPlaneNodeCount 3
```

A munkavégző csomópontok méretezéséhez futtassa a következő parancsot.

```powershell
Set-AksHciClusterNodeCount –name mycluster -linuxNodeCount 3 -windowsNodeCount 1
```

A vezérlési sík csomópontjait és a munkavégző csomópontokat egymástól függetlenül kell méretezni.

## <a name="step-3-upgrade-kubernetes-version"></a>3. lépés: a Kubernetes verziójának frissítése

A következő parancs futtatásával ellenőrizze, hogy van-e elérhető frissítés a Kubernetes-fürthöz.

```powershell
Get-AksHciClusterUpgrades -name mycluster
```

A legújabb Kubernetes-verzióra való frissítéshez futtassa a következő parancsot.

```powershell
Update-AksHciCluster -name mycluster -kubernetesVersion <k8s version>
```

Ha ezt a parancsot a Kubernetes-verzió meghatározása nélkül futtatja, a rendszer frissíti a fürtöt a legújabb verzióra. Ha nem a legújabb verzióra szeretne frissíteni, használja az [Update-AksHciCluster](./update-akshcicluster.md) `-kubernetesVersion` paramétert és a kívánt verziót az értékként (azaz v 1.18.8).

## <a name="step-4-access-your-clusters-using-kubectl"></a>4. lépés: a fürtök elérése a kubectl használatával

Ha a Kubernetes-fürtöket a kubectl használatával szeretné elérni, futtassa a [Get-AksHciCredential](./get-akshcicredential.md) parancsot. Ez a megadott fürt kubeconfig-fájlját fogja használni a kubectl alapértelmezett kubeconfig-fájljához.

```powershell
Get-AksHciCredential -name mycluster
```

## <a name="delete-a-kubernetes-cluster"></a>Kubernetes-fürt törlése

Ha törölnie kell egy Kubernetes-fürtöt, futtassa a következő parancsot.

```powershell
Remove-AksHciCluster -name mycluster
```

## <a name="get-logs"></a>Naplók lekérése

Az összes hüvelyből származó naplók lekéréséhez futtassa a [Get-AksHciLogs](./get-akshcilogs.md) parancsot. Ez a parancs létrehoz egy nevű kimeneti tömörített mappát `akshcilogs.zip` a munkakönyvtárában. Az alábbi parancs futtatása után a mappa teljes elérési útja lesz `akshcilogs.zip` a kimenet.

```powershell
Get-AksHciLogs
```

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre, méretezheti és frissíthet egy fürt Kubernetes verzióját a PowerShell használatával.

## <a name="next-steps"></a>Következő lépések

- [Kösse össze a fürtöket az Azure arc Kubernetes](./connect-to-arc.md).
- [Linux-alkalmazás üzembe helyezése a Kubernetes-fürtön](./deploy-linux-application.md).
- [Helyezzen üzembe egy Windows-alkalmazást a Kubernetes-fürtön](./deploy-windows-application.md).
