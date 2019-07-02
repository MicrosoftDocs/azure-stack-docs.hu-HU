---
title: Az Azure Stack üzembe helyezett virtuális gépek védelme |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre a helyreállítási terv az Azure Stacken szemben az adatvesztést, és nem tervezett üzemkimaradások üzembe helyezett virtuális gépek védelme érdekében.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: 9f8fe959ca200b7000df65b6826a103535aac92a
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492411"
---
# <a name="protect-vms-deployed-on-azure-stack"></a>Az Azure Stack üzembe helyezett virtuális gépek védelme

Ez a cikk egy útmutató, amellyel a virtuális gépek (VM), hogy a felhasználók az Azure Stacken védelméhez tervet használhatja.


Az adatvesztést, és nem tervezett üzemkimaradások elleni védelme érdekében szüksége felhasználói alkalmazások és az adataik biztonsági mentési-helyreállítási vagy vész-helyreállítási terv megvalósításához. Ez a csomag Előfordulhat, hogy minden alkalmazáshoz egyedi, de egy a szervezet átfogó üzleti folytonossági és vészhelyreállítási stratégia (BC/DR) által meghatározott keretek követi. Van egy jó kiindulási pont [Azure Stack: Üzleti folytonosság és vészhelyreállítás helyreállítási szempontjai](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="azure-stack-infrastructure-recovery"></a>Az Azure Stack-infrastruktúra recovery

Felhasználó felelős saját virtuális gépeket külön-külön védi az Azure Stack-infrastruktúra-szolgáltatásokat.

Az Azure Stack-infrastruktúra-szolgáltatásoknak a helyreállítási terv **nem** közé tartozik a felhasználói virtuális gépek, tárfiókok és adatbázisok helyreállítását. Az alkalmazás tulajdonosa már az alkalmazások és a helyreállítási terv végrehajtásáért felelős.

Ha hosszabb ideig offline állapotban az Azure Stack-felhőben, vagy véglegesen helyreállíthatatlan, szüksége lesz egy helyreállítási terv helyezze el, amely:

* Minimális állásidővel biztosítja.
* Kritikus fontosságú eszközök, például az adatbázis-kiszolgálón futó virtuális gépek megtartja.
* Lehetővé teszi, hogy az alkalmazásokat a felhasználói kérések tartsa karbantartási.

Az Azure Stack-felhő üzemeltetője feladata a mögöttes Azure Stack-infrastruktúra és a szolgáltatások helyreállítási terv létrehozására. További tudnivalókért lásd: [végzetes adatvesztés utáni helyreállítás](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>IaaS virtuális gépek szempontjai
Az IaaS-beli virtuális gépen telepített operációs rendszer korlátozza az adatok védelmére használható termékeket tartalmaz. Windows-alapú IaaS virtuális gépek, használhatja a Microsoft és partnerei termékek adatok védelme érdekében. Az IaaS virtuális gépek Linux-alapú, az egyetlen lehetőség partnerek termékei használatára. Tekintse meg [az Azure Stackhez készült ellenőrzött termékek BC/DR-partnerek számára a adatlap](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Forrás/cél kombinációk

Minden egyes Azure Stack-felhőben telepítve van a egyazon adatközpontban. Az alkalmazások helyreállítása egy másik környezetbe van szükség. A helyreállítási környezetre lehet egy másik Azure Stack-felhő egy másik adatközpontban lévő, vagy az Azure nyilvános felhő. Az adatok elkülönítése és az adatok adatvédelmi követelményeket határozza meg a helyreállítási környezet, az alkalmazás számára. Engedélyezte a védelmet minden alkalmazáshoz, meg kell minden egyes egy adott helyreállítási lehetőség közül. Alkalmazások az adatok biztonsági mentése egy másik adatközpontba egy előfizetéssel rendelkezhet. Egy másik előfizetésben replikálhatja adatait az Azure nyilvános felhő.

Tervezze meg a backup-helyreállítás és a vész-helyreállítási stratégia minden alkalmazás határozza meg a cél, minden alkalmazáshoz. A helyreállítási terv segít a szervezet megfelelően méretezés a tárolási kapacitás szükséges a helyszíni és a nyilvános felhőben fogyasztás projektre.

|  | Globális Azure | Az Azure Stack üzembe helyezve a CSP adatközpontjában és a CSP által működtetett | Az Azure Stack ügyfél adatközpontban helyezi üzembe, és az ügyfél által működtetett |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Az Azure Stack üzembe helyezve a CSP adatközpontjában és a CSP által működtetett** | A CSP által működtetett Azure stack felhasználói a virtuális gépek üzembe.<br><br>Felhasználói virtuális gépek biztonsági másolatból történt visszaállítása vagy feladatátvétel közvetlenül az Azure-bA. | Kriptográfiai Szolgáltató az elsődleges és másodlagos példányai az Azure Stack saját adatközpontban működik.<br><br>Felhasználói virtuális gépeket visszaállított vagy átadta a feladatait a két Azure Stack-példányok között. | Kriptográfiai Szolgáltató az Azure Stack az elsődleges helyen működik.<br><br>Ügyfél adatközpontja célja visszaállítása vagy feladatátvétel. |
| **Az Azure Stack ügyfél adatközpontban helyezi üzembe, és az ügyfél által működtetett** | Az ügyfél által működtetett Azure stack felhasználói a virtuális gépek üzembe.<br><br>Felhasználói virtuális gépek biztonsági másolatból történt visszaállítása vagy feladatátvétel közvetlenül az Azure-bA. | Ügyfél az Azure Stack az elsődleges helyen működik.<br><br>A Felhőszolgáltató adatközpontjában célja visszaállítása vagy feladatátvétel. | Ügyfél az elsődleges és másodlagos példányai az Azure Stack saját adatközpontban működik.<br><br>Felhasználói virtuális gépeket visszaállított vagy átadta a feladatait a két Azure Stack-példányok között. |

![Forrás-cél kombinációk](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="app-recovery-objectives"></a>Alkalmazás helyreállítási célok

A szervezet minden alkalmazáshoz tűri állásidőt és az adatveszteség mennyisége határozza meg. Állásidő mennyiségi meghatározására, és az adatvesztést létrehozhat egy helyreállítási tervbe, hogy a szervezete katasztrófa hatását. Az egyes alkalmazások vegye figyelembe:

 - **A helyreállítási időre vonatkozó célkitűzés (RTO)**  
RTO egy incidens után egy alkalmazás elérhetetlen maradhat maximális elfogadható idejét. Az RTO 90 perc, például azt jelenti, hogy meg kell tudni az alkalmazás visszaállítása egy működőképes állapotba 90 percen belül a katasztrófa kezdetétől. Ha alacsony RTO, akkor előfordulhat, hogy tartsa egy második üzemelő példány folyamatosan készenléti módban futhat a regionális üzemkimaradások esetére.
 - **Helyreállítási időkorlát (RPO)**  
Helyreállítási Időkorlát, egy katasztrófa során elfogadható adatvesztés maximális időtartama. Például ha egy önálló adatbázis, amely biztonsági mentést óránként végzi, és nincs más adatbázisokkal replikációs adatokat tárolja, elveszhet legfeljebb egy órányi adatot.

Az RTO-ra és az RPO-ra üzleti követelményként tekinthet. Az alkalmazás RTO és RPO meghatározásához a kockázatbecslés végez.

Egy másik metrika **helyreállítása időt** (MTTR), azaz a meghibásodás után az alkalmazás visszaállításával átlagos idejét. MTTR empirikus érték egy rendszer számára. Ha MTTR nagyobb, mint az RTO, majd a meghibásodása esetén a rendszer az üzletmenet elfogadhatatlan mértékű okozhat, mert nem lehet visszaállítani a rendszert a megadott RTO belül.

### <a name="backup-restore"></a>Backup-restore

A leggyakrabban használt védelmi Virtuálisgép-alapú alkalmazások sémája biztonsági mentési szoftver használatára. Virtuális gépek biztonsági mentéséről általában tartalmazza az operációs rendszer, az operációs rendszer konfigurálása, alkalmazás bináris fájljainak és az alkalmazásadatok. A biztonsági mentés pillanatképének elkészítése a köteteket, a lemezek vagy a teljes virtuális Gépet hoznak létre. Az Azure Stack megvan a rugalmasság a biztonsági mentés készül a vendég operációs rendszer vagy az Azure Stack-storage-ból a környezeten belül, és API-k számítási. Az Azure Stack véve a biztonsági mentések a hipervizor szintjén nem támogatja.
 
![Backup-restor](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Az alkalmazás helyreállítása szükséges egy vagy több virtuális gép visszaállítása, ugyanabban a felhőben vagy egy új felhőt. A célfelhő az adatközpontban vagy a nyilvános felhőben. A választott cloud teljesen a vezérlőben, amely az és az adatvédelmi követelmények alapján.

 - RTO: Az órában mért állásidő
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Változó adatvesztés (biztonsági mentés gyakoriságától függően)
 - Üzembe helyezési topológia: Aktív/passzív

#### <a name="planning-your-backup-strategy"></a>A biztonsági mentési stratégia tervezése

A biztonsági mentési stratégia megtervezése és a méretezési követelmények meghatározásánál tart kezdődik a mennyiségi meghatározására a védendő Virtuálisgép-példányok számát. Gyakori stratégiát az összes virtuális gépek biztonsági mentésének környezet összes kiszolgálója között is. Az Azure Stack, vannak azonban bizonyos virtuális gépek biztonsági mentése. Például egy méretezési csoportban lévő virtuális gépek lesznek figyelembe véve a rövid élettartamú erőforrások, amelyek származnak, és lépjen, néha előzetes értesítés nélkül. Védendő hosszú élettartamú adatok egy különálló, például egy adatbázisba vagy az objektum store tárházat tárolja.

Fontos szempontok a virtuális gépek biztonsági mentésének az Azure Stacken:

 - **Kategorizálási**
    - Fontolja meg egy modellt, ahol felhasználók engedélyezve a virtuális gépek biztonsági mentését.
    - Adjon meg egy helyreállítási szolgáltatásiszint-szerződés (SLA) az alkalmazások vagy az üzleti gyakorolt hatása a prioritás alapján.
 - **Méretezés**
    - Vegye figyelembe a lépcsőzetes biztonsági mentések, amikor az előkészítési új virtuális gépek nagy számú (Ha a biztonsági másolat megadása kötelező).
    - Értékelje ki a biztonsági mentési termék, amely hatékonyan rögzítése és a megoldás resource tartalmak minimalizálása érdekében a biztonsági mentési adatok továbbítására.
    - Értékelje ki a biztonsági mentési termék, amely hatékony biztonsági mentési adatok tárolása az növekményes vagy a különbözeti biztonsági mentések teljes biztonsági mentéseket szükségességének minimálisra csökkentése, minden virtuális gépen a környezetben.
 - **Visszaállítás**
    - Biztonságimásolat-készítő virtuális lemezeket, az alkalmazásadatok egy meglévő virtuális Gépet, vagy a teljes VM-erőforrás és a társított virtuális lemezekkel is helyreállíthatja. A visszaállítási séma kell attól függ, hogyan tervezi az alkalmazás visszaállításához. Például egyszerűbb lehet, telepítse újra az SQL server-sablonból, és állítsa vissza a helyett a teljes virtuális Gépet vagy virtuális gépek visszaállítása az adatbázisok.

### <a name="replicationmanual-failover"></a>Replikáció vagy manuális feladatátvétel

Egy másik megközelítést magas rendelkezésre állású, hogy az alkalmazás virtuális gépek replikálása egy másik felhőbe, és a manuális feladatátvételt. Az operációs rendszer, alkalmazás bináris fájljainak és alkalmazások adatainak a replikálását teheti meg a virtuális gép vagy vendég operációs rendszer szintjén. A feladatátvétel további szoftvereket, amely nem része az alkalmazás kezeli.

Ezzel a módszerrel telepítik az alkalmazást egy felhőben, és a virtuális gép a rendszer replikálja a többi felhőt. A feladatátvételi akkor aktiválódik, ha a másodlagos virtuális gépek kell bekapcsolni a második felhőben. Bizonyos esetekben a feladatátvétel őket a virtuális gépek és rendeli lemezeket hoz létre. Ez a folyamat végrehajtásához, különösen egy többrétegű alkalmazást, amely egy adott indítási sorrend igényel a hosszú időt vehet igénybe. Is előfordulhatnak lépéseket, amelyek az alkalmazás készen áll a szolgáltatáskéréseket elindítása előtt kell futtatni.

![Replikálás – kézi feladatátvételt](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: Percben mért állásidő
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Változó adatvesztés (replikáció gyakoriságától függően)
 - Üzembe helyezési topológia: Aktív/passzív készenléti
 
### <a name="high-availabilityautomatic-failover"></a>Magas rendelkezésre állást és automatikus feladatátvétel

Ahol csak tűri az üzleti alkalmazások néhány másodperceken vagy perceken állásidő és a minimális adatvesztéssel, fontolja meg egy magas rendelkezésre állású konfigurációban. Magas rendelkezésre állású alkalmazások gyorsan és automatikusan helyreállni hibák lettek kialakítva. Helyi hardveres hibák, az Azure Stack-infrastruktúra magas rendelkezésre állás a fizikai hálózatban két felső részén kapcsolók használatával valósítja meg. Számítási szolgáltatói hibákat az Azure Stack a skálázási egységeken belüli több csomópont használja. A virtuális gép szintjén méretezési csoportokkal együtt használhatja a tartalék tartományok csomóponthibák nem lép fel az alkalmazás biztosításához.

A méretezési csoportokkal együtt az alkalmazás kell natív módon támogatja a magas rendelkezésre állás vagy a szoftver fürtszolgáltatás használatát támogatja. Például a Microsoft SQL Server támogatja a magas rendelkezésre állású natív módon szinkron véglegesítésű módot használó adatbázisokról. Azonban csak az aszinkron replikáció is támogatottak, ha ezután lesz némi adatvesztést. Alkalmazások üzembe helyezve a feladatátvevő fürtök, ahol a fürtszoftver kezeli-e az alkalmazás az automatikus feladatátvételt is is.

Ezzel a megközelítéssel az alkalmazás csak aktív egy felhőben, de a szoftver központi telepítése több felhő. Az egyéb felhőkben is készenléti módban az alkalmazás megkezdheti a feladatátvétel elindításakor.

 - RTO: Állásidő idejének mérése másodpercben
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Minimális adatvesztéssel
 - Üzembe helyezési topológia: Aktív/aktív-készenléti

### <a name="fault-tolerance"></a>Hibatűrés

Az Azure Stack fizikai redundancia és infrastruktúra-szolgáltatás rendelkezésre állása csak a hardveres hibák és meghibásodások például a lemez, tápegység, hálózati portot vagy csomópont elleni védelem. Azonban ha az alkalmazás mindig elérhetőnek kell lennie, és soha nem elveszíthetik az adatokat, meg kell hibatűrés megvalósítása az alkalmazás natív módon, vagy további szoftverek használata a hibatűrés engedélyezéséhez.

Először csomópont-szintű leállások ellen védelmet biztosító beállítja a virtuális gépek méretezési csoport üzembe helyezett alkalmazás biztosításához. A felhő offline állapotra ellen védelmet biztosító, ugyanazt az alkalmazást már telepíteni kell egy másik felhőhöz, az továbbra is megszakítás nélkül a szolgáltatáskéréseket. Ez a modell gyakran emlegetik úgy egy aktív-aktív központi telepítés.

Ne feledje, hogy minden egyes Azure Stack-felhőben függetlenek egymástól,-e, a felhők mindig számítanak aktív, az infrastruktúra szempontjából. Ebben az esetben az alkalmazás több aktív példány egy vagy több aktív felhők vannak telepítve.

 - RTO: Állásidő nélkül
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Nincs adatvesztés
 - Üzembe helyezési topológia: Active/Active

### <a name="no-recovery"></a>Nincs recovery

Egyes alkalmazások a környezetében előfordulhat, hogy nem kell a nem tervezett leállásokkal vagy adatvesztés elleni védelem. Például a virtuális gépek fejlesztési célokra, és a tesztelés általában nem kell helyreállítani. Egy alkalmazás vagy egy adott virtuális gép védelme nélkül mellett. Az Azure Stack biztonsági mentési vagy a virtuális gépek replikálása nem nyújt az alapul szolgáló infrastruktúráról. Hasonlóan az Azure-ba, akkor minden egyes előfizetés az egyes virtuális gép védelmét szeretné.

 - RTO: Helyreállíthatatlan
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Teljes adatvesztés

## <a name="recommended-topologies"></a>Ajánlott topológiák

Az Azure Stack üzemelő példány fontos szempontjai:

|     | Ajánlás | Megjegyzések |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Virtuális gépek biztonsági mentése/visszaállítása egy már üzembe helyezte az adatközpontban található külső biztonsági mentési cél | Ajánlott | Kihasználhatja a meglévő biztonsági mentési infrastruktúra és képességeket. Ellenőrizze, hogy a biztonsági mentési infrastruktúra méretezés, hogy további Virtuálisgép-példányok védelméhez készen álljon. Ellenőrizze, hogy a forrás hálózatbővítési nincs biztonsági mentési infrastruktúra. A forrás Azure Stack, egy másodlagos Azure Stack-példányt, vagy Azure virtuális gépek is visszaállíthatók. |
| Virtuális gépek biztonsági mentése/visszaállítása egy külső biztonsági mentési cél dedikált az Azure Stackhez | Ajánlott | Új biztonsági mentési infrastruktúra vagy dedikált biztonsági mentési infrastruktúra kiépítése az Azure Stack vásárolhatja meg. Ellenőrizze, hogy a forrás hálózatbővítési nincs biztonsági mentési infrastruktúra. A forrás Azure Stack, egy másodlagos Azure Stack-példányt, vagy Azure virtuális gépek is visszaállíthatók. |
| Közvetlenül a globális Azure-ban vagy egy megbízható szolgáltató a virtuális gépek biztonsági mentése/visszaállítása | Ajánlott | Mindaddig, amíg az adatvédelem és a szabályozási követelményeknek is megfeleljen, a biztonsági másolatok tárolhatók a globális Azure-ban vagy egy megbízható szolgáltató. Ideális esetben a szolgáltató is fut-e az Azure Stack így konzisztencia működési felületen történő visszaállításához. |
| Replikálás és feladatátvételi virtuális gépek egy külön Azure Stack-példányt | Ajánlott | A feladatátvételi esetben szüksége lesz egy második teljesen működőképes Azure Stack-felhőben, kiterjesztett alkalmazás állásidő elkerülése érdekében. |
| Replikálás és feladatátvételi virtuális gépek közvetlenül az Azure-ban vagy egy megbízható szolgáltató | Ajánlott | Mindaddig, amíg az adatvédelem és a szabályozási követelményeknek is megfelel, replikálja az adatokat globális Azure-ban vagy egy megbízható szolgáltató. Ideális esetben a szolgáltató is fut-e az Azure Stack így konzisztencia működési élményt nyújt a feladatátvételt követően. |
| Az alkalmazás adatait az azonos Azure Stack-felhőben lévő biztonsági mentési cél üzembe helyezése | Nem ajánlott. | Kerülje a azonos Azure Stack-felhőben lévő biztonsági másolatok tárolásához. A felhő nem tervezett üzemkimaradások megmaradhat az elsődleges és a biztonsági mentési adatokat. Ha szeretné üzembe helyezni a biztonsági mentési cél (optimalizálást a biztonsági mentési és visszaállítási célokra) egy olyan virtuális berendezésre, választja, gondoskodnia kell minden adatot folyamatosan másol egy külső biztonsági mentési helyre. |
| Fizikai tartalék készülék, ahol az Azure Stack megoldás telepítve van-e az azonos rack üzembe helyezése | Nem támogatott | Jelenleg semmilyen más eszközök nem lehet csatlakozni, amelyek nem részei az eredeti megoldásban kapcsolók tetején. |

## <a name="next-steps"></a>További lépések

Ez a cikk általános irányelveket előírt telepíthető az Azure Stack felhasználói virtuális gépek védelmére. A felhasználói virtuális gépek védelme az Azure-szolgáltatások használatával kapcsolatos információkért tekintse meg:

 - [Azure Backup használatával biztonsági mentése a fájlok és alkalmazások az Azure Stackben](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Stack az Azure Backup Server támogatása](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Stack támogatása az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)  

A partner-termékek nyújtanak védelmet a virtuális gép az Azure Stacken kapcsolatos további információkért tekintse meg [védelméről, alkalmazásokat és adatokat az Azure Stacken](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
