---
title: Azure Stack hub-on üzembe helyezett virtuális gépek elleni védelem
description: Megtudhatja, hogyan hozhat létre helyreállítási tervet az Azure Stack hub-on üzembe helyezett virtuális gépek adatvesztéssel és nem tervezett állásidővel szembeni védelme érdekében.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2019
ms.openlocfilehash: 824352a27ae91b2bf0a351b9dc280c83bfb9d19a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703961"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Azure Stack hub-on üzembe helyezett virtuális gépek elleni védelem

Ez a cikk útmutatóként használható olyan virtuális gépek (VM-EK) védelmére, amelyeket a felhasználók a Azure Stack hubhoz telepíthetnek.


Az adatvesztés és a nem tervezett leállások elleni védelem érdekében egy biztonsági mentési vagy vész-helyreállítási tervet kell megvalósítani a felhasználói alkalmazásokhoz és adataihoz. Ez a csomag minden alkalmazás esetében egyedi lehet, de a szervezet átfogó üzletmenet-folytonossági és vész-helyreállítási (BC/DR) stratégiája által létrehozott keretrendszert követ. Jó kiindulási pont [Azure stack hub: az üzletmenet folytonosságával és a vész-helyreállítással kapcsolatos megfontolások](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="azure-stack-hub-infrastructure-recovery"></a>Azure Stack hub-infrastruktúra helyreállítása

A felhasználók a Azure Stack hub infrastruktúra-szolgáltatásaitól függetlenül védik a virtuális gépek védelmét.

A Azure Stack hub infrastruktúra-szolgáltatások helyreállítási terve **nem** tartalmazza a felhasználói virtuális gépek, a tárolási fiókok vagy az adatbázisok helyreállítását. Az alkalmazás tulajdonosaként Ön felelős az alkalmazások és az adatai helyreállítási tervének megvalósításában.

Ha az Azure Stack hub-felhő hosszabb ideig offline állapotban van, vagy tartósan helyreállíthatatlan, a következő helyre kell állítania egy helyreállítási tervet:

* Minimális állásidőt biztosít.
* Megtartja a kritikus virtuális gépeket, például az adatbázis-kiszolgálókat, amelyeken fut.
* Lehetővé teszi az alkalmazások számára a karbantartási felhasználói kérések fenntartását.

Az Azure Stack hub-felhő üzemeltetője feladata az alapul szolgáló Azure Stack hub-infrastruktúra és-szolgáltatások helyreállítási tervének létrehozása. További információ: [helyreállítás a katasztrofális ADATVESZTÉSBŐL](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>A IaaS virtuális gépekkel kapcsolatos megfontolások
A IaaS VM-ben telepített operációs rendszer korlátozza, hogy mely termékek használhatók a benne található adatvédelemhez. A Windows-alapú IaaS virtuális gépek esetében a Microsoft és a partner termékek használatával biztosíthatja az adatvédelmet. A Linux-alapú IaaS virtuális gépek esetében az egyetlen lehetőség a partneri termékek használata. Tekintse meg [ezt az adatlapot az összes BC/Dr partner számára a Azure stack hub ellenőrzött termékeivel](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Forrás/cél kombinációk

Minden Azure Stack hub-felhő üzembe helyezése egy adatközpontban történik. Az alkalmazások helyreállításához külön környezet szükséges. A helyreállítási környezet lehet egy másik Azure Stack hub-felhő egy másik adatközpontban vagy az Azure nyilvános felhőben. Az adatokra vonatkozó szuverenitási és adatvédelmi követelmények határozzák meg az alkalmazás helyreállítási környezetét. Az egyes alkalmazások védelmének engedélyezésekor rugalmasan választhatja ki az egyes alkalmazásokhoz tartozó helyreállítási lehetőségeket. Az egyes előfizetésekben lévő alkalmazások egy másik adatközpontba is készíthetők. Egy másik előfizetésben az Azure nyilvános felhőbe replikálhatja az adatforrásokat.

Tervezze meg a biztonsági mentési és vész-helyreállítási stratégiát az egyes alkalmazásokhoz, hogy meghatározza az egyes alkalmazások célját. A helyreállítási terv segít a szervezetnek a helyszíni és a projekt-felhasználáshoz szükséges tárolókapacitás méretének megfelelő méretezésében a nyilvános felhőben.

|  | Globális Azure | A CSP Datacenter rendszerbe telepített Azure Stack hub és a CSP által üzemeltetett | Az ügyfél-adatközpontba telepített és az ügyfél által üzemeltetett Azure Stack hub |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **A CSP Datacenter rendszerbe telepített Azure Stack hub és a CSP által üzemeltetett** | A felhasználói virtuális gépek a CSP által működtetett Azure Stack hubhoz vannak telepítve.<br><br>A felhasználói virtuális gépek biztonsági másolatból lettek visszaállítva, vagy közvetlenül az Azure-ba kerültek feladatátvételre. | A CSP az Azure Stack hub elsődleges és másodlagos példányait saját adatközpontokban üzemelteti.<br><br>A rendszer visszaállítja vagy átadja a felhasználói virtuális gépeket a két Azure Stack hub-példány között. | A CSP Azure Stack hubot működtet az elsődleges helyen.<br><br>Az ügyfél adatközpontja a visszaállítási vagy feladatátvételi cél. |
| **Az ügyfél-adatközpontba telepített és az ügyfél által üzemeltetett Azure Stack hub** | A felhasználói virtuális gépek üzembe helyezése az ügyfél által üzemeltetett Azure Stack hubhoz történik.<br><br>A felhasználói virtuális gépek biztonsági másolatból lettek visszaállítva, vagy közvetlenül az Azure-ba kerültek feladatátvételre. | Az ügyfél az elsődleges helyen Azure Stack hub-t üzemeltet.<br><br>A CSP adatközpontja a visszaállítási vagy feladatátvételi cél. | Az ügyfél az Azure Stack hub elsődleges és másodlagos példányait saját adatközpontokban üzemelteti.<br><br>A rendszer visszaállítja vagy átadja a felhasználói virtuális gépeket a két Azure Stack hub-példány között. |

![Forrás – cél kombinációk](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="app-recovery-objectives"></a>Alkalmazás-helyreállítási célkitűzések

Határozza meg, hogy a szervezet hány állásidőt és adatvesztést tud elviselni az egyes alkalmazásokhoz. Az állásidő és az adatvesztés mennyiségi meghatározásával létrehozhat egy helyreállítási tervet, amely lekicsinyíti a katasztrófák hatását a szervezeten belül. Az egyes alkalmazásokhoz vegye figyelembe a következőket:

 - **Helyreállítási időre vonatkozó célkitűzés (RTO)**  
A RTO az a maximális elfogadható idő, ameddig egy alkalmazás nem érhető el egy incidens után. Például egy 90 perces RTO azt jelenti, hogy az alkalmazást egy, a katasztrófa elejétől 90 percen belül futó állapotba kell állítania. Ha alacsony RTO rendelkezik, egy másik üzemelő példányt folyamatosan készenléti állapotban tarthat a regionális kimaradások elleni védelem érdekében.
 - **Helyreállítási pont célkitűzése (RPO)**  
A RPO a katasztrófa során elfogadható adatvesztés maximális időtartama. Ha például egyetlen adatbázisban tárolja az adatbiztonsági mentést, és nem végez replikálást más adatbázisokra, akkor akár egy órányi adat is elvész.

Az RTO-ra és az RPO-ra üzleti követelményként tekinthet. Kockázatértékelés végrehajtása az alkalmazás RTO és RPO meghatározásához.

Egy másik metrika a **helyreállításhoz szükséges idő** (MTTR), amely az alkalmazás meghibásodás utáni visszaállításának átlagos időtartama. A MTTR egy rendszer empirikus értéke. Ha a MTTR túllépi a RTO, akkor a rendszer meghibásodása elfogadhatatlan üzleti fennakadást okoz, mivel nem lehet visszaállítani a rendszer a megadott RTO belül.

### <a name="backup-restore"></a>Backup-restore

A virtuálisgép-alapú alkalmazások leggyakoribb védelmi sémája a Backup szoftver használata. A virtuális gépek biztonsági mentése általában tartalmazza az operációs rendszert, az operációs rendszer konfigurációját, az alkalmazás bináris fájljait és az alkalmazásadatok. A biztonsági mentéseket a kötetek, lemezek vagy a teljes virtuális gép pillanatképével hozza létre a rendszer. A Azure Stack hub segítségével rugalmasan készíthet biztonsági mentést a vendég operációs rendszer kontextusában, vagy az Azure Stack hub Storage és a számítási API-k között. Azure Stack hub nem támogatja a biztonsági másolatok meghozatalát a hypervisor szintjén.
 
![Backup-restor](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Az alkalmazás helyreállításához egy vagy több virtuális gépet ugyanarra a felhőre vagy egy új felhőre kell visszaállítani. Az adatközpontban vagy a nyilvános felhőben is megcélozhat egy felhőt. Az Ön által választott felhő teljes mértékben a vezérlőn belül van, és az adatvédelemre és a szuverenitásra vonatkozó követelményeken alapul.

 - RTO: órákban mért állásidő
 - RPO: változó adatvesztés (a biztonsági mentés gyakorisága függvényében)
 - Üzembe helyezési topológia: aktív/passzív

#### <a name="planning-your-backup-strategy"></a>A biztonsági mentési stratégia megtervezése

A biztonsági mentési stratégia megtervezése és a méretezési követelmények meghatározása a védeni kívánt virtuálisgép-példányok számának meghatározásával kezdődik. Az összes virtuális gép biztonsági mentése egy környezet összes kiszolgálóján közös stratégia. Azure Stack hub esetében azonban vannak olyan virtuális gépek, amelyekről biztonsági másolatot kell készíteni. Egy méretezési csoportba tartozó virtuális gépek például olyan időszakos erőforrásoknak számítanak, amelyek eltérhetnek, és néha értesítés nélkül. A védeni kívánt tartós adattárakat külön adattár tárolja, például egy adatbázis vagy egy objektum tárolóban.

Fontos szempontok a virtuális gépek Azure Stack hub-beli biztonsági mentéséhez:

 - **Kategorizálási**
    - Vegyünk egy modellt, amelyben a felhasználók a virtuális gépek biztonsági mentését választják.
    - A helyreállítási szolgáltatói szerződés (SLA) meghatározása az alkalmazások prioritása vagy a vállalatra gyakorolt hatás alapján.
 - **Méretezés**
    - Nagy mennyiségű új virtuális gép (ha biztonsági mentés szükséges) esetén érdemes lehet lépcsőzetes biztonsági mentést készíteni.
    - Kiértékelheti azokat a biztonsági mentési termékeket, amelyek hatékonyan rögzíthetik és továbbítják a biztonsági mentési adatokat, hogy a megoldáson az erőforrások tartalmait csökkentsék
    - Kiértékelheti a biztonsági mentési adatokat a növekményes vagy differenciált biztonsági mentésekkel hatékonyan tároló biztonsági mentési termékeket, hogy a teljes biztonsági mentést a környezetben lévő összes virtuális gépen el lehessen végezni.
 - **Visszaállítás**
    - A biztonsági mentési termékek helyreállítják a virtuális lemezeket, az alkalmazásadatok egy meglévő virtuális GÉPEN, illetve a teljes VM-erőforrást és a társított virtuális lemezeket. A szükséges helyreállítási séma attól függ, hogy miként szeretné visszaállítani az alkalmazást. Előfordulhat például, hogy könnyebben újra üzembe helyezni az SQL Servert egy sablonból, majd visszaállítja az adatbázisokat a teljes virtuális gép vagy virtuális gépek készletének visszaállítása helyett.

### <a name="replicationmanual-failover"></a>Replikáció/manuális feladatátvétel

A magas rendelkezésre állás támogatásának másik módja, ha az alkalmazás virtuális gépei egy másik felhőbe replikálódnak, és a manuális feladatátvételt használják. Az operációs rendszer, az alkalmazások bináris fájljai és az alkalmazásadatok replikálása a virtuális gép szintjén vagy a vendég operációs rendszer szintjén végezhető el. A feladatátvételt olyan további szoftverek használatával felügyeli, amelyek nem részei az alkalmazásnak.

Ezzel a módszerrel az alkalmazást egy felhőben helyezik üzembe, és a virtuális gépe replikálódik a másik felhőre. Ha a feladatátvételt aktiválták, a másodlagos virtuális gépeket a második felhőben kell bekapcsolni. Bizonyos helyzetekben a feladatátvétel létrehozza a virtuális gépeket, és lemezeket csatol hozzájuk. Ez a folyamat hosszú időt is igénybe vehet, különösen egy többrétegű alkalmazással, amely egy adott indítási sorozatot igényel. Olyan lépések is előfordulhatnak, amelyeket futtatni kell ahhoz, hogy az alkalmazás készen álljon a karbantartási kérelmek elindítására.

![Replikáció – manuális feladatátvétel](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: a leállás percben mérve
 - RPO: változó adatvesztés (a replikálás gyakorisága függvényében)
 - Üzembe helyezési topológia: aktív/passzív készenlét
 
### <a name="high-availabilityautomatic-failover"></a>Magas rendelkezésre állás/automatikus feladatátvétel

Olyan alkalmazások esetében, amelyekben a vállalata csak néhány másodpercet vagy percet vesz igénybe, és minimális adatvesztést okoz, vegye figyelembe a magas rendelkezésre állási konfigurációt. A magas rendelkezésre állású alkalmazások célja, hogy gyorsan és automatikusan helyreállítsa a hibákat. Helyi hardverhiba esetén a Azure Stack hub-infrastruktúra a magas rendelkezésre állást valósítja meg a fizikai hálózatban két felső szintű rack kapcsoló használatával. Számítási szintű hibák esetén Azure Stack hub több csomópontot használ egy méretezési egységben. A virtuális gépek szintjén a méretezési csoportokat a tartalék tartományokkal együtt használva biztosíthatja, hogy a csomópont-meghibásodások ne használják le az alkalmazást.

A méretezési csoportokkal együtt az alkalmazásnak natív módon támogatnia kell a magas rendelkezésre állást, vagy támogatnia kell a fürtözési szoftverek használatát. Például a Microsoft SQL Server támogatja a magas rendelkezésre állást natív módon az adatbázisok számára szinkron véglegesítő módban. Ha azonban csak az aszinkron replikációt támogatja, a rendszer némi adatvesztést okoz. Az alkalmazások olyan feladatátvevő fürtben is üzembe helyezhetők, ahol a fürtözési szoftver kezeli az alkalmazás automatikus feladatátvételét.

Ennek a módszernek a használatával az alkalmazás csak egy felhőben aktív, de a szoftver több felhőben is üzembe helyezhető. A többi felhő készenléti állapotban van, készen áll az alkalmazás elindítására, amikor a feladatátvétel aktiválódik.

 - RTO: a leállás másodpercben mérve
 - RPO: minimális adatvesztés
 - Üzembe helyezési topológia: aktív/aktív készenléti

### <a name="fault-tolerance"></a>Hibatűrés

Azure Stack hub fizikai redundancia és az infrastruktúra-szolgáltatás rendelkezésre állása csak a hardveres szintű hibák/hibák, például a lemez, a tápegység, a hálózati port vagy a csomópont esetében biztosít védelmet. Ha azonban az alkalmazásnak mindig elérhetőnek kell lennie, és soha nem veszít semmilyen adatvesztést, natív módon kell végrehajtania a hibatűrést az alkalmazásban, vagy további szoftvereket kell használnia a hibatűrés engedélyezéséhez.

Először is gondoskodnia kell arról, hogy az alkalmazás virtuális gépei a méretezési csoportok használatával legyenek telepítve a csomópont szintű hibák elleni védelemhez. Annak érdekében, hogy a felhővel szembeni védelem offline állapotba kerüljön, ugyanazt az alkalmazást már egy másik felhőbe kell telepíteni, hogy a rendszer megszakítás nélkül folytassa a kérelmek kiszolgálását. Ezt a modellt általában aktív-aktív telepítésnek nevezzük.

Ne feledje, hogy minden Azure Stack hub-felhő egymástól független, így a felhők mindig aktívnak számítanak az infrastruktúra szempontjából. Ebben az esetben az alkalmazás több aktív példánya van telepítve egy vagy több aktív felhőben.

 - RTO: nincs leállás
 - RPO: Nincs adatvesztés
 - Üzembe helyezési topológia: aktív/aktív

### <a name="no-recovery"></a>Nincs helyreállítás

Előfordulhat, hogy a környezet egyes alkalmazásai nem igényelnek védelmet a nem tervezett leállások vagy az adatvesztés ellen. Például a fejlesztéshez és teszteléshez használt virtuális gépeket általában nem kell helyreállítani. Az Ön döntése az alkalmazás vagy egy adott virtuális gép védelmének védelme nélkül. Azure Stack hub nem biztosít virtuális gépek biztonsági mentését vagy replikálását az alapul szolgáló infrastruktúrából. Az Azure-hoz hasonlóan az egyes előfizetésekben lévő virtuális gépek védelméhez is be kell jelentkeznie.

 - RTO: helyreállíthatatlan
 - RPO: teljes adatvesztés

## <a name="recommended-topologies"></a>Ajánlott topológiák

Fontos szempontok az Azure Stack hub üzembe helyezéséhez:

|     | Ajánlás | Megjegyzések |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Virtuális gépek biztonsági mentése/visszaállítása az adatközpontban már üzembe helyezett külső biztonsági mentési célra | Ajánlott | Használja ki a meglévő biztonsági mentési infrastruktúrát és az üzemeltetési képességeket. Ügyeljen arra, hogy a biztonsági mentési infrastruktúrát úgy méretezi, hogy az készen álljon a további virtuálisgép-példányok elleni védelemre. Ügyeljen arra, hogy a biztonsági mentési infrastruktúra ne legyen közel a forráshoz. A virtuális gépeket visszaállíthatja a forrás Azure Stack hubhoz, egy másodlagos Azure Stack hub-példányra vagy az Azure-ra. |
| Virtuális gépek biztonsági mentése/visszaállítása a Azure Stack hub számára dedikált külső biztonsági mentési célra | Ajánlott | Új biztonsági mentési infrastruktúrát vásárolhat, vagy kiépítheti az Azure Stack hub dedikált biztonsági mentési infrastruktúráját. Ügyeljen arra, hogy a biztonsági mentési infrastruktúra ne legyen közel a forráshoz. A virtuális gépeket visszaállíthatja a forrás Azure Stack hubhoz, egy másodlagos Azure Stack hub-példányra vagy az Azure-ra. |
| Virtuális gépek biztonsági mentése/visszaállítása közvetlenül a globális Azure-ba vagy egy megbízható szolgáltatóhoz | Ajánlott | A biztonsági mentések a globális Azure-ban vagy egy megbízható szolgáltatóban tárolhatók, feltéve, hogy megfelel az adatvédelmi és szabályozási követelményeknek. Ideális esetben a szolgáltató a Azure Stack hub-t is futtatja, így a visszaállítás során konzisztens lesz a működési élményben. |
| Virtuális gépek replikálása vagy feladatátvétele egy külön Azure Stack hub-példányba | Ajánlott | A feladatátvételi esetben egy második Azure Stack hub-felhőnek teljesen működőképesnek kell lennie, hogy el tudja kerülni a kiterjesztett alkalmazások leállását. |
| Virtuális gépek replikálása/feladatátvétele közvetlenül az Azure-ba vagy egy megbízható szolgáltatóhoz | Ajánlott | Ha az adatvédelmet és a szabályozást is kielégíti, az adatokat a globális Azure-ba vagy egy megbízható szolgáltatóba replikálhatja. Ideális esetben a szolgáltató a Azure Stack hub-t is futtatja, így a feladatátvétel után konzisztens lesz a működési élményben. |
| A biztonsági mentési cél üzembe helyezése ugyanazon a Azure Stack hub-felhőben az alkalmazás adataival | Nem ajánlott | Ne tárolja a biztonsági mentéseket ugyanazon a Azure Stack hub-felhőn belül. A felhő nem tervezett leállása megtarthatja az elsődleges adatok és a biztonsági mentési adatok mennyiségét. Ha úgy dönt, hogy a biztonsági mentési célt virtuális készülékként helyezi üzembe (a biztonsági mentés és a visszaállítás optimalizálása érdekében), gondoskodnia kell arról, hogy a rendszer folyamatosan másolja az összes adatforrást egy külső biztonsági mentési helyre. |
| Fizikai biztonsági mentési berendezés üzembe helyezése ugyanabba az állványba, ahol a Azure Stack hub-megoldás telepítve van | Nem támogatott | Jelenleg nem csatlakoztatható más eszközök az eredeti megoldás részét nem képező rack-kapcsolók tetejéhez. |

## <a name="next-steps"></a>Következő lépések

Ez a cikk az Azure Stack hub-on üzembe helyezett felhasználói virtuális gépek védelmére vonatkozó általános irányelveket ismertetett. További információ az Azure-szolgáltatásoknak a felhasználói virtuális gépek számára történő használatáról:

- [A folyamatos üzletmenet és a vészhelyreállítás megfontolandó szempontjai](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Azure Backup Server
 - [Fájlok és alkalmazások biztonsági mentése a Azure Stack hub Azure Backup használatával](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Stack hub Azure Backup Server támogatása](https://docs.microsoft.com/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Azure Stack hub Azure Site Recovery támogatása](https://docs.microsoft.com/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Partneri termékek
 - [Azure Stack hub Datacenter Integration partner ökoszisztémájának Adatlapja](https://aka.ms/azurestackbcdrpartners)

Ha többet szeretne megtudni azokról a partneri termékekről, amelyek Azure Stack hub virtuálisgép-védelmét nyújtanak, tekintse [meg az alkalmazások és adatok védelme Azure stack hub-on](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)című témakört.
