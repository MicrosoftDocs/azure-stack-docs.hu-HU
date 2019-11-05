---
title: Kubernetes-fürt méretezése Azure Stackon | Microsoft Docs
description: Megtudhatja, hogyan méretezheti Azure Stack Kubernetes-fürtöt.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/25/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: 39c651649aea60ab12aae9daf35b0d07615bf697
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595299"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack"></a>Kubernetes-fürt méretezése Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A fürt az AK-motorral méretezhető a **Scale** parancs használatával. A **skálázási** parancs egy új Azure Resource Manager-telepítésre vonatkozó bemenetként újrahasznosítja a fürt konfigurációs fájlját (`apimodel.json`) a kimeneti könyvtáron belül. A motor végrehajtja a skálázási műveletet a megadott ügynök-készleten. A skálázási művelet elvégzése után a motor frissíti a fürt definícióját az adott `apimodel.json` fájlban az új csomópontok számának megjelenítéséhez, hogy tükrözze a frissített, aktuális fürtkonfiguráció.

## <a name="scale-a-cluster"></a>Fürt skálázása

A `aks-engine scale` parancs növelheti vagy csökkentheti a csomópontok számát egy `aks-engine` Kubernetes-fürtben található meglévő ügynök készletében. A csomópontok mindig az ügynök készletének végéről lesznek hozzáadva vagy eltávolítva. A csomópontok a törlés előtt el lesznek szigetelve és kiürítve.

### <a name="values-for-the-scale-command"></a>A skálázási parancs értékei

A Scale parancs a következő paramétereket használja a fürt definíciós fájljának megkeresésére és a fürt frissítésére.

| Paraméter | Példa | Leírás |
| --- | --- | --- | 
| Azure – env | AzureStackCloud | Azure Stack használatakor a környezeti neveket `AzureStackCloud`értékre kell állítani. | 
| location | helyi | Ez az Azure Stack-példány régiója. ASDK esetében a régió `local`értékre van állítva.  | 
| resource-group | Kube – RG | A fürtöt tartalmazó erőforráscsoport neve. | 
| előfizetés-azonosító |  | Annak az előfizetésnek a GUID azonosítója, amely a fürt által használt erőforrásokat tartalmazza. Győződjön meg arról, hogy az előfizetéshez elegendő kvóta tartozik a skálázáshoz. | 
| ügyfél-azonosító |  | A fürtnek az AK-motorból való létrehozásához használt szolgáltatásnév ügyfél-azonosítója. | 
| ügyfél – titok |  | A fürt létrehozásakor használt egyszerű szolgáltatás titka. | 
| API – modell | Kube-RG/apimodel. JSON | A fürt definíciós fájljának elérési útja (apimodel. JSON). Ez a következő lehet: _output/\<dnsPrefix >/apimodel.JSON | 
| -új csomópontok száma | 9 | A csomópontok kívánt száma. | 
| -Master-FQDN |  | Fő FQDN. Leskálázáskor szükséges. |
| identitás-rendszerek | ADFS | Választható. Ha Active Directory összevont szolgáltatásokat (AD FS) használ, adja meg a személyazonosság-kezelési megoldást. |

A **--Azure-env** paramétert meg kell adnia a fürt Azure stack-ban való skálázásakor. A paraméterekkel és azok értékeivel kapcsolatos további információkért lásd : [Scale-parameters (méretezési paraméterek](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters)).

### <a name="command-to-scale-your-cluster"></a>A fürt skálázására szolgáló parancs

A fürt méretezéséhez futtassa a következő parancsot:

```bash
aks-engine scale \
    --azure-env AzureStackCloud   \
    --location <for an ASDK is local> \
    --resource-group <cluster resource group>
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model <path to your apomodel.json file>
    --new-node-count <desired node count> \
    --master-FQDN <master FQDN> \
    --identity-system adfs # required if using AD FS
```

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack AK-beli motorról](azure-stack-kubernetes-aks-engine-overview.md)
- [Kubernetes-fürt frissítése Azure Stack](azure-stack-kubernetes-aks-engine-upgrade.md)