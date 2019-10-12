---
title: Mi az Azure Stack? | Microsoft Docs
description: Ismerje meg, hogy Azure Stack hogyan futtathatja az Azure-szolgáltatásokat az adatközpontjában.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/16/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: 060258b07e3eef385298600794a6dd8154a461fc
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283425"
---
# <a name="azure-stack-overview"></a>Az Azure Stack áttekintése

Azure Stack az Azure kiterjesztése, amely lehetővé teszi az alkalmazások helyszíni környezetben való futtatását, és az Azure-szolgáltatások nyújtását az adatközpontban. Az egységes felhőalapú platformmal a szervezetek az üzleti követelmények alapján, a technológiai korlátozások alapján nem üzleti döntéseket hozhatnak.

## <a name="why-use-azure-stack"></a>Miért érdemes Azure Stack?

Az Azure sokoldalú platformot biztosít a fejlesztők számára modern alkalmazások létrehozásához. Egyes felhőalapú alkalmazások azonban olyan akadályokba ütköznek, mint például a késés, az időszakos kapcsolat és a szabályozás. Az Azure és a Azure Stack új hibrid Felhőbeli használati eseteket is felhasználhat az ügyfelek és a belső üzletági alkalmazások számára:

- **Edge és leválasztott megoldások**. A késéssel és a kapcsolattal kapcsolatos követelmények kezelése az adatfeldolgozással helyileg Azure Stack, majd az Azure-ban összesítve további elemzésekhez, és a közös alkalmazás-logikával együtt. Az internetről az Azure-hoz való csatlakozás nélkül is üzembe helyezhető Azure Stack. Példaként tekintse meg a gyári padlókat, a tengerjáró hajókat és a bánya-aknákat.

- **A különböző szabályozásoknak megfelelő felhőalapú alkalmazások**. Alkalmazások fejlesztése és üzembe helyezése az Azure-ban, teljes rugalmassággal a helyszíni üzembe helyezése Azure Stack a szabályozási vagy házirendi követelmények teljesítése érdekében, és nincs szükség kód módosítására. Az alkalmazás példái közé tartozik a globális audit, a pénzügyi jelentéskészítés, a deviza-kereskedelem, az online játékok és a költségek bejelentése.

- **Felhőbeli alkalmazás-modell a helyszínen**. Az Azure-szolgáltatások, tárolók, kiszolgáló nélküli és a Service-architektúrák használatával frissítheti és bővítheti a meglévő alkalmazásokat, illetve újakat építhet ki. A felhőben az Azure-ban konzisztens DevOps folyamatokat használhat, és Azure Stack a helyszínen, hogy felgyorsítsa az alkalmazások modernizációját a legfontosabb alapvető fontosságú alkalmazásokhoz.

A Azure Stack a következő lehetőségeket biztosítja a használati forgatókönyvek számára:

- Integrált kézbesítési élmény, amellyel gyorsan üzembe helyezhetők a megbízható hardveres partnerektől származó, szándékosan kialakított Azure Stack integrált rendszerek. A kézbesítést követően a Azure Stack könnyedén integrálható az adatközpontba az System Center Operations Manager felügyeleti csomag vagy a Nagios-bővítmény használatával.

- Rugalmas Identitáskezelés Azure Active Directory (Azure AD) használatával az Azure-hoz és a Azure Stack hibrid környezetekhez, valamint a Active Directory összevonási szolgáltatások (AD FS) (AD FS) kihasználása a leválasztott központi telepítésekhez. 

- Egy Azure-konzisztens alkalmazás-fejlesztési környezet a fejlesztői hatékonyság maximalizálása és a gyakori DevOps megközelítések hibrid környezetekben való használatának lehetővé tételéhez.

- A helyszíni Azure-szolgáltatások hibrid felhőalapú számítási teljesítmény használatával. Az Azure-ban és a Azure Stackban az Azure-IaaS és a Pásti-szolgáltatások üzembe helyezéséhez és üzemeltetéséhez az Azure-t használó általános működési gyakorlatot kell alkalmazni. A Microsoft folyamatos Azure-innovációt biztosít a Azure Stack számára, beleértve az új Azure-szolgáltatásokat, a meglévő szolgáltatások frissítéseit és az Azure Marketplace-alkalmazások és-lemezképek további szolgáltatásait.

