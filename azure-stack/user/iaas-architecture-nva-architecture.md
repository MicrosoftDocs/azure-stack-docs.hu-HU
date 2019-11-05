---
title: Magasan elérhető hálózati virtuális berendezések üzembe helyezése Azure Stackon | Microsoft Docs
description: Ismerje meg, hogyan helyezhetők üzembe a virtuális gépek a Azure Stackon.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: f3a590519fcfb6b6214b202920b2756484f51dfc
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595339"
---
# <a name="deploy-highly-available-network-virtual-appliances-on-azure-stack"></a>Magasan elérhető hálózati virtuális berendezések üzembe helyezése Azure Stack

Ez a cikk bemutatja, hogyan helyezhet üzembe egy hálózati virtuális berendezést (NVA) a Azure Stack magas rendelkezésre állásához. Az NVA-kat általában a szegélyhálózatokról, más néven DMZ-kről a más hálózatok és alhálózatok felé irányuló hálózati forgalom szabályozására használják. A cikk csak bejövő, csak kimenő, valamint bejövő és kimenő forgalmú példaarchitektúrákat is tartalmaz.

A [Azure stack piactéren](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items)elérhető különböző gyártóktól származó NVA az optimális teljesítmény érdekében az egyiket használhatja.

Az architektúra a következő összetevőket tartalmazza.

## <a name="networking-and-load-balancing"></a>Hálózatkezelés és terheléselosztás

-   **Virtuális hálózat és alhálózatok**. Minden Azure-beli virtuális gép üzembe helyezése egy, az alhálózatokra szegmentált virtuális hálózatba történik. Hozzon létre egy külön alhálózatot minden egyes szinthez.

