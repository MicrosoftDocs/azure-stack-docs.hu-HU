---
title: Azure Stack HCI gazdagép hálózati követelményei
description: A Azure Stack HCI-hez tartozó gazdagép hálózati követelményeinek megismerése
author: v-dasis
ms.topic: how-to
ms.date: 11/25/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 2d31294b2b3a402efcfd4376988d1de6c3dbabd1
ms.sourcegitcommit: 26901a61a44390bc9b7804c22018c213036e680d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355544"
---
# <a name="host-network-requirements-for-azure-stack-hci"></a>Azure Stack HCI gazdagép hálózati követelményei

> A következőre vonatkozik Azure Stack HCI, Version 20H2

Ez a témakör Azure Stack HCI-gazdagép hálózatkezelési szempontjait és követelményeit tárgyalja. Az adatközpont-architektúrákkal és a kiszolgálók közötti fizikai kapcsolatokkal kapcsolatos információkért lásd: [fizikai hálózati követelmények](physical-network-requirements.md).

## <a name="network-traffic-types"></a>Hálózati forgalom típusai

Azure Stack a HCI hálózati forgalmat a kívánt céllal lehet osztályozni:

- **Számítási forgalom** – virtuális GÉPHEZ (VM) származó vagy oda irányuló forgalom
- **Tárolási forgalom** – közvetlen TÁROLÓHELYEK (S2D) forgalma a Server Message Block (SMB) használatával
- **Felügyeleti forgalom** – a rendszergazdák számára fontos a fürtözési felügyelet, például a Active Directory, a távoli asztal, a Windows felügyeleti központ és a Windows PowerShell.

## <a name="selecting-a-network-adapter"></a>Hálózati adapter kiválasztása

Azure Stack HCI esetén olyan hálózati adaptert kell választani, amely a Windows Server Software-Defined adatközpont (SDDC) minősítését a standard vagy prémium szintű kiegészítő minősítéssel (AQ) valósította meg. Ezek az adapterek támogatják a platform legfejlettebb funkcióit, és a hardveres partnereink általi tesztelésen estek át. Ez a vizsgálati szint általában a hardver és az illesztőprogrammal kapcsolatos minőségi problémák csökkenését eredményezi.

