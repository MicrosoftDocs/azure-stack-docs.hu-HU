---
title: Állandó kötet használata Azure Stack HCI-fürtön található AK-ban
description: Állandó tárterület használata Windows-tárolókban és Windows-csomópontok előkészítése csoportosan felügyelt szolgáltatásfiókok számára
author: v-susbo
ms.topic: how-to
ms.date: 12/02/2020
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 4baaf83c16b3fc290d9dc1339aaf432dc05d89b7
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612607"
---
# <a name="use-persistent-volume-in-an-aks-on-azure-stack-hci-cluster"></a>Állandó kötet használata Azure Stack HCI-fürtön található AK-ban

> A következőkre vonatkozik: Azure Stack HCI, AK Runtime a Windows Server 2019 Datacenter rendszeren

Az állandó kötet a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelöli. Egy állandó kötetet egy vagy több hüvely is használhat, és hosszú távú tárolást jelent. Emellett a pod vagy a Node életciklustól függetlenül is. Míg a Windows-és Linux-csomópontokhoz is létrehozhat egy PVC-t, ebben a szakaszban láthatja, hogyan hozhat létre állandó kötetet, és hogyan használhatja ezt a kötetet a Windows-alkalmazásban. További információ: [perzisztens kötetek a Kubernetes-ben](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

## <a name="before-you-begin"></a>Előkészületek

A kezdéshez a következők szükségesek:

* Egy legalább egy Windows-munkavégző csomóponttal rendelkező Kubernetes-fürt.
* Egy kubeconfig-fájl, amely hozzáfér a Kubernetes-fürthöz.

## <a name="create-a-persistent-volume-claim"></a>Állandó kötet jogcímek létrehozása

Az állandó mennyiségi jogcímek a tárolók tárolási osztály alapján történő automatikus kiépítésére szolgálnak. Mennyiségi jogcím létrehozásához először hozzon létre egy nevű fájlt, `pvc-akshci-csi.yaml` és másolja a következő YAML-definícióba. A jogcím 10 GB méretű lemezt kér *ReadWriteOnce*-   hozzáféréssel. Az *alapértelmezett*   tárolási osztály a tárolási osztályként (vhdx) van megadva.  

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: pvc-akshci-csi
spec:
 accessModes:
 - ReadWriteOnce
 resources:
  requests:
   storage: 10Gi
```
A kötet létrehozásához futtassa a következő parancsokat egy felügyeleti PowerShell-munkamenetben az Azure Stack HCI-fürt egyik kiszolgálóján (például az [ENTER-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession) vagy a távoli asztal használatával a kiszolgálóhoz való kapcsolódáshoz): 

```
kubectl create -f pvc-akshci-csi.yaml 
```
A következő kimenet azt mutatja, hogy az állandó kötet jogcíme sikeresen létrejött:

**Kimeneti**
```
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>Állandó kötet használata

Állandó kötet használatához hozzon létre egy winwebserver. YAML nevű fájlt, és másolja a következő YAML-definícióba. Ezután létre fog hozni egy Pod-t, amely hozzáférést biztosít az állandó mennyiségi jogcímek és a vhdx számára. 

Az alábbi YAML-definícióban a *mountPath* a kötet egy tárolón belüli csatlakoztatásának elérési útja. Sikeres Pod-létrehozás után megjelenik a *\\ C* -ben létrehozott alkönyvtár *mnt* , valamint a *mnt* belül létrehozott alkönyvtár *akshciscsi* .


```yaml
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  labels: 
    app: win-webserver 
  name: win-webserver 
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: win-webserver 
  template: 
    metadata: 
      labels: 
        app: win-webserver 
      name: win-webserver 
    spec: 
     containers: 
      - name: windowswebserver 
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019 
        ports:  
          - containerPort: 80    
        volumeMounts: 
            - name: akshciscsi 
              mountPath: "/mnt/akshciscsi" 
     volumes: 
        - name: akshciscsi 
          persistentVolumeClaim: 
            claimName:  pvc-akshci-csi 
     nodeSelector: 
      kubernetes.io/os: windows 
```

A fenti YAML-definícióval rendelkező Pod létrehozásához futtassa a következőt:
```
kubectl create -f winwebserver.yaml 
```

A következő parancs futtatásával gondoskodhat arról, hogy a pod fut:. Várjon néhány percet, amíg a pod fut állapotban van, mivel a rendszerkép húzása időt vesz igénybe. 
```
kubectl get pods -o wide 
```
Miután a pod fut, tekintse meg a pod állapotát a következő parancs futtatásával: 
```
kubectl.exe describe pod %podName% 
```

Az alábbi parancs futtatásával ellenőrizheti, hogy a kötet csatlakoztatva van-e a hüvelyben:
```
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Állandó kötet jogcímek törlése

Az állandó mennyiségi jogcímek törlése előtt törölnie kell az alkalmazás központi telepítését a következő futtatásával:
```
kubectl delete deployments win-webserver
```

Ezután törölheti az állandó kötet jogcímet a futtatásával:
```
kubectl delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="next-steps"></a>További lépések
- [Helyezzen üzembe egy Windows-alkalmazást a Kubernetes-fürtön](./deploy-windows-application.md).
- [Kapcsolja össze a Kubernetes-fürtöt az Azure arc-Kubernetes](./connect-to-arc.md).
