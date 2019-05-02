---
title: Mi az Azure Stack? | Microsoft Docs
description: Ismerje meg, hogyan Azure Stack teszi lehetővé az adatközpontban található Azure-szolgáltatások futtatásához.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 382fc49caf12a578584c8680edb30a321e82ea00
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985052"
---
# <a name="azure-stack-overview"></a>Az Azure Stack áttekintése

Az Azure Stack az Azure kiterjesztése, amely biztosítja az alkalmazások futtatását a helyszíni környezetben és az adatközpontban található Azure-szolgáltatások továbbítására. Egy egységes felhőplatform, a szervezetek magabiztosan teheti meg a technológiai döntések a hozott üzleti követelmények alapján, hanem üzleti döntéseket hozhat a technológiai korlátok alapján.

## <a name="why-use-azure-stack"></a>Miért érdemes használni az Azure Stack?

Az Azure a fejlesztők számára a modern alkalmazások készítése gazdag platformot biztosít. Azonban néhány felhőalapú alkalmazások arc akadályait, például a késés, a nem állandó hálózati kapcsolat és az előírásoknak. Az Azure és az Azure Stack zárolásának feloldása új hibrid felhős használati példák a ügyfél felé irányuló mind a belső üzletági alkalmazások:

- **Peremhálózati és hálózat nélküli megoldásoknál**. Késéssel és elérhetőséggel kapcsolatos követelmények cím adatfeldolgozás helyileg az Azure Stackben és majd összesítésével, az Azure-ban további elemzés céljából, a közös alkalmazás-logikával között is. Azure Stack az Azure-hoz kapcsolódik az internethez, kapcsolat nélkül is telepítheti. Gondolja át, hogy a gyáraktól, luxushajókon részeként szerezhető be és adatbányászatot végezhessen az adataiban tengelyek példaként.

- **Felhőbeli alkalmazásokat, amelyek megfelelnek a változatos szabályzat**. Fejlesztés és üzembe helyezés az Azure-alkalmazások teljesen rugalmasan telepíthetők a helyszínen az Azure Stack használatával szabályozási vagy a házirend követelményeinek, a kódmódosítás nélkül. Globális naplózási, pénzügyi jelentések, kereskedelmi deviza, online játékokhoz és költségelszámolás alkalmazás közé.

- **Felhőalapú alkalmazás helyszíni modellje**. Azure-szolgáltatások, a tárolókat, kiszolgáló nélküli, és a mikroszolgáltatás-architektúrák segítségével frissítése és a meglévő alkalmazásaik vagy újak létrehozása. Használat egységes fejlesztési-üzemeltetési folyamatokat a felhőben Azure-ban és az Azure Stack a helyszíni alkalmazások modernizálása az alapvető fontosságú alkalmazások felgyorsítása érdekében.

Az Azure Stack lehetővé teszi, hogy ezek a használati forgatókönyvek megadásával:

- Egy integrált szolgáltatásnyújtási környezetet szolgáltatásszintje és a kifejezetten az Azure stackbe integrált rendszerekkel megbízható hardvergyártó partnerektől. Miután az Azure Stack könnyen integrálható az adatközpontban a System Center Operations Manager felügyeleti csomag vagy Nagios bővítményével figyeléssel.

- Azure Active Directory (Azure AD) használatával az Azure és az Azure Stack egy hibrid környezetek, és az Active Directory összevonási szolgáltatások (AD FS) kihasználva rugalmas Identitáskezelés központi telepítések leválasztva. 

- Az Azure-egységes alkalmazásfejlesztési környezet fejlesztői hatékonyság, és engedélyezze a közös fejlesztési és üzemeltetési megközelíti a hibrid környezetekben.

- Az Azure-szolgáltatások kézbesítési helyszíni hibrid felhőalapú számítástechnika használatával. Fogadja el az általános működési gyakorlatot Azure-ban és az Azure Stack üzembe helyezését és üzemeltetését Azure IaaS és PaaS szolgáltatások Azure azonos felügyeleti élményt és eszközök használatával. A Microsoft Azure Stack, beleértve az új Azure-szolgáltatásokat, a meglévő szolgáltatások, valamint további alkalmazások Azure Marketplace-en és képek frissítések továbbítja az Azure folyamatos innováció.

