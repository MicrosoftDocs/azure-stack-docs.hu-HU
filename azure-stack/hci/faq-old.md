---
title: Azure Stack HCI – gyakori kérdések
description: Azure Stack HCI – gyakori kérdések.
ms.topic: conceptual
author: JohnCobb1
ms.author: v-johcob
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 1/21/2021
ms.openlocfilehash: 6c84bad0de72235c6ccde1b8c23016afeb956cba
ms.sourcegitcommit: 7ee28fad5b8ba628b1a7dc3d82cabfc36aa62f0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250642"
---
# <a name="azure-stack-hci-faq"></a>Azure Stack HCI – gyakori kérdések
A Azure Stack HCI GYIK tartalmaz egy szakaszt Azure Stack HCI-kapcsolatról és egy általános gyakori kérdések szakaszáról.

## <a name="azure-stack-hci-connectivity"></a>Azure Stack HCI-kapcsolat
A Azure Stack HCI egy Azure Hybrid Service-ként szállított helyszíni hiperkonvergens infrastruktúra-verem. Telepíti a Azure Stack HCI szoftvert a helyszínen felügyelt fizikai kiszolgálókon. Ezután kapcsolódhat az Azure-hoz felhőalapú figyelési, támogatási, számlázási és opcionális felügyeleti és biztonsági funkciókkal. Ez a gyakori kérdések azt ismertetik, hogy Azure Stack HCI hogyan használja a felhőt a kapcsolódási követelményekkel és viselkedéssel kapcsolatos gyakori kérdések megválaszolásával.

### <a name="your-data-stays-on-premises"></a>Az adatai a helyszínen maradnak

**A rendszer elküldi a Azure Stack HCI-ben tárolt adataikat a felhőbe?**

Nem. A vásárlói adatokat, például a helyszíni virtuális gépek nevét, metaadatait, konfigurációját és tartalmát soha nem küldi el a felhőbe, hacsak nem kapcsol be kifejezetten erre a célra olyan további szolgáltatásokat, mint például a Azure Backup vagy a Azure Site Recovery, vagy ha nem regisztrálja ezeket a virtuális gépeket külön a felhőalapú felügyeleti szolgáltatásokhoz, például az Azure archoz.

