---
title: A fürt és a készlet Kvórumának megismerése – Azure Stack HCI
description: A fürt és a készlet Kvórumának megértése Azure Stack HCI-ben Közvetlen tárolóhelyekban, konkrét példákkal a bonyolult feladatra.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: d60ec2edb4247c72d35e69e199bf3fc28259e2ce
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572123"
---
# <a name="understanding-cluster-and-pool-quorum-on-azure-stack-hci"></a>A fürt és a készlet Kvórumának megértése Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A [Windows Server feladatátvételi fürtszolgáltatás](/windows-server/failover-clustering/failover-clustering-overview) magas rendelkezésre állást biztosít a munkaterhelések számára. Ezek az erőforrások akkor tekinthetők nagyon elérhetőnek, ha az erőforrásokat futtató csomópontok vannak. a fürthöz azonban általában több mint felet kell futtatni, ami a *kvórumnak*is ismert.

A kvórum úgy van kialakítva, hogy megakadályozza a *megosztott agyi* forgatókönyveket, amelyek akkor fordulnak elő, ha a hálózatban van partíció, és a csomópontok részhalmaza nem tud kommunikálni egymással. Ennek hatására a csomópontok mindkét részhalmaza kipróbálhatja a számítási feladatokat, és ugyanarra a lemezre írható, amely számos problémát eredményezhet. Ez azonban megakadályozza a feladatátvételi fürtszolgáltatás Kvórumának fogalmát, amely a csomópontok közül csak az egyiket kényszeríti a működés folytatására, így csak az egyik csoport fog online maradni.

A kvórum meghatározza, hogy a fürt hány hibát tart fenn, miközben továbbra is online állapotban marad. A kvórum úgy van kialakítva, hogy a fürtcsomópontok részhalmazai közötti kommunikáció során probléma legyen a forgatókönyvek kezelése során, így több kiszolgáló nem próbálkozik egyszerre az erőforráscsoport futtatásával, és ugyanazon a lemezre való írásával. A kvórum fogalma alapján a fürt arra kényszeríti a fürtszolgáltatást, hogy a csomópontok egyik részhalmazában álljon le, így biztosítva, hogy egy adott erőforráscsoport csak egy valódi tulajdonosa legyen. Ha a leállított csomópontok újra kommunikálhatnak a csomópontok fő csoportjához, a automatikusan újracsatlakozik a fürthöz, és elindítja a Fürtszolgáltatást.

A Windows Server 2019-ben a rendszer két összetevőt tartalmaz a saját kvórum mechanizmusaival:

- **Fürt kvóruma**: Ez a fürt szintjén működik (azaz elveszítheti a csomópontokat, és megtarthatja a fürtöt)
- **Készlet kvóruma**: Ez a készlet szintjén működik, ha a közvetlen tárolóhelyek engedélyezve van (azaz elveszítheti a csomópontokat és a meghajtókat, és megtarthatja a készletet). A Storage-készletek fürtözött és nem fürtözött helyzetekben is használhatók, ezért eltérő kvórum mechanizmussal rendelkeznek.

## <a name="cluster-quorum-overview"></a>A fürt Kvórumának áttekintése

Az alábbi táblázat áttekintést nyújt a fürt Kvórumának eredményeiről forgatókönyv szerint:

| Kiszolgálói csomópontok | Egy kiszolgáló-csomópont meghibásodása esetén is megmaradhat | Képes túlélni egy kiszolgáló-csomópont hibáját, majd egy másikat | Képes túlélni két egyidejű kiszolgáló csomópont hibáját |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | 50/50                               | Nem                                                | Nem                                                 |
| 2 + tanú  | Igen                                 | Nem                                                | Nem                                                 |
| 3            | Yes                                 | 50/50                                             | No                                                 |
| 3 + tanúsító  | Igen                                 | Igen                                               | Nem                                                 |
| 4            | Igen                                 | Igen                                               | 50/50                                              |
| 4 + tanúsító  | Igen                                 | Igen                                               | Igen                                                |
| 5 és újabb  | Igen                                 | Igen                                               | Igen                                                |

### <a name="cluster-quorum-recommendations"></a>Fürt kvórumával kapcsolatos javaslatok

