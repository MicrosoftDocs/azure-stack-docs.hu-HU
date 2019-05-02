---
title: Az Azure Stack üzembe helyezett virtuális gépek védelme |} A Microsoft Docs
description: Az Azure Stack üzembe helyezett virtuális gépek védelme vonatkozó útmutatónkat.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: 3a58ada2d8b6d392ba8fb3ec6099ffa4d7114cfa
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985857"
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Az Azure Stack üzembe helyezett virtuális gépek védelme

Ez a cikk egy útmutató, amellyel a virtuális gépek (VM), hogy a felhasználók az Azure Stacken védelméhez tervet használhatja.

Az adatvesztést, és nem tervezett üzemkimaradások elleni védelme érdekében szüksége felhasználói alkalmazások és az adataik biztonsági mentési-helyreállítási vagy vész-helyreállítási terv megvalósításához. Ez a csomag Előfordulhat, hogy minden alkalmazáshoz egyedi, de egy a szervezet átfogó üzleti folytonossági és vészhelyreállítási stratégia (BC/DR) által meghatározott keretek követi. Van egy jó kiindulási pont [Azure Stack: Üzleti folytonosság és vészhelyreállítás helyreállítási szempontjai](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="azure-stack-infrastructure-recovery"></a>Az Azure Stack-infrastruktúra recovery

Felhasználó felelős saját virtuális gépeket külön-külön védi az Azure Stack-infrastruktúra-szolgáltatásokat.

Az Azure Stack-infrastruktúra-szolgáltatásoknak a helyreállítási terv **nem** közé tartozik a felhasználói virtuális gépek, tárfiókok és adatbázisok helyreállítását. Az alkalmazás tulajdonosa, Ön felelős az alkalmazások és adatok helyreállítási terv megvalósításához.

Ha hosszabb ideig offline állapotban az Azure Stack-felhőben, vagy véglegesen helyreállíthatatlan, szüksége lesz egy helyreállítási terv helyezze el, amely:

* Biztosítja a minimális állásidővel
* Kritikus fontosságú eszközök, például az adatbázis-kiszolgálón futó virtuális gépek megtartja
* Lehetővé teszi az alkalmazások folyamatosan a felhasználói kérések karbantartásába

Az Azure Stack-felhő üzemeltetője feladata a mögöttes Azure Stack-infrastruktúra és a szolgáltatások helyreállítási terv létrehozására. További tudnivalókért olvassa el a cikket [végzetes adatvesztés utáni helyreállítás](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>IaaS virtuális gépek szempontjai
Az IaaS-beli virtuális gépen telepített operációs rendszer korlátozza az adatok védelmére használható termékeket tartalmaz. Windows-alapú IaaS virtuális gépek, használhatja a Microsoft és partnerei termékek adatok védelme érdekében. Az IaaS virtuális gépek Linux-alapú, az egyetlen lehetőség partnerek termékei használatára. Tekintse meg [az Azure Stackhez készült ellenőrzött termékek BC/DR-partnerek számára a adatlap](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Forrás/cél kombinációk

Minden egyes Azure Stack-felhőben telepítve van a egyazon adatközpontban. Egy másik környezetbe azért szükséges, hogy helyre tudja állítani az alkalmazások. A helyreállítási környezetre lehet egy másik Azure Stack-felhő egy másik adatközpontban lévő, vagy az Azure nyilvános felhő. Az adatok elkülönítése és az adatok adatvédelmi követelményeket állapítja meg az alkalmazás a helyreállítási környezetét. Engedélyezte a védelmet minden alkalmazáshoz, meg kell minden egyes egy adott helyreállítási lehetőség közül. Alkalmazások az adatok biztonsági mentése egy másik adatközpontba egy előfizetéssel rendelkezhet. Egy másik előfizetésben replikálhatja adatait az Azure nyilvános felhő.

Tervezze meg a backup-helyreállítás és a vész-helyreállítási stratégia minden alkalmazás határozza meg a cél, minden alkalmazáshoz. A helyreállítási terv segít a szervezet megfelelően méretezés a tárolási kapacitás szükséges a helyszíni és a nyilvános felhőben fogyasztás projektre.

|  | Globális Azure | Az Azure Stack üzembe helyezve a CSP adatközpontjában és a CSP által működtetett | Az Azure Stack ügyfél adatközpontban helyezi üzembe, és az ügyfél által működtetett |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Az Azure Stack üzembe helyezve a CSP adatközpontjában és a CSP által működtetett** | Az működtetni CSP az Azure Stack felhasználói virtuális gépeket telepít.<br><br>Felhasználói virtuális gépek biztonsági másolatból történt visszaállítása vagy feladatátvétel közvetlenül az Azure-bA. | Kriptográfiai Szolgáltató az elsődleges és másodlagos példányai az Azure Stack saját adatközpontban működik.<br><br>Felhasználói virtuális gépeket visszaállított vagy átadta a feladatait a két Azure Stack-példányok között. | Kriptográfiai Szolgáltató az Azure Stack az elsődleges helyen működik.<br><br>Ügyfél adatközpontja célja visszaállítása vagy feladatátvétel. |
| **Az Azure Stack ügyfél adatközpontban helyezi üzembe, és az ügyfél által működtetett** | Felhasználói virtuális gépeket a rendszer telepíti az ügyfél üzemeltetett Azure Stack.<br><br>Felhasználói virtuális gépek biztonsági másolatból történt visszaállítása vagy feladatátvétel közvetlenül az Azure-bA. | Ügyfél az Azure Stack az elsődleges helyen működik.<br><br>A Felhőszolgáltató adatközpontjában célja visszaállítása vagy feladatátvétel. | Ügyfél az elsődleges és másodlagos példányai az Azure Stack saját adatközpontban működik.<br><br>Felhasználói virtuális gépeket visszaállított vagy átadta a feladatait a két Azure Stack-példányok között. |

![Forrás-cél kombinációk](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Alkalmazás helyreállítási célok

Szüksége lesz a szervezet minden alkalmazáshoz tűri állásidőt és az adatveszteség mennyisége határozza meg. Mennyiségi meghatározására az üzemkimaradás és az adatvesztés létrehozhat egy helyreállítási tervbe, hogy a szervezete katasztrófa hatását. Az egyes alkalmazások vegye figyelembe:

 - **A helyreállítási időre vonatkozó célkitűzés (RTO)**  
RTO egy incidens után egy alkalmazás elérhetetlen maradhat maximális elfogadható idejét. Például az RTO 90 perc azt jelenti, hogy be kell állítani az alkalmazás egy működőképes állapotba 90 percen belül a kezdetektől vészhelyzet. Ha alacsony RTO, akkor előfordulhat, hogy tartsa egy második üzemelő példány folyamatosan készenléti módban futhat a regionális üzemkimaradások esetére.
 - **Helyreállítási időkorlát (RPO)**  
Helyreállítási Időkorlát, egy katasztrófa során elfogadható adatvesztés maximális időtartama. Ha például egyetlen adatbázisban tárol adatokat, és nem készít replikát egy másik adatbázisba, és óránként végez biztonsági mentéseket, akkor legfeljebb egy órányi adatot veszíthet.

Az RTO-ra és az RPO-ra üzleti követelményként tekinthet. Az alkalmazás RTO és RPO meghatározásához a kockázatbecslés végez.

Egy másik metrika **helyreállítása időt** (MTTR), azaz a meghibásodás után az alkalmazás visszaállításával átlagos idejét. MTTR empirikus érték egy rendszer számára. Ha az MTTR nagyobb, mint az RTO, akkor a rendszer hibája az üzletmenet elfogadhatatlan mértékű szüneteltetését okozza, mert nem lehet visszaállítani a rendszert a megadott RTO-n belül.

### <a name="backup-restore"></a>Backup-restore

A leggyakrabban használt védelmi séma Virtuálisgép-alapú alkalmazásokhoz, hogy biztonsági mentési szoftver. Virtuális gépek biztonsági mentéséről általában tartalmazza az operációs rendszer, az operációs rendszer konfigurálása, alkalmazás bináris fájljainak és az alkalmazásadatokat. A biztonsági mentés pillanatképének elkészítése a köteteket, a lemezek vagy a teljes virtuális Gépet hoznak létre. Az Azure Stack megvan a rugalmasság a biztonsági mentés készül a vendég operációs rendszer vagy az Azure Stack-storage-ból a környezeten belül, és API-k számítási. Az Azure Stack nem támogatja a véve a biztonsági mentések a hipervizor szintjén.
 
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
    - Adjon meg egy helyreállítási szolgáltatásiszint-szerződés (SLA) az alkalmazások és üzleti hatását a prioritás alapján.
 - **Méretezés**
    - Vegye figyelembe a lépcsőzetes biztonsági mentések, amikor az előkészítési új virtuális gépek nagy számú (Ha a biztonsági másolat megadása kötelező).
    - Értékelje ki a biztonsági mentési termék, amely hatékonyan rögzítése és a megoldás resource tartalmak minimalizálása érdekében a biztonsági mentési adatok továbbítására.
    - Értékelje ki a biztonsági mentési termék, amely hatékony biztonsági mentési adatok tárolása az növekményes vagy a különbözeti biztonsági mentések teljes biztonsági mentéseket szükségességének minimálisra csökkentése, minden virtuális gépen a környezetben.
 - **Visszaállítás**
    - Biztonságimásolat-készítő virtuális lemezeket, az alkalmazásadatok egy meglévő virtuális Gépet, vagy a teljes VM-erőforrás és a társított virtuális lemezekkel is helyreállíthatja. A visszaállítási séma van szüksége, attól függ, hogyan szeretné állítani az alkalmazás, és ez negatív hatással lesz az alkalmazás helyreállítási időt. Például egyszerűbb lehet, telepítse újra az SQL server-sablonból, és állítsa vissza a helyett a teljes virtuális Gépet vagy virtuális gépek visszaállítása az adatbázisok.

### <a name="replicationmanual-failover"></a>Replikáció vagy manuális feladatátvétel

Egy másik megközelítést magas rendelkezésre állású, hogy az alkalmazás virtuális gépek replikálása egy másik felhőbe, és a manuális feladatátvételt. Az operációs rendszer, az alkalmazás bináris fájljainak és az alkalmazások adatainak a replikálását végezheti el a virtuális gép vagy vendég operációs rendszer szintjén. A feladatátvétel további szoftvereket, amely nem része az alkalmazás kezeli.

Ezzel a módszerrel a rendszer telepíti az alkalmazást egy felhőben, és a virtuális gép a rendszer replikálja a többi felhőt. A feladatátvételi akkor aktiválódik, ha a másodlagos virtuális gépek kell bekapcsolni a második felhőben. Bizonyos esetekben a feladatátvétel őket a virtuális gépek és rendeli lemezeket hoz létre. Ez a folyamat végrehajtásához, különösen az olyan meghatározott indítási feladatütemezési igénylő többrétegű alkalmazást hosszú időt vehet igénybe. Is előfordulhatnak lépéseket kell futtatni, mielőtt az alkalmazás megkezdheti a szolgáltatáskéréseket.

![Replikálás – kézi feladatátvételt](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: Percben mért állásidő
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Változó adatvesztés (replikáció gyakoriságától függően)
 - Üzembe helyezési topológia: Aktív/passzív készenléti
 
### <a name="high-availabilityautomatic-failover"></a>Magas rendelkezésre állást és automatikus feladatátvétel

Alkalmazások, ahol az üzleti tűri néhány másodperceken vagy perceken állásidő és a minimális adatvesztéssel szüksége lesz vegye fontolóra egy magas rendelkezésre állású konfigurációban. Magas rendelkezésre állású alkalmazások gyorsan és automatikusan helyreállni hibák lettek kialakítva. Helyi hardveres hibák, az Azure Stack-infrastruktúra magas rendelkezésre állás a fizikai hálózatban két felső részén kapcsolók használatával valósítja meg. Számítási szolgáltatói hibákat az Azure Stack a skálázási egységeken belüli több csomópont használja. A virtuális gép szintjén, a méretezési csoportokkal együtt használhatja a tartalék tartományok csomóponthibák nem lép fel az alkalmazás biztosításához.

Az alkalmazásnak méretezési csoportokkal együtt, szüksége lesz a natív módon támogatja a magas rendelkezésre állású vagy támogatja a fürtszolgáltatás szoftver használatát. Például a Microsoft SQL Server támogatja a magas rendelkezésre állású natív módon szinkron véglegesítésű módot használó adatbázisokról. Azonban csak az aszinkron replikáció is támogatottak, ha ezután lesz némi adatvesztést. Alkalmazások üzembe helyezve a feladatátvevő fürtök, ahol a fürtszoftver kezeli-e az alkalmazás az automatikus feladatátvételt is is.

Ezzel a megközelítéssel az alkalmazás csak akkor aktív egy felhőben, de a szoftver központi telepítése több felhő. Az egyéb felhőkben is készenléti módban az alkalmazás megkezdheti a feladatátvétel elindításakor.

 - RTO: Állásidő idejének mérése másodpercben
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Minimális adatvesztéssel
 - Üzembe helyezési topológia: Aktív/aktív-készenléti

### <a name="fault-tolerance"></a>Hibatűrés

Fizikai redundancia és infrastruktúra-szolgáltatások elérhetőségét csak hardver elleni védelem érdekében az Azure Stack szintű hiba/hibák, például egy lemez, tápegység, hálózati portot vagy csomópont. Azonban ha az alkalmazás mindig elérhetőnek kell lennie, és soha nem elveszíthetik az adatokat, szüksége lesz a hibatűrés megvalósítása az alkalmazás natív módon, vagy további szoftverek használata a hibatűrés engedélyezéséhez.

Először annak biztosítása érdekében a virtuális gépek méretezési csoport üzembe helyezett alkalmazás beállítja a csomópont-szintű leállások ellen. A felhő offline állapotra ellen, ugyanazt az alkalmazást már telepíteni kell egy másik felhőbe, így az továbbra is megszakítás nélkül szolgáltatáskéréseket. Ez a modell gyakran emlegetik úgy egy aktív-aktív központi telepítés.

Ne feledje, hogy minden egyes Azure Stack-felhőben függetlenek egymástól,-e, a felhők mindig számítanak aktív, az infrastruktúra szempontjából. Ebben az esetben az alkalmazás több aktív példány egy vagy több aktív felhők vannak telepítve.

 - RTO: Állásidő nélkül
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Nincs adatvesztés
 - Üzembe helyezési topológia: Active/Active

### <a name="no-recovery"></a>Nincs recovery

Egyes alkalmazások a környezetében előfordulhat, hogy nem kell a nem tervezett leállásokkal vagy adatvesztés elleni védelem. Például a virtuális gépek fejlesztési célokra, és a tesztelés általában nem kell helyreállítani. Egy alkalmazás vagy egy adott virtuális gép védelme nélkül mellett. Az Azure Stack nem kínál biztonsági mentési vagy a virtuális gépek replikálását az alapul szolgáló infrastruktúráról. Az Azure-hoz hasonlóan kell minden egyes előfizetés az egyes virtuális gép védelmét szeretné.

 - RTO: Helyreállíthatatlan
 - HELYREÁLLÍTÁSI IDŐKORLÁT: Teljes adatvesztés

## <a name="recommended-topologies"></a>Ajánlott topológiák

Az Azure Stack üzemelő példány fontos szempontjai:

|     | Ajánlás | Megjegyzések |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Virtuális gépek biztonsági mentése/visszaállítása egy már üzembe helyezte az adatközpontban található külső biztonsági mentési cél | Ajánlott | Kihasználhatja a meglévő biztonsági mentési infrastruktúra és képességeket. Ellenőrizze, hogy a biztonsági mentési infrastruktúra méretezés, hogy további Virtuálisgép-példányok védelméhez készen álljon. Ellenőrizze, hogy a biztonsági mentési infrastruktúra nem szerepel a forrás közelében. A forrás Azure Stack, egy másodlagos Azure Stack-példányt, vagy Azure virtuális gépek is visszaállíthatók. |
| Virtuális gépek biztonsági mentése/visszaállítása egy külső biztonsági mentési cél dedikált az Azure Stackhez | Ajánlott | Új biztonsági mentési infrastruktúra vagy dedikált biztonsági mentési infrastruktúra kiépítése az Azure Stack vásárolhatja meg. Ellenőrizze, hogy a biztonsági mentési infrastruktúra nem szerepel a forrás közelében. A forrás Azure Stack, egy másodlagos Azure Stack-példányt, vagy Azure virtuális gépek is visszaállíthatók. |
| Közvetlenül a globális Azure-ban vagy egy megbízható szolgáltató a virtuális gépek biztonsági mentése/visszaállítása | Ajánlott | Mindaddig, amíg az adatvédelem és a szabályozási követelményeknek is megfeleljen, a biztonsági másolatok tárolhatók a globális Azure-ban vagy egy megbízható szolgáltató. Ideális esetben a szolgáltató is fut-e az Azure Stack így konzisztencia működési felületen történő visszaállításához. |
| Replikálás és feladatátvételi virtuális gépek egy külön Azure Stack-példányt | Ajánlott | A feladatátvételi esetben szüksége lesz egy második teljesen működőképes Azure Stack-felhővel rendelkezik, kiterjesztett alkalmazás állásidő elkerülése érdekében. |
| Replikálás és feladatátvételi virtuális gépek közvetlenül az Azure-ban vagy egy megbízható szolgáltató | Ajánlott | Mindaddig, amíg az adatvédelem és a szabályozási követelményeknek is megfelel, replikálja az adatokat globális Azure-ban vagy egy megbízható szolgáltató. Ideális esetben a szolgáltató is fut-e az Azure Stack így konzisztencia működési élményt nyújt a feladatátvételt követően. |
| Az alkalmazás adatait az azonos Azure Stack-felhőben lévő biztonsági mentési cél üzembe helyezése | Nem ajánlott. | Kerülje a azonos Azure Stack-felhőben lévő biztonsági másolatok tárolásához. A felhő nem tervezett üzemkimaradások megmaradhat az elsődleges és a biztonsági mentési adatokat. Ha szeretné üzembe helyezni a biztonsági mentési cél (optimalizálást a biztonsági mentési és visszaállítási célokra) egy olyan virtuális berendezésre, választja, gondoskodnia kell minden adatot folyamatosan másol egy külső biztonsági mentési helyre. |
| Fizikai tartalék készülék, ahol az Azure Stack megoldás telepítve van-e az azonos rack üzembe helyezése | Nem támogatott | Ezen a ponton az időben bármely egyéb eszközök nem lehet csatlakozni, amelyek nem részei az eredeti megoldásban kapcsolók tetején. |

## <a name="next-steps"></a>További lépések

Ez a cikk általános irányelveket előírt telepíthető az Azure Stack felhasználói virtuális gépek védelmére. A felhasználói virtuális gépek védelme az Azure-szolgáltatások használatával kapcsolatos információkért tekintse meg:

 - [Azure Backup szolgáltatás használatával a fájlok és alkalmazások biztonsági mentése az Azure Stackben](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Stack az Azure Backup Server támogatása](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Stack támogatása az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)  

A partner-termékek nyújtanak védelmet a virtuális gép az Azure Stacken kapcsolatos további információkért tekintse meg a "[védelméről, alkalmazásokat és adatokat az Azure Stackben](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
