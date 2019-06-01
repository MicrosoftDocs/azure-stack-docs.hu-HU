---
title: Az Azure Stack HCl áttekintése
description: Az Azure Stack HCl egy hiperkonvergens Windows Server 2019 használó fürt ellenőrzött hardverek helyszíni virtualizált számítási feladatok futtatásához. Felhőalapú biztonsági mentési, helyreállítási hely és egyéb Azure-szolgáltatások is csatlakozhat. Az Azure Stack HCl-megoldások használata a Microsoft szabványú hardveres optimális teljesítményének és megbízhatóságának biztosítása érdekében, és közé tartozik például az NVMe meghajtók, állandó memóriával és távoli-közvetlen memória-hozzáféréses (RDMA) hálózati technológiák támogatása.
ms.technology: storage
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.prod: windows-server-threshold
ms.date: 05/31/2019
ms.openlocfilehash: c3f25f38429b58d8d72037ff4ed9758d9993a2c5
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453537"
---
# <a name="azure-stack-hci-overview"></a>Az Azure Stack HCl áttekintése

>A következőkre érvényes: A Windows Server 2019

Az Azure Stack HCl egy hiperkonvergens Windows Server 2019 használó fürt ellenőrzött hardverek helyszíni virtualizált számítási feladatok futtatásához. Felhőalapú biztonsági mentési, helyreállítási hely és egyéb Azure-szolgáltatások is csatlakozhat. Az Azure Stack HCl-megoldások használata a Microsoft szabványú hardveres optimális teljesítményének és megbízhatóságának biztosítása érdekében, és közé tartozik például az NVMe meghajtók, állandó memóriával és távoli-közvetlen memória-hozzáféréses (RDMA) hálózati technológiák támogatása.

Az Azure Stack HCl olyan megoldás, amely egyesíti az több termék:

- OEM-partnertől hardver

- Windows Server 2019 Datacenter kiadás

- Windows Admin Center

- Azure-szolgáltatások (nem kötelező)

![Az Azure Stack HCl a Microsoft hiperkonvergens megoldás számos hardverszállító érhető el.](media/azure-stack-hci/azure-stack-hci-solution.png)

Az Azure Stack HCl a Microsoft hiperkonvergens megoldás számos hardverszállító érhető el. Vegye figyelembe a hiperkonvergens megoldás segít megállapítani, ha az Azure Stack HCl-e az igényeinek leginkább megfelelő megoldást a következő esetekben:

- **Frissítse az elévülés hardver.** Cserélje le a régebbi kiszolgálók és a tárolási infrastruktúra, és futtassa a Windows és Linux rendszerű virtuális gépek a helyszíni és a meglévő IT-képességekre és eszközökkel a peremhálózaton.

- **Egyesítse a virtualizált számítási feladatokhoz.** Örökölt alkalmazások hatékony, hiperkonvergens infrastruktúra egyesíthetők. Kihasználhatják a felhő növelhető a hatékonyság a Microsoft Azure rendkívül nagy kapacitású adatközpontok futtatásához használt azonos típusú.

- **Csatlakozás az Azure-ba, hibridfelhő-szolgáltatásokhoz.** Egyszerűsíthetők a felügyeleti és biztonsági szolgáltatások az Azure-ban, beleértve a telephelyen kívüli biztonsági mentési, a site recovery, felhőalapú figyelési és egyéb felhőalapú elérésének.

## <a name="the-azure-stack-family"></a>Az Azure Stack-család

Az Azure Stack HCl része az Azure és az Azure Stack-család, ugyanazzal a szoftveresen definiált számítási, tárolási és hálózatkezelési Azure Stack szoftver. Itt látható a különböző megoldások rövid összefoglalás:

- [Azure](https://azure.microsoft.com) – nyilvános felhőszolgáltatások használata
- [Az Azure Stack](azure-stack-overview.md) -működnek a felhőszolgáltatások helyi
- [Az Azure Stack HCl](https://azure.microsoft.com/overview/azure-stack/hci) -futtató virtualizált alkalmazásokhoz a helyszínen, a nem kötelező kapcsolatok az Azure-bA

![Azure-ban, és futtassa a cloud services, Azure Stack HCl futtatása közben az Azure Stack virtualizált helyszíni alkalmazásokhoz](media/azure-stack-hci/azure-family.png)

|Azure: Nyilvánosfelhő-szolgáltatások használata|Azure Stack: Cloud services helyszíni üzemeltetése|Az Azure Stack HCl: A virtualizált alkalmazások helyszíni futtatásához|
|-----------------|-----------------|-----------------|
|Igény szerinti, önkiszolgáló számítási erőforrások áttelepítése és a meglévő alkalmazások korszerűsítése, és hozzon létre új felhőbeli natív alkalmazásokat.|A build és a futtatási felhő alkalmazások a peremhálózaton, ha csatlakozik, és megfelelhet a szabályozási követelményeknek, az egységes Azure-szolgáltatások helyszíni.| Futtassa a virtualizált alkalmazásokat a helyszíni, cserélje le, és konszolidálhatja az elévülés kiszolgálói infrastruktúra csatlakoztatása az Azure cloud Services.|
|Több mint 100 világszerte 54 régiókban elérhető szolgáltatások|A Windows Azure virtuális gépek és a Linux, Azure Web Apps és Functions, Azure Key Vault, Azure Resource Manager, Azure Marketplace-en, tárolók, Azure IoT és az Event Hubs, a felügyeleti eszközök (csomagok, ajánlatok, RBAC)|Érvényesített működteti, felügyeleti és integrált Azure-szolgáltatásokhoz való hozzáférés a Hyper-V és a közvetlen tárolóhelyek a Windows Server 2019 és a Windows Admin Center HCl-megoldásokat.|

További tudnivalók:

- További információt a [Azure Stack HCl](https://azure.microsoft.com/overview/azure-stack/hci) megoldások webhelyén.
- Jeff Woolsey és Vijay Tewari a Microsoft szakértői [tárgyalják az új Azure Stack HCl megoldások](https://aka.ms/AzureStackOverviewVideo).

## <a name="hyperconverged-efficiencies"></a>Hiperkonvergens növelhető a hatékonyság

Az Azure Stack HCl-megoldások révén egy helyről érheti nagymértékben virtualizált számítási, tárolási és hálózatkezelési az iparági szabványnak megfelelő x86 kiszolgálók és összetevők. Ugyanabban a fürtben található erőforrások kombinálásával megkönnyíti, hogy üzembe, kezelhet és méretezhet. A választott parancssori automation vagy Windows Admin Center kezelheti.

Beépített támogatásával, NVMe, állandó memóriával a Hyper-V, az alapvető hipervizor technológiát a Microsoft-felhő és a közvetlen tárolóhelyek technológia és kiszolgálói alkalmazásokat iparágvezető virtuális gép teljesítmény eléréséhez és távoli-közvetlen memória-hozzáféréses (RDMA) hálózati.

Biztonsága érdekében alkalmazásokat és adatokat a védett virtuális gépek, a hálózati microsegmentation és a natív titkosítását.

## <a name="hybrid-capabilities"></a>Hibrid

Felhőbeli és helyszíni működik együtt a nyilvános felhőbeli hiperkonvergens infrastruktúra platform előnyeit is igénybe vehet. A csapat megkezdheti a felhővel kapcsolatos ismereteit az Azure-infrastruktúra management Services beépített integráció létrehozásához:

- Az Azure Site Recovery magas rendelkezésre állás és vészhelyreállítás helyreállítási szolgáltatást (DRaaS).

- Az Azure Monitor egy központosított hub segítségével nyomon követheti, mi történik az alkalmazások, hálózati és infrastruktúra – fejlett elemzés, mesterséges intelligenciával működő.

- Az Azure-t a lightweight tie megszakító fürtök kvóruma számára a felhőbeli Tanúsítót.

- Az Azure Backup a telephelyen kívüli adatvédelmet és a zsarolóprogramok ellen.

- Frissítése az Azure felügyeleti, a frissítések felmérése, és frissítse az Azure és a helyszínen futó Windows virtuális gépek központi telepítése.

- Helyszíni erőforrások a virtuális gépekkel az Azure-ban egy pont – hely VPN-en keresztül csatlakozni az Azure hálózati Adapter.

- Szinkronizálja a fájlkiszolgálóról a felhőben, az Azure File Sync használatával.

További információkért lásd: [hibrid Azure-szolgáltatásokhoz való csatlakozás Windows Server](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

## <a name="management-tools-and-system-center"></a>Felügyeleti eszközök és a System Center

Az Azure Stack HCl használja, mint az Azure Stack az azonos virtualizálási és szoftveralapú tárolási és hálózati szoftvert. Ugyanakkor az Azure Stack HCl teljes rendszergazdai jogosultságokkal rendelkezik a fürtön, és kezelheti a technológiák bármelyikét közvetlenül:

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Közvetlen tárolóhelyek](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Szoftveralapú hálózatkezelés](https://docs.microsoft.com/windows-server/networking/sdn/)
- [A Feladatátvételi fürtszolgáltatás](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

Ezek a technológiák kezeléséhez, a következő felügyeleti eszközöket használhatja:

- [Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- Más kezelési eszközök, mint például [Kiszolgálókezelő](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager), és az MMC beépülő modulok
- Például a 5Nine Manager nem a Microsofttól származó eszközök

Ha a System Center segítségével az infrastruktúra telepítéséhez és kezeléséhez, System Center Virtual Machine (VMM) felügyeleti és a System Center Operations Manager fogja használni. A VMM-mel kiépítése, és felügyelheti az erőforrásokat, létrehozása és üzembe helyezése a virtuális gépek és szolgáltatások magánfelhőben való szükséges. Az Operations Managerrel figyelheti szolgáltatások, eszközök és műveletek azonosíthatja a problémákat az azonnali intézkedés céljából a vállalaton belül.

## <a name="hardware-partners"></a>A hardvergyártó partnerektől

Ellenőrzött Azure Stack HCl megoldások Windows Server 2019 15 partnerektől vásárolhatja meg. Az előnyben részesített Microsoft-partner megkönnyíti és hosszadalmas tervezés és felépítés időpont nélkül. Egy egyetlen végrehajtási és támogatási szolgáltatások kapcsolatfelvételi pontot is elérhető.

Látogasson el a [Azure Stack HCl webhely](https://azure.microsoft.com/overview/azure-stack/hci) 70-nél több Azure Stack HCl megoldásaink jelenleg elérhető a Microsoft-partnerek megtekintése: ASUS, Axellio, bluechip, DataON, a Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine megoldások, QCT, SecureGUARD és Supermicro.

## <a name="faq"></a>GYIK

### <a name="what-do-azure-stack-and-azure-stack-hci-solutions-have-in-common"></a>Mi az Azure Stacket és az Azure Stack HCl megoldások rendelkeznek közös?

Az Azure Stack HCl-megoldások az Azure Stack, az ugyanazon a Hyper-V-alapú szoftveresen definiált számítási, tárolási és hálózatkezelési technológiákat funkció. Mindkét ajánlatok feltételnek szigorú tesztelése és ellenőrzése annak biztosítása érdekében a megbízhatóság és az alapul szolgáló hardver platformmal való kompatibilitást.

### <a name="how-are-they-different"></a>Mi a különbség közöttük?

Az Azure Stack helyszíni szolgáltatások felhő, futtassa. Futtathat az Azure IaaS és PaaS szolgáltatások következetesen hozhat létre és felhőalapú alkalmazások futtatása bárhol, az Azure portal helyszíni felügyelt helyszíni.

Az Azure Stack HCl, akkor virtualizált számítási feladatokhoz a helyszínen, futtassa a felügyelt Windows Admin Center és a jól ismert Windows Server-eszközök. Igény szerint kapcsolódhat az Azure hibrid forgatókönyvek esetén például a felhőalapú és a site recovery, figyelés és mások.

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>Miért érdemes a Microsoft az ajánlat az Azure Stack-családba HCl nemében?

A Microsoft hiperkonvergens technológia már az Azure Stack alapjai.

Számos Microsoft-ügyfél rendelkezik bonyolult informatikai környezetekben, és az a célunk, megoldások, amelyek megfelelnek a őket, hogy hol vannak a megfelelő üzleti a megfelelő technológiával van szükség. Az Azure Stack HCl hardvergyártó partnereinknél korábban elérhető a Windows Server 2016-alapú Windows Server Software-Defined (WSSD) megoldások továbbfejlesztett változata. Azt üzembe azt az Azure Stack-család mert elkezdjük zökkenőmentesen kapcsolódik az Azure infrastruktúra-felügyeleti szolgáltatásokat az új lehetőségeket kínál.

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>Nem Azure Stack HCl csatlakozva kell lennie az Azure-bA?

Nem, nem kötelező. A hibrid forgatókönyvekben, például a telephelyen kívüli biztonsági mentési és vész-helyreállítási és a felhő alapú figyelés és frissítés kezelése az Azure-integráció előnyeit élvezheti, de nem kötelező. Sem jelent problémát, az internetről leválasztott futtatni, azt.

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Hogyan kapcsolódnak az Azure Stack HCl Windows Server?

A Windows Server 2019 az alapja a szinte az összes Azure-termék. Szolgáltatások, értéke továbbra is szállíthat, és a Windows Server támogatja. Az Azure Stack HCl Microsoft szabványú hardveres partnereink segítségével helyszíni üzembe helyezése HCl, ajánlott módja.

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack"></a>Frissíthetem az Azure Stack HCl Azure Stack? 

Nem, de az ügyfelek helyezheti át a számítási feladatok az Azure Stack HCl az Azure Stack vagy az Azure.

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>Milyen Azure-szolgáltatásokat is kapcsolódni az Azure Stack HCl?

Az Azure frissített listáját, szolgáltatási kapcsolódhatnak-e, tekintse meg az Azure Stack HCl [hibrid Azure-szolgáltatásokhoz való csatlakozás Windows Server](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack"></a>Az Azure Stack HCl költségeinek összehasonlítása az Azure Stackhez 

Az Azure Stack egy teljes körűen integrált rendszer, amely magában foglalja a szolgáltatások és támogatás vásárolható meg. Egy Ön által felügyelt rendszer, vagy egy teljes körűen felügyelt szolgáltatás partnereink is vásárolhat Azure Stack. Az alapszintű rendszer mellett az Azure-szolgáltatások futó Azure Stack vagy az Azure használatalapú mint-akkor-használható alapon vásárolható meg.

Az Azure Stack HCl-megoldások kövesse a hagyományos vásárlási modell. Hitelesített hardveres vásárolhatnak az Azure Stack HCl partnerek és a meglévő csatornákon különféle (Windows Server 2019 Datacenter edition szoftveralapú adatközpont-funkciók és a Windows Admin Center) szoftvert. Az Azure-szolgáltatásokhoz a Windows Admin Center használható a fizetés az Azure-előfizetések.

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>Hogyan vásárolhat a megoldások az Azure Stack HCl?

Kövesse az alábbi lépéseket:

1. Vásároljon Microsoft szabványú hardveres rendszer előnyben részesített hardver partnerétől.
1. Telepítse a Windows Server 2019 Datacenter edition és a Windows Admin Center management, és képes csatlakozni az Azure cloud Services
1. Szükség esetén használja az Azure-fiókjával felhőalapú felügyeleti és biztonsági szolgáltatások csatlakoztatása a számítási feladatok.

![Azure Stack HCl megoldások vásárol, válassza ki a hardver partner és a konfigurációt, amely leginkább megfelel az igényeinek.](media/azure-stack-hci/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-and-azure-stack-hci"></a>Az Azure Stack és az Azure Stack HCl összehasonlítása

A szervezet digitális alakítja át, mivel előfordulhat, hogy áthelyezheti gyorsabban nyilvános felhőszolgáltatások segítségével modern architektúrák épülnek, és frissítse az örökölt alkalmazásokat. Azonban, amely tartalmazza a technológiai és szabályozási akadályok okokból számos kell maradnia a helyszínen. Az alábbi táblázat segítségével meghatározhatja, mely a Microsoft hibrid felhőstratégiát biztosít, mire van szüksége, szüksége lesz rá, a számítási feladatokhoz a felhőbeli innovációk kidolgozását, bárhol legyenek is.

|Azure Stack|Azure Stack HCI|
|--------|-------|
|Új ismeretek, innovatív folyamatok|Jól ismert azonos ismeretek elsajátítása|
|Az adatközpontban található Azure-szolgáltatások|Az Adatközpont csatlakoztatása az Azure-szolgáltatások|

### <a name="when-to-use-azure-stack"></a>Mikor érdemes használni az Azure Stackben

|Azure Stack|Azure Stack HCI|
|--------|-------|
|Az önkiszolgáló infrastruktúra--szolgáltatásként (IaaS), az Azure Stack használata erős elkülönítést és a pontos használat nyomon követése és a költséghelyi elszámolás ugyanarra a szalagra több bérlő számára. Ideális megoldás a szolgáltatók és a vállalati magánfelhők. A sablonok az Azure Marketplace-ről.|Az Azure Stack HCl nem natív módon kikényszerítheti, vagy több-bérlős adja meg.|
|Azure Stack használata a Platform--szolgáltatásként (PaaS) szolgáltatásokat használó alkalmazások fejlesztése és futtatása például a Web Apps, Functions, vagy az Event Hubs helyszíni. Ezek a szolgáltatások az Azure Stackben futnak, pontosan ugyanúgy, ahogy azok hajtsa végre az Azure-ban, a konzisztens hibrid fejlesztési és futásidejű környezet biztosítása.|Az Azure Stack HCl PaaS-szolgáltatások nem futnak a helyszínen.
|Azure Stack segítségével korszerűsítheti az alkalmazás üzembe helyezése és a fejlesztési és üzemeltetési eljárások infrastruktúra mint kód, a folyamatos integráció és folyamatos üzembe helyezés (CI/CD) és kényelmes szolgáltatások, mint az Azure-konzisztens Virtuálisgép-bővítmények műveletet. Ideális választás fejlesztéshez és a fejlesztési és üzemeltetési csapatok.|Az Azure Stack HCl nem natív módon tartalmazza a bármely DevOps-eszközök.

### <a name="when-to-use-azure-stack-hci"></a>Mikor érdemes használni az Azure Stack HCl

|Azure Stack|Azure Stack HCI|
|---------------|---------------|
|Az Azure Stack szükséges minimális 4 csomópontok és a saját hálózati kapcsolók.|Az Azure Stack HCl használata távoli kirendeltségek és ágak a erőforrásigényének. Mindössze 2 kiszolgáló-csomópontok és a maximális egyszerűség és elérhető áron switchless végpontok közötti hálózati kezdődik. Hardver ajánlatok indulni a 4 meghajtók, 64 GB memóriával, és a $10k / csomópont.
|Az Azure Stack korlátozza a Hyper-V beállíthatóság és konzisztencia az Azure-ral funkciókészlethez.|Használja az Azure Stack HCl no-frills Hyper-V hálózatvirtualizálási klasszikus vállalati alkalmazásaihoz, mint például az Exchange, SharePoint és az SQL Server és virtualizálhatja a Windows Server-szerepkörök, például a fájlkiszolgáló, DNS, DHCP, az IIS és az AD. Az összes Hyper-V-szolgáltatások, mint a védett virtuális gépek korlátlan hozzáférést.|
|Az Azure Stack nem teszik elérhetővé a infrastrukturális technológiák.|Az Azure Stack HCl segítségével szoftveresen definiált infrastruktúra helyett az elévülés tárolótömbök vagy hálózati berendezések fő rearchitecture nélkül használja. Beépített Hyper-V, a közvetlen tárolóhelyek és szoftveralapú hálózatkezelés (SDN) is közvetlenül elérhetők maradnak, és felügyelhető.|