## <a name="azure-stack-architecture"></a>Azure Stack architektúra
Azure Stack integrált rendszerek megbízható hardveres partnerek által készített, és közvetlenül az adatközpontba szállított 4-16-es kiszolgálók állványait alkotják. A kiszállítást követően a megoldás-szolgáltató együttműködve telepíti az integrált rendszerét, és gondoskodik arról, hogy a Azure Stack megoldás megfeleljen az üzleti követelményeknek. Elő kell készítenie az adatközpontot úgy, hogy az összes szükséges energiaellátást és hűtést, határt és egyéb szükséges adatközpont-integrációs követelményt biztosítja. 

> További információ az Azure Stack Datacenter integrációs felületéről: [Azure stack Datacenter Integration](azure-stack-customer-journey.md).

Az Azure Stack az iparági szabványoknak megfelelő hardverre épül, és ugyanazokkal az eszközökkel felügyelhető, mint amelyeket az Azure-előfizetések kezeléséhez már használ. Ennek eredményeképpen konzisztens DevOps-folyamatokat alkalmazhat, függetlenül attól, hogy csatlakozik-e az Azure-hoz. 

A Azure Stack architektúra lehetővé teszi, hogy az Azure-szolgáltatásokat a peremhálózat távoli helyein vagy időszakos kapcsolaton keresztül, az internetről leválasztva biztosítson. Létrehozhat olyan hibrid megoldásokat, amelyek az adatokat helyileg dolgozzák fel Azure Stack, majd az Azure-ban összesítve további feldolgozást és elemzést végeznek. Végezetül, mivel Azure Stack a helyszínen van telepítve, a konkrét szabályozási vagy házirend-követelmények teljesítése érdekében a felhőalapú alkalmazások helyszíni üzembe helyezése a kód módosítása nélkül is megváltoztatható. 

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

### <a name="production-or-evaluation-environments"></a>Üzemi vagy kiértékelési környezetek
Azure Stack két üzembe helyezési lehetőség közül választhat, amelyek megfelelnek az igényeinek, Azure Stack a termelési célú integrált rendszerek és a Azure Stack Development Kit (ASDK) számára a Azure Stack értékeléséhez:

- **Azure stack integrált rendszerek**. Azure Stack integrált rendszerek a Microsoft és a hardveres partnerek közötti partneri kapcsolaton keresztül érhetők el, így olyan megoldások hozhatók létre, amelyek a Felhőbeli innováció és a számítástechnika egyszerűségét kínálják. Mivel a Azure Stack integrált hardver-és szoftveres rendszerként érhető el, rugalmassága és szabályozása szükséges, valamint a felhőből való innováció képessége is. Azure Stack az integrált rendszerek 4-16 csomópontokból állnak, és a hardveres partner és a Microsoft közösen támogatja őket. Azure Stack integrált rendszerek használatával új forgatókönyveket hozhat létre, és új megoldásokat telepíthet az éles számítási feladatokhoz.

- **Azure stack Development Kit**. A [Azure stack Development Kit (ASDK)](../asdk/asdk-what-is.md) egy olyan Azure stack ingyenes, egycsomópontos telepítése, amelynek segítségével kiértékelheti és megismerheti a Azure stack. A ASDK fejlesztői környezetként is felhasználhatja az alkalmazások az Azure-ban konzisztens API-k és eszközök használatával történő létrehozásához. A ASDK azonban nem használható éles környezetként, és a következő korlátozások vonatkoznak a teljes integrált rendszerek éles üzembe helyezéséhez képest:

    - A ASDK csak egyetlen Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) identitás-szolgáltatóhoz társítható.
    - Mivel a Azure Stack-összetevők egyetlen gazdagépen vannak telepítve, a bérlői erőforrások számára korlátozott fizikai erőforrások állnak rendelkezésre. Ez a konfiguráció nem méretezhető vagy a teljesítmény kiértékelésére szolgál.
    - A hálózati forgatókönyvek a gazdagép és a NIC központi telepítési követelményei miatt korlátozottak.

### <a name="connection-models"></a>Kapcsolatok modelljei
Dönthet úgy is, hogy Azure Stack az internethez (és az Azure-hoz) **csatlakozik** , vagy **leválasztja** azt. Ez a választási lehetőség határozza meg, hogy mely lehetőségek érhetők el az Identity Store-ban (Azure AD vagy AD FS) és a számlázási modellben (a használaton kívüli számlázás vagy a kapacitás alapú számlázás alapján).

> További információ: a [csatlakoztatott](azure-stack-connected-deployment.md) és a [leválasztott](azure-stack-disconnected-deployment.md) üzembe helyezési modellek szempontjai. 

