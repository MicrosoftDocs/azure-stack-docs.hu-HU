---
title: Kubernetes-fürt frissítése Azure Stack hub-on
description: Ismerje meg, hogyan frissíthet egy Kubernetes-fürtöt Azure Stack hub-on.
author: mattbriggs
ms.topic: article
ms.date: 3/4/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/4/2021
ms.openlocfilehash: 0db454750b56e9c4dbb765092c48643b1df15470
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231557"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack-hub"></a>Kubernetes-fürt frissítése Azure Stack hub-on

Az AK-motorral frissítheti az eszköz használatával eredetileg üzembe helyezett fürtöt. A fürtöket az AK-motor használatával is karbantarthatja. A karbantartási feladatok hasonlóak bármely IaaS rendszerhez. Érdemes figyelembe vennie az új frissítések rendelkezésre állását, és az AK-motor használatával alkalmazhatja azokat.
## <a name="upgrade-a-cluster"></a>Fürt frissítése

A frissítési parancs frissíti a Kubernetes verzióját és az alap operációs rendszer rendszerképét. Minden alkalommal, amikor futtatja a frissítési parancsot a fürt minden csomópontján, az AK-motor egy új virtuális gépet hoz létre, amely a felhasznált **AK-motor** verziójához tartozó AK-alapú alapképpel van társítva. A `aks-engine upgrade` paranccsal megőrizheti a fürtben lévő összes főkiszolgáló és ügynök csomópontjának pénznemét. 

A Microsoft nem kezeli a fürtöt. A Microsoft azonban biztosítja a fürt felügyeletéhez használható eszközt és virtuálisgép-rendszerképet. 

Az üzembe helyezett fürtök frissítéseinek fedele:

-   Kubernetes
-   Azure Stack hub Kubernetes-szolgáltató
-   Alap operációs rendszer

Üzemi fürt frissítésekor vegye figyelembe a következőket:

-   A megfelelő fürt-specifikációt ( `apimodel.json` ) és erőforráscsoportot használja a célként megadott fürthöz?
-   Megbízható gépet használ az ügyfélszámítógép számára az AK-motor futtatásához és a frissítési műveletek végrehajtásához?
-   Ellenőrizze, hogy rendelkezik-e biztonsági mentési fürttel, és hogy működőképes-e.
-   Ha lehetséges, futtassa a parancsot a Azure Stack hub-környezetben található virtuális gépről a hálózati ugrások és a lehetséges csatlakozási hibák csökkentése érdekében.
-   Győződjön meg arról, hogy az előfizetése elegendő lemezterülettel rendelkezik a teljes folyamathoz. A folyamat új virtuális gépeket foglal le a folyamat során.
-   A rendszer nem tervez frissítéseket vagy ütemezett feladatokat.
-   Állítson be egy szakaszos frissítést egy olyan fürtön, amely pontosan úgy van konfigurálva, mint a termelési fürt, majd tesztelje a frissítést az üzemi fürtön.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Új Kubernetes-verzióra való frissítés lépései

> [!NOTE]  
> Az AK alaprendszerképe is frissülni fog, ha az AK-motor újabb verzióját használja, és a rendszerkép elérhető a piactéren.

Az alábbi utasítások a frissítés végrehajtásához szükséges minimális lépéseket használják. Ha további részleteket szeretne látni, tekintse meg a [Kubernetes-fürtök frissítése](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping)című cikket.

1. Először meg kell határoznia a frissítés céljára megcélzott verziókat. Ez a verzió a jelenleg használt verziótól függ, és ezt a verziószámot használja a frissítés elvégzéséhez. Az AK-motor által támogatott Kubernetes-verziók a következő parancs futtatásával jelenhetnek meg:
    
    ```bash
    aks-engine get-versions --azure-env AzureStackCloud
    ```
    
    Az AK-motor teljes leképezéséhez az AK alaprendszerkép-és Kubernetes-verzióit lásd: [támogatott AK-motor-verziók](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping).

