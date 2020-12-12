---
title: Az Azure Stack HCI-megoldás áttekintése
description: Azure Stack HCI egy hiperkonvergens infrastruktúra-(HCI-) fürt megoldás, amely virtualizált Windows-és Linux-munkaterheléseket, valamint a hibrid helyszíni környezetekben tárolt tárolókat üzemeltet. Az Azure Hybrid Services olyan képességekkel bővíti a fürtöt, mint például a felhőalapú figyelés, a Site Recovery és a virtuális gépek biztonsági mentése, valamint a Azure Portal összes Azure Stack HCI-telepítésének központi nézete.
ms.topic: overview
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/11/2020
ms.openlocfilehash: d8f8d1191d2e5531c377173f2a8a8c665c7355dc
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364166"
---
# <a name="azure-stack-hci-solution-overview"></a>Az Azure Stack HCI-megoldás áttekintése

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Azure Stack HCI egy hiperkonvergens infrastruktúra-(HCI-) fürt megoldás, amely virtualizált Windows-és Linux-munkaterheléseket, valamint a hibrid, helyszíni környezetben tárolt tárolókat üzemeltet. Az Azure Hybrid Services olyan képességekkel bővíti a fürtöt, mint például a felhőalapú figyelés, a Site Recovery és a virtuális gépek biztonsági mentése, valamint a Azure Portal összes Azure Stack HCI-telepítésének központi nézete. A fürtöt a meglévő eszközökkel, például a Windows felügyeleti központtal, a System centerrel és a PowerShell-lel kezelheti.

