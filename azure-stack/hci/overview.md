---
title: Azure Stack HCI – áttekintés
description: A Azure Stack HCI egy olyan Hyper-konvergens Windows Server 2019-fürt, amely ellenőrzött hardvert használ a virtualizált számítási feladatok futtatásához a helyszínen. Igény szerint kapcsolódhat az Azure-szolgáltatásokhoz a felhőalapú biztonsági mentéshez, a helyek helyreállításához és egyebekhez is. Azure Stack HCI-megoldások a Microsoft által érvényesített hardverekkel biztosítják az optimális teljesítményt és megbízhatóságot, és támogatják az olyan technológiák támogatását, mint például a NVMe-meghajtók, az állandó memória és a távoli közvetlen memória-hozzáférés (RDMA) hálózatkezelés.
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.date: 11/04/2019
ms.openlocfilehash: 2bf9af3db5a3577b62c59e92879a3680d9a3532a
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77519448"
---
# <a name="azure-stack-hci-overview"></a>Azure Stack HCI – áttekintés

Azure Stack HCI egy hiperkonvergens Windows Server 2019-fürt, amely érvényesített hardvert használ a virtualizált munkaterhelések helyszíni futtatásához. Igény szerint kapcsolódhat az Azure-szolgáltatásokhoz a felhőalapú biztonsági mentéshez, a helyek helyreállításához és egyebekhez is. Azure Stack HCI-megoldások a Microsoft által érvényesített hardverekkel biztosítják az optimális teljesítményt és megbízhatóságot, és támogatják az olyan technológiák támogatását, mint például a NVMe-meghajtók, az állandó memória és a távoli közvetlen memória-hozzáférés (RDMA) hálózatkezelés.

A Azure Stack HCI egy olyan megoldás, amely számos terméket egyesít:

- Hardver OEM-partnertől

- Windows Server 2019 Datacenter Edition

- Windows felügyeleti központ

- Azure-szolgáltatások (nem kötelező)

![Azure Stack HCI a Microsoft hiperkonvergens-megoldása, amely számos hardveres partnertől elérhető.](media/overview/azure-stack-hci-solution.png)

Azure Stack HCI a Microsoft hiperkonvergens-megoldása, amely számos hardveres partnertől elérhető. Vegye figyelembe a következő forgatókönyveket egy hiperkonvergens-megoldáshoz, amely segít megállapítani, hogy Azure Stack HCI az igényeinek leginkább megfelelő megoldás-e:

- **Az öregedési hardver frissítése.** Cserélje le a régebbi kiszolgálókat és a tárolási infrastruktúrát, és futtasson Windows-és Linux-alapú virtuális gépeket a helyszínen és a meglévő informatikai ismeretekkel és eszközökkel.

- **Virtualizált munkaterhelések összevonása.** A régi alkalmazások összevonása egy hatékony, hiperkonvergens-infrastruktúrán. A Hyper-Scale-adatközpontok, például a Microsoft Azure futtatásához hasonló típusú Felhőbeli hatékonyságot használhat.

- **Kapcsolódjon az Azure-hoz a hibrid felhőalapú szolgáltatásokhoz.** Egyszerűsítheti a Felhőbeli felügyeleti és biztonsági szolgáltatásokhoz való hozzáférést az Azure-ban, beleértve a telephelyen kívüli biztonsági mentést, a site Recoveryt, a felhőalapú monitorozást és egyebeket.

## <a name="the-azure-stack-family"></a>A Azure Stack család

A Azure Stack HCI az Azure és a Azure Stack család része, és ugyanazt a szoftveresen definiált számítási, tárolási és hálózatkezelési szoftvert használja, mint Azure Stack hub. Íme egy gyors összefoglalás a különböző megoldásokról (további részletekért lásd: [a Azure stack ökoszisztéma összevetése](../operator/compare-azure-azure-stack.md)):

