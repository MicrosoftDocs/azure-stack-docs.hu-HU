---
title: Hibrid felhő minták az Azure Stackkel való használathoz |} A Microsoft Docs
description: A hibrid felhő és az intelligens peremhálózaton egyaránt az Azure Stack minták áttekintése
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: e390e43b02d93690c1e233279d52d03665414bf7
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856408"
---
# <a name="hybrid-cloud-design-patterns-for-azure-stack"></a>Hibrid felhő tervezési minták az Azure Stackhez

A Microsoft Azure az egyetlen konzisztens hibrid felhő áll. Ez újra felhasználhatja a fejlesztési befektetéseit, és lehetővé teszi, hogy képes alkalmazások globális Azure Azure szuverén felhők, és az Azure Stack, amely a helyi adatközpontban az Azure kiterjesztése. Alkalmazásokat, amelyek részben a felhőben is nevezzük *hibrid alkalmazások*.

A [ *útmutató az Azure Alkalmazásarchitektúrájához* ](https://docs.microsoft.com/azure/architecture/guide) skálázható, rugalmas és magas rendelkezésre állású alkalmazások tervezéséhez készítésének strukturált megközelítését ismerteti. Ismertetett szempontok a [ *útmutató az Azure Alkalmazásarchitektúrájához* ](https://docs.microsoft.com/azure/architecture/guide) egyetlen felhő és az alkalmazásokat, amelyek részben a felhőkhöz tervezett alkalmazásokat egyaránt vonatkoznak.

Ez a cikk úgy bővíti a [ *szoftverminőség Alappillérei* ](https://docs.microsoft.com/azure/architecture/guide/pillars) tárgyalt a [ *Azure-alkalmazás* ](https://docs.microsoft.com/azure/architecture/guide/) [ *Architektúra – útmutató*,](https://docs.microsoft.com/azure/architecture/guide/) kifejezetten összpontosító hibrid alkalmazások tervezéséhez. Emellett hozzáadunk egy *elhelyezési* pillér hibrid alkalmazások termékfrissítésre vonatkoznak, nem egy felhőt vagy egy helyszíni adatközpontot.

A hibrid forgatókönyvek érhetők el fejlesztési erőforrások, például földrajzi hely, a biztonság, az Internet-hozzáféréssel kapcsolatos szempontok és egyéb szempontok nagy mértékben függenek. Bár ez az útmutató nem lehet számba venni az adott szempontok, biztosíthat néhány kulcsfontosságú útmutatást és ajánlott eljárások az, hogy kövesse. Sikeresen kialakítása, konfigurálása, telepítése és karbantartása hibrid alkalmazás architektúra számos, előfordulhat, hogy nem természetüknél fogva ismert, a kialakítási szempontokat foglalja magában.

Ez a dokumentum célja, hogy a lehetséges kérdéseket, a hibrid alkalmazások végrehajtása során esetlegesen felmerülő összesíteni, és szempontok (ezek területei) és az ajánlott eljárásokat is működjön velük biztosít. Ezeket a kérdéseket a tervezési fázis során elvégzésével fog elkerülése érdekében a problémákat okozhat, éles környezetben.

Lényegében ezek a kérdések hibrid alkalmazás létrehozása előtt gondolja át kell. A kezdéshez meg kell tegye a következőket:

-   Azonosítsa és kiértékelése az alkalmazás-összetevők.

-   Értékelje ki, alkalmazás-összetevők a pillér ellen.

## <a name="evaluate-the-application-components"></a>Az alkalmazás-összetevők kiértékelése

Egy alkalmazás minden összetevője a nagyobb alkalmazáson belül a saját adott szerepkörrel rendelkezik, és át kell tekinteni az összes kialakítási szempontokat. Minden egyes összetevő követelmények és szolgáltatások ezeket a szempontokat segítségével meghatározhatja, hogy az alkalmazásarchitektúra kell rendelni.

Az alkalmazás összetevőit felbontani, tanulmányozza az alkalmazásarchitektúra, és amely meghatározza, hogy miből áll. Összetevők is tartalmazhatnak más, az alkalmazás a szolgáltatással együttműködő alkalmazások. Az összetevők meghatározása, kiértékelése a megfelelő hibrid műveletek jellemzőiket, például a következők szerint:

-   Mi az a célja az összetevő?

-   Mik az összetevők között fennálló függőségeket?

Például lehet, hogy egy alkalmazás egy előtér-, és a háttér-definiálva két összetevőt. Egy hibrid forgatókönyvben az előtér egy felhőben van, és a másik a háttéralkalmazás van. Az alkalmazás

Itt az előtér és a felhasználó között és az előtér és a háttér-közötti kommunikációs csatornákat.

Alkalmazás-összetevő számos űrlapok és forgatókönyvek határozzák meg. A legfontosabb feladat azonosítja azokat, és a felhőbeli vagy helyszíni helyre.

A közös alkalmazás-összetevők szerepeltetni a leltárban szereplő 1. táblázat.

### <a name="table-1-common-app-components"></a>1\. táblázat. Közös alkalmazás-összetevők


| **Összetevő** | **Hibrid alkalmazás útmutató** |
| ---- | ---- |
| Ügyfélkapcsolatok | (Bármilyen eszközön) az alkalmazás felhasználók különböző módokon férhetnek egy egyetlen-belépési pont, többek között a következőket:<br>-A ügyfélkiszolgálói modellt, amely a felhasználót, hogy az alkalmazás működéséhez telepített ügyféllel rendelkező igényel. Egy kiszolgáló-alapú alkalmazás, amely egy webböngészőből érhető el.<br>-Ügyfélkapcsolatokat tartalmazhatnak értesítéseket, ha a kapcsolat megszakadt vagy riasztást, amikor központi díjak lehetnek érvényben. |
| Authentication  | Lehet, hogy a hitelesítési csatlakoztatása az alkalmazáshoz, vagy az egyes összetevők csatlakozik egy másik felhasználó szükséges. |
| API-k  | Programozás alapú hozzáférést az alkalmazáshoz, az API-készletre és osztálykódtárakat nyújtanak a fejlesztők számára, és a egy internetes szabványokon alapuló kapcsolat felületet kínálnak. API-k használatával bontható fel egy alkalmazás önállóan működő logikai egységeket. |
| Szolgáltatások  | A funkciókat biztosít az alkalmazás állapotára szolgáltatások is alkalmazhat. Lehet, hogy egy szolgáltatás, amely az alkalmazás fut a motor. |
| Üzenetsorok | Üzenetsorok segítségével rendszerezheti az életciklusa és állapotai az alkalmazás-összetevők állapotát. Ezek a várólisták üzenetkezelés, értesítéseket, és pufferelés előfizető feleknek képességeket biztosíthat. |
| Adattárolás | Egy alkalmazás állapot nélküli vagy állapot-nyilvántartó lehet. Állapotalapú alkalmazások kell adatokat tároló, amely számos formátumú és kötetek érheti el. |
| Adatok gyorsítótárazása  | A tervezés adatok gyorsítótárazási összetevő stratégiai késési problémák, és a felhőbeli tartalékkapacitás munkafolyamatos szerepet. |
| Adatfeldolgozás | Adatok küldheti el számos módon, és a egy webes űrlap értékei felhasználó által beküldött folyamatosan nagy mennyiségű adat flow alkalmazás. |
| Adatfeldolgozás | Az adatfeldolgozási feladatok (például a jelentések, analytics, batch exportálásokat és átalakítását) feldolgozott forrásban, vagy egy különálló összetevő használatával az adatok másolatát a kiszervezett. |

## <a name="assess-app-components-for-pillars"></a>Alkalmazás-összetevők a pillér felmérése

Az egyes összetevők értékeli az egyes pillar jellemzőit. Minden összetevőt az összes, az oszlopok előtti kipróbálhatók kérdések, előfordulhat, hogy nem tekintette előfordulhat, hogy tudomására, a hibrid alkalmazás tervét érintő. Ezeket a szempontokat ható értéket az alkalmazás optimalizálása a adja hozzá. 2\. táblázat ismerteti az egyes pillar, hibrid alkalmazások vonatkozik.

### <a name="table-2-pillars"></a>2\. táblázat. Kulcsfogalmak

| **Pillar** | **Leírás** |
| ----------- | --------------------------------------------------------- |
| Elhelyezés  | A hibrid alkalmazások összetevői stratégiai elhelyezéséhez. |
| Méretezhetőség  | A rendszer megnövekedett terhelés kezelésére vonatkozó képessége. |
| Rendelkezésre állás  | Az időarány, amíg, amelyekkel olyan hibrid alkalmazás működik és üzemel. |
| Rugalmasság | Arra, hogy az egy hibrid alkalmazás helyreállításához. |
| Kezelhetőségi | A rendszert termelési állapotban tartó működési folyamatok. |
| Biztonság | Hibrid alkalmazások és adatok védelme a fenyegetésekkel szemben. |

## <a name="placement"></a>Elhelyezés

Hibrid alkalmazás eleve rendelkezik elhelyezési veszi figyelembe, mint például az adatközpontban.

Elhelyezési fontos feladata az Elhelyezés összetevők, így a legjobb szolgáltatásokat az hibrid alkalmazás. Definíció szerint hibrid alkalmazások span helyeken, például a helyszínről a felhőbe, és más felhők között. Az alkalmazás összetevőinek helyez el a felhők két módon:

-   **Függőleges hibrid alkalmazások**  
    Alkalmazás-összetevők a helyek között oszlanak meg. Minden egyes összetevő csak egyetlen helyen található több példánya is lehet.

-   **Vízszintes hibrid alkalmazások**  
    Alkalmazás-összetevők a helyek között oszlanak meg. Minden egyes összetevő átfedés több helyen több példánya is lehet.
    
    Néhány összetevőt is vegye figyelembe a hely, míg mások nem rendelkezik a hely és elhelyezési ismerete. Egy olyan absztrakciós réteget a virtuousness érhető el. Ez a réteg egy korszerű alkalmazás-keretrendszert, mikroszolgáltatások, például a adhatja meg, hogyan szolgálja az alkalmazás alkalmazás-összetevők működő csomópontokon felhőkben való elhelyezését.

### <a name="placement-checklist"></a>Elhelyezési ellenőrzőlista

**Ellenőrizze a szükséges helyeken.** Győződjön meg arról, hogy az alkalmazás vagy annak az összetevőket a működéséhez szükséges, vagy egy adott felhőre vonatkozó minősítési szükséges. Lehetnek a vállalat az adatszuverenitási követelményekhez, vagy a törvény által. Emellett határozza meg, ha bármely helyszíni műveletekre szükség, egy adott helyen vagy a területi beállítás.

**Kapcsolat függőségek megállapítása.** Szükséges helyek és egyéb tényezők is szabályozzák az összetevők közötti kapcsolat függőségeit. Elhelyezésekor az összetevők, határozza meg, az rosszabb csatlakozási és biztonsági közöttük kommunikációra. Választási lehetőségek [ *VPN*,](https://docs.microsoft.com/azure/vpn-gateway/) [ *ExpressRoute*,](https://docs.microsoft.com/azure/expressroute/) és [ *hibrid kapcsolatok*.](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

**Értékelje ki a platform képességei.** Minden egyes alkalmazás-összetevő tekintse meg, ha az alkalmazás-összetevő szükséges erőforrás-szolgáltató érhető el a felhő és a sávszélesség képes kezelni a várt teljesítményről, valamint késésre vonatkozó követelmény.

**Tervezze meg a hordozhatóság.** A modern alkalmazás-keretrendszerekkel, mint a tárolókkal és mikroszolgáltatásokkal, használja a Műveletek áthelyezése tervezése, és a szolgáltatásfüggőségek.

**Határozza meg az adatszuverenitási követelményekhez.** Hibrid alkalmazások elhelyezési az adatok elkülönítését, mint például a helyi adatközpont egyszerűsítését szolgálja. Tekintse át az erőforrásokat az elhelyezési Ez a követelmény a sikeres optimalizálása elhelyezését.

**Tervezze meg a késés.** Helyek közötti felhőbeli műveletek is vezeti be a fizikai, az alkalmazás-összetevők közötti távolság. A követelmények minden késés befogadásához megállapítása.

**Szabályozhatja a forgalom adatfolyamait.** Használati csúcsot és a megfelelő és a biztonságos kommunikációt, az adatok személyes azonosításra alkalmas adatokat, ha hozzáfér az előtér egy nyilvános felhőben kezeléséhez.

## <a name="scalability"></a>Méretezhetőség

Méretezhetőség rendszer azon képessége, a rendszer egy alkalmazás, amely is, más tényezők, és idővel változhat, mérete és a kérelem hatókörén kívül a célközönség mérete befolyásolja a megnövekedett terhelés kezelésére.

Ez pillar core ismertetéséhez lásd: [ *méretezhetőség* ](https://docs.microsoft.com/azure/architecture/guide/pillars#scalability) a szoftverminőség Alappillérei.

A hibrid alkalmazások horizontális skálázási megközelítés lehetővé teszi a további igény szerint további példányok hozzáadása, és majd csendesebb időszakokban pedig letiltja.

A hibrid környezetek horizontális felskálázás az egyes összetevők igényel tüzetesebben átgondolni összetevők felhők vannak elosztva. Méretezés az alkalmazás egyik részének megkövetelheti egy másik skálázási. Ha az ügyfél kapcsolatok növekszik, de az alkalmazás webszolgáltatások száma a rendszer nem horizontálisan megfelelően, a terhelés például az adatbázis telítsük előfordulhat, hogy az alkalmazás.

Egyes alkalmazás-összetevők lehet terjeszteni a költségráfordításokkal egyenes arányban, míg mások méretezés függőségei, és esetenként nem eléggé mi által bővítése méretezésének. Például egy VPN-alagúton, a hibrid kapcsolatok kezeléséről az alkalmazás-összetevők helyek esetében a a sávszélességet és méretezhetők a késés. Hogyan vannak méretezve, hogy ezek a követelmények biztosítása érdekében az alkalmazás összetevőinek teljesülnek?

### <a name="scalability-checklist"></a>Méretezhetőségi ellenőrzőlisták

**Méretezési küszöbértékek megállapítása.** Az alkalmazás a különböző függőségek kezelése érdekében határozzák meg, melyik alkalmazás különböző felhőkben összetevők méretezhetők egymástól, miközben továbbra is a az alkalmazás futtatására vonatkozó követelményeknek. Hibrid alkalmazások milyen gyakran szeretné méretezni az alkalmazásban kezelni a funkció együttműködik majd hatással van az alkalmazás többi fontos területeket. Például egy előtér-példányok száma meghaladja a lehet szükség a háttér-méretezést.

**Méretezési csoport ütemterveket.** A legtöbb alkalmazás forgalmas időszakokban rendelkezik, így a ütemezések optimális méretezése koordinálására, azok időszakokban összesítenie kell.

**Egy központi monitorozási rendszer használja.** Figyelési képességek platformot tud biztosítani az automatikus skálázás, de a hibrid alkalmazások kell egy központi monitorozási rendszer, amely összefoglalja a rendszerállapot és a terhelés. Egy központi monitorozási rendszer is kezdeményezhető az egyik helyen erőforrás méretezése és a egy attól függően, egy másik helyen található erőforrás méretezése. Amely felhők automatikus skálázási erőforrásokat, és melyik felhőkben nem egy központi monitorozási rendszer követheti nyomon.

**Használja ki az automatikus skálázási funkciókat (elérhető).** Ha az automatikus skálázási funkciókat az architektúra egy része, az automatikus skálázás, amelyek meghatározzák, hogy ha egy alkalmazás-összetevő kell növelhető, ki, lefelé, és a küszöbértékek beállításával valósíthatja meg. Az automatikus skálázás egyik példája egy ügyfélkapcsolatot egy felhőbeli kezelni a megnövekedett kapacitás maximumára, de az alkalmazás különböző felhőkben helyezkednek el is lehet méretezni, egyéb függőségek okoz. Meg kell győződnie arról az automatikus méretezési képességeivel függő összetevőt.

Ha az automatikus skálázás nem érhető el, vegye fontolóra parancsfájlok és más erőforrások, hogy megfeleljen a Manuális méretezés küszöbértékek a központi felügyeleti rendszer által aktivált.

**Várható terhelési hely alapján határozzák meg.** Hibrid alkalmazások, amelyek az ügyfélkérelmek kezelését. Előfordulhat, hogy elsősorban támaszkodnak egyetlen helyen. Ha az ügyfélkérések számának terhelés eléri az,

További erőforrások is hozzáadhatók a bejövő kérések a terhelés elosztása egy másik helyen található. Győződjön meg arról, hogy az ügyfélkapcsolatok a megnövekedett terhelés kezelésére, és a terhelés kezelése érdekében az ügyfélkapcsolatok minden automatikus folyamatokat is meghatározza.

## <a name="availability"></a>Rendelkezésre állás

Rendelkezésre állási az az idő, hogy a rendszer működik és üzemel. Rendelkezésre állási százalékos hasznos üzemidőt mérjük. Az alkalmazáshibák, infrastruktúraproblémák és a rendszerterhelés egyaránt csökkenthetik a rendelkezésre állást.

Ez pillar core ismertetéséhez lásd: [ *rendelkezésre állási* ](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) a szoftverminőség Alappillérei.

### <a name="availability-checklist"></a>Rendelkezésre állási ellenőrzőlisták

**Adja meg a redundanciát a hálózati kapcsolatot.** Hibrid alkalmazások, amelyek az alkalmazás között megoszlik a felhők közötti kapcsolatot igényelnek. Megválaszthatja, technológiákkal való hibrid kapcsolathoz, ezért az elsődleges technológia tetszőleges emellett egy másik technológia redundanciájának biztosítsa az automatikus feladatátvételt is használhat, amennyiben az elsődleges technológia nem.

**Tartalék tartományok besorolása.** Több tartalék tartomány alkalmazásoknak és hibatűrő. Tartalék tartományok segít elkülöníteni a pont meg a hiba, például ha egy merevlemezen sikertelen lesz a helyszínen, ha egy top-of-rack switch leáll, vagy ha a teljes adatközpont nem érhető el. A hibrid alkalmazások esetén egy helyre egy tartalék tartományt, így csoportosíthatók. Több rendelkezésre állási követelmények vonatkoznak minél többet kell kiértékelni egyetlen tartalék tartomány besorolásának módját.

**Frissítési tartományok besorolása.** Frissítési tartományok segítségével győződjön meg arról, hogy alkalmazás-összetevők példányai érhetők el, amíg a többi összetevő példányai vannak kiszolgált frissítésekkel vagy funkciófrissítéseket. Tartalék tartományok, mint a frissítési tartományok osztályozhatók szerint helyek között. Meg kell határoznia, ha egy alkalmazás-összetevő lehetővé teszi egyetlen helyen frissítése előtt frissíteni, egy másik helyen található, vagy ha más tartományban konfigurációs szükség. Magát egy helyen több frissítési tartományt lehet.

**Példányok és a rendelkezésre állás nyomon követése** Lehet, hogy magas rendelkezésre állású alkalmazás-összetevők a terheléselosztás és a szinkron adatreplikációt. Meg kell határoznia, hogy hány példányt lehet offline állapotban, mielőtt a szolgáltatás megszakad.

**Bemutatjuk az önjavítást szem előtt tartva.** Abban az esetben, ha problémát okoz a rendelkezésre állásának romlását, egy észlelési, monitorozási rendszerek által öngyógyító tevékenységeket az alkalmazáshoz, például a hibás szolgáltatáspéldányt a kiürítés és újbóli üzembe helyezés sikerült kezdeményezni. Nagy valószínűséggel ehhez a központi figyelési, integrált megoldást hibrid folyamatos integrációs és folyamatos Készregyártás (CI/CD) folyamatot. Az alkalmazás integrálva van egy monitorozási rendszernek, hogy az alkalmazás-összetevő újbóli üzembe helyezés problémák azonosításához. A monitorozási rendszer hibrid CI/CD – ismételt üzembe helyezése, az alkalmazás-összetevő és potenciálisan bármely más függő összetevők a azonos vagy más helyeken is el lehet indítani.

**Szolgáltatásiszint-szerződések (SLA) fenntartásához.** Rendelkezésre állás fontos a szerződéseket a szolgáltatásokat és alkalmazásokat, amelyek azt az ügyfelek a kapcsolat fenntartása érdekében. Előfordulhat, hogy minden helyhez, a hibrid alkalmazás támaszkodik a saját SLA-t. Ezek különböző SLA-k hatással lehet a hibrid alkalmazás átfogó SLA-t.

## <a name="resiliency"></a>Rugalmasság

Rugalmasság arra, hogy az egy hibrid alkalmazások és a rendszert, hogy helyreálljon a hibák után, és folytassa a működést. A rugalmasság célja, hogy az alkalmazás egy hibát követően teljesen működőképes állapotba térjen vissza. Rugalmassági stratégiák közé tartoznak az olyan megoldások, mint a biztonsági mentés, a replikáció és a vészhelyreállítás.

Ez pillar core ismertetéséhez lásd: [ *rugalmasság* ](https://docs.microsoft.com/azure/architecture/guide/pillars#resiliency) a szoftverminőség Alappillérei.

### <a name="resiliency-checklist"></a>Rugalmasságra vonatkozó ellenőrzőlista

**Fedje fel a vészhelyreállítási függőségeket.** Vész-helyreállítási egy felhőben lévő alkalmazás-összetevők egy másik felhőben módosításait igénylik. Ha egy vagy több összetevőt az egyik felhőből melyek feladatai át egy másik helyre, ugyanazon a felhőn belül vagy egy másik felhőbe, a függő összetevőket kell elvégezni ezeket a változásokat figyelembe. Ez a kapcsolat függőségeket is magában foglalja. Rugalmasság a teljes tesztelésen átesett alkalmazás helyreállítási terv szükséges minden egyes felhőhöz.

**A helyreállítási folyamat létrehozásához.** Egy érvényes helyreállítási folyamat tervezési kiértékelése pufferek kezelésére képesek az alkalmazás-összetevők, újra próbálkozik, az újrapróbálkozások adatátvitel sikertelen volt, és szükség esetén térhet vissza másik szolgáltatáshoz vagy a munkafolyamat. Meg kell határoznia, milyen biztonsági mentési mechanizmus használata, a visszaállítási eljárás magában foglalja, és milyen gyakran vizsgálják. A növekményes és a teljes biztonsági mentések gyakoriságát is meg kell határozni.

**Tesztelje a részleges helyreállítást.** Az alkalmazás része a részleges helyreállítás biztosíthat viszontbiztosítási a felhasználók számára, hogy az összes ne legyen nem érhető el. Ez a terv részét kell győződjön meg arról, hogy egy részleges visszaállítási nem mellékhatásaik bármely, például a biztonsági másolat visszaállítása szolgáltatás, amellyel kommunikál az alkalmazás szabályosan leállítani a biztonsági mentés előtt.

**Vész-helyreállítási felbujtóként határozza meg, és rendelje hozzá a felelős.** A helyreállítási terv kell ismertetnie ki, és milyen szerepköröket, biztonsági mentési és helyreállítási műveletek mellett milyen biztonsági mentése és visszaállítása is kezdeményezhető.

**Hasonlítsa össze a vész-helyreállítási önjavítási küszöbértékeket.** Határozza meg, hogy egy alkalmazás önjavítási képességek az automatikus helyreállítás kezdeményezése és a egy alkalmazás önkiszolgáló-figyelembe kell venni a sikerességet vagy sikertelenséget javítás szükséges időt. Minden egyes felhőhöz a küszöbértékek meghatározásához.

**Ellenőrizze a rugalmasság szolgáltatások rendelkezésre állása.** Rugalmasság funkciói és képességei, mindegyik helyen rendelkezésre állásának meghatározása. Ha egy helyen nem ad meg a szükséges képességeket, fontolja meg az adott helyen integrálása egy központosított szolgáltatás, amely a rugalmassági funkcióval.

**Határozza meg, hogy leállás.** A várható állásidő karbantartás miatt az alkalmazás egészére, valamint alkalmazás-összetevők határozzák meg.

**A dokumentum a hibaelhárítási eljárásokkal kapcsolatban.** Adja meg az újbóli üzembe helyezés, erőforrások és alkalmazás-összetevők hibaelhárítási eljárások.

## <a name="manageability"></a>Kezelhetőségi

A hibrid alkalmazások kezelése szempontjai különösen fontos az architektúra tervezésének. Egy jól felügyelt hibrid alkalmazás, amely lehetővé teszi az integrációt a konzisztens alkalmazás kódja egy közös fejlesztési folyamat kódként infrastruktúrát biztosít. Egységes alkalmazásával rendszerszintű és az egyes módosításokat az infrastruktúrában, tesztelését, is, hogy biztosítsa az integrált központi telepítési Ha a módosításokat a vizsgálatok sikeresek lehetővé teheti, hogy a forráskód egyesíthetők.

Ez pillar core ismertetéséhez lásd: [ *felügyeleti és fejlesztési és üzemeltetési* ](https://docs.microsoft.com/azure/architecture/guide/pillars#management-and-devops) a szoftverminőség Alappillérei.

### <a name="manageability-checklist"></a>Kezelhetőségi ellenőrzőlista

**Valósítja figyelése.** Több felhő között az alkalmazás-összetevők terjednek egy központosított monitorozási rendszer használatával biztosíthat összesített nézetét azok állapotát és teljesítményét. A rendszer tartalmaz a figyelést az alkalmazás-összetevők és a kapcsolódó platform képességei.

Határozza meg, amely figyelést igényel az alkalmazás részei.

**Házirendek koordinálja.** Hibrid alkalmazás kiterjedő minden hely rendelkezhet saját szabályzattal, amely lefedi engedélyezett erőforrástípusok elnevezési konvenciók, címkék és egyéb feltételek.

**Definiálja és szerepkörök.** Adatbázis-rendszergazdaként meg kell határoznia a szükséges különböző személyekre szabott (például egy alkalmazás tulajdonosa, adatbázis-rendszergazda és a felhasználó), amely az alkalmazás-erőforrások eléréséhez szükséges engedélyekkel. Ezeket az engedélyeket az erőforrásokhoz, és az alkalmazáson belüli konfigurálni kell. A szerepköralapú hozzáférés-vezérlés (RBAC) rendszer lehetővé teszi, hogy állítson be ezeket az engedélyeket az alkalmazás-erőforrásokat. A hozzáférési jogosultságok vannak kihívást összes erőforrás egyetlen felhő vannak üzembe helyezve, de még több figyelmet igényel, amikor az erőforrások vannak elosztva a felhők. Engedélyek erőforrások egy felhőben lévő erőforrások egy másik felhőben csak akkor érvényesíthetők.

**CI/CD-folyamatok használja.** A folyamatos integráció és folyamatos fejlesztési (CI/CD) létrehozási és-alkalmazások, amelyek több felhő között, és adja meg a minőségbiztosítás infrastruktúra és alkalmazások üzembe helyezéséhez konzisztens folyamatot tud biztosítani. Ez a folyamat lehetővé teszi, hogy az infrastruktúra és az alkalmazás tesztelését egy felhőben és a egy másik felhőben üzembe helyezett. A folyamat lehetővé teszi a egy felhőbeli hibrid alkalmazás egyes összetevőinek és más összetevők üzembe helyezése egy másik felhőben, lényegében a hibrid alkalmazások központi telepítése a alapját képező is. A CI/CD rendszer kritikus fontosságú kezelésére. a függőségek alkalmazás-összetevők rendelkezik minden más, mint például a webes alkalmazás létrehozása egy kapcsolati karakterláncot az adatbázishoz a telepítés során.

**Az életciklus kezelése.** Hibrid alkalmazás erőforrásainak helyeken is kiterjedhet, mert egyes egyetlen helyen életciklus-kezelési lehetőséget kell, hogy egyetlen eszközéletciklus felügyeleti egységbe. Fontolja meg, hogyan akkor jönnek létre, frissített vagy törölt.

**Vizsgálja meg a hibaelhárítási stratégiák.** Hibrid alkalmazás hibaelhárítási magában foglalja a több alkalmazás-összetevők, mint a ugyanazt az alkalmazást, amely egy egyetlen felhőben. Amellett, hogy a felhők közötti kapcsolatot az alkalmazás fut, a két platform helyett. Fontos feladat a hibrid alkalmazások hibaelhárítása az összesített állapotának és teljesítményének figyelését az alkalmazás-összetevők vizsgálata.

## <a name="security"></a>Biztonság

Biztonság az egyik az elsődleges szempont bármely felhőalapú alkalmazás esetében, és még több kulcsfontosságú hibrid felhőbeli alkalmazások válik.

Ez pillar core ismertetéséhez lásd: [ *biztonsági* ](https://docs.microsoft.com/azure/architecture/guide/pillars#security) a szoftverminőség Alappillérei.

### <a name="security-checklist"></a>Biztonsági ellenőrzőlista

**Megsértésének feltételezése.** Ha az alkalmazás egyik részének integritása sérül, győződjön meg arról, minimálisra csökkentése érdekében a biztonsági incidens esetén nem csak az ugyanazon a helyen belül, hanem helyszínen megállítását helyen vannak a megoldások.

**Engedélyezett hálózati hozzáférés figyelése.** A hálózati hozzáférési házirendeket az alkalmazás, például csak egy adott alhálózatról a az alkalmazás elérésének meghatározásához és engedélyezése csak a minimális portok és protokollok között az alkalmazás működéséhez szükséges összetevőket.

**Robusztus hitelesítést alkalmazni.** Egy robusztus hitelesítési séma, kritikus fontosságú biztonsága érdekében az alkalmazás. Fontolja meg egy összevont identitáskezelési szolgáltató, amely egyszeri bejelentkezési funkciókat nyújt, és a egy vagy több, a következő rendszerek használ: felhasználónév és a jelszavas bejelentkezést, nyilvános és titkos kulcsokat, kétfaktoros vagy a multi-factor Authentication hitelesítést és megbízható biztonsági csoportok. Határozza meg, hogy a megfelelő erőforrásokon a bizalmas adatok és egyéb titkos adatait az alkalmazás hitelesítési tanúsítványokat és azok követelményeinek mellett.

**Titkosítás használata.** Azonosítsa az alkalmazás mely területei használata titkosításhoz, például adatok storage vagy az ügyfél kommunikációt és hozzáférés.

**Használja a biztonságos csatornákat.** A felhők közötti biztonságos csatorna fontos biztonsági és hitelesítési ellenőrzések, valós idejű védelem, karanténba és egyéb szolgáltatások nyújtására több felhő között.

**Definiálja és szerepkörök.** Szerepkörök az erőforrás-konfigurációk és egyszeres identitást hozzáférés megvalósításához több felhő között. Határozza meg, a szerepköralapú hozzáférés-vezérlés (RBAC) követelményei az alkalmazás és az ahhoz tartozó platform erőforrást.

**A rendszer naplózza.** A rendszer figyelési bejelentkezhet és összesített adatok az alkalmazás-összetevők és a kapcsolódó felhőalapú platform műveletek is.

## <a name="summary"></a>Összegzés

Ez a cikk ismerteti egy feladatlista elem meg fontos figyelembe venni a szerzői műveletek és a hibrid alkalmazások tervezése során. Az alkalmazás telepítése előtt, tekintse át ezeket területei legyenek elérhetők meggátolja, hogy ezekre a kérdésekre az éles valamilyen okból kimaradás lép be és az esetlegesen határozhat meg újból megnyitni a tervező.

Akkor úgy tűnhet időigényes feladat például előre, de ha az alkalmazás ezen oszlopok alapján tervez könnyedén lekérhesse a megtérülési ráta.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

-   [Hibrid felhő](https://azure.microsoft.com/overview/hybrid-cloud/)
-   [Hibrid felhőbeli alkalmazások](https://azure.microsoft.com/solutions/hybrid-cloud-app/)
-   [Azure Resource Manager-sablonok fejlesztése felhőkonzisztenciához](http://aka.ms/consistency)