### <a name="identity-provider"></a>Identitásszolgáltató 
A Azure Stack Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával adja meg az identitásokat. Az Azure AD a Microsoft felhőalapú, több-bérlős identitás-szolgáltatója. Az internethez csatlakoztatott központi telepítésekkel kapcsolatos legtöbb hibrid forgatókönyv az Azure AD-t használja identitás-tárolóként. 

Azure Stack leválasztott központi telepítéséhez Active Directory összevonási szolgáltatások (AD FS) (AD FS) kell használnia. Azure Stack erőforrás-szolgáltatók és más alkalmazások ugyanúgy működnek, mint a AD FS vagy az Azure AD. A Azure Stack a saját Active Directory példányát és egy Active Directory Graph API tartalmaz.

> [!IMPORTANT]
> Az identitás-szolgáltató nem módosítható a telepítés után. Más identitás-szolgáltató használatához újra kell telepítenie Azure Stack.

> Azure Stack identitással kapcsolatos szempontokat a [Azure stack identitásának áttekintése című](azure-stack-identity-overview.md)témakörben talál.

## <a name="how-is-azure-stack-managed"></a>Hogyan történik a Azure Stack kezelése?
A Azure Stack az adminisztrációs portál, a felhasználói portál vagy a [PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)segítségével kezelheti. A Azure Stack portálok mindegyike Azure Resource Manager különálló példányaival támogatott. Az **Azure stack operátorok** az adminisztrációs portálon kezelik a Azure stack, és olyan műveleteket hajtanak végre, mint a bérlői ajánlatok létrehozása, valamint az integrált rendszer állapotának és figyelésének állapota. A felhasználói portál (más néven a bérlői portál) önkiszolgáló élményt nyújt a Felhőbeli erőforrások, például a virtuális gépek, a tárolási fiókok és a webalkalmazások felhasználásához. 

> A Azure Stack felügyeleti portál használatával történő kezelésével kapcsolatos további információkért tekintse meg a [Azure stack felügyeleti portál](azure-stack-manage-portals.md)használatának első lépéseit.

