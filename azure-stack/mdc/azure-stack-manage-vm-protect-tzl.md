---
title: Azure Stack üzembe helyezett virtuális gépek elleni védelem | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre helyreállítási tervet az adatvesztéssel és a nem tervezett állásidővel Azure Stack üzembe helyezett virtuális gépek védelme érdekében.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: cfee01600d9db4017e56b7e53c9f90351b3bb323
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414044"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Azure Stack hub-on üzembe helyezett virtuális gépek elleni védelem

Ez a cikk útmutatóként használható olyan virtuális gépek (VM-EK) védelmére, amelyeket a felhasználók Azure Stack hubhoz telepíthetnek.

Az adatvesztés és a nem tervezett leállások elleni védelem érdekében hozzon létre egy adatvédelmi és vész-helyreállítási tervet Azure Stack hub virtuálisgép-alapú alkalmazásaihoz. A megvalósított védelmi terv az alkalmazás üzleti követelményeitől és kialakításától függ. A tervnek követnie kell a szervezete átfogó üzletmenet-folytonossági és vész-helyreállítási (BC/DR) stratégiája által létrehozott keretrendszert. Az Azure Stack hub BC/DR szempontjainak áttekintését lásd: Azure Stack: az üzletmenet folytonosságával és a vész-helyreállítással kapcsolatos megfontolások.

## <a name="application-recovery-objectives"></a>Alkalmazás-helyreállítási célkitűzések

Határozza meg, hogy a szervezet hány állásidőt és adatvesztést képes elviselni az egyes alkalmazásokhoz. Az állásidő és az adatvesztés mennyiségi meghatározásával létrehozhat egy helyreállítási tervet, amely lekicsinyíti a katasztrófák hatását a szervezeten belül. Az egyes alkalmazásokhoz vegye figyelembe a következőket:

- **Helyreállítási időre vonatkozó célkitűzés (RTO)**\
    A RTO az a maximális elfogadható idő, ameddig egy alkalmazás nem érhető el egy incidens után. Például egy 90 perces RTO azt jelenti, hogy az alkalmazást egy, a katasztrófa elejétől 90 percen belül futó állapotba kell állítania. Ha alacsony RTO rendelkezik, egy másik üzemelő példányt folyamatosan készenléti állapotban tarthat a regionális kimaradások elleni védelem érdekében.

- **Helyreállítási pont célkitűzése (RPO)**\
    A helyreállítási időkorlát (RPO) a katasztrófa során elfogadható adatveszteség maximális ideje. Ha például egyetlen adatbázisban tárolja az adatbiztonsági mentést, és nem végez replikálást más adatbázisokra, akkor akár egy órányi adat is elvész.

Végezzen értékelést az egyes alkalmazások RTO és RPO definiálásához.

Egy másik fontos mérőszám azt jelenti, hogy a **helyreállításhoz szükséges idő** (MTTR), amely az alkalmazás meghibásodás utáni visszaállításának átlagos időtartama. A MTTR egy rendszer empirikus értéke. Ha a MTTR túllépi a RTO, akkor a rendszer meghibásodása elfogadhatatlan üzleti fennakadást okoz, mivel nem lehet visszaállítani a rendszer a megadott RTO belül.

## <a name="protection-options-for-iaas-vms"></a>Védelmi lehetőségek a IaaS virtuális gépekhez

### <a name="backup-restore"></a>Biztonsági mentés visszaállítása

A virtuálisgép-alapú alkalmazások leggyakoribb védelmi sémája a Backup szoftver használata. A virtuális gépek biztonsági mentése általában tartalmazza az operációs rendszert, az operációs rendszer konfigurációját, az alkalmazás bináris fájljait és a virtuális gépen található állandó alkalmazási adattípusokat. A biztonsági másolatok létrehozásához használja a vendég operációs rendszer ügynökét az alkalmazás, az operációs rendszer vagy a fájlrendszer/kötetek rögzítéséhez. A másik módszer az, ha a Azure Stack hub API-kkal való integrációra támaszkodik, a virtuális gép konfigurációjával kapcsolatos információk olvasásához és a virtuális géphez csatolt lemezek pillanatképéhez. Vegye figyelembe, hogy Azure Stack hub nem támogatja közvetlenül a hypervisorból történő biztonsági mentést.

### <a name="planning-your-backup-strategy"></a>A biztonsági mentési stratégia megtervezése

A biztonsági mentési stratégia megtervezése és a méretezési követelmények meghatározása a védeni kívánt virtuálisgép-példányok számának meghatározásával kezdődik. Előfordulhat, hogy a rendszeren lévő összes virtuális gép biztonsági mentése nem a leghatékonyabb módszer az alkalmazások védetté való ellátásához. Azure Stack hub esetén a méretezési csoport vagy rendelkezésre állási csoportba tartozó virtuális gépek nem készíthetők biztonsági mentésre a virtuálisgép-szinten. Ezeket a virtuális gépeket elmúlónak tekinti a rendszer, mivel a virtuális gépek készlete méretezhető vagy kibővíthető. Ideális esetben a megőrzött adattárolók külön tárházban, például adatbázisban vagy objektum-tárolóban találhatók. Ha a kibővíthető architektúrában üzembe helyezett alkalmazások olyan adatmennyiséget tartalmaznak, amelyet meg kell őrizni és védeni kell, akkor az alkalmazás szintjének biztonsági mentésére van szükség az alkalmazás által biztosított natív képességekkel, vagy egy ügynökre támaszkodva.

