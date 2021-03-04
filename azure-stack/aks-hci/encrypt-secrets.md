---
title: Etcd-titkok titkosítása az AK-on Azure Stack HCI-on
description: Ismerje meg, hogyan titkosíthatja a etcd titkokat az AK-on Azure Stack HCI-on
author: aabhathipsay
ms.topic: how-to
ms.date: 02/02/2021
ms.author: aaabhathipsay
ms.reviewer: ''
ms.openlocfilehash: d79d6657197237b5806e419d94fbfb75a31a986c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873804"
---
# <a name="encrypt-etcd-secrets-on-aks-on-azure-stack-hci-clusters"></a>Etcd-titkok titkosítása az AK-on Azure Stack HCI-fürtökön

A Kubernetes egy titka egy olyan objektum, amely kis mennyiségű bizalmas adatot tartalmaz, például jelszavakat és SSH-kulcsokat. A Kubernetes API-kiszolgálón a titkos kulcsokat a rendszer a _etcd_ tárolja, amely egy, a Kubernetes-tárolóban tárolt adatok tárolására szolgáló, kulcsfontosságú értékeket tartalmazó tároló. Ha engedélyezi a etcd-titkok titkosítását, [titkosíthatja a titkos adatokat](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) , és automatizálhatja a titkosítási kulcsok kezelését és rotációját is. 

> [!NOTE]
> Ebben az előzetes kiadásban a etcd-titkok titkosítása csak az új munkaterhelés-fürtök esetében érhető el. 

## <a name="enable-encryption-of-etcd-secrets"></a>Etcd-titkok titkosításának engedélyezése

A `-enableSecretsEncryption` [New-AksHciCluster](./new-akshcicluster) parancs paraméterének használatával engedélyezheti a etcd-titkok titkosítását, és automatizálhatja a titkosítási kulcs rotációját az alábbiak szerint: 

```powershell
New-AksHciCluster -name mynewcluster -enableSecretsEncryption
```

Ha új fürtöt telepít a `-enableSecretsEncryption` paraméterrel, a funkció nem tiltható le.

## <a name="monitor-and-troubleshoot"></a>Figyelés és hibaelhárítás

Az alkalmazások Kubernetes-fürtökön való üzembe helyezésének egyszerűsítéséhez tekintse át az elérhető [dokumentációt és parancsfájlokat](https://github.com/microsoft/AKS-HCI-Apps) .

- A Elasticsearch, a Fluent bites és a Kibana használatával történő naplózás beállításához kövesse az [eszközök telepítésének és a naplózás beállításának](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging) lépéseit.
- A [Kubernetes-fürtben a Prometheus telepítéséhez](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#certs-and-keys-monitoring) kövesse a következő témakört: a monitoring Tool Prometheus használata.

> [!NOTE]
> A naplók a vezérlő síkja csomópont alatt találhatók `/var/log/pods` .

## <a name="additional-resources"></a>További források

- [Titkos adatok titkosítása nyugalmi állapotban](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data)
- [KMS-szolgáltató használata adattitkosításhoz](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/)

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan engedélyezheti a etcd-titkok titkosítását az új munkaterhelés-fürtök esetében. Következő lépésként a következőket teheti:
- [Linux-alkalmazások üzembe helyezése Kubernetes-fürtön](./deploy-linux-application.md).
- [Windows Server-alkalmazás üzembe helyezése Kubernetes-fürtön](./deploy-windows-application.md).