Ha többet szeretne megtudni a gyűjtött diagnosztikai adatokról, hogy Azure Stack HCI biztonságos, naprakész és a várt módon működjön, tekintse meg a következőt: [Azure stack HCI adatgyűjtési](concepts/data-collection.md) és [-adattárolási szolgáltatás az Azure-ban](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="edge-local-management-and-control"></a>Edge – helyi felügyelet és vezérlés

**A Azure Stack HCI vezérlő síkja a felhőben halad át?**

Nem. Az Edge-helyi eszközöket, például a Windows felügyeleti központot, a PowerShellt vagy a System centert is használhatja közvetlenül a gazdagép-infrastruktúra és a virtuális gépek kezelésére, még akkor is, ha a hálózati kapcsolódás a felhőhöz nem működik, vagy szigorúan korlátozott. A gyakori mindennapi műveletek, például a virtuális gépek gazdagépek közötti áthelyezése, a hibás meghajtó cseréje vagy az IP-címek konfigurálása nem a felhőre támaszkodnak. Azonban a Felhőbeli kapcsolat szükséges a csatornákon keresztüli szoftverfrissítések beszerzéséhez, az Azure-regisztráció módosításához, vagy olyan funkciók használatához, amelyek közvetlenül a felhőalapú szolgáltatásokra támaszkodnak a biztonsági mentéshez, a figyeléshez és egyebekhez.

**Vannak sávszélesség-vagy késési követelmények Azure Stack HCI és a felhő között?**

Nem. A korlátozott sávszélességű kapcsolatok, például a vidéki T1 vonalak vagy a műholdas/mobil kapcsolatok megfelelőek a Azure Stack HCI szinkronizálásához. A minimálisan szükséges kapcsolat csak több kilobájt/nap. További szolgáltatásokhoz további sávszélességre lehet szükség, különösen a teljes virtuális gépek replikálásához vagy biztonsági mentéséhez, nagyméretű szoftverfrissítések letöltéséhez vagy részletes naplók feltöltéséhez a felhőben.

### <a name="designed-for-intermittent-and-limited-connectivity"></a>Időszakos és korlátozott kapcsolathoz tervezve

**A Azure Stack HCI-nek folyamatos kapcsolatra van szüksége a felhőhöz?**

Nem. Azure Stack HCI-t úgy tervezték, hogy kezelje a korlátozott vagy zéró kapcsolatú időszakokat.

**Mi történik, ha a hálózati kapcsolódás a felhőhöz átmenetileg leáll?**

Amíg a kapcsolódás nem működik, a gazdagép-infrastruktúra és a virtuális gépek továbbra is szabályosan futnak, és a helyi eszközöket is használhatja a felügyelethez. Nem fog tudni olyan szolgáltatásokat használni, amelyek közvetlenül a Cloud Services szolgáltatásra támaszkodnak. A Azure Portalban lévő információk is elavultak lesznek, amíg Azure Stack HCI újra nem tud szinkronizálni.

**Meddig lehet Azure Stack HCI-t futtatni a kapcsolatok lefelé?**

A Azure Stack HCI-nek legalább 30 egymást követő naponta egyszer kell szinkronizálnia az Azure-t.

**Mi történik, ha túllépi a 30 napos korlátot?**

Ha Azure Stack HCI 30 egymást követő napon belül nem szinkronizálta az **Azure-t** , a fürt kapcsolati állapota a Azure Portal és a többi eszközön is megjelenik, és a fürt csökkentett működési módba lép. Ebben a módban a gazdagép-infrastruktúra továbbra is fennáll, és az aktuális virtuális gépek továbbra is rendesen futnak. Az új virtuális gépek azonban nem hozhatók létre, amíg Azure Stack HCI újra nem tud szinkronizálni. A belső technikai ok az, hogy a fürt felhőalapú licence lejárt, és az Azure-nal való szinkronizálással meg kell újítani.

### <a name="understanding-sync"></a>A szinkronizálás ismertetése

**Milyen tartalmat Azure Stack HCI szinkronizál a felhővel?**

Ez az Ön által használt funkcióktól függ. Azure Stack HCI minimálisan szinkronizálja az alapszintű fürt adatait, hogy megjelenjenek a Azure Portalban (például a fürtözött csomópontok, a hardveres modell és a szoftver verziószáma); a legutóbbi szinkronizálás óta a felhalmozott alapnapokat összegző számlázási információk és minimálisan szükséges diagnosztikai információk, amelyek segítségével a Microsoft megtarthatja a Azure Stack HCI biztonságos, naprakész és megfelelő működését. A teljes méret kicsi – néhány kilobájt. Ha bekapcsolja a további szolgáltatásokat, előfordulhat, hogy további feltöltést végez: például az Azure Log Analytics a naplók és a teljesítményszámlálók feltöltését a figyeléshez.

**Milyen gyakran történik Azure Stack HCI szinkronizálása a felhővel?**

Ez az Ön által használt funkcióktól függ. Azure Stack HCI-nek legalább 12 óránként meg kell szinkronizálnia a szinkronizálást. Ha a szinkronizálás nem sikerül, a rendszer helyileg megőrzi a tartalmat, és elküldi a következő sikeres szinkronizálással. Ezen a rendszeres időzítőn kívül bármikor manuálisan is szinkronizálhatja a `Sync-AzureStackHCI` PowerShell-parancsmag vagy a Windows felügyeleti központ használatával. Ha bekapcsolja a további szolgáltatásokat, előfordulhat, hogy gyakrabban tölt fel: például az Azure Log Analytics a figyeléshez 5 percenként feltölti.

### <a name="data-residency"></a>Adattárolási hely

**Hová kerül ténylegesen a szinkronizált adatok?**

Azure Stack HCI szinkronizál az Azure-val, és biztonságos, Microsoft által működtetett adatközpontban tárolja az adattárolást. Ha többet szeretne megtudni a gyűjtött diagnosztikai adatokról, hogy Azure Stack HCI biztonságos, naprakész és a várt módon működjön, tekintse meg a következőt: [Azure stack HCI adatgyűjtési](concepts/data-collection.md) és [-adattárolási szolgáltatás az Azure-ban](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="disconnected-or-air-gapped"></a>Leválasztva vagy "Air-gapped"

**Használhatom Azure Stack HCI-t, és soha ne kapcsolódjon az Azure-hoz?**

Nem. A Azure Stack HCI-nek 30 egymást követő naponta egyszer kell szinkronizálnia az Azure-t.

**Átvihetem az adatátvitelt egy "Air-gapped" Azure Stack a HCI és az Azure között?**

Nem. A helyszíni és az Azure közötti hálózati kapcsolat nélkül jelenleg nem lehet regisztrálni és szinkronizálni a rendszert. Nem lehet például a tanúsítványokat és a számlázási adatátvitelt a cserélhető tároló használatával. Ha elegendő vásárlói igény áll rendelkezésre, a későbbiekben megnyitjuk a szolgáltatást. Tudassa velünk a [Azure stack HCI visszajelzési fórumában](https://feedback.azure.com/forums/929833-azure-stack-hci).

## <a name="azure-stack-hci-general-faqs"></a>Azure Stack HCI általános gyakori kérdések

**Hogyan kapcsolódik Azure Stack HCI a Windows Serverhez?**

A Windows Server csaknem minden Azure-termék alapja, és az összes funkció, amelyet a Windows Server támogatásával továbbra is elérhetővé tesz. Azure Stack HCI kezdeti ajánlata a Windows Server 2019-alapú, és a hagyományos Windows Server licencelési modellt használta. Napjainkban Azure Stack HCI saját operációs rendszerrel és előfizetés-alapú licencelési modellel rendelkezik. A HCI helyszíni üzembe helyezésének ajánlott módja a Azure Stack HCI, a Microsoft által ellenőrzött hardverek használata a partnereinktől.

**Frissíthetem a Windows Server 2019-ről Azure Stack HCI-re?**

Jelenleg nem lehet helyben frissíteni a Windows Serverről Azure Stack HCI-re. A Windows Server 2019-es és a 2016-es verzióját használó hiperkonvergens-fürtöket futtató ügyfelek számára a speciális áttelepítési útmutatókkal összhangban maradhat.

**Milyen Azure-szolgáltatásokat tud csatlakozni Azure Stack HCI-hez?**

Az Azure-szolgáltatások frissített listájához, amelyhez Azure Stack HCI-t csatlakoztathatja, lásd: a [Windows Server csatlakoztatása az Azure Hybrid Serviceshez](/windows-server/manage/windows-admin-center/azure/index).

**Milyen Azure Stack hub-és Azure Stack HCI-megoldások közösek?**

Azure Stack a HCI ugyanazokat a Hyper-V-alapú, szoftveresen definiált számítási, tárolási és hálózatkezelési technológiákat tartalmazza, mint Azure Stack hub. Mindkét ajánlat szigorú tesztelési és ellenőrzési feltételekkel rendelkezik, így biztosítva a megbízhatóságot és az alapul szolgáló hardveres platformmal való kompatibilitást.

**Mi a különbség közöttük?**

A Azure Stack hub szolgáltatással helyszíni felhőalapú szolgáltatásokat futtat. Az Azure IaaS és a Péter-szolgáltatások a helyszínen futtathatók, hogy bárhol és bármikor futtassák a felhőalapú alkalmazásokat, amelyeket a helyszíni Azure Portal felügyel.

Azure Stack HCI-vel virtualizált számítási feladatokat futtathat a helyszínen, a Windows felügyeleti központtal és ismerős Windows Server-eszközökkel felügyelve. Az Azure-hoz olyan hibrid forgatókönyvekhez is csatlakozhat, mint például a felhőalapú Site Recovery, a figyelés és mások.

**Frissíthetek Azure Stack HCI-ről Azure Stack hub-ra?**

Nem, de az ügyfelek áttelepíthetik a számítási feladatokat Azure Stack HCI-ről Azure Stack hubhoz vagy az Azure-ba.

**Hogyan azonosít egy Azure Stack HCI-kiszolgálót?**

A Windows felügyeleti központ felsorolja az operációs rendszert a minden kapcsolat listában és más helyeken, vagy a következő PowerShell-paranccsal kérdezheti le az operációs rendszer nevét és verzióját.

```PowerShell
Get-ComputerInfo -Property 'osName', 'osDisplayVersion'
```

Íme néhány példa a kimenetre:

```
OsName                    OSDisplayVersion
------                    ----------------
Microsoft Azure Stack HCI 20H2
```
