---
title: Oktatóanyag – Windows-alkalmazás üzembe helyezése az AK-ban Azure Stack HCI-ben
description: Ebben az oktatóanyagban egy Windows-alkalmazást helyez üzembe a fürtön a Azure Container Registryban tárolt egyéni rendszerkép használatával.
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: f6bc0132dd7ce3ee9972b0aaff6d0718cab86843
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948891"
---
# <a name="tutorial-deploy-windows-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Oktatóanyag: Windows-alkalmazások központi telepítése az Azure Kubernetes szolgáltatásban Azure Stack HCI-ben

Ebben az oktatóanyagban egy ASP.NET-minta alkalmazást helyez üzembe egy Windows Server-tárolóban a Kubernetes-fürtön. Ezután megtudhatja, hogyan tesztelheti és méretezheti az alkalmazást. Ez az oktatóanyag feltételezi, hogy alapvető ismereteket mutat a Kubernetes fogalmakról. További információ: az [Azure Kubernetes Service Kubernetes alapfogalmai Azure stack HCI](kubernetes-concepts.md)-ben.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy az alábbi követelmények állnak készen:

* Egy Azure Kubernetes szolgáltatás Azure Stack HCI-fürtön legalább egy olyan Windows-feldolgozó csomóponttal, amely működik. 
* Egy kubeconfig-fájl, amely hozzáfér a fürthöz.
* Telepítse az Azure Kubernetes szolgáltatást Azure Stack HCI PowerShell-modulon.
* Futtassa a jelen dokumentumban található parancsokat egy PowerShell felügyeleti ablakban.
* Győződjön meg arról, hogy az operációsrendszer-specifikus munkaterhelések a megfelelő tároló gazdagépen vannak kihelyezve. Ha vegyes Linux-és Windows-munkavégző csomópontok Kubernetes-fürttel rendelkezik, használhatja a csomópont-választókat vagy a megromlásokat és a megkötéseket. További információ: [a csomópont-választók és a megfertőzés és a tolerancia használata](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a cikkben egy jegyzékfájlt használunk a ASP.NET-minta alkalmazás Windows Server-tárolóban való futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl egy Kubernetes-telepítést tartalmaz a ASP.NET-minta alkalmazáshoz és egy külső Kubernetes szolgáltatáshoz az alkalmazás internetről való eléréséhez.

A ASP.NET minta alkalmazás a .NET-keretrendszer mintáinak részeként van megadva, és Windows Server-tárolóban fut. A Azure Stack HCI-on futó Azure Kubernetes szolgáltatáshoz Windows Server-tárolók szükségesek a *Windows server 2019*rendszerképein alapul. 

A Kubernetes jegyzékfájljának meg kell határoznia egy csomópont-választót is, hogy a ASP.NET-minta alkalmazás Pod-t futtasson a Windows Server-tárolókat futtató csomóponton.

Hozzon létre egy nevű fájlt `sample.yaml` , és másolja a következő YAML-definícióba. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Telepítse az alkalmazást a `kubectl apply` paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f sample.yaml
```

A következő példa kimenete a központi telepítés és a szolgáltatás sikeres létrehozását mutatja be:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig. Alkalmanként a szolgáltatás több percig is eltarthat. Ezekben az esetekben akár 10 percet is igénybe vehet.

A folyamat figyeléséhez használja az `kubectl get service` `--watch` argumentumot a paranccsal.

```PowerShell
kubectl get service sample --watch
```

Kezdetben a *minta* szolgáltatás *külső IP-címe* *függőben*jelenik meg.

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

A minta alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![A ASP.NET-minta alkalmazás tallózásának képe](media/deploy-windows-application/asp-net-sample-app.png)

Ha a lap betöltésére tett kísérlet közben kapcsolati időtúllépést kap, ellenőrizze, hogy a minta alkalmazás készen áll-e a `kubectl get pods --watch` parancsra. Előfordulhat, hogy a külső IP-cím a Windows-tároló elindítása előtt elérhető.

## <a name="scale-application-pods"></a>Alkalmazás-hüvelyek méretezése

Létrehozta az alkalmazás kezelőfelületének egyetlen replikáját. A fürtben található hüvelyek számának és állapotának megtekintéséhez használja a `kubectl get` parancsot a következő módon:

```console
kubectl get pods -n default
```

A *minta* -telepítésben lévő hüvelyek számának módosításához használja az `kubectl scale` parancsot. Az alábbi példa az előtér-hüvelyek számát *3*-ra emeli:

```console
kubectl scale --replicas=3 deployment/sample
```

Futtassa `kubectl get pods` újra a parancsot a további hüvelyek létrehozásának ellenőrzéséhez. Hozzávetőleg egy perc elteltével az újabb podok elérhetők a fürtön:

```console
kubectl get pods -n default
```

## <a name="next-steps"></a>Következő lépések

* [A fürt és az alkalmazás figyeléséhez használja a Azure monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Használjon állandó tárterületet, és konfigurálja a gMSA-támogatást egy Windows-tárolóban](persistent-storage-windows-nodes.md).