Fontos szempontok a virtuális gépek Azure Stackon történő biztonsági mentéséhez:

- **Kategorizálás**
  - Vegyünk egy modellt, amelyben a felhasználók a virtuális gépek biztonsági mentését választják.
  - Definiáljon egy helyreállítási szolgáltatói szerződést (SLA-t) az alkalmazások prioritása vagy a vállalatra gyakorolt hatás alapján.
- **Méretezés**
  - Nagy mennyiségű új virtuális gép (ha biztonsági mentés szükséges) esetén érdemes lehet lépcsőzetes biztonsági mentést készíteni.
  - Kiértékelheti azokat a biztonsági mentési termékeket, amelyek hatékonyan rögzíthetik és továbbítják a biztonsági mentési adatokat, hogy a megoldáson az erőforrások tartalmait csökkentsék
  - Kiértékelheti a biztonsági mentési adatokat a növekményes vagy differenciált biztonsági mentésekkel hatékonyan tároló biztonsági mentési termékeket, hogy a teljes biztonsági mentést a környezetben lévő összes virtuális gépen el lehessen végezni.
- **Visszaállítás**
  - A biztonsági mentési termékek helyreállítják a virtuális lemezeket, az alkalmazásadatok egy meglévő virtuális GÉPEN, illetve a teljes VM-erőforrást és a társított virtuális lemezeket. A szükséges helyreállítási séma attól függ, hogy miként szeretné visszaállítani az alkalmazást. Előfordulhat például, hogy könnyebben újra üzembe helyezni az SQL Servert egy sablonból, majd visszaállítja az adatbázisokat a teljes virtuális gép vagy virtuális gépek készletének visszaállítása helyett.

### <a name="replicationmanual-failover"></a>Replikáció/manuális feladatátvétel

A helyreállítás támogatásának másik módja, ha az adatreplikálás egy másik környezetbe történik. Az adatai az alkalmazásra, például az adatbázis-replikálásra vagy a vendég operációs rendszerére az ügynök használatával vagy a virtuálisgép-szinten az Azure Stack hub API-k integrálásával. Vészhelyzet esetén a másodlagos helyre történő feladatátvétel szükséges. A feladatátvételt natív módon kezelheti az alkalmazás, például az SQL rendelkezésre állási csoportjaival vagy a vendég operációs rendszer szintjén az ügynökök vagy a fürtök technológiájának használatával, vagy a virtuális gép szintjén védelmi termék használatával.

### <a name="high-availabilityautomatic-failover"></a>Magas rendelkezésre állás/automatikus feladatátvétel

Azok az alkalmazások, amelyek natív módon támogatják a magas rendelkezésre állást, vagy a fürt szoftverét használják a csomópontok magas rendelkezésre állásának eléréséhez, egy Azure Stack hub-ban vagy több Azure Stack hub-példányon is üzembe helyezhetők a virtuális gépek csoportjai között. Az alkalmazások forgalmának megfelelő irányításához minden esetben szükség van bizonyos szintű terheléselosztásra. Ebben a konfigurációban az alkalmazás képes automatikusan helyreállítani a hibákat. Helyi hardverhiba esetén Azure Stack hub-infrastruktúra magas rendelkezésre állást és hibatűrést valósít meg a fizikai infrastruktúrában. Számítási szintű hibák esetén Azure Stack hub N-1 konfigurációban több csomópontot használ egy méretezési egységben. A virtuális gép szintjén, a rendelkezésre állás és a méretezési csoportok modell a méretezési egység egyes csomópontjait tartalék tartományként adja meg a csomópontok közötti affinitás biztosításához, így a csomóponti hibák nem foglalnak le elosztott alkalmazást.

### <a name="no-protection"></a>Nincs védelem

Előfordulhat, hogy egyes alkalmazások nem rendelkeznek megőrzésre szoruló adattal. Például a fejlesztéshez és teszteléshez használt virtuális gépeket általában \' nem kell helyreállítani. Egy másik példa egy állapot nélküli alkalmazás, amely meghibásodás esetén újra üzembe helyezhető egy CI/CD-folyamatból. Fontos, hogy azonosítsa azokat az alkalmazásokat, amelyek nem igényelnek védelmet a virtuális gépek szükségtelen védelme érdekében.

<!-- ## Recommended topologies

Important considerations for your Azure Stack deployment: -->

## <a name="next-steps"></a>További lépések

Ez a cikk általános iránymutatásokat ismertetett a Azure Stackon üzembe helyezett felhasználói virtuális gépek védelméhez. További információ az Azure-szolgáltatásoknak a felhasználói virtuális gépek számára történő használatáról:

- [A folyamatos üzletmenet és a vészhelyreállítás megfontolandó szempontjai](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="partner-products"></a>Partneri termékek

- [Azure Stack Datacenter Integration partner ökoszisztémájának Adatlapja](https://aka.ms/azurestackbcdrpartners)
- Ha többet szeretne megtudni a Azure Stack virtuálisgép-védelmet biztosító partneri termékekről, tekintse meg az [alkalmazások és adatok védelme Azure stackon](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)című témakört.
