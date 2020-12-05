---
title: Oktatóanyag – Linux-alkalmazás üzembe helyezése az AK-ban Azure Stack HCI-ben
description: Ebben az oktatóanyagban egy többtárolós linuxos alkalmazást helyez üzembe a fürtön a Azure Container Registryban tárolt egyéni rendszerkép használatával.
author: abha
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: e5f7f96956248dc7cc2c92ae678970b40951ece4
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612301"
---
# <a name="tutorial-deploy-linux-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Oktatóanyag: linuxos alkalmazások üzembe helyezése az Azure Kubernetes szolgáltatásban Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, AK Runtime a Windows Server 2019 Datacenter rendszeren

Ebben az oktatóanyagban egy többtárolós alkalmazást helyez üzembe, amely egy webes kezelőfelületet és egy Redis adatbázis-példányt tartalmaz az Azure Kubernetes szolgáltatásban Azure Stack HCI-fürtön. Ezután megtudhatja, hogyan tesztelheti és méretezheti az alkalmazást. 

Ez az oktatóanyag feltételezi, hogy alapvető ismereteket mutat a Kubernetes fogalmakról. További információ: az [Azure Kubernetes Service Kubernetes alapfogalmai Azure stack HCI](kubernetes-concepts.md)-ben.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy az alábbi követelmények állnak készen:

* Egy Azure Kubernetes szolgáltatás Azure Stack HCI-fürtön, és legalább egy Linux-feldolgozói csomópont működik. 
* Egy kubeconfig-fájl, amely hozzáfér a fürthöz.
* Telepítse az Azure Kubernetes szolgáltatást Azure Stack HCI PowerShell-modulon.
* Futtassa a jelen dokumentumban található parancsokat egy PowerShell felügyeleti ablakban.
* Győződjön meg arról, hogy az operációsrendszer-specifikus munkaterhelések a megfelelő tároló gazdagépen vannak kihelyezve. Ha vegyes Linux-és Windows-munkavégző csomópontok Kubernetes-fürttel rendelkezik, használhatja a csomópont-választókat vagy a megromlásokat és a megkötéseket. További információ: [a csomópont-választók és a megfertőzés és a tolerancia használata](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az [Azure vote-alkalmazás](https://github.com/Azure-Samples/azure-voting-app-redis)futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két Kubernetes-telepítést tartalmaz – egyet az Azure-beli szavazás Python-alkalmazásaihoz, a másikat pedig egy Redis-példányhoz. Két Kubernetes-szolgáltatás is létrejön – egy belső szolgáltatás a Redis-példányhoz, és egy külső szolgáltatás, amely az Azure vote alkalmazást az internetről éri el.

Hozzon létre egy nevű fájlt `azure-vote.yaml` , és másolja a következő YAML-definícióba.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Telepítse az alkalmazást a `kubectl apply` paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```PowerShell
kubectl apply -f azure-vote.yaml
```

A következő példa kimenete a sikeresen létrehozott központi telepítéseket és szolgáltatásokat mutatja:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

A folyamat figyeléséhez használja az `kubectl get service` `--watch` argumentumot a paranccsal.

```PowerShell
kubectl get service azure-vote-front --watch
```

Kezdetben a *külső IP-cím* az *Azure-vote-elülső* szolgáltatáshoz *függőben* jelenik meg.

```output
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27      <pending>     80:30572/TCP   22m
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   24m
```

Az Azure vote alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![Egy Azure-beli Kubernetes-fürt képe](media/deploy-linux-application/azure-vote.png)

## <a name="scale-application-pods"></a>Alkalmazás-hüvelyek méretezése

Létrehozta az Azure vote előtér-és Redis-példányának egyetlen replikáját. A fürtben található hüvelyek számának és állapotának megtekintéséhez használja a `kubectl get` parancsot a következő módon:

```console
kubectl get pods -n default
```

A következő példa kimenete egy előtér-Pod és egy háttér-hüvelyt mutat be:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1       Running   0          25m
azure-vote-front-84c8bf64fc-cdq86   1/1       Running   0          25m
```

Ha módosítani szeretné a hüvelyek számát az *Azure-vote-előtérben* üzemelő példányban, használja az `kubectl scale` parancsot. Az alábbi példa az előtér-hüvelyek számát *5*-re emeli:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Futtassa `kubectl get pods` újra a parancsot a további hüvelyek létrehozásának ellenőrzéséhez. Hozzávetőleg egy perc elteltével az újabb podok elérhetők a fürtön:

```console
kubectl get pods -n default

Name                                READY   STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1     Running   0          31m
azure-vote-front-84c8bf64fc-cdq86   1/1     Running   0          31m
azure-vote-front-84c8bf64fc-56h64   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-djkp8   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-jmmvs   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-znc6z   1/1     Running   0          80s
```

## <a name="next-steps"></a>További lépések

* [A fürt és az alkalmazás figyeléséhez használja a Azure monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
