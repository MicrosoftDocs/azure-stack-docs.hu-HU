---
title: Állandó tároló használata Windows-tárolóban
description: Állandó tárterület használata Windows-tárolókban és Windows-csomópontok előkészítése csoportosan felügyelt szolgáltatásfiókok számára
author: abhilashaagarwala
ms.topic: how-to
ms.date: 09/21/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 19b934e4bdec9e0ab6f4e7808dfea6e6fb648245
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94784852"
---
# <a name="use-persistent-storage-in-a-windows-container-and-prepare-windows-nodes-for-group-managed-service-accounts"></a>Állandó tárterület használata Windows-tárolókban és Windows-csomópontok előkészítése csoportosan felügyelt szolgáltatásfiókok számára

Az állandó kötet a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelöli. Egy állandó kötetet egy vagy több hüvely is használhat, és hosszú távú tárolást jelent. Emellett a pod vagy a Node életciklustól függetlenül is.  Ebből a szakaszból megtudhatja, hogyan hozhat létre állandó kötetet, és hogyan használhatja ezt a kötetet a Windows-alkalmazásban.

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


```PowerShell
kubectl create -f pvc-akshci-csi.yaml 
```
A következő kimenet azt mutatja, hogy az állandó kötet jogcíme sikeresen létrejött:

**Kimeneti**
```PowerShell
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

```PowerShell
Kubectl create -f winwebserver.yaml 
```

A következő parancs futtatásával gondoskodhat arról, hogy a pod fut:. Várjon néhány percet, amíg a pod fut állapotban van, mivel a rendszerkép húzása időt vesz igénybe.

```PowerShell
kubectl get pods -o wide 
```
Miután a pod fut, tekintse meg a pod állapotát a következő parancs futtatásával: 

```PowerShell
kubectl.exe describe pod %podName% 
```

Az alábbi parancs futtatásával ellenőrizheti, hogy a kötet csatlakoztatva van-e a hüvelyben:

```PowerShell
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Állandó kötet jogcímek törlése

Az állandó mennyiségi jogcímek törlése előtt törölnie kell az alkalmazás központi telepítését a következő futtatásával:

```PowerShell
kubectl.exe delete deployments win-webserver
```

Ezután törölheti az állandó kötet jogcímet a futtatásával:

```PowerShell
kubectl.exe delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="prepare-windows-nodes-for-group-managed-service-account-support-on-windows-nodes"></a>Windows-csomópontok előkészítése csoportosan felügyelt szolgáltatásfiókok támogatásához Windows-csomópontokon

A csoportosan felügyelt szolgáltatásfiókok olyan Active Directory-fiókok, amelyek automatikus jelszavas kezelést, egyszerűsített egyszerű szolgáltatásnév (SPN) felügyeletet biztosítanak, valamint lehetővé teszik a felügyelet delegálását más rendszergazdáknak több kiszolgáló között. A csoportosan felügyelt szolgáltatásfiókok (gMSA) a Windows-csomópontokon futó hüvelyekhez és tárolók számára történő konfigurálásához először csatlakoztatnia kell a Windows-csomópontokat egy Active Directory tartományhoz.

A csoportosan felügyelt szolgáltatásfiók támogatásának engedélyezéséhez a Kubernetes-fürt nevének 4 karakternél rövidebbnek kell lennie. Ennek az az oka, hogy a tartományhoz csatlakoztatott kiszolgálónév maximálisan támogatott hossza 15 karakter, és a munkavégző csomóponthoz Azure Stack HCI Kubernetes-fürt elnevezési konvenciójában néhány előre definiált karakter található egy csomópont neveként.

Ha a Windows-munkavégző csomópontokat tartományhoz kívánja csatlakoztatni, jelentkezzen be egy Windows Worker csomópontba `kubectl get` az érték futtatásával és megjegyzésével `EXTERNAL-IP` .

```PowerShell
kubectl get nodes -o wide
``` 

Ezután SSH-t adhat a csomóponthoz a használatával `ssh Administrator@ip` . 

Miután sikeresen bejelentkezett a Windows Worker-csomópontba, futtassa a következő PowerShell-parancsot a csomópont tartományhoz való csatlakoztatásához. A rendszer kérni fogja a **tartományi rendszergazdai fiók** hitelesítő adatainak megadását. Olyan emelt szintű felhasználói hitelesítő adatokat is használhat, amelyek jogosultságokkal rendelkeznek a számítógépek az adott tartományhoz való csatlakoztatásához. Ezután újra kell indítania a Windows Worker csomópontot.

```PowerShell
add-computer --domainame "YourDomainName" -restart
```

Miután az összes Windows-feldolgozó csomópont csatlakoztatva lett egy tartományhoz, kövesse a [gMSA konfigurálása](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa) című szakasz lépéseit a Kubernetes gMSA egyéni erőforrás-definíciók és webhookok a Kubernetes-fürtön való alkalmazásához.

A Windows-tárolóval és a gMSA kapcsolatos további információkért tekintse meg a [Windows-tárolókat és a gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Következő lépések
- [Helyezzen üzembe egy Windows-alkalmazást a Kubernetes-fürtön](./deploy-windows-application.md).
- [Kapcsolja össze a Kubernetes-fürtöt az Azure arc-Kubernetes](./connect-to-arc.md).
