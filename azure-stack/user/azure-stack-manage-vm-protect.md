---
title: Azure Stack hub-on üzembe helyezett virtuális gépek elleni védelem
description: Megtudhatja, hogyan hozhat létre helyreállítási tervet az Azure Stack hub-on üzembe helyezett virtuális gépek adatvesztéssel és nem tervezett állásidővel szembeni védelme érdekében.
author: mattbriggs
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/5/2020
ms.openlocfilehash: b5618fb39e9fc24fc8a086c99a7e1dd71f6b7a65
ms.sourcegitcommit: b185ab34c4c799892948536dd6d1d1b2fc31174e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150229"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Azure Stack hub-on üzembe helyezett virtuális gépek elleni védelem

Ez a cikk útmutatóként szolgál a Azure Stack központon üzembe helyezett, felhasználó által telepített IaaS-alapú virtuális gépek (VM-EK) adatvédelmi és vész-helyreállítási stratégiájának fejlesztéséhez.

Az adatvesztés és a kibővített állásidő elleni védelem érdekében hozzon létre egy biztonsági mentési vagy vész-helyreállítási tervet a felhasználói alkalmazásokhoz és azok adataihoz. Minden alkalmazást a szervezete átfogó üzletmenet-folytonossági és vész-helyreállítási (BC/DR) stratégiájának részeként kell kiértékelni. Jó kiindulási pont [Azure stack hub: az üzletmenet folytonosságával és a vész-helyreállítással kapcsolatos megfontolások](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="considerations-for-protecting-iaas-vms"></a>A IaaS-alapú virtuális gépek védelmének szempontjai

### <a name="roles-and-responsibilities"></a>Szerepkörök és felelősségek

Először is győződjön meg arról, hogy az alkalmazás tulajdonosai és a kezelői számára a védelem és a helyreállítás kontextusában világosan megértette a szerepköröket és a felelősségi köröket.

A felhasználók felelősek a virtuális gépek védelméért. A kezelők felelősek a Azure Stack hub online és kifogástalan állapotának megőrzésében. Az Azure Stack hub olyan szolgáltatást tartalmaz, amely a belső szolgáltatási adatok biztonsági mentését végzi az infrastruktúra-szolgáltatásokból, és **nem** tartalmaz felhasználói adatok, beleértve a felhasználó által létrehozott virtuális gépeket, a felhasználói vagy alkalmazási adatokkal rendelkező felhasználói fiókokat vagy felhasználói adatbázisokat.


| Alkalmazás tulajdonosa/építész   | Azure Stack hub operátor  |
|---    |---    |
| <ul><li>Az alkalmazások architektúrájának igazítása a Felhőbeli tervezési alapelvekkel.</li></br><li>Szükség szerint modernizálhatja a hagyományos alkalmazásokat a felhőalapú környezet előkészítéséhez.</li></br><li>Adja meg az alkalmazás elfogadható RTO és RPO.</li></br><li>Azonosítsa azokat az alkalmazás-erőforrásokat és adattárakat, amelyeket védeni kell.</li></br><li>Megvalósíthat egy olyan adat-és alkalmazás-helyreállítási módszert, amely a legjobban megfelel az alkalmazás architektúrájának és az ügyfél követelményeinek.</li></ul>     | <ul><li>Azonosítsa a szervezet üzletmenet-folytonossági és vész-helyreállítási céljait.</li></br><li>Helyezzen üzembe elég Azure Stack hub-példányokat a szervezet BC/DR céljainak kielégítése érdekében.</li></br><li>Az alkalmazás-és adatvédelmi infrastruktúra megtervezése és működtetése.</li></br><li>Felügyelt megoldásokat vagy önkiszolgáló hozzáférést biztosíthat a védelmi szolgáltatásokhoz.</li></br><li>Az alkalmazások tulajdonosai és építészei együttműködve megismerhetik az alkalmazások kialakítását, és javaslatot tesznek a védelmi stratégiákra.</li></br><li>Az infrastruktúra biztonsági mentésének engedélyezése a szolgáltatások gyógyítására és a felhőbe történő helyreállításra.</li></ul>    |

## <a name="sourcetarget-combinations"></a>Forrás/cél kombinációk

Az adatközpontok vagy a helyek meghibásodása elleni védelemmel ellátott felhasználók egy másik Azure Stack hub vagy az Azure használatával magas rendelkezésre állást és gyors helyreállítást is biztosíthatnak. Az elsődleges és a másodlagos helyen a felhasználók egy aktív/aktív vagy aktív/passzív konfigurációban telepíthetik az alkalmazásokat két környezet között. A kevésbé fontos számítási feladatokhoz a felhasználók a másodlagos helyen lévő kapacitást használhatják a biztonsági mentésből származó alkalmazások igény szerinti visszaállításához.

Egy vagy több Azure Stack hub-felhő üzembe helyezhető egy adatközpontban. A katasztrofális katasztrófák megtúlélése érdekében legalább egy Azure Stack hub-felhő üzembe helyezése egy másik adatközpontban biztosítja, hogy feladatátvételi feladatokat hajtson végre, és minimálisra csökkentse a nem tervezett állásidőt. Ha csak egy Azure Stack hub van, érdemes lehet az Azure nyilvános felhőt használni a helyreállítási felhőben. A kormányzati rendeletek, a vállalati házirendek és a szigorú késési követelmények határozzák meg, hogy az alkalmazás hogyan futtatható. Az alkalmazás megfelelő helyreállítási helyének meghatározása rugalmas. Rendelkezhet például olyan alkalmazásokkal, amelyek egy előfizetésben egy másik adatközpontba és egy másik előfizetésbe mentenek, és az Azure-beli nyilvános felhőbe replikálják azokat.

## <a name="application-recovery-objectives"></a>Alkalmazás-helyreállítási célkitűzések

Az alkalmazás tulajdonosai elsődlegesen az alkalmazás és a szervezet által elszenvedett állásidő és adatvesztés mértékének meghatározására szolgálnak. Az elfogadható állásidő és az elfogadható adatvesztés mennyiségi meghatározásával létrehozhat egy helyreállítási tervet, amely lekicsinyíti a katasztrófák hatását a szervezeten belül. Az egyes alkalmazásokhoz vegye figyelembe a következőket:

 - **Helyreállítási időre vonatkozó célkitűzés (RTO)**  
A RTO az a maximális elfogadható idő, ameddig egy alkalmazás nem érhető el egy incidens után. Például egy 90 perces RTO azt jelenti, hogy az alkalmazást egy, a katasztrófa elejétől 90 percen belül futó állapotba kell állítania. Ha alacsony RTO rendelkezik, egy másik üzemelő példányt folyamatosan készenléti állapotban tarthat a regionális kimaradások elleni védelem érdekében.
 - **Helyreállítási időkorlát (RPO)**  
A helyreállítási időkorlát (RPO) a katasztrófa során elfogadható adatveszteség maximális ideje. Ha például egyetlen adatbázisban tárolja az adatbiztonsági mentést, és nem végez replikálást más adatbázisokra, akkor akár egy órányi adat is elvész.

Egy másik metrika a *helyreállításhoz szükséges idő* (MTTR), amely az alkalmazás meghibásodás utáni visszaállításának átlagos időtartama. A MTTR egy rendszer empirikus értéke. Ha a MTTR túllépi a RTO, akkor a rendszer meghibásodása elfogadhatatlan üzleti fennakadást okoz, mivel nem lehet visszaállítani a rendszer a megadott RTO belül.

## <a name="protection-options"></a>Védelmi beállítások 

### <a name="backup-restore"></a>Biztonsági mentés visszaállítása

Az alkalmazások és adatkészletek biztonsági mentése lehetővé teszi, hogy az adatsérülés, a véletlen törlések vagy a katasztrófák miatt gyorsan helyreállítsa az állásidőt. A IaaS VM-alapú alkalmazások esetében a vendégen lévő ügynökkel biztosíthatja az alkalmazásadatok, az operációs rendszer konfigurációja és a köteteken tárolt adatmennyiségek védelme. 

#### <a name="backup-using-in-guest-agent"></a>Biztonsági mentés a vendég-ügynök használatával

A virtuális gépek vendég operációs rendszer ügynök használatával történő biztonsági mentése általában magában foglalja az operációsrendszer-konfiguráció, a fájlok/mappák, a lemezek, az alkalmazás bináris fájljainak vagy az alkalmazásadatok rögzítését. 

Az ügynök alkalmazásának helyreállításához manuálisan újra kell létrehozni a virtuális gépet, telepíteni kell az operációs rendszert, és telepíteni kell a vendég ügynököt. Ezen a ponton az adatgyűjtés a vendég operációs rendszerbe vagy közvetlenül az alkalmazásba is visszaállítható.

#### <a name="backup-using-disk-snapshot-for-stopped-vms"></a>Biztonsági mentés lemezes pillanatkép használatával leállított virtuális gépekhez

A biztonsági mentési termékek védik a IaaS virtuális gépek konfigurációját és a leállított virtuális gépekhez csatolt lemezeket. Az Azure Stack hub API-kkal integrált biztonsági mentési termékek használatával rögzítheti a virtuális gépek konfigurációját, és készíthet lemez-pillanatképeket. Ha az alkalmazás tervezett állásidője lehetséges, akkor győződjön meg arról, hogy a virtuális gép leállított állapotban van, mielőtt elindítja a biztonsági mentési munkafolyamatot.  

#### <a name="backup-using-disk-snapshot-snapshot-for-running-vms"></a>Biztonsági mentés lemezes pillanatkép-pillanatkép használatával a futó virtuális gépekhez

> [!Important]  
> A lemezes Pillanatképek használata jelenleg nem támogatott a futó állapotban lévő virtuális gépek esetében. Egy futó virtuális géphez csatolt lemez pillanatképének létrehozása csökkentheti a teljesítményt, vagy befolyásolhatja az operációs rendszer vagy alkalmazás rendelkezésre állását a virtuális gépen. Javasoljuk, hogy a vendég-ügynök használatával megvédje az alkalmazást, ha a tervezett állásidő nem lehetséges. 

### <a name="vms-in-a-scale-set-or-availability-group"></a>Virtuális gépek egy méretezési csoport vagy rendelkezésre állási csoportban

A méretezési csoportokban vagy a rendelkezésre állási csoportban lévő virtuális gépeket, amelyek nem minősülnek ideiglenes erőforrásnak, nem szabad biztonsági mentést készíteni a virtuálisgép-szinten, különösen akkor, ha az alkalmazás állapota nem megfelelő. A méretezési vagy rendelkezésre állási csoportokban üzembe helyezett állapot-nyilvántartó alkalmazások esetében érdemes megfontolni az alkalmazásadatok (például egy adatbázis vagy kötet) védelmét a tárolóban. 

### <a name="replicationmanual-failover"></a>Replikáció/manuális feladatátvétel

A minimális adatvesztést vagy minimális állásidőt igénylő alkalmazások esetében a vendég operációs rendszer vagy alkalmazás szintjén engedélyezheti az adatreplikálást, hogy az adatreplikálást egy másik helyre replikálja. Egyes alkalmazások, például a Microsoft SQL Server, natív módon támogatják a replikálást. Ha az alkalmazás nem támogatja a replikálást, a vendég operációs rendszerben található szoftverekkel replikálhatja a lemezeket, vagy egy partneri megoldást, amely ügynökként települ a vendég operációs rendszerben.

Ezzel a módszerrel az alkalmazást egy felhőben helyezik üzembe, és a rendszer a másik felhőbe, illetve az Azure-ba replikálja azokat. Feladatátvétel indításakor a célhelyen lévő alkalmazást el kell indítani, és csatolni kell a replikált adataihoz, mielőtt megkezdené a karbantartási kérelmek megkezdését.
 
### <a name="high-availabilityautomatic-failover"></a>Magas rendelkezésre állás/automatikus feladatátvétel

Olyan állapot nélküli alkalmazások esetében, amelyek csak néhány másodpercet vagy akár néhány percet is igénybe vehetnek, vegye figyelembe a magas rendelkezésre állási konfigurációt. A magas rendelkezésre állású alkalmazások úgy vannak kialakítva, hogy több helyen legyenek üzembe helyezhetők aktív/aktív topológiában, ahol az összes példány képes a kérelmek kiszolgálására. Helyi hardverhiba esetén a Azure Stack hub-infrastruktúra magas rendelkezésre állást valósít meg a fizikai hálózaton két felső szintű rack kapcsoló használatával. Számítási szintű hibák esetén Azure Stack hub több csomópontot használ egy méretezési egységben. A virtuális gépek szintjén a méretezési csoportokat a tartalék tartományokkal együtt használva biztosíthatja, hogy a csomópont-meghibásodások ne használják az alkalmazást. Ugyanezt az alkalmazást egy másodlagos helyre kell telepíteni ugyanabban a konfigurációban. Ahhoz, hogy az alkalmazás aktív/aktív legyen, a terheléselosztó vagy a DNS használatával a kérelmeket az összes rendelkezésre álló példányra irányíthatja.

### <a name="no-recovery"></a>Nincs helyreállítás

Előfordulhat, hogy a környezet egyes alkalmazásai nem igényelnek védelmet a nem tervezett leállások vagy az adatvesztés ellen. Például a fejlesztéshez és teszteléshez használt virtuális gépeket általában nem kell helyreállítani. Az Ön döntése az alkalmazások vagy adatkészletek védelmének meghozatala nélkül.

## <a name="recommended-topologies"></a>Ajánlott topológiák

Fontos szempontok az Azure Stack hub üzembe helyezéséhez:

|     | Ajánlás | Megjegyzések |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Virtuális gépek biztonsági mentése/visszaállítása az adatközpontban már üzembe helyezett külső biztonsági mentési célra | Ajánlott | Használja ki a meglévő biztonsági mentési infrastruktúrát és az üzemeltetési képességeket. Ügyeljen arra, hogy a biztonsági mentési infrastruktúrát úgy méretezi, hogy az készen álljon a további virtuálisgép-példányok elleni védelemre. Ügyeljen arra, hogy a biztonsági mentési infrastruktúra ne legyen közel a forráshoz. A virtuális gépeket visszaállíthatja a forrás Azure Stack hubhoz, egy másodlagos Azure Stack hub-példányra vagy az Azure-ra. |
| Virtuális gépek biztonsági mentése/visszaállítása a Azure Stack hub számára dedikált külső biztonsági mentési célra | Ajánlott | Új biztonsági mentési infrastruktúrát vásárolhat, vagy kiépítheti az Azure Stack hub dedikált biztonsági mentési infrastruktúráját. Ügyeljen arra, hogy a biztonsági mentési infrastruktúra ne legyen közel a forráshoz. A virtuális gépeket visszaállíthatja a forrás Azure Stack hubhoz, egy másodlagos Azure Stack hub-példányra vagy az Azure-ra. |
| Virtuális gépek biztonsági mentése/visszaállítása közvetlenül a globális Azure-ba vagy egy megbízható szolgáltatóhoz | Ajánlott | A biztonsági mentések a globális Azure-ban vagy egy megbízható szolgáltatóban tárolhatók, feltéve, hogy megfelel az adatvédelmi és szabályozási követelményeknek. Ideális esetben a szolgáltató a Azure Stack hub-t is futtatja, így a visszaállítás során konzisztens lesz a működési élményben. |
| Virtuális gépek replikálása vagy feladatátvétele egy külön Azure Stack hub-példányba | Ajánlott | A feladatátvételi esetben egy második Azure Stack hub-felhőnek teljesen működőképesnek kell lennie, hogy el tudja kerülni a kiterjesztett alkalmazások leállását. |
| Virtuális gépek replikálása/feladatátvétele közvetlenül az Azure-ba vagy egy megbízható szolgáltatóhoz | Ajánlott | Ha az adatvédelmet és a szabályozást is kielégíti, az adatokat a globális Azure-ba vagy egy megbízható szolgáltatóba replikálhatja. Ideális esetben a szolgáltató a Azure Stack hub-t is futtatja, így a feladatátvétel után konzisztens lesz a működési élményben. |
| Telepítsen egy biztonsági mentési célt ugyanarra a Azure Stack hubhoz, amely az ugyanazon biztonsági mentési cél által védett összes alkalmazást is üzemelteti. | Önálló cél: nem ajánlott </br> A biztonsági másolatok külső replikálására szolgáló cél: ajánlott | Ha Azure Stack hub-on helyez üzembe egy biztonságimásolat-berendezést (az operatív visszaállítás optimalizálásához), gondoskodnia kell arról, hogy a rendszer folyamatosan másolja az összes adatforrást egy külső biztonsági mentési helyre. |
| Fizikai biztonsági mentési berendezés üzembe helyezése ugyanabba az állványba, ahol a Azure Stack hub-megoldás telepítve van | Nem támogatott | Jelenleg nem csatlakoztatható más eszközök az eredeti megoldás részét nem képező rack-kapcsolók tetejéhez. |

## <a name="next-steps"></a>További lépések

Ez a cikk az Azure Stack hub-on üzembe helyezett felhasználói virtuális gépek védelmére vonatkozó általános irányelveket ismertetett. További információ az Azure-szolgáltatásoknak a felhasználói virtuális gépek számára történő használatáról:

- [Azure Stack IaaS – negyedik rész – a dolgok biztonsága](https://azure.microsoft.com/blog/azure-stack-iaas-part-four/)
- [A folyamatos üzletmenet és a vészhelyreállítás megfontolandó szempontjai](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Azure Backup Server
 - [Fájlok és alkalmazások biztonsági mentése a Azure Stack hub Azure Backup használatával](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Stack hub Azure Backup Server támogatása](https://docs.microsoft.com/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Azure Stack hub Azure Site Recovery támogatása](https://docs.microsoft.com/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Partneri termékek
 - [Azure Stack hub Datacenter Integration partner ökoszisztémájának Adatlapja](https://aka.ms/azurestackbcdrpartners)
