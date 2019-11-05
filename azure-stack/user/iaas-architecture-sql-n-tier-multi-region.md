---
title: N szintű alkalmazás futtatása több Azure Stack régióban a magas rendelkezésre állás érdekében | Microsoft Docs
description: Ismerje meg, hogyan futtathat N szintű alkalmazást több Azure Stack régióban a magas rendelkezésre állás érdekében.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: acfeebe626d7745fe200724c8c53c632bada1466
ms.sourcegitcommit: 8a74a5572e24bfc42f71e18e181318c82c8b4f24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569149"
---
# <a name="run-an-n-tier-application-in-multiple-azure-stack-regions-for-high-availability"></a>N szintű alkalmazás futtatása több Azure Stack régióban a magas rendelkezésre állás érdekében

Ez a hivatkozási architektúra bevált eljárásokat mutat be az N szintű alkalmazások több Azure Stack régióban való futtatásához, a rendelkezésre állás és a robusztus vész-helyreállítási infrastruktúra biztosítása érdekében. Ebben a dokumentumban Traffic Manager a magas rendelkezésre állás eléréséhez, de ha Traffic Manager nem az Ön környezetének előnyben részesített döntése, akkor egy magas rendelkezésre állású terheléselosztó is behelyettesíthető a alkalmazásba.

> [!Note]  
> Vegye figyelembe, hogy az alábbi architektúrában használt Traffic Manager konfigurálni kell az Azure-ban, és a Traffic Manager profil konfigurálásához használt végpontokat nyilvánosan irányítható IP-címekre kell beállítani.

## <a name="architecture"></a>Architektúra

Ez az architektúra az [N szintű alkalmazásban SQL Server](iaas-architecture-windows-sql-n-tier.md).

![Nagy rendelkezésre állású hálózati architektúra az Azure N szintű alkalmazásokhoz](./media/iaas-architecturesql-n-tier-multi-region/image1.png)

-   **Elsődleges és másodlagos régiók**. Használjon két régiót a magas rendelkezésre állás eléréséhez. Az egyik ebben az esetben az elsődleges régió. A másik a feladatátvétel során használt régió.