## <a name="azure-stack-architecture"></a>Az Azure Stack-architektúra
Az Azure Stack integrált rendszerek állnak a 4 és 16-kiszolgálók által készített rackszekrények megbízható a hardvergyártó partnerektől, és közvetlenül is az Adatközpont-i. Miután megoldásszolgáltató fog dolgozni helyezhet üzembe integrált rendszer, és az Azure Stack megoldás megfelel-e az üzleti követelményeinek. Az Adatközpont készíti elő az összes szükséges teljesítmény biztosítása és hűtési kell, szegély kapcsolatokat és egyéb szükséges adatközpont integrációja követelmény érvényben vannak. 

> Adatközpont integrációja az Azure Stack funkciókkal kapcsolatos további információkért lásd: [adatközpont integrációja az Azure Stack](azure-stack-customer-journey.md).

Az Azure Stack iparági szabványos hardver épül, és már használhatja az Azure-előfizetések kezeléséhez ugyanazokkal az eszközökkel kezelhetők. Ennek eredményeképpen egységes fejlesztési és üzemeltetési folyamatokat is alkalmazni, hogy kapcsolódik az Azure-e. 

Az Azure Stack-architektúra lehetővé teszi, hogy az Azure-szolgáltatásokat a peremhálózaton biztosíthat a távoli helyeken vagy a nem állandó hálózati kapcsolat, kapcsolódik az internethez. Helyileg az Azure Stackben adatok feldolgozására, és ezután összesítésben, az Azure-ban a további feldolgozás és elemzés hibrid megoldásokat hozhat létre. Végül mivel az Azure Stack telepített helyszíni, pedig megfelelhet a konkrét szabályozási vagy a házirend követelményeinek rugalmasságával helyszíni üzembe helyezése felhőalapú alkalmazás bármilyen kód átírása nélkül. 

## <a name="deployment-options"></a>Üzembe helyezési beállítások

### <a name="production-or-evaluation-environments"></a>Éles vagy a kiértékelési környezet
Az Azure Stack az igényeinek, éles környezetben való használatra Azure Stack integrált rendszerek és az Azure Stack Development Kit (ASDK) Azure Stack értékelési két üzembe helyezési lehetőség érhető el:

- **Az Azure Stack integrált rendszerek**. Az Azure Stack integrált rendszerek a hardver és a Microsoft partnereinek évente megrendezett megoldások létrehozásának kialakított partnerség révén érhetők el a felhő saját innováció és a felügyeleti egyszerűség számítástechnika kínál. Azure Stack érhető el egy integrált hardver- és szoftver-, mert rendelkezik a rugalmasság és a vezérlés van szüksége, és innovációját annak a felhőből lehetőséget. Az Azure Stack integrált rendszerek mérete 4 és 16 közötti csomópontjáról tartományt, és közösen a hardver partner és a Microsoft által támogatott. Integrált Azure Stack rendszerek használata új forgatókönyvek létrehozása és telepítése az új megoldások a termelési számítási feladatokhoz.

- **Az Azure Stack Development Kit**. A [Azure Stack Development Kit (ASDK)](../asdk/asdk-what-is.md) van egy ingyenes, egy csomópontos üzembe helyezhető Azure Stacket, amellyel kipróbálni és megismerni az Azure stackről. Emellett használhatja a ASDK egy fejlesztői környezet használatával az API-k és eszközök alkalmazásokat hozhat létre, amely egységes Azure-ral. A ASDK azonban nem javasolt éles környezetben használhatók, és az alábbi korlátozások vonatkoznak, mint a teljes, integrált rendszerek éles üzembe helyezés a korábban megszokott:

    - Lehet, hogy a ASDK csak egy egyetlen Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) identitásszolgáltató tartoznak.
    - Az Azure Stack-összetevők egy egyetlen számítógépen van telepítve, mert érhetők el korlátozott fizikai erőforrások bérlői erőforrásokhoz. Ez a konfiguráció nem javasolt a méretezési csoport vagy a teljesítmény értékeléséhez.
    - Hálózati forgatókönyvek a egyetlen gazdagép és a hálózati adapter központi telepítésére vonatkozó követelmények miatt korlátozva.

### <a name="connection-models"></a>Kapcsolati modellek
Dönthet úgy, hogy az Azure Stack üzembe helyezése vagy **csatlakoztatott** az internethez (és az Azure-bA), vagy **leválasztott** belőle. Ez a választás határozza meg, melyik lehetőség áll rendelkezésre az ügyfélidentitás-tárolóval (Azure AD vagy az AD FS) és a számlázási modellt (kell fizetnie, a használat alapú számlázási vagy a kapacitás-alapú számlázási).

> További információkért lásd: a szempontjai [csatlakoztatott](azure-stack-connected-deployment.md) és [leválasztott](azure-stack-disconnected-deployment.md) üzembe helyezési modellel. 

