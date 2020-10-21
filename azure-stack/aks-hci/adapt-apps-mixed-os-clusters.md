---
title: Alkalmazások átalakítása kevert operációs rendszerű Kubernetes-fürtökben való használatra
description: Az Azure Kubernetes szolgáltatásban a csomópont-választókkal, illetve az adatkezelési és-megtartási lehetőségekkel biztosítható, hogy az alkalmazások a Azure Stack HCI-on futó vegyes operációsrendszer-fürtökön legyenek ütemezve a megfelelő munkavégző csomópont operációs rendszeren.
author: abha
ms.topic: how-to
ms.date: 10/20/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 04b103fee921cf8bdab82a4004c6c80afd54d687
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253945"
---
# <a name="adapt-apps-for-mixed-os-kubernetes-clusters-using-node-selectors-or-taints-and-tolerations"></a>Alkalmazások adaptálása vegyes operációs rendszerű Kubernetes-fürtökhöz csomópont-választókkal vagy a megfertőzés és a tolerancia használatával

Az Azure Kubernetes Service on Azure Stack HCI lehetővé teszi, hogy Linux-és Windows-csomópontokkal is futtasson Kubernetes-fürtöket, de az alkalmazásokban kis méretű módosításokat kell végeznie a vegyes operációs rendszerű fürtökben való használatra. Ebben a útmutatóban megtudhatja, hogyan gondoskodhat arról, hogy az alkalmazás a megfelelő gazdagép operációs rendszerének megfelelően legyen ütemezve a csomópont-választókkal vagy a megromlások és a tolerancia használatával.

Ez a útmutató a Kubernetes alapelveinek ismeretét feltételezi. További információ: az [Azure Kubernetes Service Kubernetes alapfogalmai Azure stack HCI](kubernetes-concepts.md)-ben.

## <a name="node-selector"></a>Csomópont-választó

A *csomópont-választó* egy egyszerű mező a pod specifikáció YAML, amely korlátozza a hüvelyeket, hogy csak az operációs rendszernek megfelelő, kifogástalan állapotú csomópontokra legyenek ütemezve. A pod specifikáció YAML adja meg a  `nodeSelector`   -Windows vagy Linux rendszert, ahogy az alábbi példákban is látható. 

```yaml
kubernetes.io/os = Windows
```
vagy

```yaml
kubernetes.io/os = Linux
```

További információt a nodeSelectors a csomópont- [választókkal](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)foglalkozó témakörben találhat. 

## <a name="taints-and-tolerations"></a>A szennyező és a tolerálás

A *megfertőzés* és a *tolerancia* együttesen biztosítják, hogy a hüvelyek ne legyenek szándékosan ütemezve a csomópontokon. Egy csomópont "megfertőzhető", hogy ne fogadja el azokat a hüvelyeket, amelyek nem tudják explicit módon elviselni a romlottságot a pod specifikáció YAML.

Az Azure Kubernetes Service-ben a Azure Stack HCI-ben futó Windows OS-csomópontok a következő kulcs-érték párokkal oldhatók meg. A felhasználók nem használhatnak másikat.

```yaml
node.kubernetes.io/os=Windows:NoSchedule
```
Futtassa `kubectl get` és azonosítsa a megfertőzni kívánt Windows-munkavégző csomópontokat.

```PowerShell
kubectl get nodes --all-namespaces -o=custom-columns=NAME:.metadata.name,OS:.status.nodeInfo.operatingSystem
```
Kimenet:
```output
NAME                                     OS
my-aks-hci-cluster-control-plane-krx7j   linux
my-aks-hci-cluster-md-md-1-5h4bl         windows
my-aks-hci-cluster-md-md-1-5xlwz         windows
```

A Windows Server Worker csomópontjainak használata a használatával `kubectl taint node` .

```PowerShell
kubectl taint node my-aks-hci-cluster-md-md-1-5h4bl node.kubernetes.io/os=Windows:NoSchedule
kubectl taint node my-aks-hci-cluster-md-md-1-5xlwz node.kubernetes.io/os=Windows:NoSchedule
```

A pod specifikáció YAML megadhat egy Pod-t. A következő tolerancia "egyezik" a fenti kubectl szennyező vonal által létrehozott szennyező adataival, így a tolerálható Pod a saját-AK-HCI-cluster-MD-MD-1-5h4bl vagy a My-AK-HCI-cluster-MD-MD-1-5xlwz:

```yaml
tolerations:
- key: node.kubernetes.io/os
  operator: Equal
  value: Windows
  effect: NoSchedule
```
További információ a szennyező adatokról és a megterhelésekről: a [megfertőzés és a tolerancia](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). 

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan adhat hozzá csomópont-választókat, illetve hogyan lehet a Kubernetes-fürtöket a kubectl használatával felvenni. Következő lépésként a következőket teheti:
- [Linux-alkalmazások üzembe helyezése Kubernetes-fürtön](./deploy-linux-application.md).
- [Windows Server-alkalmazás üzembe helyezése Kubernetes-fürtön](./deploy-windows-application.md).
