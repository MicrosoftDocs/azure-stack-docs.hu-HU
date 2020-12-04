---
title: Windows-csomópontok előkészítése csoportosan felügyelt szolgáltatásfiók-támogatáshoz
description: Megtudhatja, hogyan konfigurálhat csoportosan felügyelt szolgáltatásfiókok Windows-csomópontokon lévő tárolók számára
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: 6710bb9c6a9e53dea60d14be686a4ea953e1bf19
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563565"
---
# <a name="prepare-windows-nodes-for-group-managed-service-account-support"></a>Windows-csomópontok előkészítése csoportosan felügyelt szolgáltatásfiók-támogatáshoz

> A következőkre vonatkozik: Azure Stack HCI, AK Runtime a Windows Server 2019 Datacenter rendszeren

A csoportosan felügyelt szolgáltatásfiókok egy adott típusú Active Directory-fiók, amely a következőket biztosítja:
- Automatikus jelszavas kezelés
- Egyszerűsített egyszerű szolgáltatásnév (SPN) kezelése
- A felügyelet delegálása más rendszergazdáknak több kiszolgáló között 

A csoportosan felügyelt szolgáltatásfiókok (gMSA) a Windows-csomópontokon futó hüvelyekhez és tárolók számára történő konfigurálásához először csatlakoztatnia kell a Windows-csomópontokat egy Active Directory tartományhoz.

A csoportosan felügyelt szolgáltatásfiók támogatásának engedélyezéséhez a Kubernetes-fürt nevének kevesebbnek kell lennie négy karakternél. Kevesebb mint négy karakterből állhat, mert a tartományhoz csatlakoztatott kiszolgálónév maximálisan támogatott hossza 15 karakter. A munkavégző csomóponthoz tartozó Azure Stack HCI Kubernetes-fürt Name konvenciója néhány előre definiált karaktert tartalmaz a csomópontok nevéhez.

## <a name="join-the-worker-nodes-to-a-domain"></a>A munkavégző csomópontok csatlakoztatása tartományhoz

Ha a Windows-munkavégző csomópontokat tartományhoz kívánja csatlakoztatni, jelentkezzen be egy Windows Worker-csomópontba a `kubectl` Get és a deting `EXTERNAL-IP` értékének futtatásával.

```
   kubectl get nodes -o wide
```  

Ezután SSH-t adhat a csomóponthoz a használatával `ssh Administrator@ip` .

Miután sikeresen bejelentkezett a Windows Worker-csomópontba, futtassa a következő PowerShell-parancsot a csomópont tartományhoz való csatlakoztatásához. A rendszer kérni fogja a **tartományi rendszergazdai fiók** hitelesítő adatainak megadását. Olyan emelt szintű felhasználói hitelesítő adatokat is használhat, amelyek jogosultságokkal rendelkeznek a számítógépek az adott tartományhoz való csatlakoztatásához. Ezután újra kell indítania a Windows Worker csomópontot. 

```powershell
add-computer --domainame "YourDomainName" -restart
```

Ha az összes Windows-feldolgozó csomópontot egy tartományhoz csatlakoztatta, kövesse az [gMSA konfigurálása](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/)című témakör lépéseit. Ezek a lépések segítséget nyújtanak a Kubernetes gMSA egyéni erőforrás-definíciók és webhookok alkalmazásához a Kubernetes-fürtön.

A Windows-tárolóval és a gMSA kapcsolatos további információkért lásd: [Windows-tárolók és-gMSA](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). Hibaelhárítási információkért tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt. 

## <a name="next-steps"></a>További lépések

* [A fürt és az alkalmazás figyeléséhez használja a Azure monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Használjon állandó kötetet egy Kubernetes-fürtön](persistent-volume.md).