A standard vagy prémium AQ-vel rendelkező adapterek azonosításához tekintse át az adapterhez tartozó [Windows Server Catalog](https://www.windowsservercatalog.com/) bejegyzést, valamint az operációs rendszer megfelelő verzióját. Alább látható egy példa a prémium AQ jelölésére:

:::image type="content" source="media/plan-networking/windows-certified.png" alt-text="Windows Certified" lightbox="media/plan-networking/windows-certified.png":::

## <a name="overview-of-key-network-adapter-capabilities"></a>A legfontosabb hálózati adapterek képességeinek áttekintése

Azure Stack HCI által kihasznált hálózati adapterek fontos funkciói a következők:

- Dinamikus virtuális gép – több várólista (dinamikus VMMQ vagy d. VMMQ)
- Távoli közvetlen memória-hozzáférés (RDMA)
- Vendég RDMA
- Beágyazott összevonás (SET) váltása

### <a name="dynamic-vmmq"></a>Dinamikus VMMQ

A prémium AQ-vel rendelkező összes hálózati adapter támogatja a dinamikus VMMQ. A dinamikus VMMQ a Switch beágyazott összevonásának használatát igényli.

**Alkalmazható adatforgalmi típusok**: számítás

**Szükséges minősítések**: prémium

A dinamikus VMMQ olyan intelligens fogadási technológia, amely a virtuálisgép-várólista (VMQ), a virtuális fogadó oldali skálázás (vRSS) és a VMMQ három fő újításának előállítására épül:

- Optimalizálja a gazdagép hatékonyságát a CPU-magok használatával
- A hálózati forgalom feldolgozásának automatikus finomhangolása a CPU-magokon, így lehetővé téve a virtuális gépek számára a várt átviteli sebesség teljesítését és karbantartását
- Engedélyezi a "feltört" számítási feladatokat, hogy megkapják a forgalom várható mennyiségét

A dinamikus VMMQ kapcsolatos további információkért tekintse meg a [szintetikus gyorsítások](https://techcommunity.microsoft.com/t5/networking-blog/synthetic-accelerations-in-a-nutshell-windows-server-2019/ba-p/653976)utáni blogbejegyzést.

### <a name="rdma"></a>RDMA

A RDMA egy hálózati verem, amely lehetővé teszi, hogy a hálózati adapter az SMB-tároló forgalmát az operációs rendszer kihagyása céljából dolgozza fel.

A RDMA lehetővé teszi a nagy átviteli sebességű, kis késleltetésű hálózatkezelést a minimális gazda CPU-erőforrások használata mellett. Ezek a gazda CPU-erőforrások később további virtuális gépek vagy tárolók futtatására is használhatók.

**Alkalmazható adatforgalmi típusok**: gazdagép tárterülete

**Szükséges minősítések**: standard

A standard vagy prémium AQ-vel rendelkező összes adapter támogatja a RDMA (távoli közvetlen memória-hozzáférés). A RDMA ajánlott üzembe helyezési lehetőség a Azure Stack HCI-ben tárolt tárolási feladatokhoz, és opcionálisan engedélyezhető a virtuális gépekhez használható tárolási feladatokhoz (SMB használatával). Tekintse meg a **vendég RDMA** szakaszt később.

Azure Stack HCI támogatja a RDMA a iWARP vagy a RoCE protokoll implementációjának használatával.

> [!IMPORTANT]
> A RDMA-adapterek csak olyan más RDMA-adapterekkel működnek, amelyek ugyanazt a RDMA protokollt (iWARP vagy RoCE) implementálják.

A szállítók nem minden hálózati adaptere támogatja a RDMA. A következő táblázat felsorolja azokat a szállítókat (betűrendben), amelyek prémium szintű tanúsítvánnyal rendelkező RDMA-adaptereket kínálnak. Vannak azonban olyan hardvergyártók, amelyek nem szerepelnek a listán, és a RDMA is támogatják. A RDMA támogatásának ellenőrzéséhez tekintse meg a [Windows Server catalogt](https://www.windowsservercatalog.com/) .

> [!NOTE]
> A InfiniBand (IB) Azure Stack HCI esetében nem támogatott.

|NIC-szállító|iWARP|RoCE|
|----|----|----|
|Broadcom|Nem|Igen|
|Chelsio|Igen|Nem|
|Intel|Igen|Igen (egyes modellek)|
|Marvell (Qlogic/Cavium)|Igen|Igen|
|NVIDIA (Mellanox)|Nem|Igen|

> [!NOTE]
> A szállítók nem minden adapter támogatja a RDMA. Ellenőrizze, hogy a RDMA-támogatás megfelel-e az adott hálózati kártya gyártójával.

A RDMA telepítésével kapcsolatos további információkért töltse le a Word Doc alkalmazást az [Sdn GitHub](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx)-adattárból.

#### <a name="internet-wide-area-rdma-protocol-iwarp"></a>Internetes nagykiterjedésű RDMA protokoll (iWARP)

a iWARP a Transmission Control Protocol (TCP) protokollt használja, és opcionálisan kibővíthető az adatközpont-áthidalás (DCB) prioritáson alapuló Flow Control (PFC) és a bővített átviteli szolgáltatás (ETS) használatával.

Javasoljuk, hogy a iWARP a következőket használja:

- Kevés vagy semmilyen hálózati élmény van, vagy kényelmetlen a hálózati kapcsolók kezelése.
- Nem szabályozza a ToR-kapcsolókat
- Az üzembe helyezést követően nem fogja kezelni a megoldást
- Már rendelkezik üzemelő példányokkal a iWARP használatával
- Nem biztos benne, hogy melyik lehetőséget kell választania

#### <a name="rdma-over-converged-ethernet-roce"></a>RDMA konvergált Etherneten (RoCE)

A RoCE a User Datagram Protocol (UDP) protokollt használja, és megköveteli, hogy az adatközpont-áthidalás a PFC és az ETS használatával biztosítson megbízhatóságot.

Javasoljuk, hogy a RoCE a következőket használja:

- Már rendelkezik a RoCE-vel rendelkező üzemelő példányokkal az adatközpontban
- Ön ismeri a fizikai hálózati követelményeket

### <a name="guest-rdma"></a>Vendég RDMA

A vendég RDMA lehetővé teszi az SMB-munkaterhelések számára a virtuális gépek számára, hogy ugyanazokat az előnyöket használják a gazdagépeken a RDMA használatával.

**Alkalmazható adatforgalmi típusok**: számítás

**Szükséges minősítések**: prémium

 A vendég RDMA használatának elsődleges előnyei a következők:

- A hálózati forgalom feldolgozására szolgáló hálózati ADAPTERre történő kiszervezés
- Rendkívül alacsony késés
- Nagy átviteli sebesség

A vendég RDMA üzembe helyezésével kapcsolatos további információkért töltse le a Word Doc-ját az [Sdn GitHub](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx)-adattárból.

### <a name="switch-embedded-teaming-set"></a>Beágyazott összevonás (SET) váltása

A Switch Embedded Teaming (SET) egy szoftveres összevonási technológia, amelyet a Windows Server operációs rendszer a Windows Server 2016 óta tartalmaz. A készlet nem függ a használt hálózati adapterek típusától.

**Alkalmazható adatforgalmi típusok**: számítás, tárolás és felügyelet

**Szükséges minősítések:** nincs (az operációs rendszerbe építve)

A SET az egyetlen, Azure Stack HCI által támogatott összevonási technológia. A terheléselosztás/feladatátvétel (LBFO) egy másik, a Windows Servert használó összevonási technológia, amelyet a Azure Stack HCI nem támogat. A Azure Stack HCI-LBFO kapcsolatos további információkért tekintse meg a [Azure stack HCI-](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642) ben elérhető blogbejegyzéseket. A SET jól működik a számítási, tárolási és felügyeleti forgalom esetében egyaránt.

A SET fontos a Azure Stack HCI esetében, mivel ez az egyetlen összevonási technológia, amely a következőket teszi lehetővé:

- RDMA-adapterek összevonása (ha szükséges)
- Vendég RDMA
- Dinamikus VMMQ
- Egyéb fontos Azure Stack HCI-funkciók (lásd: összevonás [Azure stack HCI-ben](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642))

A SET további funkciókat biztosít a LBFO felett, beleértve a minőségi és a teljesítménybeli tökéletesítéseket is. Ehhez be kell állítani a szimmetrikus (azonos) adapterek használatát – az aszimmetrikus adapterek összevonása nem támogatott. A szimmetrikus hálózati adapterek azonosak:

- Gyártmány (szállító)
- modell (verzió)
- sebesség (átviteli sebesség)
- konfiguráció

A legegyszerűbb módszer annak azonosítására, hogy az adapterek szimmetrikusak-e, ha a sebességek megegyeznek, és a csatoló leírása megegyezik. Csak a leírásban felsorolt számokban térhetnek el. A [`Get-NetAdapterAdvancedProperty`](https://docs.microsoft.com/powershell/module/netadapter/get-netadapteradvancedproperty) parancsmag használatával győződjön meg arról, hogy a megadott konfigurációban ugyanazok a tulajdonságértékek szerepelnek.

Tekintse meg az alábbi táblázatot, amely egy példát mutat be a csak számokból (#) álló illesztőfelület-leírásokra:

|Név|Interfész leírása|Kapcsolat sebessége|
|----|----|----|
|NIC1|Hálózati adapter #1|25 GB/s|
|NIC2|Hálózati adapter #2|25 GB/s|
|NIC3|Hálózati adapter #3|25 GB/s|
|NIC4|Hálózati adapter #4|25 GB/s|

### <a name="rdma-traffic-considerations"></a>RDMA-forgalom szempontjai

Az adatközpont-áthidalás (DCB) megvalósítása esetén gondoskodnia kell arról, hogy a PFC-és ETS-konfiguráció megfelelően legyen implementálva minden hálózati porton, beleértve a hálózati kapcsolókat is. DCB szükséges a RoCE, és nem kötelező a iWARP.

A RDMA telepítésével kapcsolatos részletes információkért töltse le az [Sdn GitHub](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx)-tárházból a doc szót.

A RoCE-alapú Azure Stack HCI implementációk három PFC forgalmi osztály konfigurációját igénylik, beleértve az alapértelmezett forgalmi osztályt a hálón és az összes gazdagépen:

#### <a name="cluster-traffic-class"></a>Fürt forgalmi osztálya

Ez a forgalmi osztály biztosítja, hogy elegendő sávszélesség legyen fenntartva a fürt szívveréséhez:

- Kötelező: Igen
- PFC engedélyezve: nem
- Ajánlott forgalmi prioritás: 7. prioritás
- Ajánlott sávszélesség-foglalás:
    - 10 GbE vagy alacsonyabb RDMA hálózat = 2%
    - 25 GbE vagy újabb RDMA hálózat = 1%

#### <a name="rdma-traffic-class"></a>RDMA Traffic osztály

Ez a forgalmi osztály biztosítja, hogy elegendő sávszélesség legyen fenntartva a veszteségmentes, közvetlen SMB-kommunikációhoz:

- Kötelező: Igen
- PFC engedélyezve: igen
- Ajánlott forgalmi prioritás: 3. vagy 4. prioritás
- Ajánlott sávszélesség-foglalás: 50%

#### <a name="default-traffic-class"></a>Alapértelmezett forgalmi osztály

Ez a forgalmi osztály minden egyéb, a fürtön és a RDMA-forgalomban nem meghatározott forgalmat is magában foglal, beleértve a virtuális gépek forgalmát és a felügyeleti forgalmat:

- Kötelező: alapértelmezés szerint (nincs szükség konfigurációra a gazdagépen)
- A Flow Control (PFC) engedélyezve: nem
- Ajánlott forgalmi osztály: alapértelmezés szerint (0. prioritás)
- Ajánlott sávszélesség-foglalás: alapértelmezés szerint (nincs szükség gazdagép-konfigurációra)

## <a name="storage-traffic-models"></a>Tárolási forgalmi modellek

Az SMB számos előnnyel jár, mint a Azure Stack HCI tárolási protokollja, beleértve a többcsatornás SMB-t is. Míg a többcsatornás SMB a jelen témakörön kívül esik, fontos tisztában lennie azzal, hogy a forgalom a többcsatornás SMB által használható összes lehetséges kapcsolaton alapul.

> [!NOTE]
>Azt javasoljuk, hogy több alhálózatot és VLAN-t használjon a tárolási forgalom elkülönítéséhez Azure Stack HCI-ben.

Vegye figyelembe a következő példát egy négy csomópontos fürtre. Minden kiszolgáló két tárolási portot tartalmaz (a bal és a jobb oldalon). Mivel minden egyes adapter ugyanazon az alhálózaton és VLAN-on található, a többcsatornás SMB az összes elérhető hivatkozáson keresztül terjeszti a kapcsolatokat. Ezért az első kiszolgáló bal oldali portja (192.168.1.1) csatlakozik a bal oldali porthoz a második kiszolgálón (192.168.1.2). Az első kiszolgáló (192.168.1.12) jobb oldali portja a második kiszolgáló jobb oldali portjához fog csatlakozni. Hasonló kapcsolatok vannak kialakítva a harmadik és a negyedik kiszolgálókhoz.

Ez azonban szükségtelen kapcsolatokat hoz létre, és az összekapcsolás (több alvázos kapcsolati összesítési csoport vagy az MC-LAG) által a rack (ToR) kapcsolók összekapcsolásához (a (z) XS-vel jelölt) kapcsolókat eredményező torlódást okoz. Lásd a következő ábrát:

:::image type="content" source="media/plan-networking/four-node-cluster-1.png" alt-text="négy csomópontos fürt azonos alhálózata" lightbox="media/plan-networking/four-node-cluster-1.png":::

Az ajánlott módszer külön alhálózatok és VLAN-ok használata az egyes adapterekhez. A következő ábrán a jobb oldali portok mostantól a 192.168.2. x/24 alhálózatot és a VLAN2-t használják. Ez lehetővé teszi, hogy a bal oldali portok forgalma a TOR1 maradjon, és a jobb oldali portok forgalma a TOR2 maradjon. Lásd a következő ábrát:

:::image type="content" source="media/plan-networking/four-node-cluster-2.png" alt-text="négy csomópontos fürt eltérő alhálózatai" lightbox="media/plan-networking/four-node-cluster-2.png":::

## <a name="traffic-bandwidth-allocation"></a>Forgalmi sávszélesség kiosztása

Az alábbi táblázat a különböző adatforgalmi típusok (például a közös adapterek sebessége, Azure Stack a HCI) sávszélesség-elosztását mutatja be. Vegye figyelembe, hogy ez egy konvergens megoldás, amelyben az összes forgalmi típus (számítási, tárolási és felügyeleti) ugyanazon a fizikai adaptereken fut, és a készlet használatával van összefoglalva.

Mivel ez a használati eset a legtöbb korlátozást jelenti, jó alapkonfigurációt jelent. Az adapterek és a sebességek számának tekintetében azonban figyelembe kell venni egy példát, nem pedig a támogatási követelményt.

Ehhez a példához a következő feltételezések történnek:

- A csapatnak két adaptere van
- Storage Bus Layer (SBL), Fürt megosztott kötete (CSV) és Hyper-V (Élő áttelepítés) forgalom:

    - Azonos fizikai adapterek használata
    - SMB használata
- Az SMB 50%-os sávszélesség-kiosztást kap az adatközpont-áthidalás használatával
    - A SBL/CSV a legmagasabb prioritású forgalom, és az SMB-sávszélesség foglalásának 70%-át fogadja, valamint:
    - Élő áttelepítés (LM) korlátozott a parancsmag használatával `Set-SMBBandwidthLimit` , és a fennmaradó sávszélesség 29%-át kapja
        - Ha a rendelkezésre álló sávszélesség Élő áttelepítés >= 5 GB/s, és a hálózati adapterek képesek a RDMA használatára. Ehhez használja a következő parancsmagot:

            ```Powershell
            Set-VMHost VirtualMachineMigrationPerformanceOption SMB
            ```

        - Ha a rendelkezésre álló sávszélesség Élő áttelepítés 5 GB/s <, az áramszünetek csökkentése érdekében használjon tömörítést. Ehhez használja a következő parancsmagot:

            ```Powershell
            Set-VMHost -VirtualMachineMigrationPerformanceOption Compression
            ```

 - Ha a RDMA-et Élő áttelepítés használatával használja, ügyeljen arra, hogy Élő áttelepítés forgalom ne használja fel a RDMA forgalmi osztály számára lefoglalt teljes sávszélességet az SMB sávszélesség-korlátjának használatával. Ügyeljen arra, hogy a parancsmag másodpercenként bájt/másodperc (b/s) értéket vegyen fel, míg a hálózati adapterek a bit/másodperc (bps) értékben vannak felsorolva. A következő parancsmaggal állíthatja be a 6 GB/s sávszélesség-korlátot, például:

    ```Powershell
    Set-SMBBandwidthLimit -Category LiveMigration -BytesPerSecond 750MB
    ```

    > [!NOTE]
    >750 MBps ebben a példában 6 GB/s

Az alábbi példában a sávszélesség-foglalási táblázat látható:

|Hálózati adapter sebessége|Összevont sávszélesség|SMB-sávszélesség foglalása * *|SBL/CSV%|SBL/CSV-sávszélesség|Élő áttelepítés%|Maximális Élő áttelepítés sávszélesség|Szívverés|Szívverési sávszélesség|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10 Gbit/s|20 GB/s|10 Gbit/s|70%|7 GB/s|*|*|2%|200 Mbit/s|
|25 GB/s|50 GB/s|25 GB/s|70%|17,5 GB/s|29|7,25 GB/s|1%|250 Mbps|
|40 Gbps|80 GB/s|40 Gbps|70%|28 GB/s|29|11,6 GB/s|1%|400 Mbps|
|50 GB/s|100 Gbit/s|50 GB/s|70%|35 GB/s|29|14,5 GB/s|1%|500 Mbit/s|
|100 Gbit/s|200 Gbit/s|100 Gbit/s|70%|70 GB/s|29|29 GB/s|1%|1 Gbit/s|
|200 Gbit/s|400 GB/s|200 Gbit/s|70%|140 GB/s|29|58 GB/s|1%|2 Gbps|

* – a RDMA helyett tömörítést kell használnia, mivel Élő áttelepítés forgalom sávszélesség-kiosztása <5 GB/s

* *-50%-os sávszélesség-foglalás példa erre a példára

## <a name="stretched-cluster-considerations"></a>Kiterjesztett fürtök szempontjai

A kifeszített fürtök biztosítják a több adatközpontra kiterjedő vész-helyreállítási feladatsort. Legegyszerűbben a kifeszített Azure Stack HCI-fürt hálózata így néz ki:

:::image type="content" source="media/plan-networking/stretched-cluster.png" alt-text="Kifeszített fürt" lightbox="media/plan-networking/stretched-cluster.png":::

A kiterjesztett fürtök a következő követelményekkel és jellemzőkkel rendelkeznek:

- A RDMA egyetlen helyre korlátozódik, és nem támogatott a különböző helyeken vagy alhálózatokban.
- Az azonos helyen található kiszolgálóknak ugyanabban az állványban és 2. rétegbeli határban kell lenniük.
- Helyek közötti kommunikáció – 3. rétegbeli határ; a kifeszített Layer-2 topológiák nem támogatottak.

- Ha egy hely RDMA használ a tároló adapterekhez, ezeknek az adaptereknek külön alhálózaton és VLAN-on kell lenniük, amely nem tud útvonalakat átirányítani a helyek között. Ez megakadályozza, hogy a tárolási replika a RDMA-t használja a helyek között.
- A helyek közötti kommunikációhoz használt adapterek:

    - Fizikai vagy virtuális (Host vNIC) lehet. Ha virtuális, egy vNIC kell kiépítenie a saját alhálózatában és VLAN-ban fizikai hálózati adapteren.
    - A saját alhálózatán és VLAN-on kell lennie, amely képes a helyek közötti útválasztásra.
    - A RDMA le kell tiltani a `Disable-NetAdapterRDMA` parancsmag használatával. Javasoljuk, hogy a parancsmag használatával explicit módon megkövetelje a tárolási replika használatát adott felületek használatához `Set-SRNetworkConstraint` .
    - Meg kell felelnie a tárolási replika további követelményeinek.
-   Ha feladatátvételt hajt végre egy másik helyre, gondoskodnia kell arról, hogy elegendő sávszélesség álljon rendelkezésre a munkaterhelések futtatásához a másik helyen. A biztonságos megoldás az, ha a helyek a rendelkezésre álló kapacitásuk 50%-ában vannak kiépítve. Ez nem nehéz követelmény, ha a feladatátvétel során el tudja viselni az alacsonyabb teljesítményt.

## <a name="next-steps"></a>További lépések

- A hálózati kapcsolók és a fizikai hálózati követelmények megismerése. Lásd: [fizikai hálózati követelmények](physical-network-requirements.md).
- A feladatátvételi fürtszolgáltatás alapjai. Lásd: a [feladatátvételi fürtszolgáltatás hálózatkezelésének alapjai](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- Ecset a SET használatával. Lásd: [távoli közvetlen memória-hozzáférés (RDMA) és Switch Embedded Teaming (set)](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming)
- A telepítéssel kapcsolatban lásd: [fürt létrehozása a Windows felügyeleti központtal](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster)
- A telepítéssel kapcsolatban lásd: [fürt létrehozása a Windows PowerShell használatával](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell)
