---
title: Azure Stack hub kibocsátási megjegyzései
description: Kibocsátási megjegyzések a Azure Stack hub integrált rendszereihez, beleértve a frissítéseket és a hibajavításokat is.
author: sethmanheim
ms.topic: article
ms.date: 05/07/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 54f993cd76f0d850e0dd82a2e1b9817806f2c517
ms.sourcegitcommit: 510bb047b0a78fcc29ac611a2a7094fc285249a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988320"
---
# <a name="azure-stack-hub-release-notes"></a>Azure Stack hub kibocsátási megjegyzései

Ez a cikk Azure Stack hub frissítési csomagjainak tartalmát ismerteti. A frissítés az Azure Stack hub legújabb kiadásának javításait és javításait is tartalmazza.

Ha egy másik verzió kibocsátási megjegyzéseit szeretné elérni, a bal oldali tartalomjegyzéknél használja a verziószám legördülő menüt.

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack hub integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kitra (ASDK).
::: moniker-end
::: moniker range="<azs-1907"
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

Az Azure Stack hub frissítési csomagot [a Azure stack hub letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
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
- A [diagnosztikai naplók gyűjtésének](azure-stack-diagnostic-log-collection-overview-tzl.md)fejlesztése. Az új felület egyszerűsíti és egyszerűbbé teszi a diagnosztikai naplók gyűjtését azáltal, hogy eltávolítja a blob Storage-fiók előzetes konfigurálásának szükségességét. A tárolási környezet előre konfigurálva van, így naplók küldhetők a támogatási eset megnyitása előtt, és kevesebb időt kell fordítani a támogatási hívásokra.
- Az előjelzéses [naplók és az igény szerinti naplók gyűjtésére](azure-stack-diagnostic-log-collection-overview-tzl.md) vonatkozó idő 80%-kal csökkent. A naplózási gyűjtési idő hosszabb időt vehet igénybe a várt értéknél, de Azure Stack hub-operátorok beavatkozása nem szükséges, kivéve, ha a naplózási gyűjtemény sikertelen.
- Egy Azure Stack hub-frissítési csomag letöltési folyamata mostantól látható a frissítés panelen a frissítés kezdeményezése után. Ez csak azokra a csatlakoztatott Azure Stack hub rendszerekre vonatkozik, amelyek a [frissítési csomagok automatikus letöltéssel történő előkészítését](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages)választják.
- A hálózati vezérlő gazdagép-ügynökének megbízhatósági fejlesztése.
- Egy új, DNS-Orchestrator nevű Micro-szolgáltatást vezetett be, amely javítja a belső DNS-szolgáltatások rugalmassági logikáját a javítás és a frissítés során.
- Új kérelem érvényesítése sikertelen volt, mert a virtuális gépek létrehozásakor a rendszer érvénytelen blob URI-azonosítókat adott a rendszerindítási diagnosztikai Storage-fiók paraméteréhez.
- A Rdagent és a Host Agent automatikus szervizelési és naplózási funkcióinak hozzáadása – a gazdagép két szolgáltatása, amely megkönnyíti a virtuális gépek SZIFILISZét.
- Új szolgáltatás hozzáadása a piactér-kezeléshez, amely lehetővé teszi, hogy a Microsoft olyan attribútumokat adjon hozzá, amelyek letiltják a rendszergazdáknak, hogy a különböző tulajdonságok, például a Azure Stack vagy a számlázási modell miatt nem kompatibilisek legyenek a Azure Stack. Csak a Microsoft adhatja hozzá ezeket az attribútumokat. További információ: [a Marketplace-elemek letöltésére szolgáló portál használata](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items).

### <a name="changes"></a>Változások

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

- Windows rendszerű virtuális gép PowerShell használatával történő létrehozásakor ügyeljen arra, `provisionvmagent` hogy adja hozzá a jelzőt, ha azt szeretné, hogy a virtuális gép bővítményeket helyezzen üzembe. A jelző nélkül a virtuális gép a vendég ügynök nélkül jön létre, és nem távolítható el a virtuálisgép-bővítmények üzembe helyezésének lehetősége:

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
- Kijavított egy hibát, amelyben a fizikai kapcsolók RSC funkciója létrehozta a inkonzisztenciák-t, és elejtette a terheléselosztó használatával áramló forgalmat. A RSC szolgáltatás alapértelmezés szerint le van tiltva.
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

### <a name="prerequisites-before-applying-the-2002-update"></a>Előfeltételek: az 2002-es frissítés alkalmazása előtt

Azure Stack hub 2002-es kiadását a 1910-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1910.37.132](https://support.microsoft.com/help/4550133)

### <a name="after-successfully-applying-the-2002-update"></a>Az 2002-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.2002.24.85](https://support.microsoft.com/help/4558081)
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

- A felügyeleti portál mostantól megjeleníti a Kiemelt végpontok IP-címeit a régió tulajdonságok menüjében, amely megkönnyíti a felderítést. Emellett megjeleníti az aktuálisan konfigurált időkiszolgálót és a DNS-továbbítókat. További információ: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

- A Azure Stack hub állapot-és monitorozási rendszere mostantól hibát okozhat a különböző hardver-összetevőkön. Ezek a riasztások további konfigurálást igényelnek. További információ: [Azure stack hub hardver-összetevők figyelése](azure-stack-hardware-monitoring.md).

- [Cloud-init támogatás az Azure stack hub számára](/azure/virtual-machines/linux/using-cloud-init): a Cloud-init egy széles körben használt módszer a Linux RENDSZERű virtuális gépek első indításához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a Cloud-init a kezdeti rendszerindítási folyamat során hívásra kerül, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához. A piactéren lévő Ubuntu-lemezképek frissítve lettek a Cloud-init támogatásához a kiépítés érdekében.

- Az Azure Stack hub mostantól támogatja az összes Windows Azure Linux-ügynök verzióját az Azure-ban.

- A Azure Stack hub rendszergazdai PowerShell-moduljainak új verziója érhető el. <!-- For more information, see -->

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
  - Az üzembe helyezésekhez CSS-és mérnöki támogatást kaphat.

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

### <a name="changes"></a>Változások

- A Piactéri elemek Azure-ból Azure Stack hubhoz való letöltésekor új felhasználói felület áll rendelkezésre, amely lehetővé teszi az elem egy verziójának megadását, ha több verzió létezik. Az új felhasználói felület a csatlakoztatott és a leválasztott forgatókönyvekben is elérhető. További információ: [Marketplace-elemek letöltése az Azure-ból Azure stack hubhoz](azure-stack-download-azure-marketplace-item.md).  

- Az 1910-es kiadástól kezdve a Azure Stack hub rendszernek további/20 magánhálózati belső IP-tárterületre **van szüksége** . További információért lásd: [a hálózati integráció megtervezése Azure stack](azure-stack-network.md) .
  
- Az infrastruktúra-biztonsági mentési szolgáltatás törli a részlegesen feltöltött biztonsági mentési adatok mennyiségét, ha a külső tárolóhely a feltöltési eljárás során elfogy a kapacitáson.  

- Az infrastruktúra-biztonsági mentési szolgáltatás hozzáadja az Identity Service-t a HRE-környezetek biztonsági mentési hasznos adataihoz.  

- Az Azure Stack hub PowerShell-modul a 1910-es kiadásban a 1.8.0-es verzióra frissült.<br>A módosítások a következők:
   - **Új DRP felügyeleti modul**: a telepítési erőforrás-szolgáltató (DRP) lehetővé teszi az erőforrás-szolgáltatók összehangolt központi telepítését Azure stack hubhoz. Ezek a parancsok a Azure Resource Manager réteget használják a DRP való kommunikációhoz.
   - **Brp**: <br />
           – Az Azure-beli verem-infrastruktúra biztonsági mentésének egyetlen szerepkör-visszaállítási funkciójának támogatása. <br />
           -Paraméter `RoleName` hozzáadása a parancsmaghoz `Restore-AzsBackup`.
   - **FRP**: a **meghajtó** -és **kötet** -erőforrások a következő API `2019-05-01`-verzióval történő megszakítása. A funkciókat a Azure Stack hub 1910-es és újabb verziói támogatják: <br />
            – A, a `ID`és `Name` `OperationalStatus` a `HealthStatus`érték módosult. <br />
            – Támogatott új tulajdonságok `FirmwareVersion`, `IsIndicationEnabled` `Manufacturer`,, és `StoragePool` a **meghajtó** erőforrásaihoz. <br />
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
- [Azure Stack hub gyorsjavítási 1.1908.25.78](https://support.microsoft.com/help/4552361)

### <a name="after-successfully-applying-the-1910-update"></a>Az 1910-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1910.37.132](https://support.microsoft.com/help/4550133)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908-Build referenciája

Az Azure Stack hub 1908 Update Build száma **1.1908.4.33**.

### <a name="update-type"></a>Frissítéstípus

1908 esetében a Azure Stack hub futtatására szolgáló operációs rendszer a Windows Server 2019-re frissült. Ez a frissítés lehetővé teszi az alapszintű fejlesztéseket és az Azure Stack hub további képességeinek kihasználását.

Az Azure Stack hub 1908 Update Build típusa **megtelt**. Ennek eredményeképpen az 1908-es frissítés hosszabb futtatókörnyezettel rendelkezik, mint az expressz frissítések, például a 1906 és a 1907. A teljes frissítésekhez tartozó pontos futtatókörnyezetek általában az Azure Stack hub-példány által tartalmazott csomópontok számától függenek, a rendszer által a bérlői munkaterhelések által használt kapacitástól, a rendszer hálózati kapcsolataitól (ha az internethez csatlakozik), valamint a rendszer hardverének konfigurációját. Az 1908-es frissítés a belső tesztelés következő várt futtatókörnyezeteit használta: 4 csomópont – 42 óra, 8 csomópont – 50 óra, 12 csomópont – 60 óra, 16 csomópont – 70 óra. A várt értéknél hosszabb ideig tartó frissítési futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek műveletet Azure Stack hub-operátorok, kivéve, ha a frissítés meghiúsul.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](azure-stack-updates.md).

- A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszer hardveres konfigurációja alapján használt kapacitástól függenek.
- A vártnál hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack hub-operátorok, kivéve, ha a frissítés meghiúsul.
- Ez a futtatókörnyezeti közelítés az 1908-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- 1908 esetében vegye figyelembe, hogy a Azure Stack hub futtatására szolgáló operációs rendszer a Windows Server 2019-re frissült. Ez a frissítés lehetővé teszi az alapszintű fejlesztéseket és az Azure Stack hub további képességeinek kihasználását.
- Az Azure Stack hub-infrastruktúra összes összetevője FIPS 140-2 módban működik.
- A Azure Stack hub-operátorok mostantól eltávolíthatják a portál felhasználói adatfájljait. További információ: [a portál felhasználói adatainak törlése Azure stack hub-ból](azure-stack-portal-clear.md).

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->
- Az Azure Stack hub Rest-titkosításával kapcsolatos adatok fejlesztése a titkoknak a fizikai csomópontok hardveres platformmegbízhatósági modul (TPM) való megőrzése érdekében.

### <a name="changes"></a>Változások

- A hardveres szolgáltatók a 2,1-es vagy újabb OEM-bővítményt a 1908-es Azure Stack hub-verzióval megegyező időpontban szabadítják fel. A 2,1-es vagy újabb OEM-bővítmény előfeltétele Azure Stack hub 1908-es verziójának. Az OEM-bővítmény 2,1-es vagy újabb verziójának letöltésével kapcsolatos további információkért forduljon a rendszer hardver-szolgáltatójához, és tekintse meg az [OEM-frissítések](azure-stack-update-oem.md#oem-contact-information) című cikket.  

### <a name="fixes"></a>Javítások

- A jövőbeli Azure Stack hub OEM-frissítésekkel való kompatibilitással és az ügyfél felhasználói lemezképeit használó VM-telepítéssel kapcsolatos probléma javítva. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Kijavított egy problémát az OEM belső vezérlőprogram frissítésével, és javította a AzureStack a Fabric Ring Health szolgáltatásban. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Kijavított egy problémát az OEM belső vezérlőprogram frissítési folyamatával kapcsolatban. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](release-notes-security-updates.md).

A kiadás Qualys biztonsági réseit tartalmazó jelentés letölthető a [Qualys webhelyről](https://www.qualys.com/azure-stack/).

## <a name="download-the-update"></a>A frissítés letöltése

Az Azure Stack hub 1908 frissítési csomagot [a Azure stack hub letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Győződjön meg arról, hogy a 1907-es legújabb Azure Stack hub gyorsjavítását telepíti a 1908-es Azure Stack hub frissítése előtt.

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne próbálja meg telepíteni a gyorsjavításokat a ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Előfeltételek: az 1908-es frissítés alkalmazása előtt

Azure Stack hub 1908-es kiadását a 1907-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1907.29.80](https://support.microsoft.com/help/4555650)

Az Azure Stack hub 1908 Update **Azure stack hub OEM 2,1-es vagy újabb verziójára** van szükség a rendszer hardver-szolgáltatójától. Az OEM-frissítések a Azure Stack hub rendszerhardverének illesztőprogram-és belső vezérlőprogram-frissítéseit tartalmazzák. Az OEM-frissítések alkalmazásával kapcsolatos további információkért lásd: [Azure stack hub eredeti berendezések gyártói frissítéseinek alkalmazása](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Az 1908-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1908.25.78](https://support.microsoft.com/help/4552361)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907-Build referenciája

Az Azure Stack hub 1907 Update Build száma **1.1907.0.20**.

### <a name="update-type"></a>Frissítéstípus

Az Azure Stack hub 1907 Update Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](azure-stack-updates.md) című cikk. A belső tesztelés alapján a 1907-es frissítés befejezéséhez szükséges idő körülbelül 13 óra.

- A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszer hardveres konfigurációja alapján használt kapacitástól függenek.
- A vártnál hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack hub-operátorok, kivéve, ha a frissítés meghiúsul.
- Ez a futtatókörnyezeti közelítés az 1907-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- Az Azure Stack hub diagnosztikai napló-gyűjtési szolgáltatásának általános elérhetőségi kiadása a diagnosztikai naplók gyűjtésének megkönnyítéséhez és fejlesztéséhez. Az Azure Stack hub diagnosztikai naplójának összegyűjtése szolgáltatás lehetővé teszi a diagnosztikai naplók összegyűjtését és megosztását a Microsoft ügyfél-támogatási szolgálatával (CSS). Ez a diagnosztikai napló-gyűjtési szolgáltatás új felhasználói élményt nyújt a Azure Stack hub felügyeleti portálján, amely lehetővé teszi, hogy az operátorok bizonyos kritikus riasztások esetén beállítsák a diagnosztikai naplók automatikus feltöltését egy tárolási blobba. A szolgáltatás is használható ugyanazon művelet igény szerinti végrehajtásához. További információkért lásd a [diagnosztikai napló gyűjtését](azure-stack-diagnostic-log-collection-overview.md) ismertető cikket.

- Az Azure Stack hub hálózati infrastruktúra ellenőrzésének általánosan elérhető kiadása az Azure Stack hub Validation Tool **test-AzureStack**részeként. Azure Stack hub hálózati infrastruktúrája a **test-AzureStack**részét képezi, amely meghatározza, hogy az Azure stack hub hálózati infrastruktúráján hiba fordul elő. A teszt ellenőrzi a hálózati infrastruktúra kapcsolatát, ha megkerüli a Azure Stack hub szoftveresen megadott hálózatát. Egy nyilvános VIP kapcsolatát mutatja be a konfigurált DNS-továbbítókkal, az NTP-kiszolgálókkal és az identitás-végpontokkal. Azt is ellenőrzi, hogy a Kapcsolódás az Azure-hoz, ha az Azure AD-t használja identitás-szolgáltatóként, vagy az összevont kiszolgálót az ADFS használatakor. További tudnivalókért tekintse meg az [Azure stack hub ellenőrző eszközét](azure-stack-diagnostic-test.md) ismertető cikket.

- Egy belső titkos elforgatási eljárás lett hozzáadva a belső SQL TLS-tanúsítványok szükség szerinti elforgatásához a rendszer frissítése során.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Az Azure Stack hub Update (frissítés) panel most megjeleníti az aktív frissítések **utolsó lépésének befejezési** idejét. Ezt a hozzáadást a frissítés panelre, majd a futó frissítésre kattintva tekintheti meg. Az **utolsó lépés befejeződött** , majd elérhető a **frissítés futtatása részletek** szakaszban.

- A **Start-AzureStack** és a **stop-AzureStack** operátor műveleteinek fejlesztése. Az Azure Stack hub elindításához szükséges idő átlagosan 50%-kal csökkent. Az Azure Stack hub leállításához szükséges idő átlagosan 30%-kal csökkent. Az átlagos indítási és leállítási idők változatlanok maradnak, mint a csomópontok száma a méretezési egységekben.

- Javított hibakezelés a leválasztott piactér eszközhöz. Ha a letöltés sikertelen vagy részlegesen sikeres az **export-AzSOfflineMarketplaceItem**használatakor, a részletes hibaüzenet jelenik meg, amely további részleteket tartalmaz a hibáról és a lehetséges kockázatcsökkentő lépésekről.

- Javította a felügyelt lemezek létrehozásának teljesítményét egy nagyméretű oldal blob/pillanatkép alapján. Korábban egy nagyméretű lemez létrehozásakor időtúllépést váltott ki.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Továbbfejlesztett virtuális lemez állapot-ellenőrzését a csomópont leállítása előtt, hogy elkerülje a virtuális lemezek váratlan leválasztását.

- A belső naplók továbbfejlesztett tárolása rendszergazdai műveletekhez. Ez a kiegészítés a rendszergazdai műveletek során jobb teljesítményt és megbízhatóságot eredményez azáltal, hogy minimalizálja a belső naplózási folyamatok memória-és tárterület-felhasználását. Emellett a felügyeleti portál frissítés paneljének továbbfejlesztett oldal betöltési idejét is megfigyelheti. Ennek a fejlesztésnek a részeként a hat hónapnál régebbi frissítési naplók többé nem lesznek elérhetők a rendszeren. Ha a frissítésekhez naplókra van szüksége, ügyeljen arra, hogy az 1907-es frissítés végrehajtása előtt az összes frissítési művelet [összefoglalóját töltse le](azure-stack-apply-updates.md) a hat hónapnál régebbi verzióra.

### <a name="changes"></a>Változások

- Azure Stack hub 1907-es verziója figyelmeztető riasztást tartalmaz, amely arra utasítja a kezelőket, hogy az 1908-as verzióra való frissítés előtt frissítse a rendszer OEM-csomagját a 2,1-es vagy újabb verzióra. További információ a Azure Stack hub OEM-frissítéseinek alkalmazásáról: [Azure stack hub eredeti berendezésgyártó frissítésének alkalmazása](azure-stack-update-oem.md).

- Új kimenő szabály (HTTPS) lett hozzáadva, amely lehetővé teszi a kommunikációt Azure Stack hub diagnosztikai napló gyűjtési szolgáltatásához. További információ: [Azure stack hub Datacenter Integration – közzétételi végpontok](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Az infrastruktúra-biztonsági mentési szolgáltatás törli a részlegesen feltöltött biztonsági mentéseket, ha a külső tárolóhely kapacitása elfogy.

- Az infrastruktúra biztonsági mentései már nem tartalmazzák a tartományi szolgáltatások adatai biztonsági mentését. Ez a módosítás csak a Azure Active Directoryt használó rendszerekre vonatkozik, az identitás-szolgáltatóként.

- Most ellenőrizjük, hogy egy, a **számítási > virtuálisgép-lemezképek** panelén betöltött kép oldal blob típusú.

- A Kiemelt jogosultságú Endpoint parancs **set-BmcCredential** most frissíti a hitelesítő adatokat a alaplapi felügyeleti vezérlőben.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->
- Kijavított egy olyan problémát, amelyben a közzétevőt, ajánlatot és SKU-t kis-és nagybetűkként kezelik a Resource Manager-sablonokban: a rendszerkép nem lett beolvasva telepítésre, kivéve, ha a képparaméterek nem azonosak a közzétevő, az ajánlat és az SKU esetében.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- A tárolási szolgáltatás metaadatainak biztonsági mentésének időtúllépése miatt nem sikerült a **PartialSucceeded** hibaüzenettel rendelkező biztonsági mentések hibájának kijavítása.  

- Kijavítva egy probléma, amelyben a felhasználói előfizetések törlése árva erőforrásokat eredményezett.

- Kijavított egy olyan problémát, amelyben a leírás mező nem lett mentve az ajánlat létrehozásakor.

- Kijavítva egy probléma, amelyben a **csak olvasási** jogosultsággal rendelkező felhasználók létrehozhatják, szerkeszthetik és törölhetik az erőforrásokat. Most a felhasználó csak akkor tud erőforrásokat létrehozni, ha a **közreműködői** engedély hozzá van rendelve. 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Kijavítva egy olyan problémát, amely miatt a frissítés sikertelen volt, mert a WMI-szolgáltató gazdagépe zárolta a DLL-fájlt.

- Kijavított egy problémát a frissítési szolgáltatásban, amely megakadályozta az elérhető frissítések megjelenítését a frissítés csempén vagy erőforrás-szolgáltatón. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4511282](https://support.microsoft.com/help/4511282/).

- Kijavítva egy olyan problémát, amely miatt a frissítés sikertelen volt, mert a felügyeleti sík helytelen konfiguráció miatt nem megfelelő állapotba kerül. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794/).

- Kijavított egy problémát, amely megakadályozta, hogy a felhasználók elvégezzék a harmadik féltől származó rendszerképek üzembe helyezését a piactéren. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4511259](https://support.microsoft.com/help/4511259/).

- Kijavítva egy olyan problémát, amely miatt a virtuális gép létrehozása a felügyelt lemezképből sikertelen volt a felhasználói rendszerkép-kezelő szolgáltatás összeomlása miatt. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794/)

- Kijavítva egy probléma, amelyben a virtuális gépek szifilisz-műveletei sikertelenek voltak, mert az App Gateway gyorsítótára nem a várt módon frissült. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4513119](https://support.microsoft.com/en-us/help/4513119/)

- Kijavított egy problémát az állapot-erőforrás-szolgáltatóban, amely hatással volt a régió és a riasztási panel elérhetőségére a felügyeleti portálon. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794).

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](release-notes-security-updates.md).

