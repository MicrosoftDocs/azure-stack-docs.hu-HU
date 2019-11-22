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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: c7db293cb1a24079f66dcf0b047cf66218857003
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310128"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack"></a>Kubernetes-fürt frissítése Azure Stack

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="upgrade-a-cluster"></a>Fürt frissítése

Az AK-motorral frissítheti az eszköz használatával eredetileg üzembe helyezett fürtöt. A fürtöket az AK-motor használatával is karbantarthatja. A karbantartási feladatok hasonlóak bármely IaaS rendszerhez. Érdemes figyelembe vennie az új frissítések rendelkezésre állását, és az AK-motor használatával alkalmazhatja azokat.

A frissítési parancs frissíti a Kubernetes verzióját és az alap operációs rendszer rendszerképét. Minden alkalommal, amikor futtatja a frissítési parancsot a fürt minden csomópontján, az AK-motor egy új virtuális gépet hoz létre, amely a felhasznált **AK-motor** verziójához tartozó AK-alapú alapképpel van társítva. A `aks-engine upgrade` parancs használatával megtarthatja a fürt összes főkiszolgálójának és ügynökének pénznemét. 

A Microsoft nem kezeli a fürtöt. A Microsoft azonban biztosítja a fürt felügyeletéhez használható eszközt és virtuálisgép-rendszerképet. 

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
-   Állítson be egy szakaszos frissítést egy olyan fürtön, amely pontosan úgy van konfigurálva, mint a termelési fürt, majd tesztelje a frissítést az üzemi fürtön.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Új Kubernetes-verzióra való frissítés lépései

> [!Note]  
> Az AK alaprendszerképe is frissülni fog, ha az AK-motor újabb verzióját használja, és a rendszerkép elérhető a piactéren.

Az alábbi utasítások a frissítés végrehajtásához szükséges minimális lépéseket használják. Ha további részleteket szeretne látni, tekintse meg a [Kubernetes-fürtök frissítése](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md)című cikket.

1. Először meg kell határoznia a frissítés céljára megcélzott verziókat. Ez a verzió a jelenleg használt verziótól függ, és ezt a verziószámot használja a frissítés elvégzéséhez.

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

    Például a `get-versions` parancs kimenete szerint, ha a jelenlegi Kubernetes verziója "1.13.5", a "1.13.7, 1.14.1, 1.14.3" értékre frissíthet.

2. Gyűjtse össze az `upgrade` parancs futtatásához szükséges adatokat. A frissítés a következő paramétereket használja:

    | Paraméter | Példa | Leírás |
    | --- | --- | --- |
    | Azure – env | AzureStackCloud | Annak jelzése, hogy a célként megadott platform Azure Stack használja `AzureStackCloud`. |
    | location | helyi | A Azure Stack régiójának neve. A ASDK a régió `local`értékre van állítva. |
    | resource-group | Kube – RG | Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket. |
    | előfizetés-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az előfizetés-AZONOSÍTÓját. További információ: [előfizetés egy ajánlatra](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) |
    | API – modell | ./kubernetes-azurestack.json | A fürt konfigurációs fájljának vagy API-modellének elérési útja. |
    | ügyfél-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatásnév GUID azonosítóját. Az ügyfél-azonosító az alkalmazás-AZONOSÍTÓként van azonosítva, amikor a Azure Stack rendszergazdája létrehozta a szolgáltatásnevet. |
    | ügyfél – titok | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatás titkos kulcsát. Ez a szolgáltatás létrehozásakor beállított ügyfél-titkos kulcs. |
    | identitás-rendszerek | ADFS | Választható. Ha Active Directory összevont szolgáltatásokat (AD FS) használ, adja meg a személyazonosság-kezelési megoldást. |

3. A következő parancs futtatásával adja meg az értékeket:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud   
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  Ha bármilyen okból kifolyólag a frissítési művelet hibát észlel, a probléma megoldása után újra futtathatja a frissítési parancsot. Az AK-motor folytatja a műveletet, ahol az előző alkalommal meghiúsult.

## <a name="steps-to-only-upgrade-the-os-image"></a>Az operációs rendszer rendszerképének frissítésére szolgáló lépések

1. Tekintse át [a támogatott kubernetes-verziók táblát](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) , és állapítsa meg, hogy rendelkezik-e a frissítéshez tervezett AK-motor és AK-alapú alaprendszerkép verziószámával. Az AK-motor futtatási verziójának megtekintése: `aks-engine version`.
2. Frissítse az AK-motort ennek megfelelően, azon a gépen, amelyen telepítette az AK-motor futtatását: `./get-akse.sh --version vx.xx.x` az **x. xx. x** helyett a célként megadott verziót.
3. Kérje meg Azure Stack kezelőjét, hogy adja hozzá a használni kívánt Azure Stack piactéren a szükséges AK-alapú alapképfájl verzióját.
4. Futtassa a `aks-engine upgrade` parancsot ugyanazzal a Kubernetes-verzióval, amelyet már használ, de adja hozzá a `--force`. A [frissítés kényszerítését](#forcing-an-upgrade)bemutató példát láthat.


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
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

Útmutatásért lásd: [kényszerített frissítés](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade).

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack AK-beli motorról](azure-stack-kubernetes-aks-engine-overview.md)
- [Kubernetes-fürt méretezése Azure Stack](azure-stack-kubernetes-aks-engine-scale.md)