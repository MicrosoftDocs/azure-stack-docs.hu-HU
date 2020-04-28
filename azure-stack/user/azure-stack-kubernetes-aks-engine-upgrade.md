---
title: Kubernetes-fürt frissítése Azure Stack hub-on
description: Ismerje meg, hogyan frissíthet egy Kubernetes-fürtöt Azure Stack hub-on.
author: mattbriggs
ms.topic: article
ms.date: 4/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 4/23/2020
ms.openlocfilehash: 2e8d4c04cbfce4f7140ecdd873da5323cb1e1e7d
ms.sourcegitcommit: e5b587216a137819444680ec619281c90f37bad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82167041"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack-hub"></a>Kubernetes-fürt frissítése Azure Stack hub-on

## <a name="upgrade-a-cluster"></a>Fürt frissítése

Az AK-motorral frissítheti az eszköz használatával eredetileg üzembe helyezett fürtöt. A fürtöket az AK-motor használatával is karbantarthatja. A karbantartási feladatok hasonlóak bármely IaaS rendszerhez. Érdemes figyelembe vennie az új frissítések rendelkezésre állását, és az AK-motor használatával alkalmazhatja azokat.

A frissítési parancs frissíti a Kubernetes verzióját és az alap operációs rendszer rendszerképét. Minden alkalommal, amikor futtatja a frissítési parancsot a fürt minden csomópontján, az AK-motor egy új virtuális gépet hoz létre, amely a felhasznált **AK-motor** verziójához tartozó AK-alapú alapképpel van társítva. A `aks-engine upgrade` paranccsal megőrizheti a fürtben lévő összes főkiszolgáló és ügynök csomópontjának pénznemét. 

A Microsoft nem kezeli a fürtöt. A Microsoft azonban biztosítja a fürt felügyeletéhez használható eszközt és virtuálisgép-rendszerképet. 

Az üzembe helyezett fürtök frissítéseinek fedele:

-   Kubernetes
-   Azure Stack hub Kubernetes-szolgáltató
-   Alap operációs rendszer

Üzemi fürt frissítésekor vegye figyelembe a következőket:

-   A megfelelő fürt-specifikációt (`apimodel.json`) és erőforráscsoportot használja a célként megadott fürthöz?
-   Megbízható gépet használ az ügyfélszámítógép számára az AK-motor futtatásához és a frissítési műveletek végrehajtásához?
-   Ellenőrizze, hogy rendelkezik-e biztonsági mentési fürttel, és hogy működőképes-e.
-   Ha lehetséges, futtassa a parancsot a Azure Stack hub-környezetben található virtuális gépről a hálózati ugrások és a lehetséges csatlakozási hibák csökkentése érdekében.
-   Győződjön meg arról, hogy az előfizetése elegendő lemezterülettel rendelkezik a teljes folyamathoz. A folyamat új virtuális gépeket foglal le a folyamat során.
-   A rendszer nem tervez frissítéseket vagy ütemezett feladatokat.
-   Állítson be egy szakaszos frissítést egy olyan fürtön, amely pontosan úgy van konfigurálva, mint a termelési fürt, majd tesztelje a frissítést az üzemi fürtön.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Új Kubernetes-verzióra való frissítés lépései

> [!Note]  
> Az AK alaprendszerképe is frissülni fog, ha az AK-motor újabb verzióját használja, és a rendszerkép elérhető a piactéren.

Az alábbi utasítások a frissítés végrehajtásához szükséges minimális lépéseket használják. Ha további részleteket szeretne látni, tekintse meg a [Kubernetes-fürtök frissítése](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md)című cikket.

1. Először meg kell határoznia a frissítés céljára megcélzott verziókat. Ez a verzió a jelenleg használt verziótól függ, és ezt a verziószámot használja a frissítés elvégzéséhez. A legújabb frissítésekben támogatott Kubernetes-verziók a következők: 1.14.7 és 1.15.10. Kövesse ezt a táblázatot az elérhető frissítésekhez:

| Aktuális verzió | Frissítés érhető el |
| --- | --- |
|1.14.7 | 1.15.10 |
|1.14.8 | 1.15.10 |
|1.15.4 | 1.15.10 |
|1.15.5 | 1.15.10 |

Az AK-motor teljes leképezéséhez az AK alaprendszerkép-és Kubernetes-verzióit lásd: [támogatott AK-motor-verziók](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions).

2. Gyűjtsön adatokat, amelyekre szüksége lesz a `upgrade` parancs futtatásához. A frissítés a következő paramétereket használja:

    | Paraméter | Példa | Leírás |
    | --- | --- | --- |
    | Azure – env | AzureStackCloud | Annak jelzése, hogy a célként megadott platform Azure Stack hub által használt `AzureStackCloud`AK-motor. |
    | location | helyi | Az Azure Stack hub régiójának neve. A ASDK esetében a régió a következőre van `local`beállítva:. |
    | resource-group | Kube – RG | Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevének alfanumerikusnak és kisbetűsnek kell lennie. |
    | előfizetés-azonosító | XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | Adja meg az előfizetés-AZONOSÍTÓját. További információ: [előfizetés egy ajánlatra](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) |
    | API – modell | ./kubernetes-azurestack.json | A fürt konfigurációs fájljának vagy API-modellének elérési útja. |
    | ügyfél-azonosító | XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | Adja meg az egyszerű szolgáltatásnév GUID azonosítóját. Az ügyfél-azonosító az alkalmazás AZONOSÍTÓJAként van azonosítva, amikor a Azure Stack hub rendszergazdája létrehozta a szolgáltatásnevet. |
    | ügyfél – titok | XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | Adja meg az egyszerű szolgáltatás titkos kulcsát. Ez a szolgáltatás létrehozásakor beállított ügyfél-titkos kulcs. |
    | identitás-rendszerek | ADFS | Választható. Ha Active Directory összevont szolgáltatásokat (AD FS) használ, adja meg a személyazonosság-kezelési megoldást. |

3. A következő parancs futtatásával adja meg az értékeket:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud \
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
2. Frissítse az AK-motort ennek megfelelően, azon a gépen, amelyen telepítette az AK `./get-akse.sh --version vx.xx.x` -motort: az **x. xx. x** helyére a megadott verziót használja.
3. Kérje meg az Azure Stack hub-kezelőt, hogy adja hozzá a használni kívánt Azure Stack hub-piactérhez szükséges AK-alapú alaprendszerkép verzióját.
4. Futtassa a `aks-engine upgrade` parancsot ugyanazzal a Kubernetes-verzióval, amelyet már használ, de adja hozzá a `--force`t. A [frissítés kényszerítését](#forcing-an-upgrade)bemutató példát láthat.


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

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)
- [Kubernetes-fürt méretezése Azure Stack hub-on](azure-stack-kubernetes-aks-engine-scale.md)