A kiadás Qualys biztonsági réseit tartalmazó jelentés letölthető a [Qualys webhelyről](https://www.qualys.com/azure-stack/).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues.md)
- [Biztonsági frissítések](release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

Az Azure Stack hub 1907 frissítési csomagot [a Azure stack hub letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Győződjön meg arról, hogy a 1906-es legújabb Azure Stack hub gyorsjavítását telepíti a 1907-es Azure Stack hub frissítése előtt.

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne próbálja meg telepíteni a gyorsjavításokat a ASDK.

### <a name="before-applying-the-1907-update"></a>Az 1907-es frissítés alkalmazása előtt

Azure Stack hub 1907-es kiadását a 1906-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Az 1907-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1907.29.80](https://support.microsoft.com/help/4555650)
::: moniker-end

::: moniker range=">=azs-1907"
## <a name="automatic-update-notifications"></a>Automatikus frissítési értesítések

Az infrastruktúra-hálózatról az internetre elérő rendszerek az **elérhető frissítés** üzenetét fogják látni az operátori portálon. Az internet-hozzáféréssel nem rendelkező rendszerek a megfelelő. xml fájllal tölthetik le és importálhatók a. zip fájlt.

> [!TIP]  
> Feliratkozás a következő *RSS* -vagy *Atom* -hírcsatornára Azure stack hub-gyorsjavítások megtartásához:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archívum

Ha egy régebbi verzióhoz szeretne hozzáférni az archivált kibocsátási megjegyzésekhez, használja a bal oldali tartalomjegyzéket, és válassza ki a megjeleníteni kívánt verziót.

## <a name="next-steps"></a>További lépések

- Az Azure Stack hub Update Management szolgáltatásának áttekintését lásd: a [frissítések kezelése a Azure stack hub-ban – Áttekintés](azure-stack-updates.md).  
- A frissítések Azure Stack hubhoz való alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása Azure stack hub-ban](azure-stack-apply-updates.md).
- Az Azure Stack hub karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, lásd: [Azure stack hub karbantartási szabályzata](azure-stack-servicing-policy.md).  
- Ha a privilegizált végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack központban a Kiemelt végpont használatával](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1907"
[A Azure stack hub kibocsátási megjegyzésének régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)érheti el. Ezek az archivált dokumentumok kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ az Azure Stack hub támogatásáról: [Azure stack hub karbantartási szabályzata](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.
::: moniker-end


