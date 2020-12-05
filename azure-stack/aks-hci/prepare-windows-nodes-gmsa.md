---
title: Windows-csomópontok előkészítése csoportosan felügyelt szolgáltatásfiók-támogatáshoz
description: Megtudhatja, hogyan konfigurálhat csoportosan felügyelt szolgáltatásfiókok Windows-csomópontokon lévő tárolók számára
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: 754ebc1a365efb7efa0e96eef438ae2347a069ab
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612573"
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

Ha a Windows-munkavégző csomópontokat tartományhoz kívánja csatlakoztatni, jelentkezzen be egy Windows Worker-csomópontra a futtatásával, és adja meg `kubectl get` az `EXTERNAL-IP` értéket.

```
kubectl get nodes -o wide
```  

Ezután SSH-t adhat a csomóponthoz a használatával `ssh Administrator@ip` . Az SSH használatával történő bejelentkezéssel kapcsolatos további információkért lásd: a [Windows Worker-csomópontok hibaelhárítása SSH használatával](troubleshoot.md#troubleshooting-windows-worker-nodes).

Miután sikeresen bejelentkezett a Windows Worker-csomópontba az SSH használatával, futtassa a következő parancsot a csomópont tartományhoz való csatlakoztatásához. Ezután újra kell indítania a Windows Worker csomópontot. 

```
netdom.exe join %computername% /domain:DomainName /UserD:DomainName\UserName /PasswordD:Password
```  

Ha az összes Windows-feldolgozó csomópontot egy tartományhoz csatlakoztatta, kövesse az [gMSA konfigurálása](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/)című témakör lépéseit. Ezek a lépések segítséget nyújtanak a Kubernetes gMSA egyéni erőforrás-definíciók és webhookok alkalmazásához a Kubernetes-fürtön.

A Windows-tárolóval és a gMSA kapcsolatos további információkért lásd: [Windows-tárolók és-gMSA](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>További lépések

* [A fürt és az alkalmazás figyeléséhez használja a Azure monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Használjon állandó kötetet egy Kubernetes-fürtön](persistent-volume.md).