A Azure Stack HCI, Version 20H2 már [letölthető](https://azure.microsoft.com/products/azure-stack/hci/hci-download/). Olyan helyszíni fürtökre szolgál, amelyek virtualizált számítási feladatokat futtatnak, és beépített hibrid Felhőbeli kapcsolatokkal rendelkeznek. Ennek megfelelően az Azure Stack HCI Azure-szolgáltatásként érhető el, és egy Azure-előfizetésen keresztül történik. Azure Stack HCI mostantól lehetővé teszi az Azure Kubernetes szolgáltatás üzemeltetését is; További információ: [Azure Kubernetes Service on Azure stack HCI](../aks-hci/overview.md).

Szánjon néhány percet a Azure Stack HCI magas szintű funkcióival kapcsolatos videó megtekintésére:

> [!VIDEO https://www.youtube.com/embed/fw8RVqo9dcs]

Az Azure Stack HCI a következőkkel ötvözi az egyik megoldást:

- Ellenőrzött hardver OEM-partnertől
- Azure Stack HCI operációs rendszer
- Windows felügyeleti központ
- Azure-szolgáltatások

:::image type="content" source="media/overview/azure-stack-hci-solution.png" alt-text="Az Azure Stack HCI operációs rendszer ellenőrzött hardveren fut, és a Windows felügyeleti központ felügyeli, és csatlakozik az Azure-hoz" border="false":::

Azure Stack HCI, a 20H2 verziója olyan új funkciókat biztosít, amelyek nem szerepelnek a Windows Serverben, például a Windows felügyeleti központ használatával olyan hiperkonvergens-fürtöt hozhat létre, amely a kiváló tárolási ár-teljesítmény érdekében Közvetlen tárolóhelyek használ. Ez magában foglalja azt a lehetőséget, hogy a fürtöt a helyek közötti automatikus feladatátvételre nyújtsa. A részletekért tekintse [meg a Azure stack HCI újdonságait](#whats-new-in-azure-stack-hci) .

## <a name="use-cases-for-azure-stack-hci"></a>Azure Stack HCI használati esetei

Azure Stack HCI esetében számos felhasználási eset van, bár nem virtualizált számítási feladatokhoz készült. Az ügyfelek gyakran a következő helyzetekben választják Azure Stack HCI-ket:

### <a name="data-center-consolidation-and-modernization"></a>Adatközpontok összevonása és modernizációja

Az öregedő virtualizációs gazdagépek Azure Stack HCI-vel való frissítése és összevonása javíthatja a méretezhetőséget, és egyszerűbbé teheti a környezet felügyeletét és védelmét. Lehetőség van arra is, hogy az örökölt SAN-tárolót kivonja a helyigény és a teljes tulajdonlási díj csökkentése érdekében. A műveletek és a rendszerek adminisztrációja egyszerűsített, egységes eszközökkel és felületekkel, valamint egyetlen támogatási ponttal.

### <a name="remote-and-branch-offices"></a>Távoli és fiókirodák

Ha a két kiszolgálós fürt megoldása a $20 000-nál kevesebb helyet, Azure Stack a HCI-t, a távoli és fiókirodák, a kiskereskedelmi üzletek és a mezők webhelyeinek kiszolgálása nagyszerű lehetőséget biztosít. Az innovatív beágyazott rugalmasság lehetővé teszi, hogy a kötetek online és elérhetők legyenek, még akkor is, ha egy időben több hardverhiba történik. A Felhőbeli tanúsító technológia lehetővé teszi, hogy az Azure-t egyszerű tie-MEGSZAKÍTÓKÉNT használja a fürt kvóruma számára, amely megakadályozza a felosztott agy feltételeit egy harmadik állomás díja nélkül. Az ügyfelek központilag is megtekinthetik a távoli Azure Stack HCI központi telepítéseket a Azure Portalban.

### <a name="virtual-desktops"></a>Virtuális asztali környezetek

Számos szervezet szeretné a helyi virtuális asztalokat a kis késés és az adatszuverenitás érdekében üzemeltetni. Azure Stack HCI-hez hasonló helyi teljesítmény biztosítható.

### <a name="high-performance-virtualized-workloads"></a>Nagy teljesítményű virtualizált számítási feladatok

Azure Stack HCI az iparág legjobb teljesítményét biztosíthatja SQL Server-adatbázisok és más, a teljesítményre érzékeny virtualizált munkaterhelések számára, amelyeken több millió tárolási IOPS vagy adatbázis-tranzakció szükséges

## <a name="azure-integration-benefits"></a>Azure-integrációs előnyök

Azure Stack a HCI-t egyedileg helyezik el a hibrid infrastruktúra számára, így kihasználhatja a Felhőbeli és a helyszíni erőforrások előnyeit, és natív módon figyelheti, biztonságossá teheti és biztonsági mentést készíthet a felhőbe.

Miután regisztrálta Azure Stack HCI-fürtöt az Azure-ban, használhatja a Azure Portal kezdetben a következőhöz:

- **Figyelés:** Az összes Azure Stack HCI-fürtöt egyetlen globális nézetben tekintheti meg, ahol erőforráscsoport alapján csoportosíthatja őket, és címkézheti azokat.
- **Számlázás:** Az Azure-előfizetésen keresztül Azure Stack HCI-t kell fizetnie.

Keményen dolgozunk a további képességek létrehozásán.

Feliratkozhat további Azure Hybrid Services-szolgáltatásokra is:

- **Azure site Recovery** a magas rendelkezésre álláshoz és a vész-helyreállítási szolgáltatáshoz (DRaaS).
- **Azure monitor**, egy központosított központ, amely nyomon követheti, hogy mi történik az alkalmazások, a hálózat és az infrastruktúra között – az AI által működtetett fejlett elemzési szolgáltatásokkal.
- **Felhőbeli tanúsító**, hogy az Azure-t használja a fürt Kvórumának egyszerű tie-megszakítójának.
- **Azure Backup** a telephelyen kívüli adatvédelem és a ransomware elleni védelem érdekében.
- **Azure-Update Management** az Azure-ban és a helyszínen futó Windows rendszerű virtuális gépek frissítésének értékeléséhez és frissítéséhez.
- **Azure hálózati adapter** a helyszíni erőforrások és az Azure-beli virtuális gépek pont – hely típusú VPN-kapcsolaton keresztüli összekapcsolásához.
- **Azure file Sync** a fájlkiszolgáló felhővel való szinkronizálásához.

További információ: [a Windows Server csatlakoztatása az Azure Hybrid Serviceshez](/windows-server/manage/windows-admin-center/azure/index).

## <a name="why-azure-stack-hci"></a>Miért Azure Stack HCI?

A Azure Stack HCI egy világszínvonalú, integrált virtualizációs verem, amely olyan bevált technológiákra épül, amelyek már üzembe helyezhetők, többek között a Hyper-V, a Közvetlen tárolóhelyek és az Azure által ihletett szoftver által meghatározott hálózatkezelés (SDN). Számos oka lehet annak, hogy az ügyfelek Azure Stack HCI-t választják, beleértve a következőket:

- A Hyper-V és a kiszolgálói rendszergazdák számára is ismerős, így lehetővé válik a meglévő virtualizációs és tárolási fogalmak és képességek kihasználása.
- Együttműködik a meglévő adatközpont-folyamatokkal és eszközökkel, például a Microsoft System Center, a Active Directory, a Csoportházirend és a PowerShell-parancsfájlok használatával.
- A szolgáltatás népszerű, harmadik féltől származó biztonsági mentési, biztonsági és monitorozási eszközökkel is működik.
- A rugalmas hardverkonfiguráció lehetővé teszik, hogy az ügyfelek a legjobb szolgáltatással és a földrajzuk támogatásával kiválasszák a gyártót.
- A Microsoft és a hardvergyártó közötti közös támogatás javítja a felhasználói élményt.
- A zökkenőmentes, teljes veremmel rendelkező frissítések megkönnyítik a jelenlegi környezetek megmaradása.
- A rugalmas és széles körű ökoszisztéma lehetővé teszi az informatikai szakemberek számára, hogy olyan megoldást hozzanak létre, amely a legjobban megfelel az igényeiknek.

## <a name="what-you-need-for-azure-stack-hci"></a>Azure Stack HCI-hez szükséges

A kezdéshez a következőkre lesz szüksége:

- Két vagy több olyan kiszolgálóból álló fürt, amely az Ön által előnyben részesített Microsoft Hardware partnertől vásárolt [Azure stack HCI katalógusból](https://hcicatalog.azurewebsites.net)származik.
- [Azure-előfizetés](https://azure.microsoft.com/)
- A fürt minden olyan kiszolgálójának internetkapcsolata, amely HTTPS kimenő forgalomon keresztül tud csatlakozni a jól ismert Azure-végpontokhoz legalább 30 naponként
- A helyek között kiterjesztett fürtök esetében legalább 1 1 GB-os kapcsolatra van szükség a helyek között (a 25 GB-os RDMA-kapcsolat előnyben részesített), és 5 ms-os átlagos késéssel kell rendelkeznie, ha szinkron replikálást szeretne végezni, ha mindkét helyen egyszerre történik az írás.
- Ha a szoftveres hálózatkezelés (SDN) használatát tervezi, szüksége lesz egy virtuális merevlemezre (VHD) a Azure Stack HCI operációs rendszerhez a hálózati vezérlő virtuális gépek létrehozásához (lásd: a [hálózati vezérlő üzembe helyezésének tervezése](concepts/network-controller.md))

További információ: [rendszerkövetelmények](concepts/system-requirements.md). Az Azure Kubernetes Service Azure Stack HCI-követelményekkel kapcsolatban lásd: az [AK-követelmények Azure stack HCI](../aks-hci/overview.md#what-you-need-to-get-started)-ben.

## <a name="hardware-partners"></a>Hardveres partnerek

Az Ön által előnyben részesített Microsoft-partner által ellenőrzött Azure Stack HCI-megoldásokat vásárolhat, így a hosszadalmas tervezés és a felépítési idő nélkül megkezdheti a működését. A Microsoft partnerei egyetlen kapcsolódási pontot is kínálnak a megvalósítási és támogatási szolgáltatásokhoz. Megvásárolhatja az érvényesített csomópontokat vagy egy integrált rendszert, amely magában foglalja az előre telepített Azure Stack HCI operációs rendszert, valamint az illesztőprogram-és a belső vezérlőprogram frissítéseinek partner-bővítményeit is.

Látogasson el a [Azure stack HCI Solutions](https://azure.microsoft.com/overview/azure-stack/hci) oldalra, vagy tallózzon a [Azure stack HCI-katalógusban](https://hcicatalog.azurewebsites.net) , és Azure stack tekintse meg a Microsoft-partnereinktől (például az ASUS, a Axellio, a Blue chip, a DATAON, a Dell EMC, a Fujitsu, a HPE, a Hitachi, a Huawei, a Lenovo, az NEC, a PRIMELINE Solutions, a QCT, a SecureGUARD és a nagymikro

## <a name="software-partners"></a>Szoftveres partnerek

Számos Microsoft-partner dolgozik olyan szoftvereken, amelyek kibővítik Azure Stack HCI képességeit, miközben lehetővé teszik, hogy a rendszergazdák ismerős eszközöket használjanak. További információ: segédprogram- [alkalmazások Azure stack HCI-hez](concepts/utility-applications.md).

## <a name="licensing-billing-and-pricing"></a>Licencelés, Számlázás és díjszabás

Azure Stack a HCI-számlázás a fizikai processzorok havi előfizetési díja, nem pedig örökös licenc alapján történik. Amikor az ügyfelek az Azure-hoz csatlakoznak, a felhasznált magok számát a rendszer automatikusan feltölti és kivizsgálja számlázási célokra. A Cost nem változik a fizikai processzor-magokon túli fogyasztástól, ami azt jelenti, hogy több virtuális gép nem drágább, és a sűrűbb virtuális környezetek futtatására képes ügyfeleknek jutalmat kell fizetniük.

Az ügyfelek vagy vásárolhatnak ellenőrzött kiszolgálókat egy hardveres partnertől a Azure Stack HCI operációs rendszerrel előre telepítve, vagy megvásárolhatják az érvényesített operációs rendszer nélküli kiszolgálókat egy SZÁMÍTÓGÉPGYÁRTÓtól, majd előfizethetnek a Azure Stack HCI szolgáltatásra, és [le is tölthetik a Azure stack HCI operációs rendszert](https://azure.microsoft.com/products/azure-stack/hci/).

## <a name="the-azure-stack-family"></a>A Azure Stack család

A Azure Stack HCI az Azure és a Azure Stack család része, és ugyanazt a szoftveresen definiált számítási, tárolási és hálózatkezelési szoftvert használja, mint Azure Stack hub. Az alábbiakban a különböző megoldások gyors összefoglalását ismertetjük. További információ: [a Azure stack ökoszisztémájának összevetése](../operator/compare-azure-azure-stack.md).

- Az [Azure](https://azure.microsoft.com) -ban a nyilvános felhőalapú szolgáltatások igény szerinti, önkiszolgáló számítási erőforrásokhoz használhatók a meglévő alkalmazások áttelepíthetők és modernizálása, valamint új, Felhőbeli natív alkalmazások létrehozásához.
- [Azure stack Edge](/azure/databox-online/data-box-edge-overview) – felgyorsítja a gépi tanulás feladatait, és futtatja a helyszíni alkalmazásokat vagy a helyileg virtualizált számítási feladatokat egy felhőben felügyelt berendezésen.
- [Azure stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) – virtualizált alkalmazások futtatása a helyszínen, az öregedő kiszolgáló infrastruktúrájának cseréje és összevonása, valamint az Azure-hoz való kapcsolódás a Cloud Serviceshez.
- [Azure stack hub](../operator/azure-stack-overview.md) – felhőalapú alkalmazások futtatása a helyszínen, a leválasztáskor vagy a szabályozási követelmények teljesítése érdekében, konzisztens Azure-szolgáltatások használatával.

:::image type="content" source="media/overview/azure-family-updated.png" alt-text="Azure Stack családi megoldás diagramja" border="false":::

## <a name="whats-new-in-azure-stack-hci"></a>Az Azure Stack HCI újdonságai

A Windows felügyeleti központ 2009-es verziója számos funkciót biztosít a HCI Azure Stackához, beleértve a következőket:

- **Azure Kubernetes szolgáltatás üzemeltetési képességei**: most már telepítheti az [Azure Kubernetes Service előzetes verzióját Azure stack HCI-on](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).
- A **szoftver által definiált hálózatkezelés belefoglalása a fürt létrehozása varázslóban**: a fürt létrehozása varázsló már tartalmazza a [szoftveres hálózatkezelés (Sdn)](concepts/software-defined-networking.md) hálózati vezérlő funkciójának telepítését a [fürt létrehozása](deploy/create-cluster.md#step-5-sdn-optional)során.
- **Távoli közvetlen memória-hozzáférés (RDMA) továbbfejlesztése a fürt létrehozása varázslóban**: a fürt létrehozása varázsló mostantól konfigurálhatja a RDMA a iWARP és a RoCE hálózati adapterekhez, beleértve az adatközpont-áthidalás (DCB) szolgáltatást is.

További információ az új funkciókról: [a fürt létrehozási bővítményének általánosan elérhetővé való bejelentése a Windows felügyeleti központban](https://techcommunity.microsoft.com/t5/windows-admin-center-blog/announcing-general-availability-of-the-cluster-creation/ba-p/1978332).

A Azure Stack HCI-t futtató fürtökön a következő új funkciók jelennek meg a Windows Server 2019-alapú megoldásaihoz képest: 20H2.

- **Új funkciók a Windows felügyeleti központban**: a hiperkonvergens-fürtöket intuitív felhasználói felületen keresztül hozhatja létre és frissítheti, Azure stack a HCI-t minden eddiginél könnyebben használhatja.
- Kibővített **fürtök automatikus feladatátvételhez**: a többhelyes fürtözés a tárolási replika replikálásával és az automatikus virtuális gép feladatátvétele natív vész-helyreállítást és üzletmenet-folytonosságot biztosít.
- **Affinitás és affinitási szabályok**: ezeket ugyanúgy használhatja, hogy az Azure hogyan használja Availability Zones a virtuális gépek és a tárolók összekapcsolását a több tartalék tartománnyal rendelkező fürtökön, például a kiterjesztett fürtökön.
- **Azure Portal integráció**: az Azure stack HCI-hez készült Azure Portal-élmény úgy lett kialakítva, hogy az összes Azure stack HCI-fürtöt megtekintse a világ minden részén, a fejlesztés új szolgáltatásaival.
- **GPU-gyorsítás a nagy teljesítményű** számítási feladatokhoz: az AI/ml-alkalmazások kihasználhatják a GPU-k teljesítményének növelését.
- **BitLocker-titkosítás**: mostantól a BitLocker használatával titkosíthatja az adatkötetek tartalmát Azure stack HCI-ben, így a kormányzat és más ügyfelek is megmaradnak a szabványoknak, például az FIPS 140-2-es és a HIPAA-nek.
- **Javított közvetlen tárolóhelyek mennyiségi javítási sebesség**: a kötetek gyors és zökkenőmentes javítása.

A Windows felügyeleti központ, a 20H2 verzió új fürtöt is biztosít a Windows Server-alapú fürtökhöz, beleértve az eredeti Azure Stack HCI-megoldásokat is. Ha pedig az új fürt létrehozása varázslót a Windows Serverrel is használhatja, a Windows Server-fürtök nem hozhatók létre a Közvetlen tárolóhelyek; erre a Azure Stack HCI operációs rendszerre van szükség.

## <a name="roles-you-can-run-without-virtualizing"></a>Virtualizálás nélkül futtatható szerepkörök

Mivel Azure Stack a HCI olyan virtualizációs gazdagépként szolgál, amelyben az összes számítási feladatot futtatja a virtuális gépeken, az Azure Stack HCI-feltételek lehetővé teszik, hogy csak a virtuális gépek üzemeltetéséhez szükséges funkciókat futtassa.

Ez azt jelenti, hogy a következő kiszolgálói szerepköröket futtathatja:

- Hyper-V
- A szoftver által meghatározott hálózatkezeléshez (SDN) szükséges hálózati vezérlő és egyéb összetevők

Más szerepköröknek és alkalmazásoknak azonban a virtuális gépeken belül kell futniuk. Vegye figyelembe, hogy az üzemeltetett virtuális gépek kezeléséhez és állapotához szükséges segédprogramokat, alkalmazásokat és szolgáltatásokat is futtathatja.

## <a name="video-based-learning"></a>Videó alapú tanulás

Az Azure bővített hálózati videója itt található:

- [Zökkenőmentes kapcsolódás az Azure-hoz a Windows Serverrel és a hibrid hálózatkezeléssel](https://www.youtube.com/watch?v=do2_4Y2p9dk)

Videók az Azure Stack HCI eredeti, Windows Server 2019-alapú verziójáról:

- [Azure Stack és Azure Stack HCI – áttekintés nézet](https://aka.ms/AzureStackOverviewVideo)
- [Microsoft Ignite Live 2019 – Első lépések Azure Stack HCI-vel](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [Azure Stack HCI felderítése](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [A kiskereskedelmi üzletek vagy fiókirodák modernizálása Azure Stack HCI-vel](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Az Azure Stack HCI újdonságai: 45-es dolgok 45 percek alatt](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [A Azure Stack HCI üzembe helyezése](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [A zsugorodó adatvesztés esetén: Azure Stack HCI-ben történő adatmásolások](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Dave Kawula a Azure Stack HCI-on lévő mező megjegyzései](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

Egy hibrid Felhőbeli virtuális eseményből származó videó:

- [Azure Stack HCI | Hibrid Felhőbeli virtuális esemény](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="next-steps"></a>Következő lépések

- [Azure Stack HCI letöltése](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)
- [Azure Stack HCI-fürt létrehozása és regisztrálása az Azure-ban](deploy/deployment-quickstart.md)
- [Azure Stack HCI használata a Windows felügyeleti központtal](get-started.md)
