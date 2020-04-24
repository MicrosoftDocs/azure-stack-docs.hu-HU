---
title: Hibatűrés és a tárolási hatékonyság a Azure Stack HCI-ben
description: Közvetlen tárolóhelyek rugalmassági lehetőségeinek megvitatása, beleértve a tükrözést és a paritást.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: 9ace3960b4c54461a4153c4997694e6d17ee4fd1
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "79025699"
---
# <a name="fault-tolerance-and-storage-efficiency-in-azure-stack-hci"></a>Hibatűrés és a tárolási hatékonyság a Azure Stack HCI-ben

>A következőkre vonatkozik: Windows Server 2019

Ez a témakör bemutatja a Közvetlen tárolóhelyek rendelkezésre álló rugalmassági lehetőségeket, és ismerteti a méretezési követelményeket, a tárolási hatékonyságot, valamint az egyes eszközök általános előnyeit és kompromisszumait. Emellett néhány használati útmutatót is tartalmaz a kezdéshez, és néhány nagyszerű dokumentumra, blogra és további tartalomra hivatkozik, ahol további információt talál.

Ha már ismeri a tárolóhelyeket, akkor érdemes lehet kihagyni az [Összefoglalás](#summary) szakaszt.

## <a name="overview"></a>Áttekintés

A tárolóhelyek feladata a hibatűrés biztosítása, amelyet gyakran "rugalmasságnak" nevezünk az adataihoz. A megvalósítás hasonló a RAID szolgáltatáshoz, kivéve a kiszolgálókon elosztott és a szoftverben megvalósított szoftverek között.

Ahogy a RAID esetében, a tárolóhelyek többféleképpen is megtehetik ezt, ami különböző kompromisszumokat tesz a hibatűrés, a tárolási hatékonyság és a számítási komplexitás között. Ezek nagyjából két kategóriába sorolhatók: "tükrözés" és "paritás", az utóbbi más néven "törlési kódolás".

## <a name="mirroring"></a>Tükrözés

A tükrözések hibatűrést biztosítanak, ha az összes adattal több példányt tart. Ez leginkább hasonlít a RAID-1-re. Az adatszalagos tárolás és az azokhoz tartozó információk nem triviálisak ( [ebben a blogban](https://blogs.technet.microsoft.com/filecab/2016/11/21/deep-dive-pool-in-spaces-direct/) további információkat talál), de feltétlenül igaz, hogy a tükrözést használó összes tárolt információ teljes egészében, többször is megírásra kerül. A rendszer minden egyes példányt különböző fizikai hardverre (különböző kiszolgálókon lévő különböző meghajtókra) ír, amelyeket feltételez, hogy egymástól függetlenül meghibásodik.

A tárolóhelyek a tükrözés két változatát kínálja – "kétirányú" és "háromutas".

### <a name="two-way-mirror"></a>Kétutas tükrözés

A kétirányú tükrözés mindenből két másolatot ír. A tárterület hatékonysága 50 százalék – 1 TB adat írásához legalább 2 TB fizikai tárolókapacitás szükséges. Ehhez hasonlóan legalább két [hardveres "tartalék tartományra"](/windows-server/failover-clustering/fault-domains) van szükség, közvetlen tárolóhelyek, és ez két kiszolgálót jelent.

![Kétirányú tükrözés](media/fault-tolerance/two-way-mirror-180px.png)

   >[!WARNING]
   > Ha kettőnél több kiszolgáló is van, javasoljuk, hogy használjon háromutas tükrözést.

### <a name="three-way-mirror"></a>Háromutas tükrözés

A háromutas tükrözés mindenből három másolatot ír. A tárterület hatékonysága 33,3 százalék – 1 TB adat írásához legalább 3 TB fizikai tárolókapacitás szükséges. Ehhez hasonlóan legalább három hardveres tartalék tartományra van szükség – Közvetlen tárolóhelyek, amely három kiszolgálót jelent.

A háromutas tükrözés képes biztonságosan elviselni legalább [két hardveres problémát (meghajtó vagy kiszolgáló)](#examples)egyszerre. Ha például egy kiszolgáló újraindításakor hirtelen egy másik meghajtó vagy kiszolgáló meghibásodik, az összes adat biztonságban és folyamatosan elérhető marad.

![háromutas tükrözés](media/fault-tolerance/three-way-mirror-180px.png)

## <a name="parity"></a>Parity

A paritásos kódolás, amelyet gyakran "törlési kódolásnak" neveznek, a bitenkénti aritmetika használatával biztosít hibatűrést, ami [rendkívül bonyolult](https://www.microsoft.com/research/wp-content/uploads/2016/02/LRC12-cheng20webpage.pdf)lehet. A működés kevésbé nyilvánvaló, mint a tükrözés, és számos nagyszerű online erőforrás létezik (például a harmadik féltől származó [, a kódolás törlésére vonatkozó útmutató](http://smahesh.com/blog/2012/07/01/dummies-guide-to-erasure-coding/)), amely segítséget nyújt az ötlet megszerzésében. Elég azt mondani, hogy jobb tárolási hatékonyságot biztosít anélkül, hogy a hibatűrést veszélyeztetné.

A tárolóhelyek két paritásos, "single" paritásos és "kettős" paritást biztosítanak, az utóbbi pedig a "helyi újraépítési kódok" nevű speciális technikát alkalmazza nagyobb léptékben.

> [!IMPORTANT]
> Javasoljuk, hogy a legtöbb teljesítményre érzékeny számítási feladathoz használjon tükrözést. Ha többet szeretne megtudni a teljesítmény és a kapacitás kiegyensúlyozásáról a munkaterhelés alapján, tekintse meg a [kötetek megtervezése](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type)című témakört.

### <a name="single-parity"></a>Egyetlen paritás
Az egyszeres paritás csak egy bitenkénti-paritásos szimbólumot tart fenn, amely egyszerre csak egy hibához biztosít hibatűrést. A legszorosabban hasonlít a RAID-5-re. Ha egyetlen paritást szeretne használni, legalább három hardveres tartalék tartományra van szüksége – Közvetlen tárolóhelyek, amely három kiszolgálót jelent. Mivel a háromutas tükrözések nagyobb hibatűrést biztosítanak ugyanazon a skálán, az egyetlen paritás használatának visszatartása. Itt azonban ott van, ha ragaszkodik a használathoz, és teljes mértékben támogatott.

   >[!WARNING]
   > Az egyetlen paritás használatának megakadályozása, mert az csak egyetlen hardverhiba esetén képes biztonságosan elviselni egyszerre: Ha egy kiszolgáló újraindításakor hirtelen egy másik meghajtó vagy kiszolgáló meghibásodik, a rendszer leállást tapasztal. Ha csak három kiszolgálóval rendelkezik, javasoljuk, hogy háromutas tükrözést használjon. Ha négy vagy több van, tekintse meg a következő szakaszt.

### <a name="dual-parity"></a>Kettős paritás

A kettős paritás a Reed-Salamon hibáját alkalmazza – a kódok kijavításával két bitenkénti paritásos szimbólumot biztosít, így ugyanazt a hibatűrést biztosítja, mint a háromutas tükrözés (akár két hiba egyszerre), de a jobb tárolási hatékonyság érdekében. Ez a leginkább hasonlít a RAID-6-ra. A kettős paritás használatához legalább négy hardveres tartalék tartományra van szükség – Közvetlen tárolóhelyek, amely négy kiszolgálót jelent. Ezen a skálán a tárterület hatékonysága 50% – 2 TB-nyi adat tárolásához 4 TB-os fizikai tárolókapacitásra van szükség.

![kettős paritású](media/fault-tolerance/dual-parity-180px.png)

A kettős paritás tárolási hatékonysága növeli a további hardveres tartalék tartományokat, 50%-kal, akár 80%-kal. Ha például hét (Közvetlen tárolóhelyek, azaz hét kiszolgáló), a hatékonyság 66,7 százalékra ugrik – 4 TB-nyi adat tárolására, mindössze 6 TB fizikai tárolókapacitásra van szüksége.

![kettős paritás – széles](media/fault-tolerance/dual-parity-wide-180px.png)

Tekintse meg az [Összefoglalás](#summary) szakaszt a kettős és a helyi újjáépítési kódok hatékonyságának növeléséhez minden méretben.

### <a name="local-reconstruction-codes"></a>Helyi újjáépítési kódok

A tárolóhelyek szolgáltatás a Microsoft Research által fejlesztett, "helyi újraépítési kódok" vagy a FORRÁSKÖZPONT által kifejlesztett fejlett technikát mutatja be. Nagy méretekben a kettős paritás a FORRÁSKÖZPONT használatával osztja fel a kódolást/dekódolást néhány kisebb csoportba, hogy csökkentse a hibákból való íráshoz vagy helyreállításhoz szükséges terhelést.

A merevlemez-meghajtók (HDD) esetében a csoport mérete négy szimbólum; a szilárdtest-meghajtók (SSD) esetében a csoport mérete hat szimbólum. Tegyük fel például, hogy az elrendezés úgy néz ki, mint a merevlemez-meghajtók és 12 hardverhiba-tartomány (azaz 12 kiszolgáló) – a négy adatszimbólum két csoportja létezik. Ez 72,7%-os tárolási hatékonyságot érhet el.

![helyi újjáépítés – kódok](media/fault-tolerance/local-reconstruction-codes-180px.png)

Azt javasoljuk, hogy ezt a részletesen olvasható [, a helyi újjáépítési kódok különböző meghibásodási forgatókönyveket kezelő, és miért vonzó](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/), a saját [Claus Joergensen](https://twitter.com/clausjor).

## <a name="mirror-accelerated-parity"></a>Tükrözött, gyorsított paritás

Egy Közvetlen tárolóhelyek kötet lehet a Mirror és a part paritás. Először a tükrözött részbe írja a földet, és később fokozatosan áthelyezi a paritási részbe. Ennek hatására a [tükrözés a törlési kódolás felgyorsítására használatos](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/).

A háromutas tükrözés és a kettős paritás együttes létrehozásához legalább négy tartalék tartományra van szükség, amely négy kiszolgálót jelent.

A tükrözött felgyorsított paritás tárolási hatékonysága az összes tükrözés vagy az összes paritás használata esetén, és a választott aránytól függ. A bemutató 37 perces jelzése például különböző mixekből áll, amelyek a [46 százalékát, a 54 százalékát és a 65 százalékos hatékonyságot](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s) teszik elérhetővé 12 kiszolgálóval.

> [!IMPORTANT]
> Javasoljuk, hogy a legtöbb teljesítményre érzékeny számítási feladathoz használjon tükrözést. Ha többet szeretne megtudni a teljesítmény és a kapacitás kiegyensúlyozásáról a munkaterhelés alapján, tekintse meg a [kötetek megtervezése](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type)című témakört.

## <a name="summary"></a><a name="summary"></a>Összefoglalás

Ez a szakasz összefoglalja a Közvetlen tárolóhelyek rendelkezésre álló rugalmassági típusokat, az egyes típusok használatának minimális méretezési követelményeit, az egyes típusokhoz tartozó hibák számát, valamint a megfelelő tárolási hatékonyságot.

### <a name="resiliency-types"></a>Rugalmassági típusok

|    Rugalmasság          |    Meghibásodási tolerancia       |    Tárolási hatékonyság      |
|------------------------|----------------------------|----------------------------|
|    Kétutas tükrözés      |    1                       |    50,0%                   |
|    Háromutas tükrözés    |    2                       |    33,3%                   |
|    Kettős paritás         |    2                       |    50,0% – 80,0%           |
|    Vegyes               |    2                       |    33,3% – 80,0%           |

### <a name="minimum-scale-requirements"></a>Minimális skálázási követelmények

|    Rugalmasság          |    Minimálisan szükséges tartalék tartományok   |
|------------------------|-------------------------------------|
|    Kétutas tükrözés      |    2                                |
|    Háromutas tükrözés    |    3                                |
|    Kettős paritás         |    4                                |
|    Vegyes               |    4                                |

   >[!TIP]
   > Hacsak nem használ [váz-vagy rack](/windows-server/failover-clustering/fault-domains)-hibatűrést, a tartalék tartományok száma a kiszolgálók számára utal. Az egyes kiszolgálókon található meghajtók száma nem befolyásolja, hogy mely rugalmassági típusok használhatók, ha megfelel a Közvetlen tárolóhelyek minimális követelményeinek.

### <a name="dual-parity-efficiency-for-hybrid-deployments"></a>Kettős paritású hatékonyság a hibrid üzembe helyezésekhez

Ez a táblázat a kettős paritású és a helyi újjáépítési kódok tárolási hatékonyságát mutatja a hibrid telepítések esetében, amelyek merevlemez-meghajtókat (HDD) és SSD-meghajtókat egyaránt tartalmaznak.

|    Tartalék tartományok      |    Elrendezés           |    Hatékonyság   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2 + 2           |    50,0%        |
|    5                  |    RS 2 + 2           |    50,0%        |
|    6                  |    RS 2 + 2           |    50,0%        |
|    7                  |    RS 4 + 2           |    66,7%        |
|    8                  |    RS 4 + 2           |    66,7%        |
|    9                  |    RS 4 + 2           |    66,7%        |
|    10                 |    RS 4 + 2           |    66,7%        |
|    11                 |    RS 4 + 2           |    66,7%        |
|    12                 |    FORRÁSKÖZPONT (8, 2, 1)    |    72,7%        |
|    13                 |    FORRÁSKÖZPONT (8, 2, 1)    |    72,7%        |
|    14                 |    FORRÁSKÖZPONT (8, 2, 1)    |    72,7%        |
|    15                 |    FORRÁSKÖZPONT (8, 2, 1)    |    72,7%        |
|    16                 |    FORRÁSKÖZPONT (8, 2, 1)    |    72,7%        |

### <a name="dual-parity-efficiency-for-all-flash-deployments"></a>Kettős paritású hatékonyság az összes Flash-telepítéshez

Ez a táblázat a kettős paritású és helyi újraépítési kódok tárolási hatékonyságát mutatja minden olyan Flash-telepítés esetén, amely kizárólag SSD-meghajtókat tartalmaz. A paritásos elrendezés nagyobb méretű csoportokat használhat, és jobb tárolási hatékonyságot érhet el egy teljes Flash konfigurációban.

|    Tartalék tartományok      |    Elrendezés           |    Hatékonyság   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2 + 2           |    50,0%        |
|    5                  |    RS 2 + 2           |    50,0%        |
|    6                  |    RS 2 + 2           |    50,0%        |
|    7                  |    RS 4 + 2           |    66,7%        |
|    8                  |    RS 4 + 2           |    66,7%        |
|    9                  |    RS 6 + 2           |    75,0%        |
|    10                 |    RS 6 + 2           |    75,0%        |
|    11                 |    RS 6 + 2           |    75,0%        |
|    12                 |    RS 6 + 2           |    75,0%        |
|    13                 |    RS 6 + 2           |    75,0%        |
|    14                 |    RS 6 + 2           |    75,0%        |
|    15                 |    RS 6 + 2           |    75,0%        |
|    16                 |    FORRÁSKÖZPONT (12, 2, 1)   |    80,0%        |

## <a name="examples"></a><a name="examples"></a>Példák

Ha csak két kiszolgálóval rendelkezik, javasoljuk, hogy használjon háromutas tükrözést és/vagy kettős paritást, mert jobb hibatűrést biztosítanak. Pontosabban gondoskodnak arról, hogy az összes adatbázis biztonságban maradjon, és folyamatosan elérhető legyen, még akkor is, ha két tartalék tartomány – Közvetlen tárolóhelyek esetén – két kiszolgálót jelent – egyidejű hibák érintik.

### <a name="examples-where-everything-stays-online"></a>Például minden online állapotban marad

Ez a hat példa azt mutatja be, hogy milyen háromutas tükrözést és/vagy kettős paritást **tud** viselni.

- **1.** egy meghajtó elveszett (beleértve a gyorsítótár-meghajtókat is)
- **2.** egy kiszolgáló elveszett

![hibatűrés – példák-1 és-2](media/fault-tolerance/Fault-Tolerance-Example-12.png)

- **3.** egy kiszolgáló és egy meghajtó elveszett
- **4.** két meghajtó elveszett különböző kiszolgálókon

![hibatűrési példák – 3 és 4 közötti](media/fault-tolerance/Fault-Tolerance-Example-34.png)

- **5.** a kettőnél több meghajtó is elveszett, amíg a legtöbb esetben a két kiszolgáló érintett
- **6.** két kiszolgáló elveszett

![hibatűrés – példák-5 és-6](media/fault-tolerance/Fault-Tolerance-Example-56.png)

... minden esetben a kötetek online maradnak. (Győződjön meg arról, hogy a fürt fenntartja a kvórumot.)

### <a name="examples-where-everything-goes-offline"></a>Például, hogy minden offline állapotú

Élettartama alatt a tárolóhelyek tetszőleges számú meghibásodást okozhatnak, mivel a teljes rugalmasságot visszaállítja a megfelelő idő elteltével. Azonban a legtöbb esetben a hibák egy adott pillanatban is biztonságosan befolyásolhatják a hibákat. A következőkben néhány példa arra, hogy milyen háromutas tükrözést és/vagy kettős paritást **nem lehet** tolerálni.

- **7.** a három vagy több kiszolgálón elveszett meghajtók egyszerre
- **8.** három vagy több kiszolgáló elvész egyszerre

![hibatűrés – példák – 7 és 8](media/fault-tolerance/Fault-Tolerance-Example-78.png)

## <a name="usage"></a>Használat

Tekintse meg [a kötetek létrehozását a közvetlen tárolóhelyekban](/windows-server/storage/storage-spaces/create-volumes).

## <a name="next-steps"></a>További lépések

A cikkben említett témakörökkel kapcsolatos további információkért tekintse meg a következőket:

- [Kódolások törlése az Azure-ban a Microsoft Research használatával](https://www.microsoft.com/research/publication/erasure-coding-in-windows-azure-storage/)
- [Helyi újjáépítési kódok és felgyorsuló paritású kötetek](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)
- [Kötetek a Storage Management API-ban](https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/)
- [Storage hatékonysági bemutató a Microsoft Ignite 2016-ban](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)
- [A kapacitás-kalkulátor előzetes verziója Közvetlen tárolóhelyek](https://aka.ms/s2dcalc)
