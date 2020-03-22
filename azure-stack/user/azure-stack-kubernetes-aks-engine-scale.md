---
title: Kubernetes-fürt méretezése Azure Stack hub-on
description: Megtudhatja, hogyan méretezheti a Kubernetes-fürtöt Azure Stack hub-on.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: e5a9c44d91137d8f9868a9a9c6a809a54ca09026
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80069119"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack-hub"></a>Kubernetes-fürt méretezése Azure Stack hub-on

A fürt az AK-motorral méretezhető a **Scale** parancs használatával. A **skálázási** parancs egy új Azure Resource Manager-telepítésre vonatkozó bemenetként újrahasznosítja a fürt konfigurációs fájlját (`apimodel.json`) a kimeneti könyvtáron belül. A motor végrehajtja a skálázási műveletet a megadott ügynök-készleten. A skálázási művelet elvégzése után a motor frissíti a fürt definícióját az adott `apimodel.json` fájlban az új csomópontok számának megjelenítéséhez, hogy tükrözze a frissített, aktuális fürtkonfiguráció.

## <a name="scale-a-cluster"></a>Fürt skálázása

A `aks-engine scale` parancs növelheti vagy csökkentheti a csomópontok számát egy `aks-engine` Kubernetes-fürtben található meglévő ügynök készletében. A csomópontok mindig az ügynök készletének végéről lesznek hozzáadva vagy eltávolítva. A csomópontok a törlés előtt el lesznek szigetelve és kiürítve.

### <a name="values-for-the-scale-command"></a>A skálázási parancs értékei

A Scale parancs a következő paramétereket használja a fürt definíciós fájljának megkeresésére és a fürt frissítésére.

| Paraméter | Példa | Leírás |
| --- | --- | --- | 
| Azure – env | AzureStackCloud | Azure Stack hub használatakor a környezeti neveket `AzureStackCloud`értékre kell állítani. | 
| location | helyi | Ez az Azure Stack hub-példány régiója. ASDK esetében a régió `local`értékre van állítva.  | 
| resource-group | Kube – RG | A fürtöt tartalmazó erőforráscsoport neve. | 
| előfizetés-azonosító |  | Annak az előfizetésnek a GUID azonosítója, amely a fürt által használt erőforrásokat tartalmazza. Győződjön meg arról, hogy az előfizetéshez elegendő kvóta tartozik a skálázáshoz. | 
| ügyfél-azonosító |  | A fürtnek az AK-motorból való létrehozásához használt szolgáltatásnév ügyfél-azonosítója. | 
| ügyfél – titok |  | A fürt létrehozásakor használt egyszerű szolgáltatás titka. | 
| API – modell | Kube-RG/apimodel. JSON | A fürt definíciós fájljának elérési útja (apimodel. JSON). Ez a következő lehet: _output/\<dnsPrefix >/apimodel.JSON | 
| -új csomópontok száma | 9 | A csomópontok kívánt száma. | 
| -Master-FQDN |  | Fő FQDN. Leskálázáskor szükséges. |
| identitás-rendszerek | ADFS | Választható. Ha Active Directory összevont szolgáltatásokat (AD FS) használ, adja meg a személyazonosság-kezelési megoldást. |

A **--Azure-env** paramétert kell megadnia a fürt Azure stack hub-ban való skálázásakor. A paraméterekkel és azok értékeivel kapcsolatos további információkért lásd : [Scale-parameters (méretezési paraméterek](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters)).

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

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)
- [Kubernetes-fürt frissítése Azure Stack hub-on](azure-stack-kubernetes-aks-engine-upgrade.md)