- Ha két csomóponttal rendelkezik, a tanúsító **megadása kötelező**.
- Ha három vagy négy csomóponttal rendelkezik, **erősen ajánlott**a tanúsító.
- Ha van internet-hozzáférése, használjon ** [Felhőbeli tanúsító](/windows-server/failover-clustering/deploy-cloud-witness)**
- Ha más gépekkel és fájlmegosztással rendelkező informatikai környezetben van, használjon tanúsító fájlmegosztást

## <a name="how-cluster-quorum-works"></a>A fürt Kvórumának működése

Ha a csomópontok meghibásodnak, vagy ha a csomópontok egy részhalmaza elveszti a kapcsolatot egy másik részhalmazsal, a túlélő csomópontoknak ellenőriznie kell, hogy a fürt *többsége* marad-e online állapotban. Ha nem tudják ellenőrizni, hogy offline állapotba lépnek-e.

A *többségi* koncepció azonban csak akkor működik, ha a fürt csomópontjainak teljes száma páratlan (például három csomópont egy öt csomópontos fürtben). Tehát mi a helyzet a fürtökkel a páros számú csomóponttal (azaz egy négy csomópontos fürttel)?

A fürt két módon teheti meg a páratlan *szavazatok teljes számát* :

1. Először is felteheti *az egyiket* , ha egy további szavazással felvesz egy *tanúsító* . Ehhez felhasználói beállításra van szükség.
2. Az is előfordulhat, hogy *az egyik a* nem szerencsés csomópontok szavazásának nullázása (ha szükséges, automatikusan történik).

Amikor a túlélő csomópontok sikeresen ellenőrzik a *többséget*, a *többség* definíciója frissül, hogy csak a túlélők közé tartozik. Ez lehetővé teszi, hogy a fürt elveszítse az egyik csomópontot, majd egy másikat, majd egy másikat és így tovább. Az egymást követő meghibásodások utáni *szavazatok teljes számát* a ***dinamikus kvórumnak***nevezzük.

### <a name="dynamic-witness"></a>Dinamikus tanúsító

A dinamikus tanúsító tanú bekapcsolja a tanúsító szavazatát, hogy megbizonyosodjon róla, hogy a *szavazatok teljes száma* páratlan. Ha páratlan számú szavazattal rendelkezik, a tanúsító nem rendelkezik szavazattal. Ha páros számú szavazat van, a tanúsító szavazattal rendelkezik. A dinamikus tanúsító jelentős mértékben csökkenti annak a kockázatát, hogy a fürt a tanú meghibásodása miatt leáll. A fürt eldönti, hogy a tanúsító szavazatot a fürtben elérhető szavazati csomópontok száma alapján használja-e.

A dinamikus kvórum dinamikus tanúsító szerkezettel működik az alábbiakban ismertetett módon.

### <a name="dynamic-quorum-behavior"></a>Dinamikus kvórum viselkedése

- Ha **páros** számú csomóponttal rendelkezik, és nincs tanúsító, *az egyik csomópontot a szavazatok lenullázása után kapja*meg. Például a négy csomópont közül csak három kap szavazatot, így a *szavazatok teljes száma* három, a szavazatok két túlélő pedig többségnek számít.
- Ha **páratlan** számú csomóponttal rendelkezik, és nincs tanúsító, *akkor minden szavazatot kap*.
- Ha páros számú csomópont és tanúsító **is** van, akkor *a tanú szavaz*, így a teljes összeg páratlan.
- Ha **páratlan** számú csomóponttal és tanúval rendelkezik, *a tanúsító nem szavaz*.

A dinamikus kvórum lehetővé teszi, hogy egy szavazatot dinamikusan rendeljen egy csomóponthoz, hogy elkerülje a szavazatok többségét, és lehetővé tegye, hogy a fürt egyetlen csomóponttal fusson (az utolsó ember állóként ismert). Vegyük példaként egy négy csomópontos fürtöt. Tegyük fel, hogy a kvórum 3 szavazatot igényel.

Ebben az esetben a fürt leállt, ha két csomópontot vesztett el.

![Az ábrán négy fürtcsomópont látható, amelyek mindegyike szavazást kap](media/quorum/dynamic-quorum-base.png)

A dinamikus kvórum azonban megakadályozza, hogy ez megtörténjen. A kvórumhoz szükséges *szavazatok teljes száma* az elérhető csomópontok száma alapján van meghatározva. Így a dinamikus kvórum esetén a fürt akkor is marad, ha három csomópontot veszít.

