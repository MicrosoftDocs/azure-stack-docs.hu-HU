---
title: Magasan elérhető hálózati virtuális berendezések üzembe helyezése Azure Stack hub-on
description: Ismerje meg, hogyan helyezhetők üzembe a Azure Stack hub-ban található, magasan elérhető hálózati virtuális berendezések.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 7e928757447f47c9235690f38e62778a4594faf1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873775"
---
# <a name="deploy-highly-available-network-virtual-appliances-on-azure-stack-hub"></a>Magasan elérhető hálózati virtuális berendezések üzembe helyezése Azure Stack hub-on

Ez a cikk bemutatja, hogyan helyezhet üzembe egy hálózati virtuális berendezést (NVA) a magas rendelkezésre állás érdekében Azure Stack hub-ban. Az NVA-kat általában a szegélyhálózatokról, más néven DMZ-kről a más hálózatok és alhálózatok felé irányuló hálózati forgalom szabályozására használják. A cikk csak bejövő, csak kimenő, valamint bejövő és kimenő forgalmú példaarchitektúrákat is tartalmaz.

A [Azure stack hub piactéren](../operator/azure-stack-marketplace-azure-items.md)elérhető különböző gyártóktól származó NVA az optimális teljesítmény érdekében az egyiket használhatja.

Az architektúra a következő összetevőket tartalmazza.

## <a name="networking-and-load-balancing"></a>Hálózatkezelés és terheléselosztás

-   **Virtuális hálózat és alhálózatok**. Minden Azure-beli virtuális gép üzembe helyezése egy, az alhálózatokra szegmentált virtuális hálózatba történik. Hozzon létre egy külön alhálózatot minden egyes szinthez.