- Az [Azure](https://azure.microsoft.com) -ban a nyilvános felhőalapú szolgáltatások igény szerinti, önkiszolgáló számítási erőforrásokhoz használhatók a meglévő alkalmazások migrálása és modernizálása és új felhőalapú alkalmazások létrehozása érdekében.
- [Azure stack Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) – felgyorsítja a gépi tanulás feladatait, és futtatja a helyszíni alkalmazásokat vagy a helyileg virtualizált számítási feladatokat egy felhőben felügyelt berendezésen.
- [Azure stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) – virtualizált alkalmazások futtatása a helyszínen, az öregedő kiszolgáló infrastruktúrájának cseréje és összevonása, valamint az Azure-hoz való kapcsolódás a Cloud Serviceshez.
- [Azure stack hub](../operator/azure-stack-overview.md) – felhőalapú alkalmazások futtatása a helyszínen, a leválasztáskor vagy a szabályozási követelmények teljesítése érdekében, konzisztens Azure-szolgáltatások használatával.

![Az Azure Stack Edge egy felhőben felügyelt készülék, amely a gépi tanulást és a tárolóban lévő alkalmazásokat a peremen futtatja, Azure Stack a HCI egy hiperkonvergens megoldás a virtuális gépek és a helyi tárolás futtatására, míg Azure Stack hub Felhőbeli natív, Azure-konzisztens helyszíni szolgáltatások.](media/overview/azure-family.png)

További információ:

- További információ: [Azure stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) Solutions webhelye.
- Tekintse meg a Microsoft szakértői Jeff Woolsey és [a Tewari az új Azure stack HCI-megoldásokat](https://aka.ms/AzureStackOverviewVideo).

## <a name="hyperconverged-efficiencies"></a>Hiperkonvergens hatékonysága

Azure Stack HCI-megoldások az iparági szabványnak megfelelő x86-kiszolgálókon és-összetevőkön egyesítik a rendkívül virtualizált számítási, tárolási és hálózatkezelési funkciókat. Az azonos fürt erőforrásainak egyesítésével egyszerűbbé válik a üzembe helyezése, kezelése és méretezése. Kezelheti az Ön által választott parancssori automatizálási vagy Windows felügyeleti központot.

A Hyper-V-vel, a Microsoft Cloud alapszintű hypervisor technológiájának, valamint a NVMe, az állandó memória és a Közvetlen tárolóhelyek technológia beépített támogatásával piacvezető virtuálisgép-teljesítményt érhet el a kiszolgálói alkalmazások számára. távoli közvetlen memória-hozzáférés (RDMA) hálózatkezelés.

A védett virtuális gépekkel, a hálózati Szegmentálással és a natív titkosítással biztonságossá teheti az alkalmazásokat és az adatvédelmet.

## <a name="hybrid-capabilities"></a>Hibrid képességek

A felhőben és a helyszínen is kihasználhatja a nyilvános felhőben működő hiperkonvergens-infrastruktúra platformot. A csapata a felhőalapú ismereteket az Azure Infrastructure Management Services beépített integrálásával kezdi meg:

- Azure Site Recovery a magas rendelkezésre álláshoz és a vész-helyreállítási szolgáltatáshoz (DRaaS).

- Azure Monitor, egy központosított központ, amely nyomon követheti, hogy mi történik az alkalmazások, a hálózat és az infrastruktúra között – az AI által működtetett fejlett elemzési szolgáltatásokkal.

- Felhőbeli tanúsító, hogy az Azure-t használja a fürt Kvórumának egyszerű nyakkendő-megszakítójának.

- Azure Backup a telephelyen kívüli adatvédelem és a ransomware elleni védelem érdekében.

- Azure-Update Management az Azure-ban és a helyszínen futó Windows rendszerű virtuális gépek frissítésének értékeléséhez és frissítéséhez.

- Azure hálózati adapter a helyszíni erőforrások és az Azure-beli virtuális gépek pont – hely típusú VPN-kapcsolaton keresztüli összekapcsolásához.

- A fájlkiszolgáló szinkronizálása a felhővel Azure File Sync használatával.

Részletekért lásd: [a Windows Server csatlakoztatása az Azure Hybrid Serviceshez](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

## <a name="management-tools-and-system-center"></a>Felügyeleti eszközök és System Center

Azure Stack a HCI ugyanazt a virtualizációs és szoftveresen definiált tárolási és hálózatkezelési szoftvert használja Azure Stack hub-ként. Azure Stack HCI-mel azonban teljes körű rendszergazdai jogosultságokkal rendelkezik a fürtön, és közvetlenül felügyelheti annak bármely technológiáját:

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Közvetlen tárolóhelyek](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Szoftver által definiált hálózatkezelés](https://docs.microsoft.com/windows-server/networking/sdn/)
- [Feladatátvételi fürtszolgáltatás](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

Ezeknek a technológiáknak a kezeléséhez a következő felügyeleti eszközöket használhatja:

- [Windows felügyeleti központ](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- Más felügyeleti eszközök, például a [Kiszolgálókezelő](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager)és az MMC beépülő modulok
- Nem a Microsofttól származó eszközök, például a 5Nine Manager

Ha úgy dönt, hogy a System centert használja az infrastruktúra üzembe helyezéséhez és kezeléséhez, használja a System Center virtuálisgép-felügyeletet (VMM) és a System Center Operations Manager. A VMM használatával a virtuális gépek és szolgáltatások saját felhőkbe való létrehozásához és üzembe helyezéséhez szükséges erőforrásokat kell kiépíteni és felügyelni. A Operations Manager segítségével figyelheti a vállalaton belüli szolgáltatásokat, eszközöket és műveleteket az azonnali művelettel kapcsolatos problémák azonosításához.

## <a name="hardware-partners"></a>Hardveres partnerek

A Windows Server 2019-et 20 partnerről is megvásárolhatja ellenőrzött Azure Stack HCI-megoldásokkal. Az Ön által preferált Microsoft-partner hosszadalmas tervezési és létrehozási idő nélkül működik. Emellett egyetlen kapcsolódási pontot is kínálnak a megvalósításhoz és a támogatási szolgáltatásokhoz.

Látogasson el a [Azure stack HCI webhelyre](https://azure.microsoft.com/overview/azure-stack/hci) , ahol megtekintheti a jelenleg a Microsoft partnerei által elérhető 70 + Azure stack HCI-megoldásokat: az ASUS, a Axellio, a Bluechip, a DataON, a Dell EMC, a Fujitsu, a HPE, a Hitachi, a Huawei, a Lenovo, az NEC, a primeLine Solutions, a QCT, a SecureGUARD és

## <a name="video-based-learning"></a>Videó alapú tanulás

Íme néhány videó a Microsoft Ignite 2019-munkamenetekről:

- [Microsoft Ignite Live 2019 – Első lépések Azure Stack HCI-vel](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [Azure Stack HCI felderítése](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [A kiskereskedelmi üzletek vagy fiókirodák modernizálása Azure Stack HCI-vel](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Az Azure Stack HCI újdonságai: 45-es dolgok 45 percek alatt](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [A Azure Stack HCI üzembe helyezése](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [A zsugorodó adatvesztés esetén: Azure Stack HCI-ben történő adatmásolások](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Dave Kawula a Azure Stack HCI-on lévő mező megjegyzései](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

Az alábbi videó egy hibrid Felhőbeli virtuális eseményből áll:

- [Azure Stack HCI | Hibrid Felhőbeli virtuális esemény](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="faq"></a>GYIK

### <a name="what-do-azure-stack-hub-and-azure-stack-hci-solutions-have-in-common"></a>Milyen Azure Stack hub-és Azure Stack HCI-megoldások közösek?

Azure Stack HCI-megoldások ugyanazt a Hyper-V-alapú, szoftveresen definiált számítási, tárolási és hálózatkezelési technológiát Azure Stack hub-ként. Mindkét ajánlat szigorú tesztelési és ellenőrzési feltételekkel rendelkezik, így biztosítva a megbízhatóságot és az alapul szolgáló hardveres platformmal való kompatibilitást.

### <a name="how-are-they-different"></a>Mi a különbség közöttük?

A Azure Stack hub szolgáltatással helyszíni felhőalapú szolgáltatásokat futtat. Az Azure IaaS és a Péter-szolgáltatások a helyszínen futtathatók, hogy bárhol és bármikor futtassák a felhőalapú alkalmazásokat, amelyeket a helyszíni Azure Portal felügyelnek.

Azure Stack HCI-vel virtualizált számítási feladatokat futtathat a helyszínen, a Windows felügyeleti központtal és ismerős Windows Server-eszközökkel felügyelve. Opcionálisan kapcsolódhat az Azure-hoz olyan hibrid forgatókönyvekhez, mint például a felhőalapú site Recovery, a monitoring és más rendszerekhez.

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>Miért hozza a Microsoft a HCI-ajánlatot a Azure Stack családba?

A Microsoft hiperkonvergens technológiája már Azure Stack hub alapja.

Számos Microsoft-ügyfél összetett informatikai környezettel rendelkezik, és célunk, hogy olyan megoldásokat nyújtsanak, amelyek megfelelnek a megfelelő üzleti igényeknek megfelelő technológiának. Azure Stack HCI a hardveres partnereinktől korábban elérhető Windows Server 2016-alapú Windows Server szoftveres (WSSD) megoldások fejlődése. Bevezette a Azure Stack családba, mert elkezdtük új lehetőségeket biztosítani az Azure-hoz az infrastruktúra-felügyeleti szolgáltatásokhoz való zökkenőmentes csatlakozáshoz.

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>Csatlakoztatni kell Azure Stack HCI-t az Azure-hoz?

Nem, nem kötelező. Kihasználhatja az Azure-nal való integráció előnyeit hibrid forgatókönyvek, például a telephelyen kívüli biztonsági mentés és a vész-helyreállítási funkciók, valamint a felhőalapú figyelési és frissítési felügyelet révén, de nem kötelező. Nem jelent problémát az internetről való leválasztás.

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Hogyan kapcsolódik Azure Stack HCI a Windows Serverhez?

A Windows Server 2019 szinte minden Azure-termék alapja. Az Ön által megadott összes funkció továbbra is elérhető lesz a Windows Serverben. A HCI helyszíni üzembe helyezésének ajánlott módja a Azure Stack HCI, a Microsoft által ellenőrzött hardverek használata a partnereinktől.

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack-hub"></a>Frissíthetek Azure Stack HCI-ről Azure Stack hub-ra? 

Nem, de az ügyfelek áttelepíthetik a számítási feladatokat Azure Stack HCI-ről Azure Stack hubhoz vagy az Azure-ba.

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>Milyen Azure-szolgáltatásokat tud csatlakozni Azure Stack HCI-hez?

Az Azure-szolgáltatások frissített listájához, amelyhez Azure Stack HCI-t csatlakoztathatja, lásd: a [Windows Server csatlakoztatása az Azure Hybrid Serviceshez](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack-hub"></a>Hogyan hasonlítja össze a Azure Stack HCI Azure Stack hub-vel való összehasonlításának költségeit? 

Azure Stack hub-t teljes mértékben integrált rendszerként értékesítjük, amely szolgáltatásokat és támogatást is tartalmaz. Megvásárolhatja Azure Stack hub-t felügyelt rendszerként vagy teljes körűen felügyelt szolgáltatásként a partnereinktől. Az alaprendszeren kívül a Azure Stack hub-on vagy az Azure-on futtatott Azure-szolgáltatásokat a rendszer az Ön által használt fizetési alapon is értékesíti.

Azure Stack HCI-megoldások a hagyományos vásárlási modellt követik. A különböző meglévő csatornákon keresztül Azure Stack HCI-partnerek és szoftverek (Windows Server 2019 Datacenter Edition, szoftveres adatközpont-képességekkel és Windows felügyeleti központtal) vásárolhatók ellenőrzött hardverek. A Windows felügyeleti központban használható Azure-szolgáltatásokhoz Azure-előfizetéssel kell fizetni.

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>Hogyan vásárolni Azure Stack HCI-megoldásokat?

Kövesse az alábbi lépéseket:

1. Vásároljon egy Microsoft által érvényesített hardvert a kívánt hardveres partnertől.
1. A Windows Server 2019 Datacenter Edition és a Windows felügyeleti központ telepítése a felügyelethez és az Azure for Cloud Serviceshez való kapcsolódás lehetősége
1. Igény szerint Azure-fiókjával is csatlakoztathatja a felhőalapú felügyeleti és biztonsági szolgáltatásokat a munkaterhelésekhez.

![Azure Stack HCI-megoldások megvásárlásához válassza ki az igényeinek leginkább megfelelő hardverprofilt és konfigurációt.](media/overview/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-hub-and-azure-stack-hci"></a>A Azure Stack hub és a Azure Stack HCI összehasonlítása

A szervezet digitális átalakításával azt tapasztalhatja, hogy a nyilvános Cloud Services használatával gyorsabban mozoghat a modern architektúrák és a régi alkalmazások frissítése révén. A technológiai és szabályozási akadályokat is magában foglaló okok miatt azonban sok számítási feladatnak a helyszínen kell maradnia. Az alábbi táblázat segítségével meghatározhatja, hogy melyik Microsoft Hybrid Cloud-stratégia biztosítja, hogy mire van szüksége, hol van szüksége a Felhőbeli innovációra a számítási feladatokhoz, bárhol legyenek is.

| Azure Stack hub | Azure Stack HCI |
| --------------- | --------------- |
| Új ismeretek, innovatív folyamatok | Ugyanazok a képességek, ismerős folyamatok |
| Az adatközpontban található Azure-szolgáltatások | Az adatközpont összekötése az Azure-szolgáltatásokkal |

### <a name="when-to-use-azure-stack-hub"></a>Mikor kell használni a Azure Stack hub-t

| Azure Stack hub | Azure Stack HCI |
| --------------- | --------------- |
| Az önkiszolgáló infrastruktúra-szolgáltatás (IaaS) esetében használja az Azure Stack hub-t, amely erős elkülönítést és pontos használatot biztosít a több közös elhelyezésű bérlő esetében. Ideális a szolgáltatók és a vállalati privát felhők számára. Sablonok az Azure piactéren. | Azure Stack HCI nem kényszerített natív módon, vagy nem biztosít több bérlőt. |
| Az Azure Stack hub használatával olyan alkalmazásokat fejleszthet és futtathat, amelyek a szolgáltatásként nyújtott platformon alapuló (például a Web Apps, a functions vagy a Event Hubs helyszíni) szolgáltatásokra támaszkodnak. Ezek a szolgáltatások a Azure Stack hub-on futnak, ugyanúgy, mint az Azure-ban, és egységes hibrid fejlesztési és futtatókörnyezeti környezetet biztosítanak. | Azure Stack HCI nem futtatja a Pásti-szolgáltatásokat a helyszínen. |
| A Azure Stack hub használatával modernizálhatja az alkalmazások üzembe helyezését és működését olyan DevOps eljárásokkal, mint például a kód, a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD), valamint a kényelmes funkciók, például az Azure-konzisztens virtuálisgép-bővítmények. Ideális fejlesztői és DevOps csapatoknak. | Azure Stack HCI nem natív módon tartalmaz bármilyen DevOps-eszközt. |

### <a name="when-to-use-azure-stack-hci"></a>Mikor kell használni a Azure Stack HCI-t

| Azure Stack hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack hub minimális 4 csomópontot és saját hálózati kapcsolókat igényel. | A távoli irodák és ágak minimális helyigényéhez használjon Azure Stack HCI-t. Az egyszerűség és a megfizethetőség érdekében csak 2 kiszolgáló-csomóponttal és visszaváltható, visszatérhető hálózatkezeléssel kezdheti a munkát. A hardveres ajánlatok 4 meghajtóval, 64 GB memóriával, valamint a $10k/Node értékkel kezdődnek. |
| Azure Stack hub korlátozza a Hyper-V konfigurálását és az Azure-hoz való konzisztencia-szolgáltatást. | Használjon Azure Stack HCI-t a klasszikus vállalati alkalmazások, például az Exchange, a SharePoint és a SQL Server, valamint a Windows Server-szerepkörök, például a fájlkiszolgáló, a DNS, a DHCP, az IIS és az AD virtualizálása érdekében. Korlátlan hozzáférés az összes Hyper-V-szolgáltatáshoz, például a védett virtuális gépekhez.|
| Azure Stack hub nem teszi elérhetővé ezeket az infrastrukturális technológiákat. | A Azure Stack HCI használatával szoftveresen definiált infrastruktúrát használhat az öregedő tárolási tömbök vagy hálózati berendezések helyett, jelentős Újraépítés nélkül. A beépített Hyper-V, Közvetlen tárolóhelyek és a szoftveresen definiált hálózatkezelés (SDN) közvetlenül elérhető és kezelhető. |
