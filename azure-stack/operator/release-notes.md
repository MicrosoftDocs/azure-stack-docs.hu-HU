---
title: Azure Stack hub kibocsátási megjegyzései
description: Kibocsátási megjegyzések a Azure Stack hub integrált rendszereihez, beleértve a frissítéseket és a hibajavításokat is.
author: sethmanheim
ms.topic: article
ms.date: 09/28/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 08/11/2020
ms.openlocfilehash: cff17c92a44bec23d0a10620d6c182ad5240c451
ms.sourcegitcommit: 703be61f2f1565bf478b8c184753869c29e5c33c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91495796"
---
# <a name="azure-stack-hub-release-notes"></a>Azure Stack hub kibocsátási megjegyzései

Ez a cikk Azure Stack hub frissítési csomagjainak tartalmát ismerteti. A frissítés az Azure Stack hub legújabb kiadásának javításait és javításait is tartalmazza.

Ha egy másik verzió kibocsátási megjegyzéseit szeretné elérni, a bal oldali tartalomjegyzéknél használja a verziószám legördülő menüt.

::: moniker range=">=azs-1910"
> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack hub integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kitra (ASDK).
::: moniker-end
::: moniker range="<azs-1910"
> [!IMPORTANT]  
> Ha a Azure Stack hub-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues.md)
- [Biztonsági frissítések](release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](release-notes-checklist.md)

