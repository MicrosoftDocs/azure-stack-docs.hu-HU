---
title: A Azure Stack hub Azure Kubernetes Service (ak) motorjának kibocsátási megjegyzései
description: Megtudhatja, milyen lépéseket kell végrehajtania a Azure Stack hub-ban lévő AK-os motor frissítéséhez.
author: mattbriggs
ms.topic: article
ms.date: 4/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 4/23/2020
ms.openlocfilehash: 1e58943e4ff315d2b5c2a9370f623846e920443d
ms.sourcegitcommit: c51e7787e36c49d34ee86cabf9f823fb98b61026
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82220725"
---
# <a name="release-notes-for-the-aks-engine-on-azure-stack-hub"></a>Kibocsátási megjegyzések a Azure Stack hub AK-motorja számára

Ez a cikk az Azure Kubernetes Service (ak) motorjának tartalmát mutatja be Azure Stack hub Update-ben. A frissítés a Azure Stack hub platformra irányuló AK-motor legújabb kiadásával kapcsolatos javításokat és javításokat tartalmaz. Figyelje meg, hogy ez nem a globális Azure-hoz készült AK-motor kiadási információinak dokumentálása.

## <a name="update-planning"></a>Frissítés tervezése

Az AK-motor verziófrissítési parancsa teljesen automatizálja a fürt frissítési folyamatát, és gondoskodik a virtuális gépekről, a hálózati, a tárolási, a Kubernetes és a előkészítési feladatokról. A frissítés alkalmazása előtt tekintse át az ebben a cikkben szereplő információkat.

### <a name="upgrade-considerations"></a>Frissítési megfontolások