2. Gyűjtsön adatokat, amelyekre szüksége lesz a parancs futtatásához `upgrade` . A frissítés a következő paramétereket használja:

    | Paraméter | Példa | Leírás |
    | --- | --- | --- |
    | Azure – env | AzureStackCloud | Annak jelzése, hogy a célként megadott platform Azure Stack hub által használt AK-motor `AzureStackCloud` . |
    | location | helyi | Az Azure Stack hub régiójának neve. A ASDK esetében a régió a következőre van beállítva: `local` . |
    | resource-group | Kube – RG | Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevének alfanumerikusnak és kisbetűsnek kell lennie. |
    | előfizetés-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az előfizetés-AZONOSÍTÓját. További információ: [előfizetés egy ajánlatra](./azure-stack-subscribe-services.md#subscribe-to-an-offer) |
    | API – modell | ./kubernetes-azurestack.jsbekapcsolva | A fürt konfigurációs fájljának vagy API-modellének elérési útja. |
    | ügyfél-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatásnév GUID azonosítóját. Az ügyfél-azonosító az alkalmazás AZONOSÍTÓJAként van azonosítva, amikor a Azure Stack hub rendszergazdája létrehozta a szolgáltatásnevet. |
    | ügyfél – titok | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatás titkos kulcsát. Ez a szolgáltatás létrehozásakor beállított ügyfél-titkos kulcs. |
    | identitás-rendszerek | ADFS | Választható. Ha Active Directory összevont szolgáltatásokat (AD FS) használ, adja meg a személyazonosság-kezelési megoldást. |

3. A következő parancs futtatásával adja meg az értékeket:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud \
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.18.15 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  Ha bármilyen okból kifolyólag a frissítési művelet hibát észlel, a probléma megoldása után újra futtathatja a frissítési parancsot. Az AK-motor folytatja a műveletet, ahol az előző alkalommal meghiúsult.

## <a name="steps-to-only-upgrade-the-os-image"></a>Az operációs rendszer rendszerképének frissítésére szolgáló lépések

1. Tekintse át [a támogatott kubernetes-verziók táblát](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping) , és állapítsa meg, hogy rendelkezik-e a frissítéshez tervezett AK-motor és AK-alapú alaprendszerkép verziószámával. Az AK-motor futtatási verziójának megtekintése: `aks-engine version` .
2. Frissítse az AK-motort ennek megfelelően, azon a gépen, amelyen telepítette az AK-motort: az `./get-akse.sh --version vx.xx.x` **x. xx. x** helyére a megadott verziót használja.
3. Kérje meg az Azure Stack hub-kezelőt, hogy adja hozzá a használni kívánt Azure Stack hub-piactérhez szükséges AK-alapú alaprendszerkép verzióját.
4. Futtassa a `aks-engine upgrade` parancsot ugyanazzal a Kubernetes-verzióval, amelyet már használ, de adja hozzá a t `--force` . A [frissítés kényszerítését](#forcing-an-upgrade)bemutató példát láthat.


## <a name="steps-to-update-cluster-to-os-version-ubuntu-1804"></a>A fürt az Ubuntu 18,04-es verzióra való frissítésének lépései

A 0.60.1 és újabb rendszerekben a fürtön futó virtuális gépeket az Ubuntu 16,04 és 18,04 közötti verzióra frissítheti. Kövesse az alábbi lépéseket:

1. Keresse meg és szerkessze az `api-model.json` üzembe helyezés során létrehozott fájlt. Ennek a fájlnak azonosnak kell lennie a frissítési és a skálázási művelethez `aks-engine` .
2. A és a részeinek megkereséséhez módosítsa az értékét a következőre: `masterProfile` `agentPoolProfiles` `distro` `aks-ubuntu-18.04` .
2. Mentse a `api-model.json` fájlt, és használja a `api-model.json` parancsot a ` aks-engin upgrade` parancsban, ahogy azt a [lépéseket követve újabb Kubernetes-verzióra frissíthet](#steps-to-upgrade-to-a-newer-kubernetes-version)

## <a name="forcing-an-upgrade"></a>Frissítés kényszerítése

Előfordulhat, hogy a fürt frissítését kényszeríteni szeretné. Például az első napon egy fürtöt helyez üzembe egy leválasztott környezetben a legújabb Kubernetes-verzió használatával. A következő Day Ubuntu egy javítást bocsát ki egy olyan biztonsági réssel, amelyhez a Microsoft új **AK-alapú Alaprendszerképet** hoz létre. Az új lemezképet alkalmazhatja, ha a frissítést a már telepített Kubernetes-verzió használatával kényszeríti.

```bash  
aks-engine upgrade \
--azure-env AzureStackCloud   
--location <for an ASDK is local> \
--resource-group kube-rg \
--subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--api-model kube-rg/apimodel.json \
--upgrade-version 1.18.15 \
--client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

Útmutatásért lásd: [kényszerített frissítés](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade).

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)
- [Kubernetes-fürt méretezése Azure Stack hub-on](azure-stack-kubernetes-aks-engine-scale.md)