### <a name="identity-provider"></a>Identitásszolgáltató 
Az Azure Stack Identitásszolgáltatóként, vagy az Azure Active Directory (Azure AD), vagy az Active Directory összevonási szolgáltatások (AD FS) létesíteni az Azure Stack-identitásokat használ. 

> [!IMPORTANT]
> Ez a fő döntési pont! Választhatja az Azure AD vagy az AD FS, az identitásszolgáltató egy egyszeri döntési üzembe helyezéskor kell végrehajtania. Nem módosíthatja ezt később ismételt üzembe helyezése a teljes rendszer nélkül.

Az Azure AD a Microsoft felhőalapú, több-bérlős identitásszolgáltató. Internetkapcsolattal rendelkező üzemelő példányok a legtöbb hibrid forgatókönyvek kialakítását ügyfélidentitás-tárolóval, az Azure AD használatára. Azonban előfordulhat, hogy használni kívánja az Active Directory összevonási szolgáltatások (AD FS) az Azure Stack kapcsolat nélküli telepítésekhez. Az Azure Stack erőforrás-szolgáltatók és más alkalmazások működnek az AD FS-sel ugyanolyan módon, az Azure ad-ben. Az Azure Stack saját Active Directory-példányból, és a egy Active Directory Graph API is tartalmaz. 

