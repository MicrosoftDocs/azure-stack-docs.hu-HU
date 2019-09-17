---
title: Kubernetes-fürt frissítése Azure Stackon | Microsoft Docs
description: Megtudhatja, hogyan frissíthet egy Kubernetes-fürtöt Azure Stackon.
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 280c5bf6a09670479a8497ecedb0364e6e27f949
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019204"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack"></a>Kubernetes-fürt frissítése Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

## <a name="upgrade-a-cluster"></a>Fürt frissítése

Az AK-motorral frissítheti az eszköz használatával eredetileg üzembe helyezett fürtöt. A fürtöket az AK-motor használatával is karbantarthatja. A karbantartási feladatok hasonlóak bármely IaaS rendszerhez. Érdemes figyelembe vennie az új frissítések rendelkezésre állását, és az AK-motor használatával alkalmazhatja azokat.

A Microsoft nem kezeli a fürtöt.

Az üzembe helyezett fürtök frissítéseinek fedele:

-   Kubernetes
-   Azure Stack Kubernetes-szolgáltató
-   Alap operációs rendszer

Üzemi fürt frissítésekor vegye figyelembe a következőket:

-   A megfelelő fürt-specifikációt (`apimodel.json`) és erőforráscsoportot használja a célként megadott fürthöz?
-   Megbízható gépet használ az ügyfélszámítógép számára az AK-motor futtatásához és a frissítési műveletek végrehajtásához?
-   Ellenőrizze, hogy rendelkezik-e biztonsági mentési fürttel, és hogy működőképes-e.
-   Ha lehetséges, futtassa a parancsot a Azure Stack környezetben található virtuális gépről a hálózati ugrások és a lehetséges csatlakozási hibák csökkentése érdekében.
-   Győződjön meg arról, hogy az előfizetése elegendő lemezterülettel rendelkezik a teljes folyamathoz. A folyamat új virtuális gépeket foglal le a folyamat során.
-   A rendszer nem tervez frissítéseket vagy ütemezett feladatokat.
-   Állítson be egy szakaszos frissítést olyan fürtön, amely pontosan úgy van konfigurálva, mint az éles fürt, majd tesztelje a frissítést, mielőtt ezt megtenné az üzemi fürtben.

## <a name="steps-to-upgrade"></a>A frissítés lépései

1. Kövesse a cikk utasításait, és [frissítse a Kubernetes-fürtöket](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md). 
2. Először meg kell határoznia a frissítés céljára megcélzott verziókat. Ez a verzió a jelenleg használt verziótól függ, és ezt a verziószámot használja a frissítés elvégzéséhez.

    Futtassa az alábbi parancsot:

    ```bash  
    $ aks-engine get-versions
    Version Upgrades
    1.15.0
    1.14.3  1.15.0
    1.14.1  1.14.3, 1.15.0
    1.13.7  1.14.1, 1.14.3
    1.13.5  1.13.7, 1.14.1, 1.14.3
    1.12.8  1.13.5, 1.13.7
    1.12.7  1.12.8, 1.13.5, 1.13.7
    1.11.10 1.12.7, 1.12.8
    1.11.9  1.11.10, 1.12.7, 1.12.8
    1.10.13 1.11.9, 1.11.10
    1.10.12 1.10.13, 1.11.9, 1.11.10
    1.9.11  1.10.12, 1.10.13
    1.9.10  1.9.11, 1.10.12, 1.10.13
    1.6.9   1.9.10, 1.9.11
    ```

    Például a `get-versions` parancs kimenetének megfelelően, ha a jelenlegi Kubernetes verziója "1.13.5", a "1.13.7, 1.14.1, 1.14.3" értékre frissíthet.

3. Gyűjtsön adatokat, amelyekre szüksége lesz a `upgrade` parancs futtatásához. A frissítés a következő paramétereket használja:

    | Paraméter | Példa | Leírás |
    | --- | --- | --- |
    | Azure – env | AzureStackCloud | Annak jelzése, hogy a célként megadott platform Azure Stack használni `AzureStackCloud`kívánt AK-motor. |
    | location | helyi | A Azure Stack régiójának neve. Azon ASDK esetében, amelyekhez a régió `local`van beállítva. |
    | resource-group | Kube – RG | Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket. |
    | előfizetés-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az előfizetés-AZONOSÍTÓját. További információ: [előfizetés az ajánlatra](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) |
    | API – modell | ./kubernetes-azurestack.json | A fürt konfigurációs fájljának vagy API-modellének elérési útja. |
    | ügyfél-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatásnév GUID azonosítóját. Az ügyfél-azonosító az alkalmazás-AZONOSÍTÓként van azonosítva, amikor a Azure Stack rendszergazdája létrehozta a szolgáltatásnevet. |
    | ügyfél – titok | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatás titkos kulcsát. Ez a szolgáltatás létrehozásakor beállított ügyfél-titkos kulcs. |


4. A következő parancs futtatásával adja meg az értékeket:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud   
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

5.  Ha bármilyen okból kifolyólag a frissítési művelet hibát észlel, a probléma megoldása után újra futtathatja a frissítési parancsot. Az AK-motor folytatja a műveletet, ahol az előző alkalommal meghiúsult.

## <a name="forcing-an-upgrade"></a>Frissítés kényszerítése

Előfordulhat, hogy a fürt frissítését kényszeríteni szeretné. Például az első napon egy fürtöt helyez üzembe egy leválasztott környezetben a legújabb Kubernetes-verzió használatával. A következő Day Ubuntu egy javítást bocsát ki egy olyan biztonsági réssel, amelyhez a Microsoft új **AK-alapú Alaprendszerképet**hoz létre. Az új lemezképet alkalmazhatja, ha a frissítést a már telepített Kubernetes-verzió használatával kényszeríti.

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud   
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    --force
    ```

Útmutatásért lásd: [kényszerített frissítés](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade).

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack AK-beli motorról](azure-stack-kubernetes-aks-engine-overview.md)
- [Kubernetes-fürt méretezése Azure Stack](azure-stack-kubernetes-aks-engine-scale.md)