-   **7. rétegbeli Load Balancer.** Mivel a Application Gateway még nem érhető el az Azure stack hub-on, a [Azure stack hub piacon](../operator/azure-stack-marketplace-azure-items.md) elérhető alternatívák is rendelkezésre állnak, például: [Kemp Loadmaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) /  [F5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) vagy [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Terheléselosztó.** A [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)használatával terjesztheti a webes szinten lévő hálózati forgalmat az üzleti szintjére, valamint az üzleti szintjétől a SQL Serverig.

-   **Hálózati biztonsági csoportok** (NSG). A NSG használata a virtuális hálózaton belüli hálózati forgalom korlátozására. Az itt bemutatott háromrétegű architektúrában például az adatbázis-réteg nem fogadja el a webes kezelőfelületről érkező forgalmat, csak az üzleti rétegből és a felügyeleti alhálózatból.

-   **UDR.** A [*felhasználó által megadott útvonalak*](/azure/virtual-network/virtual-networks-udr-overview/) (UDR-EK) használatával irányíthatja a forgalmat az adott terheléselosztó felé.

Ez a cikk az Azure Stack hub hálózatkezelésének alapvető ismeretét feltételezi.

## <a name="architecture-diagrams"></a>Architektúra-diagramok

Egy NVA számos különböző architektúrában üzembe helyezhető a peremhálózaton. A következő ábra például egyetlen NVA bejövő forgalomhoz való használatát illusztrálja.

![Képernyőkép, amely egyetlen NVA használatát mutatja be a bejövő forgalom számára.](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image1.svg)

Ebben az architektúrában az NVA biztonságos hálózati határt biztosít az összes bejövő és kimenő hálózati forgalom ellenőrzésével, és csak azt a forgalmat engedi át, amely megfelel a hálózati biztonsági szabályoknak. Az a tény, hogy az összes hálózati forgalomnak át kell haladnia a NVA, azt jelenti, hogy a NVA egyetlen meghibásodási pont a hálózaton. Ha az NVA leáll, akkor nincs más útvonal a hálózati forgalom számára, és a háttérbeli alhálózatok nem lesznek elérhetők.

Az NVA magas rendelkezésre állásúvá tételéhez helyezzen üzembe több NVA-t egy rendelkezésre állási csoport részeként.

A következő architektúrák bemutatják a magas rendelkezésre állású NVA-khoz szükséges erőforrásokat és konfigurációkat:

| Megoldás | Előnyök | Megfontolandó szempontok |
| --- | --- | --- |
| Bejövő forgalom 7-es rétegű NVA-kkal | Minden NVA-csomópont aktív. | Olyan NVA igényel, amely képes a kapcsolatok megszakítására és a SNAT használatára.<br>A vállalati hálózatról/internetről és Azure Stack hub-ról érkező forgalomhoz külön NVA-készletre van szükség.<br>Csak Azure Stack hub-on kívülről származó forgalomhoz használható.  |
| Kimenő forgalom 7-es rétegű NVA-kkal | Minden NVA-csomópont aktív. | Olyan NVA igényel, amely képes megszakítani a kapcsolatokat, és implementálja a forrás hálózati címfordítást (SNAT). |
| Bejövő és kimenő forgalom 7-es rétegű NVA-kkal | Minden csomópont aktív.<br>Képes kezelni a forgalmat Azure Stack hub-ból. | Olyan NVA igényel, amely képes a kapcsolatok megszakítására és a SNAT használatára.<br>A vállalati hálózatról/internetről és Azure Stack hub-ról érkező forgalomhoz külön NVA-készletre van szükség. |

## <a name="ingress-with-layer-7-nvas"></a>Bejövő forgalom 7-es rétegű NVA-kkal

Az alábbi ábra egy magas rendelkezésre állású architektúrát mutat be, amely egy internetkapcsolattal rendelkező terheléselosztó mögötti bejövő peremhálózati hálózatot valósít meg. Ez az architektúra úgy lett kialakítva, hogy kapcsolatot biztosítson Azure Stack hub munkaterhelésekkel a 7. rétegbeli forgalomhoz, például HTTP vagy HTTPS:

![A Térkép leírásának automatikusan generált képernyőképe](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image2.svg)

Az ilyen architektúra előnye, hogy minden NVA aktív, és ha az egyik meghibásodik, akkor a terheléselosztó átirányítja a hálózati forgalmat egy másikra. Mindkét NVA a belső terheléselosztóra irányítja a forgalmat, így mindaddig, amíg egy NVA aktív, a forgalom nem akad el. Az NVA-knak le kell zárnia a webes szintű virtuális gépek felé tartó SSL-forgalmat. Ezek a NVA nem terjeszthetők ki a vállalati hálózati forgalom kezelésére, mert a vállalati hálózati forgalomhoz egy másik dedikált NVA-készletre van szükség a saját hálózati útvonalakkal.

## <a name="egress-with-layer-7-nvas"></a>Kimenő forgalom 7-es rétegű NVA-kkal

A 7. rétegbeli NVA architektúra kiterjeszthető, hogy kiterjeszthető peremhálózat legyen a Azure Stack hub számítási feladatból származó kérelmek számára. A következő architektúra úgy lett kialakítva, hogy magas rendelkezésre állást biztosítson a peremhálózat NVA a 7. rétegbeli forgalomhoz, például HTTP vagy HTTPS:

![A mobiltelefon-Leírás automatikusan generált képernyőképe](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image4.svg)

Ebben az architektúrában az Azure Stack hub-ból származó összes forgalmat egy belső terheléselosztó felé irányítja a rendszer. A terheléselosztó elosztja a kimenő kérelmeket egy NVA-készlet tagjai között. Ezek az NVA-k az internetre irányítják a forgalmat saját nyilvános IP-címeik használatával.

## <a name="ingress-egress-with-layer-7--nvas"></a>Bejövő forgalom-kimenő forgalom 7. rétegbeli NVA

A két bejövő és kimenő architektúrában külön peremhálózati hálózat volt a bejövő és kimenő forgalomhoz. A következő architektúra azt mutatja be, hogyan lehet létrehozni egy peremhálózati hálózatot, amely a 7. rétegbeli forgalom (például HTTP vagy HTTPS) esetében egyaránt használható a bejövő és kimenő forgalomhoz:

![A közösségi média utáni Leírás automatikusan generált képernyőképe](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image4.svg)

A bejövő forgalomban a 7. rétegbeli NVA architektúrában a NVA a 7. rétegbeli Load Balancer beérkező kérelmeket dolgozza fel. Az NVA-k a terheléselosztó háttérkészletében található, számítási feladatokat végző virtuális gépek kimenő kérelmeit is feldolgozzák. Mivel a bejövő forgalom egy 7. rétegbeli Load balancerrel van irányítva, és a kimenő forgalom egy SLB (Azure Stack hub alapszintű Load Balancer) van irányítva, a NVA a munkamenet-affinitás fenntartásáért felelősek. Ez azt a 7. rétegbeli terheléselosztó a bejövő és kimenő kérelmek leképezését is fenntartja, hogy a megfelelő választ továbbítsa az eredeti kérelmezőnek. A belső terheléselosztó azonban nem fér hozzá a 7. rétegbeli terheléselosztó-hozzárendelésekhez, és a saját logikával küldi el a válaszokat a NVA. Lehetséges, hogy a terheléselosztó választ küldhet egy olyan NVA, amely eredetileg nem kapta meg a kérelmet a 7. rétegbeli terheléselosztótól. Ebben az esetben a NVA kommunikálni kell, és át kell adni a választ, hogy a megfelelő NVA továbbítsa a választ a 7. rétegbeli terheléselosztó számára.

> [!NOTE]  
> Úgy is megoldhatja az aszimmetrikus útvonal-választási problémát, ha gondoskodik róla, hogy az NVA-k bejövő forráshálózati címfordítást (SNAT) végezzenek. Ez lecserélné a kérelmező eredeti forrás IP-címét az NVA által a bejövő adatfolyamon használt IP-címeinek egyikével. Így gondoskodni lehet róla, hogy több NVA is használható legyen egyszerre az útvonal szimmetriájának megőrzése mellett.

## <a name="next-steps"></a>További lépések

- Azure Stack hub virtuális gépekkel kapcsolatos további tudnivalókért lásd: [Azure stack hub](azure-stack-vm-considerations.md)virtuálisgép-funkciók.  
- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](/azure/architecture/patterns).