> További, az Azure Stack identitás szempontokról [áttekintése az Azure stack-identitás](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Hogyan kezeli az Azure Stack?
Miután az Azure Stack egy integrált rendszerek üzembe helyezési vagy ASDK telepítés már telepítve lett, az elsődleges interakció az Azure Stack módszereket a felügyeleti portálon, a felhasználói portál és a PowerShell. Az Azure Stack portálon minden egyes biztonsági külön példányok az Azure Resource Manager által. Egy **Azure Stack-operátorokról** használ a felügyeleti portálon kezelheti az Azure Stack, és van például a bérlő létrehozása ajánlatok és karbantartására használható integrált rendszer állapotának és a figyelő állapotát. A felhasználói portál (más néven a bérlői portál) önkiszolgáló élményt nyújt a felhőbeli erőforrások, például a virtuális gépek, tárfiókok és a web apps fogyasztását. 

> A felügyeleti portál használatával az Azure Stack kezelésével kapcsolatos további információkért tekintse meg a használatát a [Azure Stack a felügyeleti portál rövid](azure-stack-manage-portals.md).

Kezelőként Azure Stack üzembe helyezhető széles körének szolgáltatások és alkalmazások, például [virtuális gépek](azure-stack-tutorial-tenant-vm.md), [webes alkalmazások](azure-stack-app-service-overview.md), magas rendelkezésre állású [SQL Server](azure-stack-tutorial-sql.md), és [MySQL-kiszolgáló](azure-stack-tutorial-mysql.md) adatbázisok. Is [Azure Stack a rövid útmutató az Azure Resource Manager-sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) SharePoint, Exchange és több üzembe helyezéséhez. 

A felügyeleti portál használatával [konfigurálása az Azure Stack-szolgáltatások továbbítására](azure-stack-plan-offer-quota-overview.md) csomagokat, kvótákat, ajánlatok és előfizetések használata bérlők számára. Több ajánlattal is fizessen elő a bérlői felhasználó számára. Ajánlatok egy vagy több csomag is rendelkezhet, és a csomagok egy vagy több szolgáltatást is rendelkezhet. Operátorok is kapacitás kezelése és válaszadás a riasztásokra. 

Ha az Azure Stack van konfigurálva, egy **Azure Stack felhasználói** (más néven a bérlői) használ a szolgáltatások, amelyek az operátor kínál fel. Felhasználók kiépítéséhez, figyeléséhez és azok feliratkozott, például a web apps, a storage és a virtuális gépek szolgáltatások felügyeletéhez.

> Tudjon meg többet az Azure Stack, beleértve a mi where, tipikus operátor felelősségekről, használandó fiókok kezelése, mit kell tudniuk a felhasználóknak, és hogyan kérhet segítséget, tekintse át [Azure Stack az Adminisztráció alapjai](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Erőforrás-szolgáltatók 
Erőforrás-szolgáltatók olyan webszolgáltatásokat, amelyekhez alapjai az összes Azure Stack IaaS és PaaS-szolgáltatások. Az Azure Resource Manager-szolgáltatásokhoz való hozzáférés biztosításához másik erőforrás-szolgáltatók támaszkodik. Mindegyik erőforrás-szolgáltató segítségével a konfigurálása, valamint szabályozhatja a megfelelő erőforrásokkal. Szolgáltatás-rendszergazdákat is hozzáadhat új egyéni erőforrás-szolgáltatók. 

### <a name="foundational-resource-providers"></a>Alapvető erőforrás-szolgáltatók 
Nincsenek három alapvető IaaS erőforrás-szolgáltatók: Számítási, hálózati és tárolási:

- **COMPUTE**. A számítási erőforrás-szolgáltató lehetővé teszi, hogy az Azure Stack-bérlők számára saját virtuális gépek létrehozása. A számítási erőforrás-szolgáltató képes létrehozni virtuális gépeket, valamint a virtuális gépi bővítmények tartalmazza. A virtuálisgép-bővítmény szolgáltatás segít az IaaS-képességeket nyújt a Windows és Linux rendszerű virtuális gépek.  Tegyük fel a számítási erőforrás-szolgáltató használatával egy Linux rendszerű virtuális gép üzembe helyezése és futtatása a Bash-szkriptek konfigurálása a virtuális gép üzembe helyezése során.
- **Hálózati erőforrás-szolgáltató**. A hálózati erőforrás-szolgáltató a magánfelhő hálózati függvény virtualizációs (NFV) és a szoftveralapú hálózatkezelés (SDN) funkciókat kínál. A hálózati erőforrás-szolgáltató használatával olyan erőforrásokhoz, mint a szoftveres terheléselosztókat, nyilvános IP-címek, hálózati biztonsági csoportok és virtuális hálózatok létrehozása.
- **Tárolásierőforrás-szolgáltató**. A Tárolásierőforrás-szolgáltató négy egységes Azure-storage-szolgáltatások nyújtásának: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [várólista](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tábla](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage), és a KeyVault felügyeletének biztosító felügyeleti és titkos adatait, például a jelszavak és tanúsítványok naplózását. A tárolásierőforrás-szolgáltató nyújt egy felhőalapú felügyeleti társzolgáltatás megkönnyítése érdekében a service provider felügyeleti egységes Azure-Storage-szolgáltatás is. Az Azure Storage tárolja, és hívhat le nagy mennyiségű strukturálatlan adat, például dokumentumokat és médiafájlokat az Azure-Blobok, rugalmasan és strukturált NoSQL-alapú adatok az Azure Tables segítségével. 

### <a name="optional-resource-providers"></a>Nem kötelező erőforrás-szolgáltatók
Nincsenek három választható PaaS erőforrás-szolgáltató üzembe helyezése és használata az Azure Stack használatával: App Service-ben, az SQL Server és a MySQL-kiszolgáló erőforrás-szolgáltatók:

- **App Service-ben**. [Az Azure App Service az Azure Stacken](azure-stack-app-service-overview.md) érhető el a Microsoft Azure platform –-szolgáltatásként (PaaS) ajánlat az Azure Stackhez. A szolgáltatás lehetővé teszi, hogy a belső vagy külső ügyfelei számára, hogy hozzon létre a webes API-t és az Azure Functions alkalmazások bármilyen platformra vagy eszközre. 
- **Egy SQL Server**. Használja a [erőforrás-szolgáltató SQL Server](azure-stack-sql-resource-provider.md) az Azure Stack, szolgáltatásként nyújtott SQL Database-adatbázisok. Miután telepítette az erőforrás-szolgáltató, és csatlakoztathatja azt egy vagy több SQL Server-példányt, és a felhasználók felhőbeli natív alkalmazásokat, webhelyeket, amelyek az SQL és egyéb számítási feladatokat, amelyek használják az SQL adatbázist is létrehozhat.
- **A MySQL-kiszolgáló**. Használja a [MySQL-kiszolgáló erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md) elérhetővé a MySQL-adatbázisok Azure Stack szolgáltatásként. A MySQL erőforrás-szolgáltató szolgáltatásként fut, a Windows Server 2016 Server Core virtuális gépeken (VM).

## <a name="providing-high-availability"></a>Magas rendelkezésre állás biztosítása
Magas rendelkezésre állás az Azure-beli virtuális gépre kiterjedő éles rendszer, a virtuális gépek kerüljenek a egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) , amely osztja el azokat több tartalék tartomány és frissítési tartományok között. Az Azure Stack kisebb mennyiségű egy tartalék tartományt egy rendelkezésre állási csoportba egy csomópontot a skálázási egységben van definiálva.  

Azure Stack infrastruktúrája már hatással a meghibásodások, míg az alapul szolgáló technológiát (feladatátvételi fürtszolgáltatás) továbbra is leállást bizonyos virtuális gépek érintett fizikai kiszolgálón hardverhiba esetén. Az Azure Stack támogatja a rendelkezésre állási csoport, amely legfeljebb három tartalék tartományt az Azure-ral konzisztens kellene.

- **Tartalék tartományok**. Virtuális gépeket egy rendelkezésre állási csoportot helyezett osztja szét őket lehető legegyenletesebben több tartalék tartomány (az Azure Stack-csomópontok) keresztül lesz fizikailag különítve egymástól. Hardverhiba esetén a sikertelen tartalék tartomány virtuális gépeket fogja indítani a többi tartalék tartományban, de, külön tartalék tartományokban, ha lehetséges, a más virtuális gépek ugyanazon rendelkezésre állási csoportban tartani. A hardver visszatér online állapotba, ha virtuális gépek fog rebalanced magas rendelkezésre állás fenntartása érdekében. 
 
- **Frissítési tartományok**. Frissítési tartományok egy másik Azure demonstráció létrehozásában, amely a rendelkezésre állási csoportokat magas rendelkezésre állást biztosít. Frissítési tartomány, amelyek karbantartása egy időben is mennek keresztül alapul szolgáló hardver logikai csoportjai. Az azonos frissítési tartományban található virtuális gépek együtt tervezett karbantartás során újraindul. Bérlő virtuális gépek létrehozása rendelkezésre állási csoportban, mint az Azure platform automatikusan elosztja a virtuális gépek között ezek frissítési tartományok. Az Azure Stackben, virtuális gépek élő áttelepítése a fürt többi online gazdagép között, a mögöttes állomás frissítése előtt. A gazdagép frissítése közben nem bérlői jár, mivel a frissítési tartomány funkció az Azure Stacken csak létezik sablon kompatibilitás érdekében az Azure-ral. 

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepkör alapú hozzáférés-vezérlés (RBAC) segítségével a rendszer hozzáférési jogot engedéllyel rendelkező felhasználók, csoportok és szolgáltatások egy előfizetés, erőforráscsoport vagy egyéni erőforrás szintjén szerepkörök hozzárendelésével. Minden egyes szerepkör a felhasználó, csoport vagy szolgáltatás rendelkezik a Microsoft Azure Stack-erőforrások hozzáférési szint határozza meg.

Az Azure Stack RBAC rendelkezik, amelyek érvényesek az összes erőforrástípus három alapvető szerepkörök: Tulajdonos, közreműködő és olvasó. Tulajdonos az összes olyan erőforrásokhoz, beleértve a jogot arra, hogy mások való hozzáférés delegálására teljes hozzáféréssel rendelkezik. Közreműködői hozhat létre és az Azure-erőforrások összes típusa felügyelhető, de nem adhat hozzáférést másoknak. Olvasó csak meglévő erőforrások megtekintéséhez. Az RBAC-szerepkörök többi adott Azure-erőforrások kezelését teszi lehetővé. A virtuális gépek Közreműködője szerepkör például-virtuális gépek létrehozása és kezelése lehetővé teszi, de nem teszi lehetővé a virtuális hálózat és az alhálózathoz, amelyhez csatlakozik a virtuális gép felügyeletét.

> Lásd: [Manage Role-Based hozzáférés-vezérlés](azure-stack-manage-permissions.md) további információt. 

## <a name="reporting-usage-data"></a>Jelentéskészítés a használati adatok
A Microsoft Azure Stack gyűjti és összesíti az összes erőforrás-szolgáltató különböző használati adatait, és továbbítja azt az Azure-bA a feldolgozáshoz az Azure kereskedelmi. Az Azure Stacken az összegyűjtött használati adatok a REST API-n keresztül is megtekinthetők. Van egy egységes Azure-bérlői API-t, valamint a szolgáltató és delegált szolgáltató API-k az összes bérlő előfizetés használati adatainak beolvasásához. Ezeket az adatokat egy külső eszközzel vagy a szolgáltatás számlázási vagy költséghelyi elszámolás integrálásához használható. Használat az Azure kereskedelmi által feldolgozott, amint azt az Azure számlázási portálján lehet megtekinteni.

> Tudjon meg többet [Azure Stack használati adatokról szóló jelentéseket küldeni az Azure-bA](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>További lépések

[Azure Stack és a globális Azure összehasonlítása](compare-azure-azure-stack.md)

[Az Adminisztráció alapjai](azure-stack-manage-basics.md)

[Gyors útmutató: az Azure Stack felügyeleti portál használata](azure-stack-manage-portals.md)