Azure Stack kezelőként számos szolgáltatást és alkalmazást, például [virtuális gépeket](azure-stack-tutorial-tenant-vm.md), [webalkalmazásokat](azure-stack-app-service-overview.md), nagy rendelkezésre állású [SQL Server](azure-stack-tutorial-sql.md)és [MySQL kiszolgálói](azure-stack-tutorial-mysql.md) adatbázisokat biztosíthat. A SharePoint, az Exchange és más rendszereken is üzembe helyezhetők a Azure Stack rövid útmutató [Azure Resource Manager sablonjai](https://github.com/Azure/AzureStack-QuickStart-Templates) . 

A felügyeleti portálon konfigurálhatja a szolgáltatásokat a bérlők számára a csomagok, kvóták, ajánlatok és előfizetések használatával történő [kézbesítéshez Azure stack](service-plan-offer-subscription-overview.md) . A bérlői felhasználók több ajánlatra is fizethetnek. Az ajánlatok egy vagy több csomaggal rendelkezhetnek, a csomagok pedig egy vagy több szolgáltatással rendelkezhetnek. A kezelők is kezelhetik a kapacitást, és reagálnak a riasztásokra. 

Ha Azure Stack konfigurálva van, egy **Azure stack felhasználó** (más néven bérlő) az operátor által kínált szolgáltatásokat használja fel. A felhasználók üzembe helyezhetik, felügyelhetik és kezelhetik a előfizetett szolgáltatásokat, például a webalkalmazásokat, a tárolókat és a virtuális gépeket.

> Ha többet szeretne megtudni a Azure Stack kezeléséről, többek között azokról a fiókokról, amelyekben a jellemző operátori feladatok, a felhasználók tájékoztatása és a Súgó beszerzése, tekintse át a [Azure stack adminisztráció alapjai](azure-stack-manage-basics.md)című témakört.

## <a name="resource-providers"></a>Erőforrás-szolgáltatók 
Az erőforrás-szolgáltatók olyan webszolgáltatások, amelyek az összes Azure Stack IaaS és a Péter-szolgáltatás alapjait alkotják. Azure Resource Manager a különböző erőforrás-szolgáltatókra támaszkodik, hogy hozzáférést biztosítson a szolgáltatásokhoz. Minden erőforrás-szolgáltató segíti a megfelelő erőforrások konfigurálását és felügyeletét. A szolgáltatás-rendszergazdák hozzáadhatnak új egyéni erőforrás-szolgáltatókat is. 

### <a name="foundational-resource-providers"></a>Alapvető erőforrás-szolgáltatók 
Három alapvető IaaS erőforrás-szolgáltató létezik: 

- **Számítás**. A számítási erőforrás-szolgáltató lehetővé teszi, hogy Azure Stack bérlők saját virtuális gépeket hozzanak létre. A számítási erőforrás-szolgáltató a virtuális gépek és a virtuálisgép-bővítmények létrehozását is lehetővé teszi. A virtuálisgép-bővítmény szolgáltatás a Windows-és Linux-alapú virtuális gépek IaaS funkcióit segíti elő. A számítási erőforrás-szolgáltató segítségével például Linux rendszerű virtuális gépeket helyezhet üzembe, és a telepítés során bash-parancsfájlokat futtathat a virtuális gép konfigurálásához.
- **Hálózati erőforrás-szolgáltató**. A hálózati erőforrás-szolgáltató a szoftver által meghatározott hálózatkezelés (SDN) és a Network Function Virtualization (NFV) funkcióit biztosítja a privát felhőhöz. A hálózati erőforrás-szolgáltató használatával olyan erőforrásokat hozhat létre, mint például a szoftveres terheléselosztó, a nyilvános IP-címek, a hálózati biztonsági csoportok és a virtuális hálózatok.
- **Tárolási erőforrás-szolgáltató**. A Storage erőforrás-szolgáltató négy Azure-konzisztens tárolási szolgáltatást biztosít: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [üzenetsor](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tábla](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)és kulcstartó-fiókok kezelése a titkok kezeléséhez és naplózásához, például jelszavakhoz [és tanúsítványokhoz](https://docs.microsoft.com/azure/key-vault/) . A Storage erőforrás-szolgáltató egy tárolási Felhőbeli felügyeleti szolgáltatást is kínál, amely megkönnyíti az Azure-konzisztens tárolási szolgáltatások szolgáltatói felügyeletét. Az Azure Storage rugalmasságot biztosít a nagy mennyiségű strukturálatlan adat, például dokumentumok és médiafájlok tárolására és beolvasására az Azure-Blobokkal, valamint a strukturált NoSQL-alapú adattárolást az Azure Tables használatával. 

### <a name="optional-resource-providers"></a>Választható erőforrás-szolgáltatók
A Azure Stack a következők közül három opcionálisan telepíthető és használható: 

- **App Service**. [A Azure app Service on Azure stack](azure-stack-app-service-overview.md) a Microsoft Azure elérhető platform-szolgáltatás, amely Azure stack számára érhető el. A szolgáltatás lehetővé teszi, hogy a belső vagy külső ügyfelei webes, API-és Azure Functions alkalmazásokat hozzanak létre bármely platformra vagy eszközre. 
- **Egy SQL Server**. Az [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider.md) használatával Azure stack szolgáltatásként kínál SQL-adatbázisokat. Miután telepítette az erőforrás-szolgáltatót, és összekapcsolta egy vagy több SQL Server példánnyal, Ön és a felhasználók létrehozhatnak adatbázisokat a Felhőbeli natív alkalmazásokhoz, az SQL-t használó webhelyekhez és az SQL-t használó egyéb munkaterhelésekhez.
- **MySQL-kiszolgáló**. A [MySQL-kiszolgáló erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md) használatával Azure stack szolgáltatásként elérhetővé teheti a MySQL-adatbázisokat. A MySQL erőforrás-szolgáltató szolgáltatásként fut egy Windows Server 2016 Server Core virtuális gépen (VM).

## <a name="providing-high-availability"></a>Magas rendelkezésre állás biztosítása
Egy több virtuális gépre kiterjedő, Azure-beli üzemi rendszerek magas rendelkezésre állásának eléréséhez a virtuális gépeket egy [rendelkezésre állási csoportba](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) helyezi, amely több tartalék tartományon és frissítési tartományon keresztül terjed ki. A Azure Stack kisebb méretekben a rendelkezésre állási csoportokban lévő tartalék tartomány egyetlen csomópontként van definiálva a skálázási egységben.  

Habár a Azure Stack infrastruktúrája már rugalmas a hibákhoz, az alapul szolgáló technológia (feladatátvételi fürtszolgáltatás) továbbra is leállást okoz az érintett fizikai kiszolgálókon futó virtuális gépeknél, ha hardverhiba van. Azure Stack támogatja, hogy a rendelkezésre állási csoport legfeljebb három tartalék tartománnyal legyen konzisztens az Azure-ban.

- Tartalék **tartományok**. A rendelkezésre állási csoportba helyezett virtuális gépeket fizikailag el kell különíteni egymástól a több tartalék tartományon (Azure Stack csomóponton) a lehető legegyenletesebb módon. Ha hardverhiba van, a meghibásodott tartalék tartományból származó virtuális gépek más tartalék tartományokban is újraindulnak, de a többi virtuális gépről külön tartalék tartományban kell tartani, ha lehetséges. Ha a hardver online állapotba kerül, a virtuális gépek újra lesznek egyenlítve a magas rendelkezésre állás fenntartása érdekében. 
 
- **Frissítse a tartományokat**. A frissítési tartományok egy másik Azure-koncepció, amely magas rendelkezésre állást biztosít a rendelkezésre állási csoportokban. A frissítési tartomány a mögöttes hardver logikai csoportja, amely egyszerre végezhető el a karbantartásban. Az ugyanabban a frissítési tartományban található virtuális gépek a tervezett karbantartás során újraindulnak. Mivel a bérlők virtuális gépeket hoznak létre egy rendelkezésre állási csoporton belül, az Azure platform automatikusan elosztja a virtuális gépeket ezen frissítési tartományok között. Azure Stack a virtuális gépeket a fürt többi online gazdagépén át kell telepíteni a mögöttes gazdagép frissítése előtt. Mivel a gazdagép frissítése során nem áll rendelkezésre a bérlői állásidő, a Azure Stack frissítése funkció csak az Azure-hoz készült sablon-kompatibilitás esetén létezik. A rendelkezésre állási csoportba tartozó virtuális gépek a frissítési tartományuk alapján **0** értéket fognak látni a portálon. 

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével hozzáférést biztosíthat a jogosult felhasználókhoz, csoportokhoz és szolgáltatásokhoz, ha hozzárendeli a szerepköröket egy előfizetéshez, erőforráscsoporthoz vagy egyedi erőforrás-szinthez. Minden szerepkör egy felhasználó, csoport vagy szolgáltatás hozzáférési szintjét határozza meg Microsoft Azure Stack erőforrásokon keresztül.

Azure Stack a RBAC három alapvető szerepkört tartalmaz, amelyek minden erőforrástípus esetében érvényesek: tulajdonos, közreműködő és olvasó. A tulajdonos teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a másokhoz való hozzáférés delegálására vonatkozó jogosultságot is. A közreműködő az Azure-erőforrások összes típusát létrehozhatja és kezelheti, de mások számára nem biztosít hozzáférést. Az olvasó csak a meglévő erőforrásokat tudja megtekinteni. A többi RBAC-szerepkör lehetővé teszi bizonyos Azure-erőforrások kezelését. A virtuális gépi közreműködő szerepkör például lehetővé teszi a virtuális gépek létrehozását és felügyeletét, de nem engedélyezi a virtuális hálózat vagy azon alhálózat felügyeletét, amelyhez a virtuális gép csatlakozik.

> További információt a [szerepköralapú hozzáférés-vezérlés kezelése](azure-stack-manage-permissions.md) című témakörben talál. 

## <a name="reporting-usage-data"></a>Jelentési használati adatok
Azure Stack gyűjti és összesíti a használati adatokat az összes erőforrás-szolgáltatónál, és továbbítja azt az Azure-ba az Azure Commerce általi feldolgozáshoz. A Azure Stackban összegyűjtött használati adatok megtekinthetők egy REST API keresztül. Létezik egy Azure-konzisztens bérlői API, valamint a szolgáltató és a delegált szolgáltató API-k, amelyek a használati adatokat az összes bérlői előfizetésben lekérhetik. Ezek az információk a külső eszközzel vagy szolgáltatással való integrálásra használhatók számlázáshoz vagy jóváíráshoz. Miután az Azure Commerce feldolgozta a használatot, az Azure számlázási portálon tekinthető meg.

> További információ a [Azure stack használati adatok Azure-ba történő jelentéskészítéséről](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Következő lépések

[A Azure Stack és a globális Azure összehasonlítása](compare-azure-azure-stack.md)

[Adminisztráció alapjai](azure-stack-manage-basics.md)

[Rövid útmutató: a Azure Stack felügyeleti portál használata](azure-stack-manage-portals.md)