-   **Azure Traffic Manager**. [Traffic Manager](https://azure.microsoft.com/services/traffic-manager) a bejövő kérelmeket az egyik régióba irányítja. A normál működés során a rendszer az elsődleges régió felé irányítja a kérelmeket. Ha az adott régió nem érhető el, a Traffic Manager átadj a feladatokat a másodlagos régiónak. További információt a következő szakaszban talál: [A Traffic Manager konfigurációja](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server#traffic-manager-configuration).

-   **Erőforráscsoportok**. Hozzon létre külön [erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az elsődleges régióhoz, a másodlagos régióhoz. Ennek köszönhetően rugalmasan, egyetlen erőforráskészletként kezelheti az egyes régiókat, például ismételten üzembe helyezhet egy régiót a másik eltávolítása nélkül. [Az erőforráscsoportok összekapcsolása](https://docs.microsoft.com/azure/resource-group-link-resources), így futtathat egy lekérdezést az alkalmazás összes erőforrásának listázásához.

-   **Virtuális hálózatok**. Hozzon létre külön virtuális hálózatot az egyes régiókban. Ügyeljen arra, hogy a címterek ne legyenek átfedésben.

-   **SQL Server Always On rendelkezésre állási csoport**. Ha SQL Server használ, a magas rendelkezésre állás érdekében ajánlott az [SQL always on rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) használata. Hozzon létre egyetlen rendelkezésre állási csoportot, amely mindkét régióban tartalmazza az SQL Server-példányokat.

-   **VNET a VNET VPN-kapcsolathoz**. Mivel a virtuális társhálózatok létesítése még nem érhető el Azure Stackon, a VNET használatával VNET a VPN-kapcsolatot a két virtuális hálózatok csatlakoztatása érdekében. További információ: [VNET to VNET in Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-network-howto-vnet-to-vnet?view=azs-1908) .

## <a name="recommendations"></a>Ajánlatok

A többrégiós architektúrák magasabb rendelkezésre állást biztosíthatnak, mint az egyetlen régióban történő üzembe helyezés. Ha a regionális leállás hatással van az elsődleges régióra, a [Traffic Manager](https://azure.microsoft.com/services/traffic-manager) használatával feladatátvételt hajthat végre a másodlagos régióba. Ez az architektúra az alkalmazás egy adott alrendszerének meghibásodása esetén is segíthet.

A régiók közötti magas rendelkezésre állás elérésének több általános megközelítése van:

-   **Aktív/passzív készenléti állapotú**. A forgalom az egyik régióra irányul, míg a másik készenléti kiszolgálón várakozik. Készenléti kiszolgáló esetében a másodlagos régió virtuális gépei folyamatosan le vannak foglalva és futnak.

-   **Aktív/passzív, hideg készenléti állapottal**. A forgalom az egyik régióra irányul, míg a másik hidegtartalékként áll készenlétben. Hidegtartalék esetében a másodlagos régió virtuális gépei nincsenek lefoglalva, amíg nem szükségessé nem válnak feladatátvétel céljából. Ezen megközelítés futtatása kevesebb költséggel jár, de a meghibásodáskor általában hosszabb időt vesz igénybe az üzembe állás.

-   **Aktív/aktív**. Mindkét régió aktív, és a kérelmek terheléselosztással oszlanak meg közöttük. Ha egy régió elérhetetlenné válik, kikerül a rotációból.

Ez a referenciaarchitektúra a készenléti kiszolgálóval konfigurált aktív/passzív üzemmódra összpontosít, a Traffic Managert használva a feladatátvételhez. Kis mennyiségű virtuális gépet telepíthet készenléti készenléti állapotba, majd igény szerint kibővítheti azokat.

### <a name="traffic-manager-configuration"></a>A Traffic Manager konfigurációja

A Traffic Manager konfigurálásakor vegye figyelembe a következő szempontokat:

-   **Útválasztás**. Traffic Manager számos [útválasztási algoritmust](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods)támogat. A cikkben leírt forgatókönyvhöz a *prioritásos* útválasztást (régebbi nevén *feladatátvétel esetén használt* útválasztás) használja. Ezzel a beállítással a Traffic Manager az összes kérelmet az elsődleges régió felé irányítja, kivétel akkor, ha az nem elérhető. Ebben az esetben automatikusan átadja a feladatokat a másodlagos régiónak. Lásd: a [feladatátvételi útválasztási módszer konfigurálása](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-failover-routing-method).

-   **Állapotminta**. A Traffic Manager egy HTTP-(vagy HTTPS [-)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) mintavételt használ az egyes régiók rendelkezésre állásának figyelésére. A mintavétel 200-as HTTP-választ vár egy megadott URL-címhez. Ajánlott eljárásként hozzon létre egy olyan végpontot, amely az alkalmazás általános állapotáról ad jelentést, és ezt a végpontot használja az állapotmintához. Ellenkező esetben előfordulhat, hogy a mintavétel megfelelően működő végpontot jelent, miközben az alkalmazás kritikus fontosságú részei valójában hibásak. További információ: állapot- [végpont figyelési mintája](https://docs.microsoft.com/azure/architecture/patterns/health-endpoint-monitoring).

Amikor a Traffic Manager átadja a feladatokat, az alkalmazás egy ideig nem lesz elérhető a felhasználók számára. Ez az időtartam a következő tényezőktől függ:

-   Az állapotmintának észlelnie kell, ha az elsődleges régió elérhetetlenné válik.

-   A DNS-kiszolgálóknak frissíteniük kell az IP-címhez tartozó gyorsítótárazott DNS-rekordokat. Ez a DNS élettartamától (TTL) függ. Az alapértelmezett TTL 300 másodperc (5 perc), de ezt az értéket a Traffic Manager-profil létrehozásakor konfigurálhatja.

Részletekért lásd: [a Traffic Manager figyelése](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring).

Ha a Traffic Manager feladatátvételt hajt végre, automatikus feladat-visszavétel megvalósítása helyett a manuális feladat-visszavételt ajánlunk. Ellenkező esetben előfordulhat, hogy egyes esetekben az alkalmazás oda-vissza váltogat a régiók között. A feladat-visszavétel előtt ellenőrizze, hogy az alkalmazás minden alrendszere működik-e.

Vegye figyelembe, hogy a Traffic Manager alapértelmezés szerint automatikusan végrehajtja a feladat-visszavételt. Ennek megelőzéséhez manuálisan csökkentse az elsődleges régió prioritását a feladatátvétel után. Tegyük fel például, hogy az elsődleges régió 1-es prioritású, a második pedig 2-es. A feladatátvétel után az automatikus visszavétel megelőzéséhez állítsa az elsődleges régió prioritását 3-asra. Ha készen áll a váltásra, a vissza, frissítse a prioritást 1-re.

A következő [Azure CLI](https://docs.microsoft.com/cli/azure/) -parancs frissíti a prioritást:

```cli  
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type externalEndpoints --priority 3
```

Másik megoldásként ideiglenesen letilthatja a végpontot, amíg készen nem áll a feladat-visszavételre:

```cli
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type externalEndpoints --endpoint-status Disabled
```

A feladatátvétel okától függően előfordulhat, hogy újra üzembe kell helyeznie az erőforrásokat a régión belül. A feladat-visszavétel előtt tesztelje a működési készenlétet. A teszt többek között a következőket ellenőrzi:

-   A virtuális gépek megfelelően vannak-e konfigurálva. (Az összes szükséges szoftver telepítve van, az IIS fut stb.)

-   Az alkalmazás alrendszerei működőképesek-e.

-   Funkciótesztelés. (Pl. az adatbázisszint elérhető a webes szintről.)

### <a name="configure-sql-server-always-on-availability-groups"></a>Az SQL Server Always On rendelkezésre állási csoportok konfigurálása

A Windows Server 2016-nál régebbi kiadásokban az SQL Server Always On rendelkezésre állási csoportok tartományvezérlőt igényelnek, és a rendelkezésre állási csoport összes csomópontjának azonos Active Directory (AD) tartományban kell lennie.

Az rendelkezésre állási csoport konfigurálása:

-   Legalább két tartományvezérlőt helyezzen mindegyik régióba.

-   Minden tartományvezérlőhöz rendeljen egy statikus IP-címet.

-   Hozzon létre [VPN-](https://docs.microsoft.com/azure-stack/user/azure-stack-vpn-gateway-about-vpn-gateways) t a két virtuális hálózat közötti kommunikáció engedélyezéséhez.

-   Minden egyes virtuális hálózat esetében adja hozzá a tartományvezérlők IP-címeit (mindkét régióból) a DNS-kiszolgáló listájához. Ezt az alábbi CLI-paranccsal teheti meg. További információ: [DNS-kiszolgálók módosítása](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network#change-dns-servers).

    ```cli
    az network vnet update --resource-group <resource-group> --name <vnet-name> --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
    ```

-   Hozzon létre egy [Windows Server feladatátvételi fürtszolgáltatási](https://msdn.microsoft.com/library/hh270278.aspx) (WSFC-) fürtöt, amely mindkét régióban tartalmazza a SQL Server példányokat.

-   Hozzon létre egy SQL Server Always On rendelkezésre állási csoportot, amely tartalmazza az elsődleges és a másodlagos régió SQL Server-példányait. Ennek lépéseit az [Always On rendelkezésre állási csoport kiterjesztése távoli Azure adatközpontra (PowerShell)](https://techcommunity.microsoft.com/t5/DataCAT/Extending-AlwaysOn-Availability-Group-to-Remote-Azure-Datacenter/ba-p/305217) című cikkben találja.

    -   Az elsődleges régiót helyezze az elsődleges régióba.

    -   Helyezzen egy vagy több másodlagos replikát az elsődleges régióba. Konfigurálja azokat a szinkron véglegesítés használatára, automatikus feladatátvétellel.

    -   Helyezzen egy vagy több másodlagos replikát a másodlagos régióba. Ezeket a teljesítmény érdekében *aszinkron* véglegesítés használatára konfigurálja. (Ellenkező esetben az összes T-SQL-tranzakciónak várnia kell, míg az adatok körbeérnek a hálózaton a másodlagos régióig.)

> [!Note]  
> Az aszinkron véglegesített replikák nem támogatják az automatikus feladatátvételt.

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Komplex N szintű alkalmazás esetén előfordulhat, hogy nem kell replikálnia a teljes alkalmazást a másodlagos régióba. Ehelyett elég lehet replikálni egy kritikus fontosságú alrendszert, amely az üzletmenet folytonosságának fenntartásához szükséges.

A Traffic Manager a rendszer egyik lehetséges meghibásodási pontja. Ha a Traffic Manager szolgáltatás meghibásodik, az ügyfelek a leállás ideje alatt nem érhetik el az alkalmazást. Tekintse át a [Traffic Manager SLA](https://azure.microsoft.com/support/legal/sla/traffic-manager)-t, és állapítsa meg, hogy a Traffic Manager önmagában a magas rendelkezésre állás érdekében megfelel-e az üzleti követelményeinek. Ha nem, akkor a biztonság érdekében érdemes lehet hozzáadni egy másik forgalomkezelési szolgáltatást a feladat-visszavételhez. Ha az Azure Traffic Manager szolgáltatás meghibásodik, módosítsa a CNAME rekordját a DNS-ben úgy, hogy a többi forgalomkezelő szolgáltatásra mutasson. (Ezt a lépést manuálisan kell elvégezni. Amíg a DNS-módosítások érvénybe nem lépnek, az alkalmazás nem lesz elérhető.)

Az SQL Server-fürt esetében két feladatátvételi forgatókönyvet kell figyelembe venni:

-   Az összes SQL Server-adatbázisreplika meghibásodik az elsődleges régióban. Ez például regionális kimaradás során fordulhat elő. Ebben az esetben manuálisan kell elvégeznie a rendelkezésre állási csoport feladatátvételét, annak ellenére, hogy a Traffic Manager az előtérben automatikusan elvégzi a feladatátvételt. Kövesse a [Kényszerített manuális feladatátvétel elvégzése SQL Server rendelkezésre állási csoporton](https://msdn.microsoft.com/library/ff877957.aspx) című cikk lépéseit. A cikk leírja, hogyan végezhető kényszerített feladatátvétel az SQL Server Management Studio, a Transact-SQL vagy a PowerShell használatával az SQL Server 2016-ban.

    > [!Warning]  
    > A kényszerített feladatátvétel esetében adatvesztés fordulhat elő. Amint az elsődleges régió újra elérhetővé válik, készítsen pillanatfelvételt az adatbázisról, és használja a [tablediff](https://msdn.microsoft.com/library/ms162843.aspx) parancsot a különbségek megkereséséhez.

-   A Traffic Manager átadja a feladatokat a másodlagos régiónak, de az elsődleges SQL Server-adatbázisreplika továbbra is elérhető marad. Például előfordulhat, hogy az előtérréteg meghibásodik, de ez nincs hatással az SQL Servert futtató virtuális gépekre. Ebben az esetben a rendszer átirányítja az internetes forgalmat a másodlagos régióba, és ez a régió továbbra is csatlakozhat az elsődleges replikához. Ekkor azonban nagyobb lesz a késés, mivel az SQL Server-kapcsolatoknak több régión kell áthaladniuk. Ebben a helyzetben manuálisan kell végrehajtania a feladatátvételt az alábbiak szerint:

    1.  Ideiglenesen állítson át egy SQL Server-adatbázisreplikát a másodlagos régióban *szinkron* véglegesítésre. Ez biztosítja, hogy ne legyen adatvesztés a feladatátvétel során.

    2.  Végezze el a feladatátvételt erre a replikára.

    3.  Miután megtörtént a feladat-visszavétel az elsődleges régióra, állítsa vissza az aszinkron véglegesítést.

## <a name="manageability-considerations"></a>Felügyeleti szempontok

Amikor frissíti az üzemelő példányt, egyszerre egy régiót frissítsen. Ezzel csökkenthető a nem megfelelő konfiguráció vagy az alkalmazásban felmerülő hiba okozta globális meghibásodás esélye.

Tesztelje a rendszer meghibásodásokkal szembeni rugalmasságát. Alább található néhány gyakori meghibásodási helyzet:

-   Állítson le virtuálisgép-példányokat.

-   Terhelje az erőforrásokat, például a processzort és a memóriát.

-   Bontsa/késleltesse a hálózati kapcsolatot.

-   Váltsa ki egyes folyamatok összeomlását.

-   Alkalmazzon lejárt tanúsítványokat.

-   Szimuláljon hardverhibákat.

-   Állítsa le a DNS-szolgáltatást a tartományvezérlőkön.

Mérje meg a helyreállítási időtartamokat, és győződjön meg róla, hogy azok megfelelnek az üzleti követelményeinek. Több hibaállapot kombinációját is tesztelje.

## <a name="next-steps"></a>További lépések

- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](https://docs.microsoft.com/azure/architecture/patterns).