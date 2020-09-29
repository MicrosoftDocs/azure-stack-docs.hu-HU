---
title: Ismert problémák az Azure Stack hub-beli AK-motorral
description: Ismerje meg a Azure Stack hub AK-motorjának használatával kapcsolatos ismert problémákat.
author: mattbriggs
ms.topic: article
ms.date: 09/11/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/11/2020
ms.openlocfilehash: 685cf02aed8e6e485d596531c37653f496a4bc5f
ms.sourcegitcommit: a845ae0d3794b5d845b2ae712baa7e38f3011a7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2020
ms.locfileid: "90045278"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Ismert problémák az Azure Stack hub-beli AK-motorral

Ez a témakör a Azure Stack hub AK-motorjának ismert problémáit ismerteti.

## <a name="disk-detach-operation-fails-in-aks-engine-0550"></a>A lemez leválasztási művelete meghiúsul az AL0.55.0on Engine-ban

- **A**következőre vonatkozik: Azure stack Hub (2005-es frissítés), AK-motor 0.55.0
- **Leírás**: Ha az adatmegőrzési köteteket tartalmazó központi telepítést próbál törölni, a törlési művelet csatolási/leválasztási hibák sorozatát indítja el. Ennek oka, hogy egy hiba van a 0.55.0-os Engine v-ben. A felhőalapú szolgáltató az API újabb verziójával hívja meg a Azure Resource Managert, mint az Azure Stack hub által jelenleg támogatott Azure Resource Manager (2005-es frissítés).
- **Szervizelés**: megtalálhatja a részleteket és a kockázatcsökkentő lépéseket az [AK-motor GitHub-tárházában (3817-es probléma)](https://github.com/Azure/aks-engine/issues/3817#issuecomment-691329443). A frissítést azonnal megtekintheti, ha az AK-motor és a hozzá tartozó rendszerkép új buildje elérhető.
- **Előfordulás**: az adatmegőrzési köteteket tartalmazó központi telepítés törlése.

## <a name="upgrade-issues-in-aks-engine-0510"></a>Az AL0.51.0on-motor frissítési problémái

* A Kubernetes-fürt a (z) 1.15. x és 1.16. x verzióra való frissítése során a következő Kubernetes-összetevők frissítéséhez további manuális lépések szükségesek: **Kube-proxy**, **Azure-CNI-networkmonitor**, **CSI-Secrets-Store**, **Kubernetes-Dashboard**. Az alábbiakban bemutatjuk, hogy mire figyeljen, és hogyan lehet megkerülni a problémákat.

  * A csatlakoztatott környezetekben nem nyilvánvaló, hogy ez a probléma észreveheti, mivel nincsenek olyan jelek a fürtben, amelyeket az érintett összetevők nem frissítették. Úgy tűnik, hogy minden a várt módon működik.
  <!-- * In disconnected environments, you can see this problem when you run a query for the system pods status and see that the pods for the components mentioned below are not in "Ready" state: -->

    ```bash  
    kubectl get pods -n kube-system
    ```

  * Ennek a hibának a megoldásához a következő táblázatban található Áthidaló megoldás oszlopban futtassa a parancsot.

    |Összetevő neve |Áthidaló megoldás |Érintett helyzetek|
    |---------------|-----------|------------------|
    |**Kube – proxy**     | `kubectl delete ds kube-proxy -n kube-system` |Csatlakoztatva, leválasztva |
    |**Azure-CNI – networkmonitor**   | `kubectl delete ds azure-cni-networkmonitor -n kube-system`   | Csatlakoztatva, leválasztva |
    |**CSI-Secrets-Store**  |`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/secrets-store-csi-driver.yaml`<br>`kubectl delete ds csi-secrets-store -n kube-system` | Leválasztott |
    |**kubernetes – irányítópult** |Futtassa a következő parancsot az egyes fő csomópontokon:<br>`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/kubernetes-dashboard.yaml` |Leválasztott |

* A Kubernetes 1,17 nem támogatott ebben a kiadásban. Bár vannak olyan GitHub pull-kérések (PR), amelyek a 1,17-re hivatkoznak, nem támogatottak.

## <a name="aks-engine-get-versions-command-limitations"></a>ak – a Get-Versions parancs korlátozásai

Az **AK-motor** `get-versions` parancs kimenete csak a globális Azure-ra, és nem Azure stack hub-ra vonatkozik. A különböző frissítési útvonalakkal kapcsolatos további információkért tekintse meg az [újabb Kubernetes-verzióra való frissítés lépéseit](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version).

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub – áttekintés](azure-stack-kubernetes-aks-engine-overview.md)