A frissítésekkel és a frissítési folyamattal kapcsolatos segítségért lásd: [a Azure stack hub javításával és frissítésével kapcsolatos hibák elhárítása](azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>A frissítés letöltése

Az Azure Stack hub frissítési csomagot [az Azure stack hub Update Downloader Tool](https://aka.ms/azurestackupdatedownload)használatával töltheti le.

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>2005-Build referenciája

Az Azure Stack hub 2005 Update Build száma **1.2005.6.53**.

### <a name="update-type"></a>Frissítéstípus

Az Azure Stack hub 2005 Update Build típusa **megtelt**.

Az 2005-es frissítési csomag mérete nagyobb a korábbi frissítésekhez képest. A megnövelt méret a letöltendő időt eredményezi. A frissítés hosszú ideig az **előkészítési** állapotban marad, és a kezelők a korábbi frissítéseknél hosszabb időt vehetnek igénybe. Az 2005-es frissítés a belső tesztelési környezet következő várt futtatókörnyezeteit használta: 13-20 óra, 8 csomópont: 16-26 óra, 12 csomópont: 19-32 óra, 16 csomópont: 22-38 óra. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél rövidebb vagy hosszabb futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack hub-operátorok számára, kivéve, ha a frissítés meghiúsul. Ez a futtatókörnyezeti közelítés az 2005-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->
- Ez a Build a következő 3 új GPU virtuálisgép-típust támogatja: NCv3 (NVIDIA V100), NVv4 (AMD MI25) és NCas_v4 (NVIDIA T4) VM-méretek. A virtuális gépek központi telepítése sikeres lesz azok számára, akik rendelkeznek a megfelelő hardverrel, és az Azure Stack hub GPU előzetes programba vannak beépítve. Ha érdekli, regisztráljon a GPU előzetes programjára a következő címen: https://aka.ms/azurestackhubgpupreview . További információ [:](../user/gpu-vms-about.md).
- Ez a kiadás egy új szolgáltatást biztosít, amely lehetővé teszi egy önálló gyógyító képességet, amely észleli a hibákat, értékeli a hatásokat, és biztonságosan csökkenti a rendszerproblémákat. Ezzel a szolgáltatással a rendszer manuális beavatkozás nélkül is nagyobb rendelkezésre állásra dolgozunk. A 2005-es és újabb verziókban az ügyfelek a riasztások számának csökkenését fogják tapasztalni. Az ebben a folyamatban lévő hibák nem igénylik a Azure Stack hub operátorok beavatkozását, kivéve, ha értesítést kapnak.
- Új lehetőség van a Azure Stack hub felügyeleti portál gapped/leválasztott Azure Stack hub-ügyfelei számára, hogy helyileg mentse a naplókat. A naplókat helyi SMB-megosztásban is tárolhatja, ha Azure Stack hub le van választva az Azure-ból.
- A Azure Stack hub felügyeleti portál mostantól blokkolja bizonyos műveleteket, ha már folyamatban van egy rendszerművelet. Ha például egy frissítés folyamatban van, nem lehet új méretezési egység csomópontot felvenni.
- Ez a kiadás az Azure-ra épülő, a 1910 előtti virtuális gépekkel kapcsolatos további hálós konzisztenciát biztosít. 1910-ben a Microsoft bejelentette, hogy minden újonnan létrehozott virtuális gép a wireserver protokollt fogja használni, amely lehetővé teszi, hogy az ügyfelek ugyanazt a WALA-ügynököt és a Windows-ügynököt használják az Azure-ban, így könnyebben használhatja az Azure images szolgáltatást Azure Stack hub-on Ebben a kiadásban az 1910-nál korábbinál korábban létrehozott virtuális gépeket a rendszer automatikusan áttelepíti a wireserver protokoll használatára. Ez a virtuális gépek és a virtuálisgép-bővítmények üzembe helyezését, valamint az állandó állapotú üzemidő fejlesztését is lehetővé teszi.
- Az Azure Stack hub Storage mostantól támogatja az Azure Storage Services API 2019-02-02-es verzióját. Az Azure ügyféloldali kódtárak esetében, amelyek kompatibilisek az új REST API verziójával. További információ: [Azure stack hub Storage fejlesztői eszközök](../user/azure-stack-storage-dev.md#azure-client-libraries).
- Az Azure Stack hub mostantól támogatja a CreateUiDefinition legújabb verzióját [(2. verzió)](/azure/azure-resource-manager/managed-applications/create-uidefinition-overview).
- Új útmutató a kötegelt virtuális gépek üzembe helyezéséhez. További információkért [tekintse meg ezt a cikket](../operator/azure-stack-capacity-planning-compute.md).
- Az Azure Stack hub Marketplace CoreOS-tároló linuxos eleme a teljes [élettartamot közelíti](https://azure.microsoft.com/updates/flatcar-in-azure/)meg. További információ: [áttelepítés a CoreOS Container Linux rendszerből](https://docs.flatcar-linux.org/os/migrate-from-container-linux/).

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- A Storage infrastruktúra-fürtszolgáltatás naplói és eseményeinek fejlesztése. A Storage infrastruktúra-fürtszolgáltatás naplóit és eseményeit legfeljebb 14 napig őrzi meg a rendszer a jobb diagnosztika és hibaelhárítás érdekében.
- Az Azure Stack hub indításának és leállításának megbízhatóságát növelő újítások.
- A frissítési futtatókörnyezetet a deközpontosító használatával és a függőségek eltávolításával csökkentheti. Az 2002-es frissítéssel összehasonlítva a 4 csomópontos időbélyeg-frissítési idő 15-42 óra és 13-20 óra között csökken. 8 csomópont 20-50 óra és 16-26 óra között csökkent. 12 csomópont 20-60 óra és 19-32 óra között csökkent. 16 csomópont 25-70 óra és 22-38 óra között csökkent. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek.
- A frissítés mostantól leáll, ha van bizonyos Helyreállíthatatlan hiba.
- A frissítési csomag továbbfejlesztett rugalmassága az internetről való letöltéskor.
- Javult a virtuális gép felszabadításának leállítása.
- A hálózati vezérlő gazdagép-ügynökének javított rugalmassága.
- További mezők lettek hozzáadva a syslog-üzenetek CEF hasznos adataihoz a forrás IP-cím és a privilegizált végponthoz és a helyreállítási végponthoz való kapcsolódáshoz használt fiók jelentéséhez. A részletekért lásd: [Azure stack hub integrálása figyelési megoldásokkal a syslog forwarding használatával](azure-stack-integrate-security.md) .
- Windows Defender-események (eseményazonosító: 5001, 5010, 5012) hozzáadása a syslog-ügyfélen keresztül kibocsátott események listájához.
- Riasztások hozzáadása a Azure Stack felügyeleti portálon a Windows Defender szolgáltatással kapcsolatos eseményekhez, a Defender platform és az aláírások verziójának inkonzisztencia-adatainak jelentéséhez, valamint az észlelt kártevők elleni műveletek elmulasztásához.
- A 4 határos eszköz támogatása a Azure Stack hub adatközpontba való integrálásakor.

### <a name="changes"></a>Módosítások

- Eltávolította a leállításhoz, leállításhoz és az infrastruktúra szerepkör-példányának újraindításához szükséges műveleteket a felügyeleti portálról. A kapcsolódó API-k is el lettek távolítva a háló erőforrás-szolgáltatóban. A következő PowerShell-parancsmagok a rendszergazda RM-modulban és az előzetes verzióban Azure Stack hub-ban már nem működnek: **stop-AzsInfrastructureRoleInstance**, **disable-InfrastructureRoleInstance**, és **restart-InfrastructureRoleInstance**. Ezek a parancsmagok el lesznek távolítva a Azure Stack hub következő rendszergazdájától az Module kiadásával.
- A Azure Stack hub 2005 mostantól csak [az Azure stack hub 2020 (87. x verzió) App Service](app-service-release-notes-2020-Q2.md)támogatja.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavítva egy olyan hibát, amely miatt a javítási méretezési egység csomópontja meghiúsul, mert nem találta meg az alap operációs rendszer rendszerképének elérési útját.
- Kijavítottuk a méretezési egység csomópontjainak kijavításával kapcsolatos lépcsőzetes hatással rendelkező támogatási infrastruktúra szerepkörre kiterjedő, skálázással kapcsolatos problémát.
- Kijavítva egy probléma, amelyben a. A VHD-bővítmény (a. vhd helyett) nem engedélyezett, ha az operátorok a Azure Stack hub felügyeleti portálra felvettek saját lemezképeket az **összes szolgáltatásban > számítási > virtuálisgép-lemezképek > Hozzáadás gombra**.
- Kijavítva egy probléma, amelyben egy korábbi virtuális gép újraindítási művelete egy későbbi, váratlan újraindítást okozott a virtuális gép frissítési művelete után (lemezek, címkék stb. hozzáadása).
- Kijavított egy olyan problémát, amelyben egy duplikált DNS-zóna létrehozása miatt a portál nem válaszol. Ekkor a megfelelő hibát kell megmutatnia.
- Kijavított egy problémát, amelyben a **Get-AzureStackLogs** nem gyűjtötte össze a szükséges naplókat a hálózati problémák elhárításához. 
- Kijavított egy problémát, amelyben a portálon kevesebb hálózati adapter csatlakoztatható, mint amit valójában engedélyez. 
- Rögzített kód-integritási házirend, amely nem bocsát ki szabálysértési eseményeket bizonyos belső szoftverekhez. Ez csökkenti a syslog-ügyfélen keresztül kibocsátott kód integritásának megsértésével kapcsolatos események okozta zajt.
- Rögzített **set-TLSPolicy** parancsmag az új házirend betartatásához a https-szolgáltatás újraindítása vagy a gazdagép újraindítása nélkül.
- Kijavítva egy probléma, amelyben a Linux NTP-kiszolgáló hibásan hoz létre riasztásokat a felügyeleti portálon.  
- Kijavítva a hiba, ahol a biztonságimásolat-vezérlő szolgáltatási példányának feladatátvétele az automatikus biztonsági mentések letiltását eredményezte.
- Kijavítva a probléma, amelyben a belső titkos rotáció meghiúsul, ha az infrastrukturális szolgáltatások nem rendelkeznek internetkapcsolattal.
- Kijavított egy problémát, amelyben a felhasználók nem tekinthetik meg az előfizetési engedélyeket az Azure Stack hub-portálok használatával.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](release-notes-security-updates.md).

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Az 2005-es kiadástól kezdve, amikor új főverzióra frissít (például: 1.2002. x – 1.2005. x), a rendszer automatikusan telepíti a legújabb gyorsjavításokat (ha vannak ilyenek) az új főverzióban. Ettől a ponttól kezdve, ha a buildhez kiadott egy gyorsjavítást, telepítse azt.

> [!NOTE]
> Azure Stack hub gyorsjavítási kiadásai kumulatívak; a legújabb gyorsjavítást csak akkor kell telepíteni, ha az adott verzióhoz tartozó korábbi gyorsjavítások összes javítását beolvassa.

További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="prerequisites-before-applying-the-2005-update"></a>Előfeltételek: az 2005-es frissítés alkalmazása előtt

Azure Stack hub 2005-es kiadását a 2002-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

- [Azure Stack hub gyorsjavítási 1.2002.56.152](https://support.microsoft.com/help/4582983)

### <a name="after-successfully-applying-the-2005-update"></a>Az 2005-es frissítés sikeres alkalmazása után

Az 2005-es kiadástól kezdve, amikor új főverzióra frissít (például: 1.2002. x – 1.2005. x), a rendszer automatikusan telepíti a legújabb gyorsjavításokat (ha vannak ilyenek) az új főverzióban.

Ha a 2005-es verzió telepítése után a rendszer a 2005-es gyorsjavításokat is felszabadítja, telepítse őket:

- [Azure Stack hub gyorsjavítási 1.2005.13.68](https://support.microsoft.com/help/4583399)
::: moniker-end

::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>2002-Build referenciája

Az Azure Stack hub 2002 Update Build száma **1.2002.0.35**.

> [!IMPORTANT]  
> A Azure Stack hub 2002-es frissítésével a Microsoft átmenetileg kiterjeszti [Azure stack hub támogatási szabályzatának utasításait](azure-stack-servicing-policy.md).  A COVID-19-re válaszoló világbeli ügyfelekkel dolgozunk, akik fontos döntéseket hozhatnak a Azure Stack hub-rendszerekkel kapcsolatban, hogyan frissülnek és felügyelnek, és ennek eredményeképpen gondoskodnak arról, hogy az adatközpont üzleti műveletei továbbra is megfelelően működjenek. Ügyfeleink támogatásában a Microsoft egy ideiglenes támogatási szabályzatot kínál, amely három korábbi frissítési verziót tartalmaz.  Ennek eredményeképpen az újonnan kiadott 2002-es frissítés és a három korábbi frissítési verzió (például a 1910, az 1908 és az 1907) közül bármelyiket támogatja a rendszer.

### <a name="update-type"></a>Frissítéstípus

Az Azure Stack hub 2002 Update Build típusa **megtelt**.

Az 2002-es frissítési csomag mérete nagyobb a korábbi frissítésekhez képest. A megnövelt méret a letöltendő időt eredményezi. A frissítés hosszú ideig az **előkészítési** állapotban marad, és a kezelők a korábbi frissítéseknél hosszabb időt vehetnek igénybe. Az 2002-es frissítés a belső tesztelési környezet következő várt futtatókörnyezeteit használta: 15-42 óra, 8 csomópont: 20-50 óra, 12 csomópont: 20-60 óra, 16 csomópont: 25-70 óra. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél rövidebb vagy hosszabb futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack hub-operátorok számára, kivéve, ha a frissítés meghiúsul. Ez a futtatókörnyezeti közelítés az 2002-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- Elérhető a AzureRM-alapú Azure Stack hub rendszergazdai PowerShell-modulok új verziója (1.8.1).
- A Azure Stack hub felügyeleti REST API új verziója érhető el. A végpontokkal kapcsolatos részleteket és az [API-referenciák](/rest/api/azure-stack/)változásait is megtalálhatja.
- Az új Azure PowerShell bérlői modulok a Azure Stack hub 2020. április 15-én lesznek közzétéve. A jelenleg használt Azure RM-modulok továbbra is működni fognak, de az 2002-es Build után már nem lesznek frissítve.
- Új figyelmeztető riasztás hozzáadva a Azure Stack hub felügyeleti portálján a konfigurált syslog-kiszolgálóval fennálló csatlakozási problémák jelentéséhez. A riasztás címe **a syslog-ügyfél hálózati hibát észlelt a syslog-üzenet küldése közben**.
- Új figyelmeztető riasztás hozzáadva az Azure Stack hub felügyeleti portálján a Network Time Protocol (NTP) kiszolgáló csatlakozási problémáinak jelentéséhez. A riasztás címe **érvénytelen időforrást mutat a következőn: [csomópont neve]**.
- A [Java SDK](https://azure.microsoft.com/develop/java/) új csomagokat adott közzé a TLS-korlátozásokkal kapcsolatos 2002-es megszakítási változás miatt. Telepítenie kell az új Java SDK-függőséget. Az utasításokat a [Java és az API-verziók profiljaiban](../user/azure-stack-version-profiles-java.md?view=azs-2002#java-and-api-version-profiles)találja.
- Az System Center Operations Manager-Azure Stack hub felügyeleti csomag új verziója (1.0.5.10) elérhető és szükséges a 2002-at futtató összes rendszerhez az API-változások megszakítása miatt. Az API megváltoztatja a biztonsági mentési és a tárolási teljesítmény irányítópultokat, és azt javasoljuk, hogy a felügyeleti csomag frissítése előtt frissítse az összes rendszert a 2002-re.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Ez a frissítés a frissítési folyamat olyan változásait tartalmazza, amelyek jelentősen javítják a jövőbeli teljes frissítések teljesítményét. Ezek a változások a 2002-es kiadás utáni következő teljes frissítéssel lépnek érvénybe, és konkrétan a gazdagép operációs rendszereinek frissítésére szolgáló teljes frissítés fázisának teljesítményét célozzák meg. A gazda operációsrendszer-frissítések teljesítményének javítása jelentősen csökkenti azt az időpontot, amelyben a bérlői munkaterhelések hatással vannak a teljes frissítések során.
- Az Azure Stack hub Readiness-ellenőrző eszköz mostantól ellenőrzi az AD Graph-integrációt az AD Graph számára lefoglalt összes TCP IP-port használatával.
- Az offline szindikált eszköz frissítése megbízhatósági frissítésekkel történt. Az eszköz már nem érhető el a GitHubon, és [át lett helyezve a PowerShell-galériaba](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/). További információ: [Marketplace-elemek letöltése Azure stack hubhoz](azure-stack-download-azure-marketplace-item.md).
- Új figyelési képesség bevezetése folyamatban van. A fizikai gazdagépek és az infrastruktúra-virtuális gépek alacsony lemezterület-riasztása automatikusan kijavítja a platformot, és csak akkor, ha ez a művelet meghiúsul, a riasztás látható lesz az Azure Stack hub felügyeleti portálján, hogy az operátor végrehajtsa a műveletet.
- A [diagnosztikai naplók gyűjtésének](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)fejlesztése. Az új felület egyszerűsíti és egyszerűbbé teszi a diagnosztikai naplók gyűjtését azáltal, hogy eltávolítja a blob Storage-fiók előzetes konfigurálásának szükségességét. A tárolási környezet előre konfigurálva van, így naplók küldhetők a támogatási eset megnyitása előtt, és kevesebb időt kell fordítani a támogatási hívásokra.
- Az előjelzéses [naplók és az igény szerinti naplók gyűjtésére](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)vonatkozó idő   80%-kal csökkent. A naplózási gyűjtési idő hosszabb időt vehet igénybe a várt értéknél, de Azure Stack hub-operátorok beavatkozása nem szükséges, kivéve, ha a naplózási gyűjtemény sikertelen.
- Egy Azure Stack hub-frissítési csomag letöltési folyamata mostantól látható a frissítés panelen a frissítés kezdeményezése után. Ez csak azokra a csatlakoztatott Azure Stack hub rendszerekre vonatkozik, amelyek a [frissítési csomagok automatikus letöltéssel történő előkészítését](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages)választják.
- A hálózati vezérlő gazdagép-ügynökének megbízhatósági fejlesztése.
- Egy új, DNS-Orchestrator nevű Micro-szolgáltatást vezetett be, amely javítja a belső DNS-szolgáltatások rugalmassági logikáját a javítás és a frissítés során.
- Új kérelem érvényesítése sikertelen volt, mert a virtuális gépek létrehozásakor a rendszer érvénytelen blob URI-azonosítókat adott a rendszerindítási diagnosztikai Storage-fiók paraméteréhez.
- A Rdagent és a Host Agent automatikus szervizelési és naplózási funkcióinak hozzáadása – a gazdagép két szolgáltatása, amely megkönnyíti a virtuális gépek SZIFILISZét.
- Új szolgáltatás hozzáadása a piactér-kezeléshez, amely lehetővé teszi, hogy a Microsoft olyan attribútumokat adjon hozzá, amelyek letiltják a rendszergazdáknak, hogy a különböző tulajdonságok, például a Azure Stack vagy a számlázási modell miatt nem kompatibilisek legyenek a Azure Stack. Csak a Microsoft adhatja hozzá ezeket az attribútumokat. További információ: [a Marketplace-elemek letöltésére szolgáló portál használata](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items).

### <a name="changes"></a>Módosítások

- A felügyeleti portál most azt jelzi, hogy egy művelet folyamatban van-e, és a Azure Stack régió melletti ikonra kattint. Amikor a ikon fölé viszi a kurzort, megjelenik a művelet neve. Ez lehetővé teszi a futó rendszer hátterében lévő műveletek azonosítását; például egy biztonsági mentési feladatokra vagy egy tárolási bővítésre, amely több órán keresztül is futtatható.

- A következő rendszergazdai API-k elavultak:

  | Erőforrás-szolgáltató       | Erőforrás              | Verzió            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft. Storage. admin | farmok                 | 2015-12-01 – előzetes verzió |
  | Microsoft. Storage. admin | farmok/beszerzések    | 2015-12-01 – előzetes verzió |
  | Microsoft. Storage. admin | farmok/megosztások          | 2015-12-01 – előzetes verzió |
  | Microsoft. Storage. admin | farmok/storageaccounts | 2015-12-01 – előzetes verzió |

- A következő rendszergazdai API-kat egy újabb verzió váltotta fel (2018-09-01):

  | Erőforrás-szolgáltató      | Erőforrás              | Verzió    |
  |------------------------|-----------------------|------------|
  | Microsoft. backup. admin | backupLocation         | 2016-05-01 |
  | Microsoft. backup. admin | mentések                | 2016-05-01 |
  | Microsoft. backup. admin | műveletek             | 2016-05-01 |

- Windows rendszerű virtuális gép PowerShell használatával történő létrehozásakor ügyeljen arra, hogy adja hozzá a `provisionvmagent` jelzőt, ha azt szeretné, hogy a virtuális gép bővítményeket helyezzen üzembe. A jelző nélkül a virtuális gép a vendég ügynök nélkül jön létre, és nem távolítható el a virtuálisgép-bővítmények üzembe helyezésének lehetősége:

   ```powershell
   $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName "MainComputer" `
     -Credential $Credential -ProvisionVMAgent
  ```

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavított egy problémát, amelyben a virtuális gép ugyanazon hálózati adapterén található több nyilvános IP-cím hozzáadása internetkapcsolattal kapcsolatos problémákat eredményezett. A két nyilvános IP-címmel rendelkező hálózati adapter a várt módon működik.
- Kijavított egy problémát, amely miatt a rendszer riasztást küld, ami azt jelzi, hogy az Azure AD-kezdőkönyvtár konfigurálása szükséges.
- Kijavítva egy olyan hibát, amely miatt a riasztás nem zárult le automatikusan. A riasztás azt jelezte, hogy az Azure AD-kezdőkönyvtár konfigurálása kötelező, de a probléma enyhítése után sem zárult le.
- Kijavítva egy olyan problémát, amely a frissítés előkészítési fázisában hibát okozott a frissítések erőforrás-szolgáltatójának belső meghibásodása miatt.
- Kijavított egy problémát, ami az erőforrás-szolgáltatói műveletek Azure Stack hub titkos elforgatása után meghiúsul.
- Kijavított egy problémát, amely az Azure Stack hub frissítési hibáinak gyakori okát okozta a ERCS szerepkörön történt memória-nyomás miatt.
- Kijavított egy hibát abban a frissítési panelen, amelyben a frissítési állapot az Azure Stack hub-frissítés előkészítési fázisában való **felkészülés** helyett **telepítésként** látható.
- Kijavított egy hibát, amelyben a virtuális kapcsolók RSC funkciója létrehozta a inkonzisztenciák, és eldobott egy terheléselosztó használatával áramló forgalmat. A RSC szolgáltatás alapértelmezés szerint le van tiltva.
- Kijavítva a hiba, ahol a hálózati adapter több IP-konfigurációja okozta a forgalmat, és megakadályozta a kimenő kapcsolatot. 
- Kijavított egy hibát, amikor egy hálózati adapter MAC-címe gyorsítótárazva lett, és az adott címnek egy másik erőforráshoz való hozzárendelésének oka a virtuális gép üzembe helyezésének meghibásodása volt.
- Kijavítva a hiba, hogy a kereskedelmi csatornán futó Windows rendszerbeli rendszerképeket nem lehetett aktiválni a AVMA.
- Kijavított egy hibát, amelyben a virtuális gépeket nem lehet létrehozni, ha a virtuális gép által igényelt virtuális magok száma a csomópont fizikai magokkal egyenlő. Mostantól engedélyezzük, hogy a virtuális magok egyenlőek legyenek a csomópont fizikai magokkal, vagy kisebbek legyenek.
- Kijavított egy problémát, amelyben nem engedélyezzük, hogy a licenc "NULL" értékűre legyen állítva az utólagos elszámolású lemezképek BYOL való váltásához.
- Kijavított egy problémát, amely lehetővé teszi a bővítmények hozzáadását a virtuálisgép-méretezési csoportokhoz.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](release-notes-security-updates.md).

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Győződjön meg arról, hogy a 1910-es legújabb Azure Stack hub gyorsjavítását telepíti a 2002-es Azure Stack hub frissítése előtt.

> [!NOTE]
> Azure Stack hub gyorsjavítási kiadásai kumulatívak; a legújabb gyorsjavítást csak akkor kell telepíteni, ha az adott verzióhoz tartozó korábbi gyorsjavítások összes javítását beolvassa.

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

A gyorsjavításokkal kapcsolatos további információkért tekintse meg a [Azure stack hub karbantartási házirendjét](azure-stack-servicing-policy.md#hotfixes).

### <a name="prerequisites-before-applying-the-2002-update"></a>Előfeltételek: az 2002-es frissítés alkalmazása előtt

Azure Stack hub 2002-es kiadását a 1910-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1910.63.186](https://support.microsoft.com/help/4574735)

### <a name="after-successfully-applying-the-2002-update"></a>Az 2002-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást.

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.2002.56.152](https://support.microsoft.com/help/4582983)
::: moniker-end

::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910-Build referenciája

Az Azure Stack hub 1910 Update Build száma **1.1910.0.58**.

### <a name="update-type"></a>Frissítéstípus

Az 1908-es verziójától kezdve a mögöttes operációs rendszer, amelyen Azure Stack hub fut, a Windows Server 2019-re frissült. Ez a frissítés lehetővé teszi az alapszintű fejlesztéseket és az Azure Stack hub további képességeinek kihasználását.

Az Azure Stack hub 1910 Update Build típusa **expressz**.

Az 1910-es frissítési csomag mérete nagyobb a korábbi frissítésekhez képest, ami hosszabb letöltési időt eredményez. A frissítés hosszú ideig az **előkészítési** állapotban marad, és a kezelők elvárhatják, hogy ez a folyamat továbbra is a korábbi frissítéseknél tovább tartson. Az 1910-es frissítés befejezésének várható időpontja körülbelül 10 óra, az Azure Stack hub-környezetben található fizikai csomópontok számától függetlenül. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek műveletet Azure Stack hub-operátorok, kivéve, ha a frissítés meghiúsul. Ez a futtatókörnyezeti közelítés az 1910-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- A felügyeleti portál mostantól megjeleníti a Kiemelt végpontok IP-címeit a régió tulajdonságok menüjében, amely megkönnyíti a felderítést. Emellett megjeleníti az aktuálisan konfigurált időkiszolgálót és a DNS-továbbítókat. További információkat [a kiemelt végpont Azure Stack Hubbeli használatát ismertető részben](azure-stack-privileged-endpoint.md) talál.

- A Azure Stack hub állapot-és monitorozási rendszere mostantól hibát okozhat a különböző hardver-összetevőkön. Ezek a riasztások további konfigurálást igényelnek. További információ: [Azure stack hub hardver-összetevők figyelése](azure-stack-hardware-monitoring.md).

- [Cloud-init támogatás az Azure stack hub számára](/azure/virtual-machines/linux/using-cloud-init): a Cloud-init egy széles körben használt módszer a Linux RENDSZERű virtuális gépek első indításához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a Cloud-init a kezdeti rendszerindítási folyamat során hívásra kerül, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához. A piactéren lévő Ubuntu-lemezképek frissítve lettek a Cloud-init támogatásához a kiépítés érdekében.

- Az Azure Stack hub mostantól támogatja az összes Windows Azure Linux-ügynök verzióját az Azure-ban.

- A Azure Stack hub rendszergazdai PowerShell-moduljainak új verziója érhető el. <!-- For more information, see -->

- A Azure Stack hub új Azure PowerShell bérlői moduljait 2020. április 15-én adták ki. A jelenleg használt Azure RM-modulok továbbra is működni fognak, de az 2002-es Build után már nem lesznek frissítve.

- Hozzáadta a **set-AzSDefenderManualUpdate** parancsmagot az emelt szintű végponthoz (PEP) a Windows Defender-definíciók manuális frissítésének konfigurálásához az Azure stack hub-infrastruktúrában. További információ: [a Windows Defender víruskereső frissítése Azure stack hub-on](azure-stack-security-av.md).

- Hozzáadta a **Get-AzSDefenderManualUpdate** parancsmagot a Kiemelt jogosultságú végponthoz (PEP), hogy lekérje a Windows Defender-definíciók manuális frissítésének konfigurációját az Azure stack hub-infrastruktúrában. További információ: [a Windows Defender víruskereső frissítése Azure stack hub-on](azure-stack-security-av.md).

- Hozzáadta a **set-AzSDnsForwarder** parancsmagot a Kiemelt jogosultságú végponthoz (PEP) a Azure stack hub DNS-kiszolgálóinak továbbító beállításainak módosításához. A DNS-konfigurációval kapcsolatos további információkért lásd: [Azure stack hub Datacenter DNS-integráció](azure-stack-integrate-dns.md).

- Hozzáadta a **Get-AzSDnsForwarder** parancsmagot a Kiemelt jogosultságú végponthoz (PEP) a DNS-kiszolgálók továbbító beállításainak lekéréséhez Azure stack központban. A DNS-konfigurációval kapcsolatos további információkért lásd: [Azure stack hub Datacenter DNS-integráció](azure-stack-integrate-dns.md).

- A **Kubernetes-fürtök** az [AK-motor](../user/azure-stack-kubernetes-aks-engine-overview.md)használatával történő felügyeletének támogatása. Ettől a frissítéstől kezdve az ügyfelek üzembe helyezhetik a termelési Kubernetes-fürtöket. Az AK-motor lehetővé teszi a felhasználók számára a következőket:
  - Kezelheti a Kubernetes-fürtök életciklusát. Létrehozhatják, frissíthetik és méretezhetik a fürtöket.
  - A fürtöket az AK és a Azure Stack hub csapatai által készített felügyelt lemezképek használatával kezelheti.
  - Használjon olyan Azure Resource Manager integrált Kubernetes-szolgáltatót, amely natív Azure-erőforrásokkal rendelkező fürtöket hoz létre.
  - Fürtök üzembe helyezése és kezelése csatlakoztatott vagy leválasztott Azure Stack hub-bélyegzővel.
  - Azure Hybrid-funkciók használata:
    - Integráció az Azure Arcmal.
    - Integráció a Azure Monitor for containers szolgáltatással.
  - Használjon Windows-tárolókat az AK-motorral.
  - Microsoft ügyfélszolgálata és mérnöki támogatás fogadása az üzemelő példányokhoz.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Az Azure Stack hub javította a frissítési hibák vagy a megakadályozta, hogy az operátorok egy Azure Stack hub-frissítés kezdeményezésének elindításához szükséges javítási és frissítési problémákat. Ennek eredményeképpen a **test-AzureStack-UpdateReadiness** csoport kevesebb tesztet tartalmaz. További információ: [Azure stack hub rendszer állapotának ellenőrzése](azure-stack-diagnostic-test.md#groups). A következő három teszt marad a **UpdateReadiness** csoportban:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Olyan naplózási szabályt adott hozzá, amely azt jelenti, hogy egy külső eszköz (például egy USB-kulcs) csatlakoztatva van az Azure Stack hub-infrastruktúra egy csomópontjához. A napló a syslog használatával lett kibocsátva, és a **Microsoft-Windows-Security-audit: 6416 | Plug and Play események**. A syslog-ügyfél konfigurálásával kapcsolatos további információkért lásd: [syslog-továbbítás](azure-stack-integrate-security.md).

- Azure Stack hub a belső tanúsítványok 4096 bites RSA-kulcsaira vált. A belső titkos kód futtatásával a régi 2048 bites tanúsítványokat a 4096 bites tanúsítványokkal helyettesíti. További információ a Azure Stack hub Secret rotációs szolgáltatásáról: [titkok Elforgatása Azure stack központban](azure-stack-rotate-secrets.md).

- A titkosítási algoritmusok és a kulcs erősségének frissítése több belső összetevő esetében, hogy megfeleljen a nemzetbiztonsági rendszerek Bizottságának – Policy 15 (CNSSP-15), amely ajánlott eljárásokat biztosít a nyilvános szabványok biztonságos információk megosztására való használatához. A tökéletesítések közé tartozik a Kerberos-hitelesítés és a VPN-titkosítás SHA384 AES256. A CNSSP-15-re vonatkozó további információkért tekintse [meg a nemzeti biztonsági rendszerek, szabályzatok oldalának Bizottságát](http://www.cnss.gov/CNSS/issuances/Policies.cfm).

- A fenti frissítés miatt Azure Stack hub mostantól új alapértelmezett értékeket tartalmaz az IPsec/IKEv2 konfigurációkhoz. Az Azure Stack hub oldalon használt új alapértelmezett értékek a következők:

   **Az IKE 1. fázis (Elsődleges mód) paraméterei**

   | Tulajdonság              | Érték|
   |-|-|
   | IKE verziószám           | IKEv2 |
   |Diffie-Hellman Group   | ECP384 |
   | Hitelesítési módszer | Előre megosztott kulcs |
   |Titkosító és kivonatoló algoritmus | AES256, SHA384 |
   |SA élettartama (Idő)     | 28 800 másodperc|

   **Az IKE 2. fázis (Gyors mód) paraméterei**

   | Tulajdonság| Érték|
   |-|-|
   |IKE verziószám |IKEv2 |
   |Titkosítási & kivonatoló algoritmusok (titkosítás)     | GCMAES256|
   |Titkosítási & kivonatolási algoritmusok (hitelesítés) | GCMAES256|
   |SA élettartama (Idő)  | 27 000 másodperc  |
   |SA élettartama (kilobájt) | 33 553 408     |
   |Sérülés utáni titkosságvédelem (PFS) | ECP384 |
   |Kapcsolat megszakadásának észlelése | Támogatott|

   Ezek a változások az [alapértelmezett IPSec/IKE-javaslat](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters) dokumentációjában is megjelennek.

- Az infrastruktúra-biztonsági mentési szolgáltatás javítja a logikát, amely kiszámítja a biztonsági mentések kívánt szabad területét, ahelyett, hogy rögzített küszöbértékre kellene támaszkodnia. A szolgáltatás a külső tárolási hely biztonsági mentési, adatmegőrzési szabályzatának, fenntartásának és aktuális kihasználtságának méretét fogja használni annak megállapítására, hogy egy figyelmeztetést kell-e kiemelni a kezelőnek.

### <a name="changes"></a>Módosítások

- A Piactéri elemek Azure-ból Azure Stack hubhoz való letöltésekor új felhasználói felület áll rendelkezésre, amely lehetővé teszi az elem egy verziójának megadását, ha több verzió létezik. Az új felhasználói felület a csatlakoztatott és a leválasztott forgatókönyvekben is elérhető. További információ: [Marketplace-elemek letöltése az Azure-ból Azure stack hubhoz](azure-stack-download-azure-marketplace-item.md).  

- Az 1910-es kiadástól kezdve a Azure Stack hub rendszernek további/20 magánhálózati belső IP-tárterületre **van szüksége** . További információért lásd: [a hálózati integráció megtervezése Azure stack](azure-stack-network.md) .
  
- Az infrastruktúra-biztonsági mentési szolgáltatás törli a részlegesen feltöltött biztonsági mentési adatok mennyiségét, ha a külső tárolóhely a feltöltési eljárás során elfogy a kapacitáson.  

- Az infrastruktúra-biztonsági mentési szolgáltatás hozzáadja az Identity Service-t a HRE-környezetek biztonsági mentési hasznos adataihoz.  

- Az Azure Stack hub PowerShell-modul a 1910-es kiadásban a 1.8.0-es verzióra frissült.<br>A módosítások a következők:
   - **Új DRP felügyeleti modul**: a telepítési erőforrás-szolgáltató (DRP) lehetővé teszi az erőforrás-szolgáltatók összehangolt központi telepítését Azure stack hubhoz. Ezek a parancsok a Azure Resource Manager réteget használják a DRP való kommunikációhoz.
   - **Brp**: <br />
           – Az Azure-beli verem-infrastruktúra biztonsági mentésének egyetlen szerepkör-visszaállítási funkciójának támogatása. <br />
           -Paraméter hozzáadása `RoleName` a parancsmaghoz `Restore-AzsBackup` .
   - **FRP**: a **meghajtó** -és **kötet** -erőforrások a következő API-verzióval történő megszakítása `2019-05-01` . A funkciókat a Azure Stack hub 1910-es és újabb verziói támogatják: <br />
            – A, a `ID` és a érték `Name` `HealthStatus` `OperationalStatus` módosult. <br />
            – Támogatott új tulajdonságok `FirmwareVersion` ,,, `IsIndicationEnabled` `Manufacturer` és `StoragePool` a **meghajtó** erőforrásaihoz. <br />
            – A tulajdonságok `CanPool` és `CannotPoolReason` a **meghajtó** erőforrásai elavultak; használja `OperationalStatus` helyette.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- Kijavítva egy olyan problémát, amely megakadályozta a TLS 1,2 házirend betartatását az Azure Stack hub 1904 kiadása előtt üzembe helyezett környezetekben.
- A probléma kijavítva, ha az SSH-hitelesítéssel létrehozott Ubuntu 18,04-es virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- A **jelszó alaphelyzetbe állítása** a virtuálisgép-méretezési csoport felhasználói felületéről eltávolítva.
- Kijavítva a probléma, amelyben a terheléselosztó törlése a portálról nem eredményezte az objektum törlését az infrastruktúra rétegében.
- Kijavított egy problémát, amely az átjáró-készlet kihasználtsági riasztásának pontatlan százalékát mutatta a felügyeleti portálon.
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](release-notes-security-updates.md).

A kiadás Qualys biztonsági réseit tartalmazó jelentés letölthető a [Qualys webhelyről](https://www.qualys.com/azure-stack/).

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Győződjön meg arról, hogy a 1908-es legújabb Azure Stack hub gyorsjavítását telepíti a 1910-es Azure Stack hub frissítése előtt.

> [!NOTE]
> Azure Stack hub gyorsjavítási kiadásai kumulatívak; a legújabb gyorsjavítást csak akkor kell telepíteni, ha az adott verzióhoz tartozó korábbi gyorsjavítások összes javítását beolvassa.

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Előfeltételek: az 1910-es frissítés alkalmazása előtt

Azure Stack hub 1910-es kiadását a 1908-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1908.51.133](https://support.microsoft.com/help/4574734)

### <a name="after-successfully-applying-the-1910-update"></a>Az 1910-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1910.63.186](https://support.microsoft.com/help/4574735)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1908"
## <a name="1908-archived-release-notes"></a>1908 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-release-notes"></a>1907 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>1906 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>1905 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802 archivált kibocsátási megjegyzések
::: moniker-end

::: moniker range="<azs-1910"
[A Azure stack hub kibocsátási megjegyzésének régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)érheti el. Ezek az archivált dokumentumok kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ az Azure Stack hub támogatásáról: [Azure stack hub karbantartási szabályzata](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.
::: moniker-end