![Az ábrán négy fürtcsomópont látható, ahol a csomópontok egy időben meghibásodnak, és az egyes hibák után a szükséges szavazatok száma módosítva.](media/quorum/dynamic-quorum-step-through.png)

A fenti forgatókönyv olyan általános fürtre vonatkozik, amelyen nincs Közvetlen tárolóhelyek engedélyezve. Ha azonban Közvetlen tárolóhelyek van engedélyezve, a fürt csak két csomópontos hibát támogat. Ezt részletesebben a [készlet kvóruma szakaszban találja](#pool-quorum-overview).

### <a name="examples"></a>Példák

#### <a name="two-nodes-without-a-witness"></a>Két csomópont tanúsító nélkül.
Az egyik csomópont szavazata nulla, így a *többségi* szavazás összesen **1 szavazattal**van meghatározva. Ha a nem szavazó csomópont váratlanul leáll, a túlélő a 1/1-as, a fürt pedig életben marad. Ha a szavazati csomópont váratlanul leáll, a túlélő a 0/1-as és a fürt leáll. Ha a szavazati csomópontot szabályosan leállítja, a rendszer átviszi a szavazást a másik csomópontra, a fürt pedig életben marad. ***Ezért fontos a tanúsító konfigurálása.***

![A határozatképesség magyarázata két, tanúsító nélküli csomópont esetében](media/quorum/2-node-no-witness.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **50%**-os eséllyel.
- Egyetlen kiszolgálóhiba esetén is fennmaradhat, majd egy másik: **nem**.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **nem**.

#### <a name="two-nodes-with-a-witness"></a>Két csomópont tanúsító.
Mindkét csomópont szavaz, és a tanú szavazata is van, így a *többséget* összesen **3 szavazattal**határozzuk meg. Ha bármelyik csomópont leáll, a túlélő a 2/3-as, a fürt pedig életben marad.

![A határozatképesség magyarázata az esetben két, tanúsító csomóponttal](media/quorum/2-node-witness.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egyetlen kiszolgálóhiba esetén is fennmaradhat, majd egy másik: **nem**.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **nem**.

#### <a name="three-nodes-without-a-witness"></a>Három csomópont tanúsító nélkül.
Minden csomópont szavaz, így a *többséget* összesen **3 szavazattal**határozzuk meg. Ha bármelyik csomópont leáll, a túlélők 2/3, a fürt pedig életben marad. A fürt két, tanúsító nélküli csomópont lesz – ezen a ponton az 1. forgatókönyvben van.

![A kvórum magyarázata három, tanúsító csomópont nélkül](media/quorum/3-node-no-witness.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egy kiszolgáló meghibásodása esetén is fennmaradhat, majd egy másik: **50%**-os eséllyel.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **nem**.

#### <a name="three-nodes-with-a-witness"></a>Három csomópont tanúsító.
Minden csomópont szavaz, így a tanúsító kezdetben nem szavaz. A *többség* megállapítása összesen **3 szavazattal**történik. Egy hiba után a fürt két, tanúsító csomóponttal rendelkezik, amely visszakerül a 2. forgatókönyvbe. Tehát most a két csomópont és a tanú szavaz.

![A határozatképesség ismertetése három, tanúsító csomóponttal](media/quorum/3-node-witness.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egy kiszolgáló meghibásodása esetén is fennmaradhat, majd egy másik: **Igen**.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **nem**.

#### <a name="four-nodes-without-a-witness"></a>Négy csomópont tanúsító nélkül
Az egyik csomópont szavazata nulla, így a *többséget* összesen **3 szavazattal**határozzuk meg. Egy hiba után a fürt három csomópontba kerül, és a 3. forgatókönyvben van.

![A határozatképesség ismertetése négy, tanúsító nélküli csomópont esetében](media/quorum/4-node-no-witness.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egy kiszolgáló meghibásodása esetén is fennmaradhat, majd egy másik: **Igen**.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **50%**-os eséllyel.

#### <a name="four-nodes-with-a-witness"></a>Négy csomópont tanúsító.
Az összes csomópont és a tanú szavazata, így a *többséget* összesen **5 szavazat**határozza meg. Egy hiba után a 4. forgatókönyvben van. Két egyidejű hiba után ugorjon le a 2. forgatókönyvre.

![A határozatképesség ismertetése négy, tanúsító csomóponttal](media/quorum/4-node-witness.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egy kiszolgáló meghibásodása esetén is fennmaradhat, majd egy másik: **Igen**.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **Igen**.

#### <a name="five-nodes-and-beyond"></a>Öt csomópont és azon kívül.
Minden csomópont szavaz, vagy csak egy szavazatot, bármi is legyen az egész páratlan. Közvetlen tárolóhelyek a kettőnél több csomópontot nem tud kezelni, ezért ezen a ponton nincs szükség tanúsító vagy hasznos elemre.

![A kvórum magyarázata az esetben öt csomóponttal és azon túl](media/quorum/5-nodes.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egy kiszolgáló meghibásodása esetén is fennmaradhat, majd egy másik: **Igen**.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **Igen**.

Most, hogy megértettük, hogyan működik a kvórum, tekintsük át a kvórum tanúinak típusait.

### <a name="quorum-witness-types"></a>Kvórum tanúsító típusai

A feladatátvételi fürtszolgáltatás háromféle típusú kvórumot támogat:

- **[Felhőbeli tanú](/windows-server/failover-clustering/deploy-cloud-witness)** – a blob Storage az Azure-ban a fürt összes csomópontja számára elérhető. Egy tanúsító. log fájlban tárolja a fürtszolgáltatási információkat, de nem tárolja a fürt adatbázisának másolatát.
- **Tanúsító fájlmegosztás** – olyan SMB-fájlmegosztás, amely Windows Server rendszert futtató fájlkiszolgálón van konfigurálva. Egy tanúsító. log fájlban tárolja a fürtszolgáltatási információkat, de nem tárolja a fürt adatbázisának másolatát.
- **Tanúsító lemez** – egy kisméretű fürtözött lemez, amely a fürt rendelkezésre álló tárolási csoportjában található. Ez a lemez nagyon elérhető, és képes a csomópontok közötti feladatátvételre. A fürt adatbázisának másolatát tartalmazza.  ***A tanúsító lemez közvetlen tárolóhelyek esetén nem támogatott***.

## <a name="pool-quorum-overview"></a>Pool kvórum – áttekintés

Épp most beszéltünk a fürt Kvórumáról, amely a fürt szintjén működik. Most tekintsük át a Pool-kvórumot, amely a készlet szintjén működik (azaz elveszítheti a csomópontokat és a meghajtókat, és megmarad a készlet). A Storage-készletek fürtözött és nem fürtözött helyzetekben is használhatók, ezért eltérő kvórum mechanizmussal rendelkeznek.

Az alábbi táblázat áttekintést nyújt a készlet Kvórumának eredményeiről:

| Kiszolgálói csomópontok | Egy kiszolgáló-csomópont meghibásodása esetén is megmaradhat | Képes túlélni egy kiszolgáló-csomópont hibáját, majd egy másikat | Képes túlélni két egyidejű kiszolgáló csomópont hibáját |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | Nem                                  | Nem                                                | Nem                                                 |
| 2 + tanú  | Igen                                 | Nem                                                | Nem                                                 |
| 3            | Igen                                 | Nem                                                | Nem                                                 |
| 3 + tanúsító  | Igen                                 | Nem                                                | Nem                                                 |
| 4            | Igen                                 | Nem                                                | Nem                                                 |
| 4 + tanúsító  | Igen                                 | Igen                                               | Igen                                                |
| 5 és újabb  | Igen                                 | Igen                                               | Igen                                                |

## <a name="how-pool-quorum-works"></a>A készlet Kvórumának működése

Ha a meghajtók meghibásodnak, vagy ha a meghajtók egyes részhalmazai elvesztik a kapcsolatot egy másik alkészlettel, a túlélő meghajtóknak ellenőriznie kell, hogy a készlet *többsége* az interneten marad-e. Ha nem tudják ellenőrizni, hogy offline állapotba lépnek-e. A készlet az az entitás, amely offline állapotba kerül, vagy online állapotban marad attól függően, hogy elegendő lemez van-e a kvórumhoz (50% + 1). A készlet erőforrás-tulajdonosa (aktív fürt csomópontja) lehet a + 1.

A készlet kvóruma azonban eltérően működik a fürt kvórumán, a következő módokon:

- a készlet a fürt egyik csomópontját használja tanúsító, hogy az elmaradt meghajtók fele túlélje (ez a csomópont a készlet erőforrás-tulajdonosa).
- a készlet nem rendelkezik dinamikus kvórumtal
- a készlet nem implementálja a szavazatok eltávolításának saját verzióját

### <a name="examples"></a>Példák

#### <a name="four-nodes-with-a-symmetrical-layout"></a>Négy csomópont szimmetrikus elrendezéssel.
A 16 meghajtó mindegyike egy szavazattal rendelkezik, a két csomópont pedig egy szavazattal is rendelkezik (mivel ez a készlet erőforrás-tulajdonos). A *többséget* összesen **16 szavazat**határozza meg. Ha a három és négy csomópont leáll, a túlélő részhalmaz 8 meghajtóval és a készlet erőforrás-tulajdonosával rendelkezik, amely 9/16 szavazat. Így a készlet életben marad.

![Készlet kvóruma 1](media/quorum/pool-1.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egy kiszolgáló meghibásodása esetén is fennmaradhat, majd egy másik: **Igen**.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **Igen**.

#### <a name="four-nodes-with-a-symmetrical-layout-and-drive-failure"></a>Négy csomópont szimmetrikus elrendezéssel és meghajtó meghibásodásával.
A 16 meghajtó mindegyike egy szavazattal rendelkezik, a 2. csomópont pedig egy szavazattal is rendelkezik (mivel ez a készlet erőforrás-tulajdonosa). A *többséget* összesen **16 szavazat**határozza meg. Először is, a 7. meghajtó leáll. Ha a három és négy csomópont leáll, a túlélő részhalmaz 7 meghajtóval és a készlet erőforrás-tulajdonosával rendelkezik, amely 8/16 szavazat. Így a készlet nem rendelkezik a többséggel, és leáll.

![Készlet 2. kvóruma](media/quorum/pool-2.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egyetlen kiszolgálóhiba esetén is fennmaradhat, majd egy másik: **nem**.
- Egyszerre két kiszolgáló meghibásodását képes túlélni: **nem**.

#### <a name="four-nodes-with-a-non-symmetrical-layout"></a>Négy csomópont nem szimmetrikus elrendezéssel.
A 24 meghajtó mindegyike egy szavazattal rendelkezik, a két csomópont pedig egy szavazattal is rendelkezik (mivel ez a készlet erőforrás-tulajdonos). A *többséget* összesen **24 szavazattal**határozzuk meg. Ha a három és négy csomópont leáll, a túlélő részhalmaz 8 meghajtóval és a készlet erőforrás-tulajdonosával rendelkezik, amely 9/24 szavazat. Így a készlet nem rendelkezik a többséggel, és leáll.

![Pool-kvórum 3](media/quorum/pool-3.png)

- Egyetlen kiszolgálóhiba esetén is fennmaradhat: **Igen**.
- Egy kiszolgáló meghibásodása esetén is megmaradhat, majd egy másik: * * a (z) * * függvénytől függ (nem tud fennmaradni, ha a három és a négy csomópont is leáll, de az összes többi esetben képes túlélni
- Egyszerre két kiszolgáló meghibásodását képes túlélni: * * a (z) * * függvénytől függ (nem lehet túlélni, ha a három és négy csomópont is leáll, de az összes többi esetben képes túlélni.

### <a name="pool-quorum-recommendations"></a>Pool kvórum javaslatai

- Győződjön meg arról, hogy a fürt minden csomópontja szimmetrikus (mindegyik csomópont azonos számú meghajtóval rendelkezik)
- Engedélyezze a háromutas tükrözést vagy a kettős paritást, hogy el tudja viselni a csomópontok hibáit, és a virtuális lemezeket online állapotban tartsa.
- Ha a kettőnél több csomópont van, vagy két csomópont, és egy másik csomóponton lévő lemez nem érhető el, előfordulhat, hogy a kötetek nem férnek hozzá az összes adat mindhárom példányához, ezért offline állapotba kerülhetnek, és nem érhetők el. Javasoljuk, hogy a-kiszolgálókat a köteten lévő összes adatmennyiség rugalmasságának biztosítása érdekében a lehető leggyorsabban hozza vissza vagy cserélje le a lemezeket.

## <a name="next-steps"></a>Következő lépések

További információkat a következő cikkekben talál:

- [Kvórum konfigurálása és kezelése](/windows-server/failover-clustering/manage-cluster-quorum)
- [Tanúsító fürt beállítása](../deploy/witness.md)
