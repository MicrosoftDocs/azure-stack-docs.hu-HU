---
title: Azure Monitor használata a tárolók számára Azure Stack központban
description: Megtudhatja, hogyan használhatja a Azure Monitor for containers szolgáltatást Azure Stack hub-on.
author: mattbriggs
ms.topic: article
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 5b3172695fd6e0536360eed2dc4e370dd8bccecc
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885247"
---
# <a name="use-azure-monitor-for-containers-on-azure-stack-hub"></a>Azure Monitor használata a tárolók számára Azure Stack központban

Az [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/) for containers használatával figyelheti a tárolókat egy Kubernetes-fürtben a Azure stack hub-ban. 

> [!IMPORTANT]
> A Azure Stack hub tárolók Azure Monitor jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A tárolók teljesítményének áttekinthető Azure Monitor azáltal, hogy a metrikák API használatával gyűjti a Kubernetes elérhető vezérlőket, csomópontokat és tárolókat a memória és a processzor metrikáinak összegyűjtésével. Emellett a szolgáltatás a tároló naplóit is gyűjti. Ezeket a naplókat a helyszíni fürt Azure-ból származó problémáinak diagnosztizálására használhatja. Miután beállította a Kubernetes-fürtök figyelését, a rendszer automatikusan összegyűjti ezeket a metrikákat és naplókat. A Azure Monitor Log Analytics Agent Linux-ügynökének egy tárolós verziója gyűjti a naplókat. Azure Monitor tárolja a log Analytics-munkaterületen elérhető metrikákat és naplókat az Azure-előfizetésében.

A Azure Monitor kétféleképpen engedélyezhető a fürtön. Mindkét módszerhez szükség van egy Azure Monitor Log Analytics-munkaterület beállítására az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

Mindkét módszerhez az [Azure monitor – tárolókban](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers)felsorolt [Előfeltételek](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers#pre-requisites) szükségesek.

## <a name="method-one"></a>Egy metódus

A rendszer a [Helm](https://helm.sh/) diagram segítségével is telepítheti a figyelési ügynököket a fürtben. Kövesse az alábbi, [Azure monitor – tárolók](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers)című cikk utasításait.

## <a name="method-two"></a>Második módszer

Megadhat egy **addont** az AK-motor fürt specifikációjának JSON-fájljában. Ezt a fájlt API-modellnek is nevezik. Ebben az addonban adja meg az Azure Log Analytics munkaterület **WorkspaceGUID** és **WorkspaceKey** Base64 kódolású verzióját, ahol a figyelési adatokat tárolni fogja.

Az Azure Stack hub-fürt támogatott API-definíciói a következő példában találhatók: [kubernetes-Container-monitoring_existing_workspace_id_and_key. JSON](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Pontosabban keresse meg az **addons** tulajdonságot a **kubernetesConfig**-ben:

```JSON  
 "orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Guid in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)  
- További információ a [tárolók Azure monitoráról – áttekintés](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)