-   **7. rétegbeli Load Balancer.** Mivel a Application Gateway még nem érhető el Azure Stackon, a [Azure stack piacon](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items) elérhető alternatívák vannak, például: [Kemp Loadmaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [F5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) vagy [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Terheléselosztók**. A [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)használatával terjesztheti a webes szinten lévő hálózati forgalmat az üzleti szintjére, valamint az üzleti szintjétől a SQL Serverig.

-   **Hálózati biztonsági csoportok** (NSG). A NSG használata a virtuális hálózaton belüli hálózati forgalom korlátozására. Az itt bemutatott háromrétegű architektúrában például az adatbázis-réteg nem fogadja el a webes kezelőfelületről érkező forgalmat, csak az üzleti rétegből és a felügyeleti alhálózatból.

-   **UDR.** A [*felhasználó által megadott útvonalak*](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview/) (UDR-EK) használatával irányíthatja a forgalmat az adott terheléselosztó felé.

Ez a cikk a Azure Stack hálózatkezelés alapszintű ismeretét feltételezi.

## <a name="architecture-diagrams"></a>Architektúra-diagramok

Egy NVA számos különböző architektúrában üzembe helyezhető a peremhálózaton. Az alábbi ábra például egyetlen NVA használatát szemlélteti a bejövő forgalomhoz.

![A közösségi média utáni Leírás automatikusan generált képernyőképe](./media/iaas-architecture-nva-architecture/image1.png)

Ebben az architektúrában az NVA biztonságos hálózati határt biztosít az összes bejövő és kimenő hálózati forgalom ellenőrzésével, és csak azt a forgalmat engedi át, amely megfelel a hálózati biztonsági szabályoknak. Az a tény, hogy az összes hálózati forgalomnak át kell haladnia a NVA, azt jelenti, hogy a NVA egyetlen meghibásodási pont a hálózaton. Ha az NVA leáll, akkor nincs más útvonal a hálózati forgalom számára, és a háttérbeli alhálózatok nem lesznek elérhetők.

Az NVA magas rendelkezésre állásúvá tételéhez helyezzen üzembe több NVA-t egy rendelkezésre állási csoport részeként.

A következő architektúrák bemutatják a magas rendelkezésre állású NVA-khoz szükséges erőforrásokat és konfigurációkat:

| Megoldás | Előnyök | Megfontolandó szempontok |
| --- | --- | --- |
| Bejövő forgalom 7-es rétegű NVA-kkal | Minden NVA-csomópont aktív. | Olyan NVA igényel, amely képes a kapcsolatok megszakítására és a SNAT használatára.<br>A NVA külön készletet igényel a vállalati hálózatról/internetről érkező, illetve a Azure Stackból érkező forgalomhoz.<br>Csak Azure Stackon kívülről származó forgalomhoz használható.  |
| Kimenő forgalom 7-es rétegű NVA-kkal | Minden NVA-csomópont aktív. | Olyan NVA igényel, amely képes megszakítani a kapcsolatokat, és implementálja a forrás hálózati címfordítást (SNAT). |
| Bejövő forgalom-kimenő forgalom 7. rétegbeli NVA | Minden csomópont aktív.<br>A forgalom a Azure Stackből származik. | Olyan NVA igényel, amely képes a kapcsolatok megszakítására és a SNAT használatára.<br>A NVA külön készletet igényel a vállalati hálózatról/internetről érkező, illetve a Azure Stackból érkező forgalomhoz. |

## <a name="ingress-with-layer-7-nvas"></a>Bejövő forgalom 7-es rétegű NVA-kkal

Az alábbi ábra egy magas rendelkezésre állású architektúrát mutat be, amely egy internetkapcsolattal rendelkező terheléselosztó mögötti bejövő peremhálózati hálózatot valósít meg. Ez az architektúra úgy lett kialakítva, hogy kapcsolatot biztosítson a 7. rétegbeli forgalom Azure Stack munkaterhelésével, például HTTP vagy HTTPS:

![A Térkép leírásának automatikusan generált képernyőképe](./media/iaas-architecture-nva-architecture/image2.png)

Az ilyen architektúra előnye, hogy minden NVA aktív, és ha az egyik meghibásodik, akkor a terheléselosztó átirányítja a hálózati forgalmat egy másikra. Mindkét NVA a belső terheléselosztóra irányítja a forgalmat, így mindaddig, amíg egy NVA aktív, a forgalom nem akad el. Az NVA-knak le kell zárnia a webes szintű virtuális gépek felé tartó SSL-forgalmat. Ezek a NVA nem terjeszthetők ki a vállalati hálózati forgalom kezelésére, mert a vállalati hálózati forgalomhoz egy másik dedikált NVA-készletre van szükség a saját hálózati útvonalakkal.

## <a name="egress-with-layer-7-nvas"></a>Kimenő forgalom 7-es rétegű NVA-kkal

A 7. rétegbeli NVA architektúra kiterjeszthető, hogy kiterjeszthető peremhálózat legyen a Azure Stack számítási feladatból származó kérelmek számára. A következő architektúra úgy lett kialakítva, hogy magas rendelkezésre állást biztosítson a peremhálózat NVA a 7. rétegbeli forgalomhoz, például HTTP vagy HTTPS:

![A mobiltelefon-Leírás automatikusan generált képernyőképe](./media/iaas-architecture-nva-architecture/image3.png)

Ebben az architektúrában a Azure Stackból származó összes forgalmat egy belső terheléselosztó irányítja. A terheléselosztó elosztja a kimenő kérelmeket egy NVA-készlet tagjai között. Ezek az NVA-k az internetre irányítják a forgalmat saját nyilvános IP-címeik használatával.

## <a name="ingress-egress-with-layer-7--nvas"></a>Bejövő forgalom-kimenő forgalom 7. rétegbeli NVA

A két bejövő és kimenő architektúrában külön peremhálózati hálózat volt a bejövő és kimenő forgalomhoz. A következő architektúra azt mutatja be, hogyan lehet létrehozni egy peremhálózati hálózatot, amely a 7. rétegbeli forgalom (például HTTP vagy HTTPS) esetében egyaránt használható a bejövő és kimenő forgalomhoz:

![A közösségi média utáni Leírás automatikusan generált képernyőképe](./media/iaas-architecture-nva-architecture/image4.png)

A bejövő forgalomban a 7. rétegbeli NVA architektúrában a NVA a 7. rétegbeli Load Balancer beérkező kérelmeket dolgozza fel. Az NVA-k a terheléselosztó háttérkészletében található, számítási feladatokat végző virtuális gépek kimenő kérelmeit is feldolgozzák. Mivel a bejövő forgalom egy 7. rétegbeli Load balancerrel van irányítva, és a kimenő forgalom egy SLB (Azure Stack alapszintű Load Balancer) van irányítva, a NVA felelősek a munkamenet-affinitás fenntartásáért. Ez azt a 7. rétegbeli terheléselosztó a bejövő és kimenő kérelmek leképezését is fenntartja, hogy a megfelelő választ továbbítsa az eredeti kérelmezőnek. A belső terheléselosztó azonban nem fér hozzá a 7. rétegbeli terheléselosztó-hozzárendelésekhez, és a saját logikával küldi el a válaszokat a NVA. Lehetséges, hogy a terheléselosztó választ küldhet egy olyan NVA, amely eredetileg nem kapta meg a kérelmet a 7. rétegbeli terheléselosztótól. Ebben az esetben a NVA kommunikálni kell, és át kell adni a választ, hogy a megfelelő NVA továbbítsa a választ a 7. rétegbeli terheléselosztó számára.

> [!Note]  
> Úgy is megoldhatja az aszimmetrikus útvonal-választási problémát, ha gondoskodik róla, hogy az NVA-k bejövő forráshálózati címfordítást (SNAT) végezzenek. Ez lecserélné a kérelmező eredeti forrás IP-címét az NVA által a bejövő adatfolyamon használt IP-címeinek egyikével. Így gondoskodni lehet róla, hogy több NVA is használható legyen egyszerre az útvonal szimmetriájának megőrzése mellett.

## <a name="next-steps"></a>További lépések

- Azure Stack virtuális gépekkel kapcsolatos további tudnivalókért lásd: Azure Stack virtuálisgép- [funkciók](azure-stack-vm-considerations.md).  
- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](https://docs.microsoft.com/azure/architecture/patterns).
