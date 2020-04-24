---
title: Hibrid alkalmazások tervezési szempontjai az Azure-ban és Azure Stack hub-ban
description: Ismerje meg a tervezési szempontokat, amikor hibrid alkalmazást készít az intelligens felhőhöz és az intelligens környezethez, beleértve az elhelyezést, a méretezhetőséget, a rendelkezésre állást és a rugalmasságot.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 8f37967534452c43788cbe0c40490ee4021ca5f0
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80812337"
---
# <a name="hybrid-app-design-considerations"></a>Hibrid alkalmazások kialakításával kapcsolatos szempontok

Microsoft Azure az egyetlen konzisztens hibrid felhő. Lehetővé teszi a fejlesztési beruházások felépítését, és lehetővé teszi az olyan alkalmazások használatát, amelyek a globális Azure-ra, a szuverén Azure-felhőre és a Azure Stackra is kiterjedhetnek, ami az Azure kiterjesztése az adatközpontban. A felhőkre kiterjedő alkalmazásokat *hibrid alkalmazásoknak*is nevezzük.

Az [*Azure-alkalmazás architektúrájának útmutatója*](https://docs.microsoft.com/azure/architecture/guide) a skálázható, rugalmas és nagyfokú rendelkezésre állású alkalmazások tervezésének strukturált megközelítését ismerteti. Az [*Azure Application Architecture útmutatóban*](https://docs.microsoft.com/azure/architecture/guide) ismertetett megfontolások egyformán érvényesek azokra az alkalmazásokra, amelyek egyetlen felhőhöz lettek kialakítva, és a felhőkre kiterjedő alkalmazásokhoz.

Ez a cikk az [*Azure Application*](https://docs.microsoft.com/azure/architecture/guide/) [ *Architecture útmutatójában*](https://docs.microsoft.com/azure/architecture/guide/) ismertetett [*szoftverek minőségének pilléreit*](https://docs.microsoft.com/azure/architecture/guide/pillars) fokozza, különös tekintettel a hibrid alkalmazások tervezésére. Emellett felvesszük az *elhelyezési* oszlopokat, mivel a hibrid alkalmazások nem kizárólagosak egyetlen felhőben vagy egy helyszíni adatközpontban.

A hibrid forgatókönyvek nagy mértékben eltérnek a fejlesztéshez rendelkezésre álló erőforrásokkal, és olyan szempontokat is figyelembe vesznek, mint például a földrajz, a biztonság, az Internet-hozzáférés és egyéb megfontolások. Bár ez az útmutató nem tudja enumerálni az adott szempontokat, néhány kulcsfontosságú irányelvet és ajánlott eljárásokat is megadhat. A hibrid alkalmazások architektúrájának tervezése, konfigurálása, üzembe helyezése és karbantartása sok olyan kialakítási szempontot foglal magába, amelyek nem ismertek az Ön számára.

Ez a dokumentum célja, hogy összesítse a hibrid alkalmazások megvalósítása során felmerülő lehetséges kérdéseket, valamint a velük való együttműködéshez szükséges szempontokat (ezek a pilléreket) és az ajánlott eljárásokat. Ha ezeket a kérdéseket a tervezési fázis során intézi, azzal elkerülhető, hogy milyen problémákat okozhatnak az éles környezetben.

Ezek a kérdések alapvetően a hibrid alkalmazások létrehozása előtt szükségesek. A kezdéshez a következő műveleteket kell elvégeznie:

- Azonosítsa és értékelje ki az alkalmazás összetevőit.
- Az alkalmazás-összetevők kiértékelése a pillérek között.

## <a name="evaluate-the-app-components"></a>Az alkalmazás-összetevők kiértékelése

Az alkalmazás minden összetevőjének saját szerepköre van a nagyobb alkalmazáson belül, és az összes tervezési szempontot figyelembe kell venni. Az egyes összetevőkre vonatkozó követelmények és funkciók az alkalmazás architektúrájának meghatározásához a következő szempontokat kell leképezniük.

Az alkalmazás architektúrájának tanulmányozásával felépítheti az alkalmazást az összetevőkbe, és meghatározhatja, hogy a mit tartalmaz. Az összetevők tartalmazhatnak más alkalmazásokat is, amelyeket az alkalmazás kommunikál. Az összetevők azonosítása közben értékelje ki a kívánt hibrid műveleteket a jellemzőiknek megfelelően a következő kérdések megválaszolásával:

- Mi az összetevő célja?
- Mik az összetevők közötti egymásrautaltságok?

Egy alkalmazásnak lehet például két összetevőként definiált előtér-és háttér-végpontja. Hibrid forgatókönyv esetén az előtér egy felhőben van, a háttér pedig a másikban van. Az alkalmazás kommunikációs csatornákat biztosít az előtér és a felhasználó között, valamint az előtér és a háttér között is.

Az alkalmazás-összetevőket számos űrlap és forgatókönyv határozza meg. A legfontosabb feladat, hogy azonosítsa őket, valamint a Felhőbeli vagy a helyszíni helyet.

A leltárba foglalandó általános alkalmazás-összetevők az 1. táblázatban találhatók.

### <a name="table-1-common-app-components"></a>1. táblázat Gyakori alkalmazás-összetevők

| **Összetevő** | **Hibrid alkalmazások – útmutató** |
| ---- | ---- |
| Ügyfélkapcsolatok | Az alkalmazás (bármely eszközön) különböző módokon férhet hozzá a felhasználókhoz egy egyszeri belépési pontról, az alábbi módokon:<br>– Egy ügyfél-kiszolgáló modell, amelyhez a felhasználónak telepítve kell lennie az alkalmazással való együttműködéshez. Egy böngészőből elérhető, kiszolgáló-alapú alkalmazás.<br>– Az ügyfélkapcsolatok tartalmazhatnak értesítéseket, ha a kapcsolat megszakad, vagy ha barangolási díjak esetén riasztást küld. |
| Hitelesítés  | A hitelesítés szükséges lehet az alkalmazáshoz csatlakozó vagy egy másik összetevővel csatlakozó felhasználó számára. |
| API-k  | Az API-készletekkel és az osztály-tárakkal programozott hozzáférést biztosíthat a fejlesztőknek, és az internetes szabványokon alapuló kapcsolati felületet is biztosíthat. Az API-k segítségével az alkalmazásokat egymástól függetlenül működő logikai egységekre lehet bontani. |
| Szolgáltatások  | Az alkalmazások funkcióinak biztosításához tömör szolgáltatásokat alkalmazhat. A szolgáltatás lehet az a motor, amelyen az alkalmazás fut. |
| Üzenetsorok | A várólisták használatával rendezheti az alkalmazás-összetevők életciklusának és állapotának állapotát. Ezek a várólisták lehetővé teszik az üzenetküldést, az értesítéseket és a pufferelési képességeket az előfizető felek számára. |
| Adattárolás | Egy alkalmazás állapot nélküli vagy állapot-nyilvántartó lehet. Az állapot-nyilvántartó alkalmazásoknak olyan adattárolásra van szükségük, amely számos formátumban és köteten is kielégíthető. |
| Adatok gyorsítótárazása  | A kialakításban található adat-gyorsítótárazási összetevő stratégiailag képes kezelni a késési problémákat, és szerepet játszik a felhő kiváltásának elindításában. |
| Adatfeldolgozás | Az adatok többféleképpen is elhelyezhetők egy alkalmazásba, a webes űrlapok felhasználó által beküldött értékeitől kezdve a folyamatosan nagy mennyiségű adatfolyamig. |
| Adatfeldolgozás | Az adatfeldolgozási feladatok (például a jelentések, az elemzések, a kötegelt exportálások és az adatátalakítások) feldolgozhatók a forráson, vagy egy külön összetevőn, az adatok másolatát használva. |

## <a name="assess-app-components-for-pillars"></a>A pillérekhez tartozó alkalmazás-összetevők felmérése

Minden egyes összetevő esetében értékelje ki az egyes oszlopok jellemzőit. Az egyes összetevőknek az összes pillérrel való kiértékelése során előfordulhat, hogy olyan kérdésekkel találkozhat, amelyek befolyásolják a hibrid alkalmazás kialakítását. Ezen megfontolások alapján az alkalmazás optimalizálásával értéket adhat hozzá. A 2. táblázat az egyes oszlopok leírását tartalmazza, a hibrid alkalmazásokhoz kapcsolódóan.

### <a name="table-2-pillars"></a>2. táblázat Alappilléreknek

| **Pillér** | **Leírás** |
| ----------- | --------------------------------------------------------- |
| Elhelyezés  | Az összetevők stratégiai elhelyezése a hibrid alkalmazásokban. |
| Méretezhetőség  | A rendszer megnövekedett terhelés kezelésére vonatkozó képessége. |
| Rendelkezésre állás  | A hibrid alkalmazások működésének és működésének aránya. |
| Rugalmasság | A hibrid alkalmazások helyreállításának lehetősége. |
| Kezelhetőség | A rendszert termelési állapotban tartó működési folyamatok. |
| Biztonság | A hibrid alkalmazások és adatok védelme a fenyegetésektől. |

## <a name="placement"></a>Elhelyezés

A hibrid alkalmazások eleve rendelkeznek elhelyezési megfontolással, például az adatközponthoz.

Az elhelyezés a helymeghatározási összetevők fontos feladata, hogy a lehető leghatékonyabban lehessen kiszolgálni egy hibrid alkalmazást. Definíció szerint a hibrid alkalmazások a helyszínen, a felhőbe és a különböző felhőkbe kerülnek. Az alkalmazás összetevőit kétféleképpen helyezheti el a felhőkben:

- **Vertikális hibrid alkalmazások**  
    Az alkalmazás-összetevők elosztása a különböző helyszíneken történik. Minden egyes összetevőhöz több példány is tartozhat, amely csak egyetlen helyen található.

- **Horizontális hibrid alkalmazások**  
    Az alkalmazás-összetevők elosztása a különböző helyszíneken történik. Minden egyes összetevő több példányban is több helyet is felölel.

    Egyes összetevők tisztában lehetnek a helyükkel, míg mások nem ismerik a helyüket és elhelyezését. Ez az erényes megoldás egy absztrakciós réteggel is megvalósítható. Ez a réteg egy modern alkalmazás-keretrendszerrel (például a szolgáltatásokkal) meghatározhatja, hogy az alkalmazás hogyan legyen kiszolgálva a csomópontokon futó alkalmazás-összetevőknek a felhőkön keresztül történő elhelyezésével.

### <a name="placement-checklist"></a>Elhelyezési ellenőrzőlista

**Ellenőrizze a szükséges helyet.** Győződjön meg arról, hogy az alkalmazás vagy annak összetevői szükségesek a-ben való működéséhez, vagy az adott felhőre vonatkozó tanúsítvány megköveteléséhez. Ebbe beletartozhatnak a vállalat szuverenitási követelményei, vagy törvény által diktáltak. Azt is állapítsa meg, hogy egy adott helyhez vagy területi beállításhoz van-e szükség helyszíni műveletekre.

**Csatlakozási függőségek megállapítása.** A szükséges helyszínek és egyéb tényezők az összetevők közötti kapcsolati függőségeket is diktálják. Az összetevők elhelyezésekor határozza meg az optimális kapcsolatot és biztonságot a közöttük zajló kommunikációhoz. A lehetőségek közé tartozik a [ *VPN*, a](https://docs.microsoft.com/azure/vpn-gateway/) [ *ExpressRoute*](https://docs.microsoft.com/azure/expressroute/) és a [ *hibrid kapcsolatok*.](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

**A platform képességeinek kiértékelése.** Az egyes alkalmazás-összetevőknél ellenőrizze, hogy az alkalmazás-összetevőhöz szükséges erőforrás-szolgáltató elérhető-e a felhőben, és hogy a sávszélesség képes-e megfelelni a várt átviteli sebességnek és a késési követelményeknek.

**A hordozhatóság megtervezése.** A műveletek áthelyezésének és a szolgáltatási függőségek megelőzésére a modern alkalmazás-keretrendszerek, például a tárolók és a szolgáltatások használata.

**Határozza meg az adatszuverenitási követelményeket.** A hibrid alkalmazások az adatelkülönítést, például egy helyi adatközpontban való elhelyezését célozzák. Tekintse át az erőforrások elhelyezését, hogy optimalizálja a követelmény befogadásának sikerességét.

**Tervezze meg a késést.** A felhő közötti műveletek az alkalmazás-összetevők közötti fizikai távolságot is bevezethetik. Győződjön meg arról, hogy milyen követelmények vonatkoznak a késésre.

**A forgalmi folyamatok szabályozása.** Kezelje a csúcsérték-használatot, valamint a személyes azonosításra alkalmas adatokra vonatkozó megfelelő és biztonságos kommunikációt, ha az előtér egy nyilvános felhőben érhető el.

## <a name="scalability"></a>Méretezhetőség

A méretezhetőség a rendszer azon képessége, hogy az alkalmazás megnövekedett terhelését kezelje, ami az idő múlásával változhat, ahogy más tényezők és erők a célközönség méretét, valamint az alkalmazás méretét és hatókörét is érintik.

Ennek az oszlopnak a fő vitájában lásd: [*skálázhatóság*](https://docs.microsoft.com/azure/architecture/guide/pillars#scalability) az architektúra kiválóságának öt pillérében.

A hibrid alkalmazások horizontális skálázási megközelítése lehetővé teszi, hogy további példányokat adjon hozzá az igények kielégítéséhez, majd tiltsa le őket a csendesebb időszakok során.

Hibrid forgatókönyvekben az egyes összetevők horizontális felskálázása további figyelmet igényel, ha az összetevők a felhők között oszlanak el. Az alkalmazás egy részének skálázásához szükség lehet egy másik méretezésre. Ha például az ügyfélkapcsolatok száma nő, de az alkalmazás webszolgáltatásai nem megfelelően vannak kibővítve, akkor az adatbázis terhelése felmerülhet az alkalmazásban.

Egyes alkalmazás-összetevők lineárisan horizontálisan méretezhetők, míg mások skálázási függőségekkel rendelkeznek, és az is előfordulhat, hogy milyen mértékben méretezhetők. Például egy, az alkalmazás-összetevők helyeihez hibrid kapcsolatot biztosító VPN-alagút korlátozza a sávszélességet és a késést. Hogyan méretezhető az alkalmazás összetevői a követelmények teljesítése érdekében?

### <a name="scalability-checklist"></a>Méretezhetőségi ellenőrzőlisták

**A skálázási küszöbértékek megállapítása.** Az alkalmazás különböző függőségeinek kezeléséhez határozza meg, hogy a különböző felhőkben lévő alkalmazás-összetevők milyen mértékben méretezhetők egymástól függetlenül, miközben továbbra is megfelelnek az alkalmazás futtatásához szükséges követelményeknek. A hibrid alkalmazásoknak gyakran kell méretezniük bizonyos területeket az alkalmazásban, hogy az interakciót és a többi alkalmazást érintsék. Előfordulhat például, hogy az előtér-példányok száma meghaladja a háttér méretezését.

**Méretezési ütemtervek definiálása.** A legtöbb alkalmazás foglalt időszakokat tartalmaz, ezért az optimális skálázás koordinálásához összesíteni kell a maximális időpontokat az ütemtervekben.

**Központi figyelési rendszer használata.** A platform figyelési képességei lehetővé teszik az automatikus skálázást, de a hibrid alkalmazásoknak egy központi figyelési rendszerre van szükségük, amely összegzi a rendszerállapotot és a terhelést. Egy központi figyelési rendszer egy erőforrás skálázását kezdeményezheti egy helyen, és az erőforrástól függően egy másik helyen is méretezhető. Emellett egy központi figyelési rendszer nyomon követheti, hogy mely felhők automatikus méretezési erőforrásai és mely felhők ne legyenek.

**Kihasználhatja az automatikus skálázási képességeket (ahogy elérhető).** Ha az automatikus skálázási képességek az architektúra részét képezik, az automatikus skálázást olyan küszöbértékek megadásával valósítja meg, amelyek meghatározzák, hogy mikor kell felskálázást végezni, ki, le vagy bekapcsolni az alkalmazás-összetevőt. Az automatikus skálázásra példa egy olyan Ügyfélkapcsolat, amely az egyik felhőben Automatikus méretezéssel kezeli a megnövekedett kapacitást, de az alkalmazás más függőségeit is kiterjeszti a különböző felhők között, és a méretezést is lehetővé teszi. Meg kell győződnie ezeknek a függő összetevőknek az automatikus skálázási képességeiről.

Ha az automatikus skálázás nem érhető el, érdemes lehet parancsfájlokat és egyéb erőforrásokat megvalósítani a manuális skálázáshoz, amelyet a központi figyelési rendszer küszöbértékei indítottak el.

**A várt terhelés meghatározása a hely alapján.** Az ügyfelek kérelmeit kezelő hibrid alkalmazások elsődlegesen egyetlen helyről számíthatnak. Ha az ügyfélalkalmazások terhelése meghaladja a küszöbértéket, további erőforrásokat adhat hozzá egy másik helyen a bejövő kérések terhelésének elosztásához. Győződjön meg arról, hogy az ügyfélkapcsolatok képesek kezelni a megnövekedett terheléseket, valamint a terhelés kezeléséhez az ügyfélkapcsolatok automatizált eljárásait is.

## <a name="availability"></a>Rendelkezésre állás

A rendelkezésre állás az az idő, ameddig a rendszer működőképes és működik. A rendelkezésre állás az üzemidő százalékában mérhető. Az alkalmazás hibái, az infrastrukturális problémák és a rendszerterhelések egyaránt csökkentik a rendelkezésre állást.

Ennek az oszlopnak a fő vitájában a [*rendelkezésre állást*](/azure/architecture/framework/) az architektúra kiválóságának öt pillérében tekintheti meg.

### <a name="availability-checklist"></a>Rendelkezésre állási ellenőrzőlisták

**Redundancia biztosítása a kapcsolathoz.** A hibrid alkalmazásokhoz a felhőben való kapcsolat szükséges, amelyet az alkalmazás szétoszt. A hibrid kapcsolatokhoz választható technológiákat használhat, így az elsődleges technológián kívül egy másik technológiával is biztosíthatja az automatikus feladatátvételi képességeket, ha az elsődleges technológia meghibásodik.

**Tartalék tartományok besorolása.** A hibatűrő alkalmazások több tartalék tartományt igényelnek. A tartalék tartományok segítenek elkülöníteni a meghibásodási pontot, például ha egy merevlemez meghibásodik a helyszínen, ha egy felső szintű kapcsoló leáll, vagy ha a teljes adatközpont nem érhető el. Hibrid alkalmazásokban a helyek tartalék tartományként is besorolhatók. Több rendelkezésre állási követelmény esetén minél többre van szükség az egyetlen tartalék tartomány besorolásának kiértékeléséhez.

**Frissítési tartományok besorolása.** A frissítési tartományok segítségével biztosítható, hogy az alkalmazás-összetevők példányai elérhetők legyenek, míg az azonos összetevő más példányai a frissítésekkel vagy a funkciók frissítéseivel vannak szervizelve. A tartalék tartományokhoz hasonlóan a frissítési tartományok is besorolhatók az elhelyezésük helyei között. Meg kell határoznia, hogy egy alkalmazás-összetevő képes-e a frissítésre egy adott helyen, mielőtt újabb helyre frissítené, vagy ha más tartományi konfigurációra van szükség. Egyetlen hely több frissítési tartománnyal is rendelkezhet.

**A példányok és a rendelkezésre állás nyomon követése.** A magasan elérhető alkalmazás-összetevők a terheléselosztás és a szinkron adatreplikáció révén érhetők el. Meg kell határoznia, hogy hány példány lehet offline állapotban a szolgáltatás megszakítása előtt.

**Saját gyógyulás megvalósítása.** Abban az esetben, ha egy probléma megszakítja az alkalmazás rendelkezésre állását, a figyelőrendszer észlelése önjavító tevékenységeket kezdeményez az alkalmazás számára, például a sikertelen példány kiürítését és újbóli üzembe helyezését. Legvalószínűbb, hogy ez egy központi figyelési megoldás, amely egy hibrid folyamatos integrációval és folyamatos szállítási (CI/CD) folyamattal van integrálva. Az alkalmazás egy figyelő rendszerbe van integrálva az alkalmazás-összetevők újratelepítését igénylő problémák azonosításához. A figyelési rendszer a hibrid CI/CD-t is kiválthatja az alkalmazás-összetevő újbóli üzembe helyezéséhez, illetve az azonos vagy más helyen található egyéb függő összetevők újratelepítéséhez.

**Szolgáltatási szintű szerződések (SLA-kat) fenntartása.** A rendelkezésre állás kritikus fontosságú minden olyan szerződés esetében, amely kapcsolatot tart fenn az ügyfeleivel kötött szolgáltatásokkal és alkalmazásokkal. Előfordulhat, hogy a hibrid alkalmazásra támaszkodó helyek saját SLA-val rendelkeznek. A különböző SLA-kat befolyásolhatja a hibrid alkalmazás teljes SLA-ja.

## <a name="resiliency"></a>Rugalmasság

A rugalmasság lehetővé teszi, hogy egy hibrid alkalmazás és rendszer helyreállítsa a hibákat, és folytassa a működést. A rugalmasság célja, hogy az alkalmazást egy hiba bekövetkezése után teljesen működőképes állapotba adja vissza. A rugalmassági stratégiák olyan megoldások, mint a biztonsági mentés, a replikálás és a vész-helyreállítás.

Ennek az oszlopnak a fő vitájában lásd: [*rugalmasság*](https://docs.microsoft.com/azure/architecture/guide/pillars#resiliency) az architektúra kiválóságának öt pillérében.

### <a name="resiliency-checklist"></a>Rugalmasságra vonatkozó ellenőrzőlista

**A katasztrófa-helyreállítási függőségek felderítése.** Az egyik Felhőbeli vész-helyreállításhoz szükség lehet egy másik felhőben lévő alkalmazás-összetevők módosítására. Ha egy vagy több, egy felhőből származó összetevő feladatátvételt hajt végre egy másik helyre, vagy egy felhőben vagy egy másik felhőben, a függő összetevőket is ismerni kell a változásokról. Ez magában foglalja a kapcsolati függőségeket is. A rugalmassághoz minden felhőhöz teljes körűen tesztelt alkalmazás-helyreállítási terv szükséges.

**Helyreállítási folyamat létrehozása.** A helyreállítási folyamat hatékony kialakítása kiértékelte az alkalmazás-összetevőket, hogy képesek legyenek a pufferek, az újrapróbálkozások, a sikertelen adatforgalom újrapróbálkozására, és ha szükséges, térjen vissza egy másik szolgáltatásra vagy munkafolyamatra. Meg kell határoznia a használni kívánt biztonsági mentési mechanizmust, a visszaállítási eljárását, valamint azt, hogy milyen gyakran legyen tesztelve. A növekményes és a teljes biztonsági mentés gyakoriságát is meg kell határoznia.

**Részleges helyreállítások tesztelése.** Az alkalmazás egy részének részleges helyreállítása megadhatja a felhasználók számára, hogy az összes nem érhető el. A terv ezen részének biztosítania kell, hogy a részleges visszaállításnak ne legyen mellékhatása, például egy olyan biztonsági mentési és visszaállítási szolgáltatás, amely együttműködik az alkalmazással, hogy a biztonsági mentés előtt biztonságosan le legyen állítva.

**A vész-helyreállítási kezdeményezők meghatározása és a felelősség kiosztása.** A helyreállítási tervnek meg kell határoznia, hogy ki és milyen szerepköröket hozhat létre biztonsági mentési és helyreállítási műveletekhez, valamint arról, hogy miről is készíthető biztonsági mentés és visszaállítás.

**Az önjavító küszöbértékek összehasonlítása vész-helyreállítással.** Határozza meg az alkalmazások önjavító képességeit az automatikus helyreállítás megkezdéséhez, és az alkalmazás önjavító működéséhez szükséges időt a hiba vagy a siker szempontjából. Határozza meg az egyes felhő küszöbértékeit.

**A rugalmassági funkciók rendelkezésre állásának ellenőrzése.** Határozza meg az egyes helyek rugalmassági funkcióinak és képességeinek rendelkezésre állását. Ha egy hely nem biztosítja a szükséges képességeket, érdemes lehet integrálni az adott helyet egy központi szolgáltatásba, amely biztosítja a rugalmassági funkciókat.

**Határozza meg az állásidőt.** Határozza meg a várható állásidőt az alkalmazás teljes és alkalmazás-összetevőinek karbantartása miatt.

**Dokumentálja a hibaelhárítási eljárásokat.** Az erőforrások és az alkalmazás-összetevők újbóli üzembe helyezésével kapcsolatos hibaelhárítási eljárások meghatározása.

## <a name="manageability"></a>Kezelhetőség

A hibrid alkalmazások kezelésének szempontjai elengedhetetlenek az architektúra megtervezéséhez. A jól felügyelt hibrid alkalmazások olyan programkódot biztosítanak, amely lehetővé teszi az egységes alkalmazás kódjának közös fejlesztési folyamatban való integrálását. A következetes, rendszerszintű és egyedi, az infrastruktúrában végrehajtott módosítások végrehajtásával biztosíthatja az integrált telepítést, ha a módosítások átadják a teszteket, így azok egyesítve lesznek a forráskódba.

Ennek az oszlopnak a fő vitájában lásd: [*DevOps*](/azure/architecture/framework/#devops) az architektúra kiválóságának öt pillérében.

### <a name="manageability-checklist"></a>Kezelhetőségi ellenőrzőlista

**A figyelés implementálása.** A Felhőbeli alkalmazás-összetevők központosított figyelési rendszerének használata az állapot és a teljesítmény összesített nézetének biztosításához. Ez a rendszer az alkalmazás-összetevők és a kapcsolódó platform-képességek figyelését is magában foglalja.

A figyelést igénylő alkalmazás részeinek meghatározása.

**Koordinálja a házirendeket.** A hibrid alkalmazások által felölelt minden hely rendelkezhet saját házirenddel, amely az engedélyezett erőforrástípusok, a elnevezési konvenciók, a címkék és más feltételek alapján lehetséges.

**Szerepkörök definiálása és használata.** Adatbázis-rendszergazdaként meg kell határoznia a különböző személyekhez (például az alkalmazás tulajdonosához, az adatbázis-rendszergazdához és a végfelhasználóhoz) szükséges engedélyeket, amelyeknek hozzá kell férniük az alkalmazás erőforrásaihoz. Ezeket az engedélyeket az erőforrásokon és az alkalmazáson belül kell konfigurálni. A szerepköralapú hozzáférés-vezérlési (RBAC) rendszer lehetővé teszi, hogy ezeket az engedélyeket az alkalmazás erőforrásaira állítsa be. Ezek a hozzáférési jogosultságok akkor kihívást jelentenek, ha az összes erőforrást egyetlen felhőben telepítik, de még nagyobb figyelmet igényelnek, ha az erőforrások a felhők között oszlanak meg. Az egyik felhőben beállított erőforrásokra vonatkozó engedélyek nem vonatkoznak a más felhőben beállított erőforrásokra.

**CI/CD-folyamatok használata.** A folyamatos integráció és a folyamatos fejlesztés (CI/CD) folyamata egységes folyamatot biztosít a felhőkre kiterjedő alkalmazások létrehozásához és üzembe helyezéséhez, valamint az infrastruktúra és az alkalmazás minőségének biztosításához. Ez a folyamat lehetővé teszi, hogy az infrastruktúra és az alkalmazás egy felhőben legyen tesztelve, és egy másik felhőben legyen üzembe helyezhető. A folyamat lehetővé teszi a hibrid alkalmazások bizonyos összetevőinek üzembe helyezését egy felhőbe és más összetevőkbe egy másik felhőbe, amely lényegében a hibrid alkalmazások üzembe helyezésének alapja. A CI/CD rendszer kritikus fontosságú a függőségek alkalmazás-összetevőinek a telepítés során történő kezelésére, például a webalkalmazáshoz, amelyhez kapcsolati sztringre van szükség az adatbázishoz.

**A életciklus kezelése.** Mivel a hibrid alkalmazások erőforrásai a helyekre terjedhetnek, minden egyes hely életciklus-kezelési képességét egyetlen életciklusú felügyeleti egységbe kell összevonni. Gondolja át, hogyan lettek létrehozva, frissítve és törölve.

**A hibaelhárítási stratégiák vizsgálata.** A hibrid alkalmazások hibaelhárítása több alkalmazás-összetevőt is magában foglal, mint az egyetlen felhőben futó alkalmazás. A felhők közötti kapcsolat mellett az alkalmazás két platformon fut egy helyett. A hibrid alkalmazások hibaelhárításának egyik fontos feladata az alkalmazás-összetevők összesített állapotának és teljesítményének figyelése.

## <a name="security"></a>Biztonság

A biztonság a felhőalapú alkalmazások egyik elsődleges szempontja, és még fontosabbá válik a hibrid felhőalapú alkalmazásokhoz.

Ennek az oszlopnak a fő vitájában lásd: [*Biztonság*](https://docs.microsoft.com/azure/architecture/guide/pillars#security) az architektúra kiválóságának öt pillérében.

### <a name="security-checklist"></a>Biztonsági ellenőrzőlista

**A szabálysértés feltételezése.** Ha az alkalmazás egy része biztonságban van, ellenőrizze, hogy vannak-e olyan megoldások, amelyekkel csökkenthető a szerződésszegés eloszlása, nem csupán ugyanazon a helyen, hanem a helyek között is.

**Az engedélyezett hálózati hozzáférés figyelése.** Határozza meg az alkalmazáshoz tartozó hálózati hozzáférési házirendeket, például csak egy adott alhálózatból származó alkalmazást, és csak az alkalmazás megfelelő működéséhez szükséges összetevők közötti minimális portokat és protokollokat engedélyezze.

**Robusztus hitelesítés alkalmazása.** Az alkalmazás biztonsága szempontjából rendkívül fontos a robusztus hitelesítési séma. Fontolja meg egy összevont identitás-szolgáltató használatát, amely egyszeri bejelentkezéses képességeket biztosít, és a következő sémák közül egyet vagy többet alkalmaz: Felhasználónév és jelszó bejelentkezés, nyilvános és titkos kulcsok, kétfaktoros vagy többtényezős hitelesítés és megbízható biztonsági csoportok. Határozza meg a megfelelő erőforrásokat a bizalmas adatok és egyéb titkos kódok tárolására az alkalmazás hitelesítéséhez a tanúsítványok típusai és a rájuk vonatkozó követelmények mellett.

**Használjon titkosítást.** Határozza meg, hogy az alkalmazás mely területei használják a titkosítást, például az adattároláshoz vagy az ügyfél-kommunikációhoz és a hozzáféréshez.

**Biztonságos csatornák használata.** A felhőben a biztonságos csatorna kritikus fontosságú a biztonsági és hitelesítési ellenőrzések, a valós idejű védelem, a karanténba helyezés és más szolgáltatások biztosításához a felhők között.

**Szerepkörök definiálása és használata.** Szerepkörök implementálása erőforrás-konfigurációkhoz és egyidentitású hozzáférés a felhők között. Határozza meg a szerepköralapú hozzáférés-vezérlés (RBAC) követelményeit az alkalmazáshoz és a platform erőforrásaihoz.

**A számítógép naplózása.** A Rendszerfigyelő az alkalmazás-összetevőkből és a kapcsolódó felhőalapú platform műveleteiből is naplózhatja és összesítheti az adatokat.

## <a name="summary"></a>Összefoglalás

Ez a cikk azon elemek listáját tartalmazza, amelyeket fontos figyelembe venni a hibrid alkalmazások létrehozásakor és tervezésekor. Az alkalmazás üzembe helyezése előtt tekintse át ezeket az oszlopokat, hogy ne fusson ezen kérdésekre az éles környezetben, és esetleg a terv újbóli megkeresését is szükségessé teszi.

Úgy tűnhet, mint egy időigényes feladat, de a beruházások megtérülését egyszerűen megteheti, ha az alkalmazást ezen oszlopok alapján tervezik.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

- [Hibrid felhő](https://azure.microsoft.com/overview/hybrid-cloud/)
- [Hibrid felhőalapú alkalmazások](https://azure.microsoft.com/solutions/hybrid-cloud-app/)
- [Azure Resource Manager-sablonok fejlesztése felhőkonzisztenciához](https://aka.ms/consistency)