---
title: A szolgáltatásfiók-jogkivonat mennyiségének kivetítésének engedélyezése az AK-motorhoz Azure Stack hub-on
description: Megtudhatja, hogyan engedélyezheti a szolgáltatásfiók-tokenek mennyiségi kivetítését Azure Stack hub AK-motorja számára
author: mattbriggs
ms.topic: article
ms.date: 10/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 5e712577bb01f7f084c24eadfd3931ebb67f23d9
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906162"
---
# <a name="enabling-service-account-token-volume-projection-for-the-aks-engine-on-azure-stack-hub"></a>A szolgáltatásfiók-jogkivonat mennyiségének kivetítésének engedélyezése az AK-motorhoz Azure Stack hub-on

A Istio egy konfigurálható, nyílt forráskódú Service-Mesh réteg, amely összekapcsolja, figyeli és védi a tárolókat egy Kubernetes-fürtben. A Istio 1,3-es és újabb verziója a Kubernetes *nevű szolgáltatást* használja. Ez a funkció alapértelmezés szerint nincs engedélyezve az AK-motor által üzembe helyezett Kubernetes-fürtökben. Ebből a cikkből megtudhatja, hogy az API-modell JSON-tulajdonságai milyen `apiServerConfig` KUBERNETES API-kiszolgáló jelzőket jelenítenek meg a fürthöz tartozó szolgáltatásfiók-jogkivonat mennyiségi leképezésének engedélyezéséhez.

A szolgáltatásfiók-jogkivonat mennyiségi kivetítésével kapcsolatos további információkért lásd: [szolgáltatásfiók-jogkivonat kötetének leképezése](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection).

## <a name="enable-service-account-token-volume-projection"></a>Szolgáltatásfiók-jogkivonat mennyiségi kivetítésének engedélyezése

A szolgáltatásfiók-jogkivonat mennyiségi leképezésének engedélyezéséhez adja hozzá a következő beállításokat az API-modell JSON-fájljához. 

```json
{
    "kubernetesConfig": {
        "apiServerConfig": {
            "--service-account-api-audiences": "api,istio-ca",
            "--service-account-issuer": "kubernetes.default.svc",
            "--service-account-signing-key-file": "/etc/kubernetes/certs/apiserver.key"
        }
    }
}
```

> [!Note]  
> Előfordulhat, hogy módosítania kell az `--service-account-api-audiences` `--service-account-issuer` adott használati esetet.

A teljes példa API-modell esetében tekintse [ meg aistio.json](https://github.com/Azure/aks-engine/blob/master/examples/service-mesh/istio.json)című témakört.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)
- [Kubernetes-fürt frissítése Azure Stack hub-on](azure-stack-kubernetes-aks-engine-upgrade.md)
