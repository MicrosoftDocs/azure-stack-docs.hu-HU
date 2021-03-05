---
title: Tároló-tároló hozzáadása az Kubernetes-hez Azure Stack hub-ban
description: Megtudhatja, hogyan adhat hozzá tároló-tárolót a Kubernetes-hez Azure Stack hub-ban.
author: mattbriggs
ms.topic: how-to
ms.date: 3/4/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/4/2021
ms.openlocfilehash: ee176f8e111c6e10831124b78afd99535d2fe7ee
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102233853"
---
# <a name="add-container-storage-to-kubernetes-in-azure-stack-hub"></a>Tároló-tároló hozzáadása az Kubernetes-hez Azure Stack hub-ban

> [!IMPORTANT]  
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Kubernetes közösségi tevékenységének részeként ([a Kubernetes a CSI-kötetek áttelepítésére](https://kubernetes.io/blog/2019/12/09/kubernetes-1-17-feature-csi-migration-beta/)) a faszintű szolgáltatók a tárolóba való áthelyezéséhez a következő két [CSI-illesztőprogram](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)található a Azure stack: Azure Disk és NFS.

|                       | **Azure Disk CSI-illesztőprogram**                                                                                                    | **NFS CSI-illesztőprogram**                                                       |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| Projekt tárháza    | [azuredisk-CSI-illesztőprogram](https://github.com/kubernetes-sigs/azuredisk-csi-driver)                                              | [CSI-illesztőprogram – NFS](https://github.com/kubernetes-csi/csi-driver-nfs)       |
| CSI-illesztőprogram verziója    | v 1.0.0 +                                                                                                                      | v 3.0.0 +                                                                  |
| Hozzáférési mód           | ReadWriteOnce                                                                                                                | ReadWriteOnce ReadOnlyMany ReadWriteMany                                 |
| Windows-ügynök csomópontja    | Támogatás                                                                                                                      | Nem támogatott                                                              |
| Dinamikus kiépítés  | Támogatás                                                                                                                      | Támogatás                                                                  |
| Megfontolandó szempontok        | [Az Azure Disk CSI-illesztőprogram korlátozásai](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/limitations.md) | A felhasználók felelősek lesznek az NFS-kiszolgáló beállításához és karbantartásához. |
| Slack támogatási csatorna | [\#Szolgáltató – Azure](https://kubernetes.slack.com/archives/C5HJXTT9Q)                                                          | [\#SIG-Storage](https://kubernetes.slack.com/archives/C09QZFCE5)         |

A leválasztott környezetek jelenleg nem támogatják a CSI-illesztőprogramokat.

## <a name="requirements"></a>Követelmények

-   Azure Stack Build 2011-es és újabb verziók.
-   KABAi motor v 0.60.1 és újabb verziók.
-   A Kubernetes 1,18-es és újabb verziója.
-   Mivel a CSI-illesztőprogramok vezérlő kiszolgálójának két replikára van szüksége, egyetlen csomópontos főkészletet nem ajánlott használni.
-   [3. Helm](https://helm.sh/docs/intro/install/)

## <a name="install-and-uninstall-csi-drivers"></a>A CSI-illesztőprogramok telepítése és eltávolítása

Ebben a szakaszban a statefulset-alkalmazáshoz használt CSI-illesztőprogramot üzembe helyező példákat követve telepítheti a parancsokat.

## <a name="azure-disk-csi-driver"></a>Azure Disk CSI-illesztőprogram

### <a name="install-csi-driver"></a>A CSI-illesztőprogram telepítése

```bash  
helm repo add azuredisk-csi-driver https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/charts
helm install azuredisk-csi-driver azuredisk-csi-driver/azuredisk-csi-driver --namespace kube-system --set cloud=AzureStackCloud --set controller.runOnMaster=true --version v1.0.0

```
### <a name="deploy-storage-class"></a>Tárolási osztály üzembe helyezése

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/storageclass-azuredisk-csi-azurestack.yaml
```

### <a name="deploy-example-statefulset-application"></a>Példa statefulset-alkalmazás üzembe helyezése

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/statefulset.yaml
```

### <a name="validate-volumes-and-applications"></a>Kötetek és alkalmazások ellenőrzése

Ekkor meg kell jelennie az időbélyegek sorrendjének a köteten.

```bash  
kubectl exec statefulset-azuredisk-0 -- tail /mnt/azuredisk/outfile
```

### <a name="delete-example-statefulset-application"></a>Példa statefulset-alkalmazás törlése

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/statefulset.yaml
```

### <a name="delete-storage-class"></a>Tárolási osztály törlése

A tárolási osztály törlése előtt győződjön meg arról, hogy a tároló osztályt használó hüvelyek megszakadtak.

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/storageclass-azuredisk-csi-azurestack.yaml
```

### <a name="uninstall-csi-driver"></a>A CSI-illesztőprogram eltávolítása

```bash  
helm uninstall azuredisk-csi-driver --namespace kube-system
helm repo remove azuredisk-csi-driver
```

## <a name="nfs-csi-driver"></a>NFS CSI-illesztőprogram

### <a name="install-csi-driver"></a>A CSI-illesztőprogram telepítése

```bash  
helm repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
helm install csi-driver-nfs csi-driver-nfs/csi-driver-nfs --namespace kube-system --set controller.runOnMaster=true --version v3.0.0
```

### <a name="deploy-nfs-server"></a>NFS-kiszolgáló üzembe helyezése

> [!NOTE]  
> Az NFS-kiszolgáló csak az érvényesítéshez szükséges, az NFS-kiszolgáló beállítása és karbantartása éles környezetben.

Az NFS-kiszolgáló beállítása és karbantartása éles környezetben.

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="deploy-storage-class"></a>Tárolási osztály üzembe helyezése

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/storageclass-nfs.yaml
```

### <a name="deploy-example-statefulset-application"></a>Példa statefulset-alkalmazás üzembe helyezése

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/statefulset.yaml
```

### <a name="validate-volumes-and-applications"></a>Kötetek és alkalmazások ellenőrzése

Ekkor meg kell jelennie az időbélyegek sorrendjének a köteten.

```bash  
kubectl exec statefulset-nfs-0 -- tail /mnt/nfs/outfile
```

### <a name="delete-example-statefulset-application"></a>Példa statefulset-alkalmazás törlése

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="delete-storage-class"></a>Tárolási osztály törlése

A tárolási osztály törlése előtt győződjön meg arról, hogy a tároló osztályt használó hüvelyek megszakadtak.

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/storageclass-nfs.yaml
```
### <a name="delete-example-nfs-server"></a>Példa NFS-kiszolgáló törlése

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="uninstall-csi-driver"></a>A CSI-illesztőprogram eltávolítása

```bash  
helm uninstall csi-driver-nfs --namespace kube-system
helm repo remove csi-driver-nfs
```
## <a name="azure-disk-csi-driver-limitations-on-azure-stack-hub"></a>Az Azure Disk CSI-illesztőprogram korlátozásai az Azure Stack hub-ban

-   Az Azure Disk IOPS értéke 2300, a [Azure stack hub által támogatott](azure-stack-vm-sizes.md) virtuálisgép-méretek ismertetése.
-   Azure Stack hub nem támogatja a megosztott lemez használatát, így `maxShares` az 1-nél nagyobb paraméterek nem érvényesek StorageClass.
-   Az Azure Stack hub csak a szabványos, helyileg redundáns (Standard_LRS) és prémium szintű helyileg redundáns (Premium_LRS) Storage-fiókok típusait támogatja, így csak Standard_LRS és Premium_LRS érvényesek az a paraméterhez `skuName` `StorageClass` .
-   Azure Stack hub nem támogatja a növekményes lemezek pillanatképét, így csak a False érték használható az a paraméterhez `incremental` `VolumeSnapshotClass` .
-   A Windows-ügynök csomópontjain telepítenie kell a Windows CSI-proxyt, lásd: [Windows CSI proxy](https://github.com/kubernetes-csi/csi-proxy). Ha a proxyt az KABAi Motor API-modelljén keresztül szeretné engedélyezni, tekintse meg a [Windowshoz készült CSI proxyt](https://github.com/Azure/aks-engine/blob/master/docs/topics/csi-proxy-windows.md).

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)  
