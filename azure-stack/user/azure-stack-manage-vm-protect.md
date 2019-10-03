---
title: Azure Stack üzembe helyezett virtuális gépek elleni védelem | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre helyreállítási tervet az adatvesztéssel és a nem tervezett állásidővel Azure Stack üzembe helyezett virtuális gépek védelme érdekében.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: f633f000968fcf4f373fc502898fa18084f93f80
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824302"
---
# <a name="protect-vms-deployed-on-azure-stack"></a>Azure Stack üzembe helyezett virtuális gépek elleni védelem

Ez a cikk útmutatóként használható olyan virtuális gépek (VM-EK) védelmére, amelyeket a felhasználók Azure Stack telepíthetnek.


Az adatvesztés és a nem tervezett leállások elleni védelem érdekében egy biztonsági mentési vagy vész-helyreállítási tervet kell megvalósítani a felhasználói alkalmazásokhoz és adataihoz. Ez a csomag minden alkalmazás esetében egyedi lehet, de a szervezet átfogó üzletmenet-folytonossági és vész-helyreállítási (BC/DR) stratégiája által létrehozott keretrendszert követ. Jó kiindulási pont a [Azure stack: Az üzletmenet folytonosságának és a vész-helyreállítási megfontolásoknak a no__t-0.

## <a name="azure-stack-infrastructure-recovery"></a>Infrastruktúra-helyreállítás Azure Stack

A felhasználók a Azure Stack infrastrukturális szolgáltatásaitól függetlenül védik a virtuális gépek védelmét.

A Azure Stack infrastruktúra-szolgáltatások helyreállítási terve **nem** tartalmazza a felhasználói virtuális gépek, a tárolási fiókok vagy az adatbázisok helyreállítását. Az alkalmazás tulajdonosaként Ön felelős az alkalmazások és az adatai helyreállítási tervének megvalósításában.

Ha a Azure Stack-felhő hosszabb ideig offline állapotban van, vagy tartósan helyreállíthatatlan, a következő helyre kell állítania egy helyreállítási tervet:

* Minimális állásidőt biztosít.
* Megtartja a kritikus virtuális gépeket, például az adatbázis-kiszolgálókat, amelyeken fut.
* Lehetővé teszi az alkalmazások számára a karbantartási felhasználói kérések fenntartását.

A Azure Stack felhő üzemeltetője feladata az alapul szolgáló Azure Stack infrastruktúra és szolgáltatások helyreállítási tervének létrehozása. További információ: [helyreállítás a katasztrofális ADATVESZTÉSBŐL](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>A IaaS virtuális gépekkel kapcsolatos megfontolások
A IaaS VM-ben telepített operációs rendszer korlátozza, hogy mely termékek használhatók a benne található adatvédelemhez. A Windows-alapú IaaS virtuális gépek esetében a Microsoft és a partner termékek használatával biztosíthatja az adatvédelmet. A Linux-alapú IaaS virtuális gépek esetében az egyetlen lehetőség a partneri termékek használata. Tekintse meg a [BC/Dr partnereknek szóló adatlapot, amely a Azure stack ellenőrzött termékeit](https://aka.ms/azurestackbcdrpartners)használja.

## <a name="sourcetarget-combinations"></a>Forrás/cél kombinációk

Minden Azure Stack felhő üzembe helyezése egy adatközpontban történik. Az alkalmazások helyreállításához külön környezet szükséges. A helyreállítási környezet lehet egy másik Azure Stack felhő egy másik adatközpontban vagy az Azure nyilvános felhőben. Az adatokra vonatkozó szuverenitási és adatvédelmi követelmények határozzák meg az alkalmazás helyreállítási környezetét. Az egyes alkalmazások védelmének engedélyezésekor rugalmasan választhatja ki az egyes alkalmazásokhoz tartozó helyreállítási lehetőségeket. Az egyes előfizetésekben lévő alkalmazások egy másik adatközpontba is készíthetők. Egy másik előfizetésben az Azure nyilvános felhőbe replikálhatja az adatforrásokat.

Tervezze meg a biztonsági mentési és vész-helyreállítási stratégiát az egyes alkalmazásokhoz, hogy meghatározza az egyes alkalmazások célját. A helyreállítási terv segít a szervezetnek a helyszíni és a projekt-felhasználáshoz szükséges tárolókapacitás méretének megfelelő méretezésében a nyilvános felhőben.

|  | Globális Azure | A CSP Datacenter rendszerbe telepített és a CSP által üzemeltetett Azure Stack | Az ügyfél-adatközpontba telepített és az ügyfél által üzemeltetett Azure Stack |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **A CSP Datacenter rendszerbe telepített és a CSP által üzemeltetett Azure Stack** | A rendszer telepíti a felhasználói virtuális gépeket a CSP által működtetett Azure Stackra.<br><br>A felhasználói virtuális gépek biztonsági másolatból lettek visszaállítva, vagy közvetlenül az Azure-ba kerültek feladatátvételre. | A CSP a Azure Stack elsődleges és másodlagos példányait a saját adatközpontokban üzemelteti.<br><br>A rendszer visszaállítja a felhasználói virtuális gépeket, vagy feladatátvételt a két Azure Stack példány között. | A CSP az elsődleges helyen Azure Stack működik.<br><br>Az ügyfél adatközpontja a visszaállítási vagy feladatátvételi cél. |
| **Az ügyfél-adatközpontba telepített és az ügyfél által üzemeltetett Azure Stack** | A felhasználói virtuális gépek üzembe helyezése az ügyfél által üzemeltetett Azure Stackon történik.<br><br>A felhasználói virtuális gépek biztonsági másolatból lettek visszaállítva, vagy közvetlenül az Azure-ba kerültek feladatátvételre. | Az ügyfél az elsődleges helyen Azure Stack működik.<br><br>A CSP adatközpontja a visszaállítási vagy feladatátvételi cél. | Az ügyfél a Azure Stack elsődleges és másodlagos példányait a saját adatközpontokban üzemelteti.<br><br>A rendszer visszaállítja a felhasználói virtuális gépeket, vagy feladatátvételt a két Azure Stack példány között. |

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

A virtuálisgép-alapú alkalmazások leggyakoribb védelmi sémája a Backup szoftver használata. A virtuális gépek biztonsági mentése általában tartalmazza az operációs rendszert, az operációs rendszer konfigurációját, az alkalmazás bináris fájljait és az alkalmazásadatok. A biztonsági mentéseket a kötetek, lemezek vagy a teljes virtuális gép pillanatképével hozza létre a rendszer. A Azure Stack lehetővé teszi a biztonsági mentés rugalmasságát a vendég operációs rendszer kontextusában, illetve a Azure Stack Storage és a számítási API-k között. Azure Stack nem támogatja a biztonsági másolatok meghozatalát a hypervisor szintjén.
 
![Backup-restor](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Az alkalmazás helyreállításához egy vagy több virtuális gépet ugyanarra a felhőre vagy egy új felhőre kell visszaállítani. Az adatközpontban vagy a nyilvános felhőben is megcélozhat egy felhőt. Az Ön által választott felhő teljes mértékben a vezérlőn belül van, és az adatvédelemre és a szuverenitásra vonatkozó követelményeken alapul.

 - RTO Órákban mért állásidő
 - RPO Változó adatvesztés (a biztonsági mentési gyakoriságtól függően)
 - Üzembe helyezési topológia: Aktív/passzív

#### <a name="planning-your-backup-strategy"></a>A biztonsági mentési stratégia megtervezése

A biztonsági mentési stratégia megtervezése és a méretezési követelmények meghatározása a védeni kívánt virtuálisgép-példányok számának meghatározásával kezdődik. Az összes virtuális gép biztonsági mentése egy környezet összes kiszolgálóján közös stratégia. Azure Stack esetében azonban vannak olyan virtuális gépek, amelyekről biztonsági másolatot kell készíteni. Egy méretezési csoportba tartozó virtuális gépek például olyan időszakos erőforrásoknak számítanak, amelyek eltérhetnek, és néha értesítés nélkül. A védeni kívánt tartós adattárakat külön adattár tárolja, például egy adatbázis vagy egy objektum tárolóban.

Fontos szempontok a virtuális gépek Azure Stackon történő biztonsági mentéséhez:

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

 - RTO Percek alatt mért állásidő
 - RPO Változó adatvesztés (a replikálási gyakoriságtól függően)
 - Üzembe helyezési topológia: Aktív/passzív készenlét
 
### <a name="high-availabilityautomatic-failover"></a>Magas rendelkezésre állás/automatikus feladatátvétel

Olyan alkalmazások esetében, amelyekben a vállalata csak néhány másodpercet vagy percet vesz igénybe, és minimális adatvesztést okoz, vegye figyelembe a magas rendelkezésre állási konfigurációt. A magas rendelkezésre állású alkalmazások célja, hogy gyorsan és automatikusan helyreállítsa a hibákat. Helyi hardveres hibák esetén Azure Stack infrastruktúra a fizikai hálózat magas rendelkezésre állását valósítja meg két felső szintű rack kapcsoló használatával. Számítási szintű hibák esetén Azure Stack egy méretezési egységben több csomópontot használ. A virtuális gépek szintjén a méretezési csoportokat a tartalék tartományokkal együtt használva biztosíthatja, hogy a csomópont-meghibásodások ne használják le az alkalmazást.

A méretezési csoportokkal együtt az alkalmazásnak natív módon támogatnia kell a magas rendelkezésre állást, vagy támogatnia kell a fürtözési szoftverek használatát. Például a Microsoft SQL Server támogatja a magas rendelkezésre állást natív módon az adatbázisok számára szinkron véglegesítő módban. Ha azonban csak az aszinkron replikációt támogatja, a rendszer némi adatvesztést okoz. Az alkalmazások olyan feladatátvevő fürtben is üzembe helyezhetők, ahol a fürtözési szoftver kezeli az alkalmazás automatikus feladatátvételét.

Ennek a módszernek a használatával az alkalmazás csak egy felhőben aktív, de a szoftver több felhőben is üzembe helyezhető. A többi felhő készenléti állapotban van, készen áll az alkalmazás elindítására, amikor a feladatátvétel aktiválódik.

 - RTO Másodpercek alatt mért állásidő
 - RPO Minimális adatvesztés
 - Üzembe helyezési topológia: Aktív/aktív készenlét

### <a name="fault-tolerance"></a>Hibatűrés

Azure Stack a fizikai redundancia és az infrastruktúra-szolgáltatás rendelkezésre állása csak a hardveres szintű hibákra/hibákra, például a lemezre, a tápegységre, a hálózati portra vagy a csomópontra. Ha azonban az alkalmazásnak mindig elérhetőnek kell lennie, és soha nem veszít semmilyen adatvesztést, natív módon kell végrehajtania a hibatűrést az alkalmazásban, vagy további szoftvereket kell használnia a hibatűrés engedélyezéséhez.

Először is gondoskodnia kell arról, hogy az alkalmazás virtuális gépei a méretezési csoportok használatával legyenek telepítve a csomópont szintű hibák elleni védelemhez. Annak érdekében, hogy a felhővel szembeni védelem offline állapotba kerüljön, ugyanazt az alkalmazást már egy másik felhőbe kell telepíteni, hogy a rendszer megszakítás nélkül folytassa a kérelmek kiszolgálását. Ezt a modellt általában aktív-aktív telepítésnek nevezzük.

Ne feledje, hogy minden Azure Stack-felhő egymástól független, így a felhők mindig aktívnak számítanak az infrastruktúra szempontjából. Ebben az esetben az alkalmazás több aktív példánya van telepítve egy vagy több aktív felhőben.

 - RTO Nincs leállás
 - RPO Nincs adatvesztés
 - Üzembe helyezési topológia: Aktív/aktív

### <a name="no-recovery"></a>Nincs helyreállítás

Előfordulhat, hogy a környezet egyes alkalmazásai nem igényelnek védelmet a nem tervezett leállások vagy az adatvesztés ellen. Például a fejlesztéshez és teszteléshez használt virtuális gépeket általában nem kell helyreállítani. Az Ön döntése az alkalmazás vagy egy adott virtuális gép védelmének védelme nélkül. Azure Stack nem biztosít virtuális gépek biztonsági mentését vagy replikálását az alapul szolgáló infrastruktúrából. Az Azure-hoz hasonlóan az egyes előfizetésekben lévő virtuális gépek védelméhez is be kell jelentkeznie.

 - RTO Helyreállíthatatlan
 - RPO Teljes adatvesztés

## <a name="recommended-topologies"></a>Ajánlott topológiák

Fontos szempontok a Azure Stack üzembe helyezéséhez:

|     | Ajánlás | Megjegyzések |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Virtuális gépek biztonsági mentése/visszaállítása az adatközpontban már üzembe helyezett külső biztonsági mentési célra | Ajánlott | Használja ki a meglévő biztonsági mentési infrastruktúrát és az üzemeltetési képességeket. Ügyeljen arra, hogy a biztonsági mentési infrastruktúrát úgy méretezi, hogy az készen álljon a további virtuálisgép-példányok elleni védelemre. Ügyeljen arra, hogy a biztonsági mentési infrastruktúra ne legyen közel a forráshoz. A virtuális gépeket visszaállíthatja a forrás-Azure Stackra, egy másodlagos Azure Stack-példányra vagy az Azure-ra. |
| Virtuális gépek biztonsági mentése/visszaállítása a Azure Stack dedikált külső biztonsági mentési célra | Ajánlott | Új biztonsági mentési infrastruktúrát vásárolhat, vagy kiépítheti Azure Stack számára a dedikált biztonsági mentési infrastruktúrát. Ügyeljen arra, hogy a biztonsági mentési infrastruktúra ne legyen közel a forráshoz. A virtuális gépeket visszaállíthatja a forrás-Azure Stackra, egy másodlagos Azure Stack-példányra vagy az Azure-ra. |
| Virtuális gépek biztonsági mentése/visszaállítása közvetlenül a globális Azure-ba vagy egy megbízható szolgáltatóhoz | Ajánlott | A biztonsági mentések a globális Azure-ban vagy egy megbízható szolgáltatóban tárolhatók, feltéve, hogy megfelel az adatvédelmi és szabályozási követelményeknek. Ideális esetben a szolgáltató Azure Stack is fut, így a visszaállítás során konzisztens lesz a működési élményben. |
| Virtuális gépek replikálása vagy feladatátvétele egy külön Azure Stack példányba | Ajánlott | A feladatátvételi esetben egy második Azure Stack-felhőt kell teljes mértékben működőképesnek lennie, hogy el tudja kerülni a kiterjesztett alkalmazások leállását. |
| Virtuális gépek replikálása/feladatátvétele közvetlenül az Azure-ba vagy egy megbízható szolgáltatóhoz | Ajánlott | Ha az adatvédelmet és a szabályozást is kielégíti, az adatokat a globális Azure-ba vagy egy megbízható szolgáltatóba replikálhatja. Ideális esetben a szolgáltató Azure Stack is fut, így a feladatátvételt követően konzisztens lesz a működési élményben. |
| A biztonsági mentési cél üzembe helyezése ugyanazon a Azure Stack-felhőben az alkalmazás adataival | Nem ajánlott | Ne tárolja a biztonsági mentéseket ugyanazon a Azure Stack-felhőn belül. A felhő nem tervezett leállása megtarthatja az elsődleges adatok és a biztonsági mentési adatok mennyiségét. Ha úgy dönt, hogy a biztonsági mentési célt virtuális készülékként helyezi üzembe (a biztonsági mentés és a visszaállítás optimalizálása érdekében), gondoskodnia kell arról, hogy a rendszer folyamatosan másolja az összes adatforrást egy külső biztonsági mentési helyre. |
| Fizikai biztonsági mentési berendezés üzembe helyezése ugyanabba az állványba, ahol a Azure Stack megoldás telepítve van | Nem támogatott | Jelenleg nem csatlakoztatható más eszközök az eredeti megoldás részét nem képező rack-kapcsolók tetejéhez. |

## <a name="next-steps"></a>További lépések

Ez a cikk általános iránymutatásokat ismertetett a Azure Stackon üzembe helyezett felhasználói virtuális gépek védelméhez. További információ az Azure-szolgáltatásoknak a felhasználói virtuális gépek számára történő használatáról:

 - [Fájlok és alkalmazások biztonsági mentése a Azure Backup használatával Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Stack Azure Backup Server támogatása](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Stack Azure Site Recovery támogatása](https://docs.microsoft.com/azure/site-recovery/)  

Ha többet szeretne megtudni azokról a partneri termékekről, amelyek a Azure Stack virtuálisgép-védelmet kínálnak, tekintse [meg az alkalmazások és az adatok védelme Azure stackon](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)című témakört