-   A megfelelő Piactéri elem, az AK-alap Ubuntu 16,04 – LTS rendszerkép-disztribúciót használja az AK-motor verziójához? A verziókat a szakaszban találja, [töltse le az új AK alapszintű Ubuntu-rendszerképét és az Kabai motor verzióját](#download-new-image-and-aks-engine).

-   A megfelelő fürt-specifikációt (apimodel. JSON) és erőforráscsoportot használja a célként megadott fürthöz? Amikor eredetileg üzembe helyezte a fürtöt, ez a fájl a kimeneti könyvtárban lett létrehozva. Tekintse meg a [Kubernetes-fürt üzembe](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-cluster#deploy-a-kubernetes-cluster)helyezésére szolgáló parancs paramétereit.

-   Megbízható gépet használ az AK-motor futtatásához és a frissítési műveletek végrehajtásához?

-   Ha aktív számítási feladatokkal rendelkező operatív fürtöt frissít, a frissítést anélkül is alkalmazhatja, hogy az hatással lenne rájuk, feltéve, hogy a fürt normális terhelés alatt van. Ha azonban szükség van arra, hogy a felhasználók átirányítsák a biztonsági mentési fürtöt.

-   Ha lehetséges, futtassa a parancsot a Azure Stack hub-környezetben található virtuális gépről a hálózati ugrások és a lehetséges csatlakozási hibák csökkentése érdekében.

-   Győződjön meg arról, hogy az előfizetéshez elegendő kvóta tartozik a teljes folyamathoz. A folyamat új virtuális gépeket foglal le a folyamat során. Az eredményül kapott virtuális gépek száma megegyezik az eredetivel, de a folyamat során több virtuális gép létrehozása is megtervezhető.

-   A rendszer nem tervez frissítéseket vagy ütemezett feladatokat.

-   Állítson be egy szakaszos frissítést egy olyan fürtön, amely az éles fürttel megegyező értékekkel van konfigurálva, majd tesztelje a frissítést az éles fürtben.

### <a name="use-the-upgrade-command"></a>A verziófrissítési parancs használata

Az [Kubernetes-fürt Azure stack hub-on való frissítéséhez](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-upgrade)a következő cikkben leírtak szerint kell használnia a "verziófrissítés" parancsot.

### <a name="upgrade-interruptions"></a>Frissítési megszakítások

Időnként váratlan tényezők megszakítják a fürt frissítését. Megszakítás akkor fordulhat elő, ha az AK-motor hibát jelez, vagy valami történik az AK-motor végrehajtási folyamata során. Vizsgálja meg a megszakítás okát, küldje el, majd küldje el újra ugyanezt a frissítési parancsot a frissítési folyamat folytatásához. A **frissítési** parancs idempotens, és a parancs újbóli elküldése után folytatnia kell a fürt frissítését. A megszakítások általában megnövelik a frissítés befejezésének idejét, de nem befolyásolhatják a befejezését.

### <a name="estimated-upgrade-time"></a>Becsült frissítési idő

A becsült idő 12 – 15 perc a fürtben virtuális gépenként. Egy 20 csomópontos fürt például a frissítéshez körülbelül öt (5) órát is igénybe vehet.

## <a name="download-new-image-and-aks-engine"></a>Új rendszerkép és AK-motor letöltése

Töltse le az AK Base Ubuntu-rendszerkép és az AK-motor új verzióit.

Ahogy a Azure Stack hub dokumentációjában a Kubernetes-fürt üzembe helyezése című rész ismerteti, a két fő összetevő szükséges: 
- Az AK-motor bináris fájlja
- AK Base Ubuntu 16,04 – LTS rendszerkép-disztribúció

A frissítések új verziói a következő frissítéssel érhetők el:

-   Az Azure Stack hub-operátornak le kell töltenie egy új AK Base Ubuntu-rendszerképet:

    -   név:`AKS Base Ubuntu 16.04-LTS Image Distro, March 2020`
    -   Verziója`2020.03.19`
    -   Kövesse a következő cikk utasításait az [Azure Kubernetes Services (ak) motor előfeltételeinek hozzáadása az Azure stack hub Marketplace](https://docs.microsoft.com/azure-stack/operator/azure-stack-aks-engine) -hez

-   A Kubernetes-fürt rendszergazdájának le kell töltenie az új AK-motor 0.48.0 verzióját. A következő cikkben található útmutatást követve [telepítse az AK-motort Linux rendszeren Azure stack hub-ban](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux). A fürt első telepítéséhez használt eljárást követheti. A frissítés felülírja az előző bináris fájlt. Ha például használta a `get-akse.sh` parancsfájlt, kövesse a cikkben ismertetett lépéseket a [telepítés csatlakoztatott környezetben](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux#install-in-a-connected-environment)című cikk ismerteti. Ugyanez a folyamat vonatkozik arra az esetre, ha Windows rendszerre telepíti a-t, a [Azure stack hub-on lévő Windows rendszeren telepítse az AK-motort](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-windows).

## <a name="kubernetes-version-upgrade-path"></a>Kubernetes verziófrissítési útvonala

Az aktuális verziót és a verziófrissítést a következő táblázatban találja Azure Stack hubhoz. Ne kövesse az AK-Engine `get-versions` parancsot, mert a parancs az egyik a globális Azure-ban támogatott verziókat is tartalmazza. A következő verzió és verziófrissítési táblázat a Azure Stack hub AK-motor fürtjére vonatkozik.

| **Aktuális verzió** | **Frissítés érhető el** |
| --------------------| ----------------------|
| 1.14.7 | 1.15.10 |
| 1.14.8 | 1.15.10 |
| 1.15.4 | 1.15.10 |
| 1.15.5 | 1.15.10 |

## <a name="whats-new"></a>Újdonságok 

-   A Kubernetes-verzió 1.15.10 ([\#2834](https://github.com/Azure/aks-engine/issues/2834)) támogatása. Új fürt telepítésekor ügyeljen arra, hogy az API-modell JSON-fájljában legyen megadva (a.k.s. fürt definíciós fájlja) a kiadás verziószáma, valamint a másodlagos verziószám. Megtalálhatja például a következőket: [kubernetes-azurestack. JSON](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json):

    - `"orchestratorRelease": "1.15`,

    - `"orchestratorVersion": "1.15.10"`

    > [!Note]  
    > Ha a Kubernetes verziója nincs explicit módon megadva az API-modell JSON-fájljában, `1.15` a rendszer a verziót fogja használni ([\#2932](https://github.com/Azure/aks-engine/issues/2932)), és a` 1.15.11`orchestratorVersion alapértelmezés szerint a következő lesz:, ami hibát okoz a fürt üzembe helyezése során.

-   Az AK-Engine v 0.43.1 esetében a felhőalapú szolgáltató alapértelmezett gyakorisági beállításai vezérlik a vezérlési hurkot, és az egyéb feladatok nem működnek megfelelően Azure Stack hub Resource Manager küszöbértékekkel a bejövő kérelmek esetében. Ez a frissítés Azure stack hub alapértelmezett értékeit módosítja az újrapróbálkozási terhelés Azure stack hub Resource Managerbe ([\#2861](https://github.com/Azure/aks-engine/issues/2861)) való csökkentése érdekében.

-   Az AK-motor új ellenőrzési lépése vagy a végrehajtás leállítása vagy figyelmeztetés megjelenítése, ha az API-modell JSON-fájlja Azure stack hub által nem támogatott tulajdonságokat tartalmaz ([\#2717](https://github.com/Azure/aks-engine/issues/2717)).

-   Egy új ellenőrző beadással az AK-motor ellenőrzi, hogy rendelkezésre áll-e az AK-motor futtatásához szükséges AK-alaprendszerkép verziója ([\#2342](https://github.com/Azure/aks-engine/issues/2342)). Ez az API-modell fájljának elemzése és az Azure Stack hub Resource Manager meghívása előtt következik be.

-   A "--Control-Plane-only" paranccsal a "frissítés" parancsban a felhasználó csak a főVirtual Machines ([\#2635](https://github.com/Azure/aks-engine/issues/2635)) megcélzására használható.

-   A Linux kernel 4.15.0-1071-Azure for Ubuntu 16,04-LTS verziójának frissítése. A részletekért tekintse meg a "[csomag: Linux-rendszerkép-4.15.0-1071- \[Azure\](4.15.0-1071.76) Security](https://packages.ubuntu.com/xenial/linux-image-4.15.0-1071-azure)" című témakört.

-   Új hyperkube-frissítések a Kubernetes-verziók 1.14.8 és 1.15.10 való támogatásához.

-   Frissítse a kubectl, hogy megegyezzen a fürt Kubernetes verziójával. Ez az összetevő a Kubernetes-fürt főcsomópontjaiban érhető el, és SSH-ban is futtatható a főkiszolgálón.

-   Frissítések az Azure Container monitor bővítményhez a legújabb [február 2020-es kiadással](https://github.com/microsoft/Docker-Provider/blob/ci_feature_prod/README.md) ([\#2850](https://github.com/Azure/aks-engine/issues/2850)).

-   Frissítés a `coredns` verzióra: v 1.6.6 ([\#2555](https://github.com/Azure/aks-engine/issues/2555)).

-   Frissítsen `etcd` a 3.3.18 verzióra ([\#2462](https://github.com/Azure/aks-engine/issues/2462)).

-   Frissítsen `moby` a 3.0.11 verzióra ([\#2887](https://github.com/Azure/aks-engine/issues/2887)).

-   Ezzel a kiadással az AK-os motor `k8s.gcr.io` megrövidíti a függőséget, hogy most a hivatalos `Kubernetes MCR registry @ mcr.microsoft.com` képet használja a lemezképek összeállításakor ([\#2722](https://github.com/Azure/aks-engine/issues/2722)).

## <a name="known-issues"></a>Ismert problémák

-  Ha egy fürtön belül több Kubernetes-szolgáltatást is üzembe helyez, az alapszintű terheléselosztó konfigurációjában hibát okozhat. Ha lehetséges, telepítsen egy szolgáltatást.

-  Az AK-motor lekéréses verzióinak futtatása az Azure-ra és Azure Stack hub-ra vonatkozó információkat fog tartalmazni, azonban nem lehet explicit módon felismerni, hogy mi felel meg Azure Stack hub-nak. Ne használja ezt a parancsot annak megállapításához, hogy a frissítendő verziók milyen verziójúak legyenek. Használja a fent ismertetett frissítési hivatkozási táblázatot.

-  Mivel az AK-motor eszköz az Azure-ban és Azure Stack hub-ban található megosztási forráskódok tárháza. A számos kibocsátási Megjegyzés és a lekéréses kérelmek vizsgálatával feltételezhető, hogy az eszköz támogatja a Kubernetes és az operációs rendszer platformjának más verzióit is a fent említetteken kívül, figyelmen kívül hagyhatja őket, és a frissítéshez tartozó hivatalos útmutatóként használhatja a fenti verzió táblázatot.

## <a name="reference"></a>Referencia

Az alábbiakban felsoroljuk a kijavított hibák némelyikét, valamint a 0.44.0-ből a 0.48.0-re vonatkozó kibocsátási megjegyzések teljes készletét, figyelje meg, hogy az utóbbi lista tartalmazza az Azure-t és Azure Stack hub-elemeket

### <a name="bug-fixes"></a>Hibajavítások

-   `userAssignedIdentityId`a Windows `azure.json` hiányzó idézőjelek között ([\#2327](https://github.com/Azure/aks-engine/issues/2327))

-   Az `update config` addons csak frissítéssel ([\#2282](https://github.com/Azure/aks-engine/issues/2282))

-   A Windows-csomópontok felügyeleti IP-címének lekéréséhez szükséges időkorlát ([\#2284](https://github.com/Azure/aks-engine/issues/2284))

-   1.0.28 Azure CNI zip-fájl hozzáadása a Windows VHD-hez ([\#2268](https://github.com/Azure/aks-engine/issues/2268))

-   A IPAddressCount beállításának helyes alapértelmezett sorrendje ([\#2358](https://github.com/Azure/aks-engine/issues/2358))

-   Frissítsen egyetlen omsagent-YAML az összes k8s-verzióra, hogy elkerülje a manuális hibákat és az egyszerű karbantartást ([\#2692](https://github.com/Azure/aks-engine/issues/2692))

### <a name="release-notes"></a>Kibocsátási megjegyzések

Ez az Azure-hoz és Azure Stack hub-hoz készült kibocsátási megjegyzések teljes készlete

-   https://github.com/Azure/aks-engine/releases/tag/v0.44.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.45.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.3
-   https://github.com/Azure/aks-engine/releases/tag/v0.47.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.48.0

## <a name="next-steps"></a>További lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)
