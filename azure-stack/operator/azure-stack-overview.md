---
title: Azure Stack áttekintése | Microsoft Docs
description: Annak áttekintése, hogy milyen Azure Stack és hogyan teszi lehetővé az Azure-szolgáltatások futtatását az adatközpontban.
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
ms.openlocfilehash: cd6cfeca83fc94c1a25ef446b43bdee50689acab
ms.sourcegitcommit: acebda8a42ac8ecdeba490fc1738e9041479dab0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72814019"
---
# <a name="azure-stack-overview"></a>Az Azure Stack áttekintése

Azure Stack az Azure egy bővítménye, amely lehetővé teszi az alkalmazások helyszíni környezetben való futtatását és az Azure-szolgáltatások az adatközpontban való továbbítását. Az egységes felhőalapú platformmal a szervezetek az üzleti követelmények alapján, a technológiai korlátozások alapján nem üzleti döntéseket hozhatnak.

## <a name="why-use-azure-stack"></a>Miért érdemes Azure Stack?

Az Azure sokoldalú platformot biztosít a fejlesztők számára modern alkalmazások létrehozásához. Egyes felhőalapú alkalmazások azonban olyan akadályokba ütköznek, mint a késés, az időszakos kapcsolat és a szabályozás. Az Azure és a Azure Stack új hibrid Felhőbeli használati eseteket is felhasználhat az ügyfelek és a belső üzletági alkalmazások számára:

- **Edge és leválasztott megoldások**. A késéssel és a kapcsolattal kapcsolatos követelmények kezelése az adatfeldolgozással helyileg Azure Stack, majd az Azure-ban összesítve további elemzésekhez, és a közös alkalmazás-logikával együtt. Az internetről az Azure-hoz való csatlakozás nélkül is üzembe helyezhető Azure Stack. Példaként tekintse meg a gyári padlókat, a tengerjáró hajókat és a bánya-aknákat.

- **A különböző szabályozásoknak megfelelő felhőalapú alkalmazások**. Alkalmazások fejlesztése és üzembe helyezése az Azure-ban teljes rugalmassággal a helyszíni üzembe helyezéshez Azure Stack a szabályozási vagy a szabályzati követelmények teljesítése érdekében. Nincs szükség kód módosítására. Az alkalmazás példái közé tartozik a globális audit, a pénzügyi jelentéskészítés, a deviza-kereskedelem, az online játékok és a költségek bejelentése.

- Helyszíni **Cloud app Model**. Az Azure-szolgáltatások, tárolók, kiszolgáló nélküli és a Service-architektúrák használatával frissítheti és bővítheti a meglévő alkalmazásokat, illetve újakat építhet ki. A felhőben az Azure-ban konzisztens DevOps folyamatokat használhat, és Azure Stack a helyszínen, hogy felgyorsítsa az alkalmazások modernizációját a legfontosabb alapvető fontosságú alkalmazásokhoz.

A Azure Stack a következő lehetőségeket biztosítja a használati forgatókönyvek számára:

- Integrált kézbesítési élmény, amellyel gyorsan üzembe helyezhetők a megbízható hardveres partnerektől származó, szándékosan kialakított Azure Stack integrált rendszerek. A kézbesítést követően a Azure Stack könnyedén integrálható az adatközpontba az System Center Operations Manager felügyeleti csomag vagy a Nagios-bővítmény használatával.

- Rugalmas Identitáskezelés Azure Active Directory (Azure AD) használatával az Azure-hoz és a Azure Stack hibrid környezetekhez, valamint a Active Directory összevonási szolgáltatások (AD FS) (AD FS) kihasználása a leválasztott központi telepítésekhez.

- Egy Azure-konzisztens alkalmazás-fejlesztési környezet, amely maximalizálja a fejlesztői hatékonyságot, és lehetővé teszi a közös DevOps megközelítését a hibrid környezetek között.

- A helyszíni Azure-szolgáltatások hibrid felhőalapú számítási teljesítmény használatával. Az Azure-ban és a Azure Stackban az Azure-IaaS (infrastruktúra-szolgáltatás) és a Péter (platform-szolgáltatásként nyújtott platform) szolgáltatások üzembe helyezésére és üzemeltetésére szolgáló általános működési eljárások bevezetése az Azure-ban. A Microsoft folyamatos Azure-innovációt biztosít a Azure Stackhoz, beleértve az új Azure-szolgáltatásokat, a meglévő szolgáltatások frissítéseit és további Azure Marketplace-alkalmazásokat és-lemezképeket.

## <a name="azure-stack-architecture"></a>Azure Stack architektúra

Azure Stack integrált rendszerek megbízható hardveres partnerek által készített, és közvetlenül az adatközpontba szállított 4-16-es kiszolgálók állványait alkotják. A kiszállítást követően a megoldás-szolgáltató együttműködve telepíti az integrált rendszerét, és gondoskodik arról, hogy a Azure Stack megoldás megfeleljen az üzleti követelményeknek. Készítse elő az adatközpontot úgy, hogy az összes szükséges energiaellátási és hűtési, határ-és egyéb szükséges adatközpont-integrációs követelményt biztosítja.

> További információ az Azure Stack Datacenter integrációs felületéről: [Azure stack Datacenter Integration](azure-stack-customer-journey.md).

Az Azure Stack az iparági szabványoknak megfelelő hardverre épül, és ugyanazokkal az eszközökkel felügyelhető, mint amelyeket az Azure-előfizetések kezeléséhez már használ. Ennek eredményeképpen konzisztens DevOps-folyamatokat alkalmazhat, függetlenül attól, hogy csatlakozik-e az Azure-hoz.

A Azure Stack architektúra lehetővé teszi, hogy az Azure-szolgáltatásokat a távoli helyekhez vagy időszakos kapcsolatokhoz, az internetről leválasztva biztosítson. Létrehozhat olyan hibrid megoldásokat, amelyek az adatokat helyileg dolgozzák fel Azure Stack, majd az Azure-ban összesítve további feldolgozást és elemzést végeznek. Végezetül, mivel Azure Stack a helyszínen van telepítve, a különböző szabályozási vagy házirend-követelményekkel is találkozhat a felhőalapú alkalmazások helyszíni üzembe helyezésének rugalmasságával a kód módosítása nélkül.

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

### <a name="production-or-evaluation-environments"></a>Üzemi vagy kiértékelési környezetek

Azure Stack két üzembe helyezési lehetőség közül választhat, amelyek megfelelnek az igényeinek, Azure Stack a termelési célú integrált rendszerek és a Azure Stack Development Kit (ASDK) számára a Azure Stack értékeléséhez:

- **Azure stack integrált rendszerek**: Azure stack integrált rendszerek a Microsoft és a hardveres partnerek partneri kapcsolatán keresztül érhetők el, és olyan megoldást hoznak létre, amely a Felhőbeli innovációt és a számítástechnikai felügyelet egyszerűségét kínálja. Mivel a Azure Stack integrált hardver-és szoftveres rendszerként érhető el, rugalmassága és szabályozása szükséges, valamint a felhőből való innováció képessége is. Azure Stack az integrált rendszerek 4-16 csomópontokból állnak, és a hardveres partner és a Microsoft közösen támogatja őket. Azure Stack integrált rendszerek használatával új forgatókönyveket hozhat létre, és új megoldásokat telepíthet az éles számítási feladatokhoz.

- **Azure stack Development Kit (ASDK)** : [a ASDK](../asdk/asdk-what-is.md) egy olyan Azure stack ingyenes, egycsomópontos üzembe helyezése, amelynek segítségével kiértékelheti és megismerheti a Azure stack. A ASDK fejlesztői környezetként is felhasználhatja az alkalmazások az Azure-ban konzisztens API-k és eszközök használatával történő létrehozásához. A ASDK azonban nem használható éles környezetként, és a következő korlátozások vonatkoznak a teljes integrált rendszerek éles üzembe helyezéséhez képest:

    - A ASDK csak egyetlen Azure AD-vagy AD FS-identitáshoz társítható.
    - Mivel a Azure Stack-összetevők egyetlen gazdagépen vannak telepítve, a bérlői erőforrások számára korlátozott fizikai erőforrások állnak rendelkezésre. Ez a konfiguráció nem méretezhető vagy a teljesítmény kiértékelésére szolgál.
    - A hálózati forgatókönyvek a gazdagép és a NIC központi telepítési követelményei miatt korlátozottak.

### <a name="connection-models"></a>Kapcsolatok modelljei

Dönthet úgy is, hogy Azure Stack az internethez (és az Azure-hoz) **csatlakozik** , vagy **leválasztja** azt. Ez a választási lehetőség határozza meg, hogy mely lehetőségek érhetők el az Identity Store-ban (Azure AD vagy AD FS) és a számlázási modellben (a használaton kívüli számlázás vagy a kapacitás alapú számlázás alapján).

> További információ: a [csatlakoztatott](azure-stack-connected-deployment.md) és a [leválasztott](azure-stack-disconnected-deployment.md) üzembe helyezési modellek szempontjai.

### <a name="identity-provider"></a>Identitásszolgáltató 

Azure Stack az Azure AD-t vagy az aktív AD FS használja az identitások biztosításához. Az Azure AD a Microsoft felhőalapú, több-bérlős identitás-szolgáltatója. Az internethez csatlakoztatott központi telepítésekkel kapcsolatos legtöbb hibrid forgatókönyv az Azure AD-t használja identitás-tárolóként.

Azure Stack leválasztott központi telepítéséhez AD FS kell használnia. Azure Stack erőforrás-szolgáltatók és más alkalmazások ugyanúgy működnek, mint a AD FS vagy az Azure AD. A Azure Stack a saját Active Directory példányát és egy Active Directory Graph API tartalmaz.

> [!IMPORTANT]
> Az identitás-szolgáltató nem módosítható a telepítés után. Más identitás-szolgáltató használatához újra kell telepítenie Azure Stack. Azure Stack identitással kapcsolatos szempontokat a [Azure stack identitásának áttekintése című](azure-stack-identity-overview.md)témakörben talál.

## <a name="how-is-azure-stack-managed"></a>Hogyan történik a Azure Stack kezelése?

A Azure Stack a felügyeleti portál, a felhasználói portál vagy a [PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)használatával kezelheti. A Azure Stack portálok mindegyike Azure Resource Manager különálló példányaival támogatott. Az **Azure stack operátor** a felügyeleti portál használatával kezeli a Azure stack, és olyan műveleteket hajt végre, mint például a bérlői ajánlatok létrehozása és az integrált rendszer állapotának és figyelésének állapota. A felhasználói portál önkiszolgáló élményt biztosít a felhőalapú erőforrások, például a virtuális gépek, a Storage-fiókok és a webalkalmazások felhasználásához.

> A Azure Stack felügyeleti portál használatával történő kezelésével kapcsolatos további információkért tekintse meg a következő témakört: a [Azure stack felügyeleti portál](azure-stack-manage-portals.md)gyors üzembe helyezése.

Azure Stack operátorként számos szolgáltatást és alkalmazást, például [virtuális gépeket](azure-stack-tutorial-tenant-vm.md), [webalkalmazásokat](azure-stack-app-service-overview.md), nagy rendelkezésre állású [SQL Server](azure-stack-tutorial-sql.md)és [MySQL kiszolgálói](azure-stack-tutorial-mysql.md) adatbázisokat biztosíthat. A SharePoint, az Exchange és más rendszereken is üzembe helyezhetők a Azure Stack rövid útmutató [Azure Resource Manager sablonjai](https://github.com/Azure/AzureStack-QuickStart-Templates) .

A felügyeleti portál használatával úgy [konfigurálhatja a Azure stack, hogy a szolgáltatásokat](service-plan-offer-subscription-overview.md) a bérlők számára a csomagok, kvóták, ajánlatok és előfizetések használatával kézbesítse. A bérlői felhasználók több ajánlatra is fizethetnek. Az ajánlatok egy vagy több csomaggal rendelkezhetnek, a csomagok pedig egy vagy több szolgáltatással rendelkezhetnek. A kezelők is kezelhetik a kapacitást, és reagálnak a riasztásokra.

Ha Azure Stack konfigurálva van, egy **Azure stack felhasználó** a kezelő által kínált szolgáltatásokat használja fel. A felhasználók üzembe helyezhetik, felügyelhetik és kezelhetik a előfizetett szolgáltatásokat, például a webalkalmazásokat, a tárolókat és a virtuális gépeket.

> Ha többet szeretne megtudni a Azure Stack kezeléséről, többek között azokról a fiókokról, amelyekben a jellemző operátori feladatok, a felhasználók tájékoztatása és a Súgó beszerzése, tekintse át a [Azure stack adminisztráció alapjai](azure-stack-manage-basics.md)című témakört.

## <a name="resource-providers"></a>Erőforrás-szolgáltatók

Az erőforrás-szolgáltatók olyan webszolgáltatások, amelyek az összes Azure Stack IaaS és a Péter-szolgáltatás alapjait alkotják. Azure Resource Manager a különböző erőforrás-szolgáltatókra támaszkodik, hogy hozzáférést biztosítson a szolgáltatásokhoz. Minden erőforrás-szolgáltató segíti a megfelelő erőforrások konfigurálását és felügyeletét. A szolgáltatás-rendszergazdák hozzáadhatnak új egyéni erőforrás-szolgáltatókat is.

### <a name="foundational-resource-providers"></a>Alapvető erőforrás-szolgáltatók

Három alapvető IaaS erőforrás-szolgáltató létezik:

- **Számítás**: a számítási erőforrás-szolgáltató lehetővé teszi, hogy Azure stack bérlők saját virtuális gépeket hozzanak létre. A számítási erőforrás-szolgáltató a virtuális gépek és a virtuálisgép-bővítmények létrehozását is lehetővé teszi. A virtuálisgép-bővítmény szolgáltatás a Windows-és Linux-alapú virtuális gépek IaaS funkcióit segíti elő. A számítási erőforrás-szolgáltató segítségével például Linux rendszerű virtuális gépet építhet ki, és a telepítés során bash-parancsfájlokat futtathat a virtuális gép konfigurálásához.
- **Hálózati erőforrás-szolgáltató**: a hálózati erőforrás-szolgáltató számos szoftver által meghatározott hálózatkezelési (Sdn) és hálózati Function VIRTUALIZATION (NFV) funkciót biztosít a privát felhőhöz. A hálózati erőforrás-szolgáltató használatával olyan erőforrásokat hozhat létre, mint például a szoftveres terheléselosztó, a nyilvános IP-címek, a hálózati biztonsági csoportok és a virtuális hálózatok.
- **Tárolási erőforrás-szolgáltató**: a tárolási erőforrás-szolgáltató négy Azure-konzisztens tárolási szolgáltatást biztosít: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [üzenetsor](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tábla](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)és [Key Vault](https://docs.microsoft.com/azure/key-vault/) Fiókkezelés a titkok felügyeletét és naplózását biztosítva. például jelszavakat és tanúsítványokat. A Storage erőforrás-szolgáltató egy tárolási Felhőbeli felügyeleti szolgáltatást is kínál, amely megkönnyíti az Azure-konzisztens tárolási szolgáltatások szolgáltatói felügyeletét. Az Azure Storage rugalmasságot biztosít a nagy mennyiségű strukturálatlan adat tárolásához és lekéréséhez, például dokumentumok és médiafájlok Azure-Blobokkal való ellátásához, valamint strukturált NoSQL-alapú adattároláshoz az Azure Tables használatával.

### <a name="optional-resource-providers"></a>Választható erőforrás-szolgáltatók

A Azure Stack a következők közül három opcionálisan telepíthető és használható:

- **App Service**: a (z) [Azure Stack Azure app Service a](azure-stack-app-service-overview.md) Microsoft Azure által Azure stack számára elérhető. A szolgáltatás lehetővé teszi, hogy a belső vagy külső ügyfelei webes, API-és Azure Functions alkalmazásokat hozzanak létre bármely platformra vagy eszközre.
- **SQL Server**: használja a [SQL Server erőforrás-szolgáltatót](azure-stack-sql-resource-provider.md) , hogy az SQL-adatbázisokat Azure stack-szolgáltatásként kínálja. Miután telepítette az erőforrás-szolgáltatót, és összekapcsolta egy vagy több SQL Server példánnyal, Ön és a felhasználók létrehozhatnak adatbázisokat a Felhőbeli natív alkalmazásokhoz, az SQL-t használó webhelyekhez és az SQL-t használó egyéb munkaterhelésekhez.
- **MySQL-kiszolgáló**: a MySQL- [kiszolgáló erőforrás-szolgáltatójának](azure-stack-mysql-resource-provider-deploy.md) használatával Azure stack szolgáltatásként elérhetővé teheti a MySQL-adatbázisokat. A MySQL erőforrás-szolgáltató szolgáltatásként fut egy Windows Server 2016 Server Core virtuális gépen.

## <a name="providing-high-availability"></a>Magas rendelkezésre állás biztosítása

Egy több virtuális gépre kiterjedő, Azure-beli üzemi rendszerek magas rendelkezésre állásának eléréséhez a virtuális gépeket egy [rendelkezésre állási csoportba](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) helyezi, amely több tartalék tartományon és frissítési tartományon keresztül terjed ki. A Azure Stack kisebb méretekben a rendelkezésre állási csoportokban lévő tartalék tartomány egyetlen csomópontként van definiálva a skálázási egységben.  

Habár a Azure Stack infrastruktúrája már rugalmas a hibákhoz, az alapul szolgáló technológia (feladatátvételi fürtszolgáltatás) továbbra is leállást okoz az érintett fizikai kiszolgálókon futó virtuális gépeknél, ha van hardverhiba. Azure Stack támogatja, hogy a rendelkezésre állási csoport legfeljebb három tartalék tartománnyal legyen konzisztens az Azure-ban.

- Tartalék **tartományok**: a rendelkezésre állási csoportba helyezett virtuális gépeket fizikailag el kell különíteni egymástól a több tartalék tartományon (Azure stack csomóponton) a lehető legegyenletesebb módon. Hardverhiba esetén a sikertelen tartalék tartományba tartozó virtuális gépek más tartalék tartományokban lesznek újraindítva. A többi virtuális gépről külön tartalék tartományokban lesznek tárolva, de ha lehetséges, ugyanabban a rendelkezésre állási készletben. Ha a hardver online állapotba kerül, a virtuális gépek újra lesznek egyenlítve a magas rendelkezésre állás fenntartása érdekében.

- **Frissítési tartományok**: a frissítési tartományok egy másik Azure-koncepció, amely magas rendelkezésre állást biztosít a rendelkezésre állási csoportokban. A frissítési tartomány a mögöttes hardver logikai csoportja, amely egyszerre végezhető el a karbantartásban. Az ugyanabban a frissítési tartományban található virtuális gépek a tervezett karbantartás során újraindulnak. Mivel a bérlők virtuális gépeket hoznak létre egy rendelkezésre állási csoporton belül, az Azure platform automatikusan elosztja a virtuális gépeket ezen frissítési tartományok között. Azure Stack a virtuális gépeket a fürt többi online gazdagépén át kell telepíteni a mögöttes gazdagép frissítése előtt. Mivel a gazdagép frissítése során nincs szükség bérlői állásidőre, a Azure Stack frissítése funkció csak az Azure-hoz készült sablon-kompatibilitás esetén létezik. A rendelkezésre állási csoportba tartozó virtuális gépek a frissítési tartományuk alapján **0** értéket fognak látni a portálon.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével hozzáférést biztosíthat a jogosult felhasználókhoz, csoportokhoz és szolgáltatásokhoz, ha hozzárendeli a szerepköröket egy előfizetéshez, erőforráscsoporthoz vagy egyedi erőforrás-szinthez. Minden szerepkör egy felhasználó, csoport vagy szolgáltatás hozzáférési szintjét határozza meg Microsoft Azure Stack erőforrásokon keresztül.

Azure Stack a RBAC három alapvető szerepkört tartalmaz, amelyek minden erőforrástípus esetében érvényesek: tulajdonos, közreműködő és olvasó. A tulajdonos teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a másokhoz való hozzáférés delegálására vonatkozó jogosultságot is. A közreműködő az Azure-erőforrások összes típusát létrehozhatja és kezelheti, de mások számára nem biztosít hozzáférést. Az olvasó csak a meglévő erőforrásokat tudja megtekinteni. A többi RBAC-szerepkör lehetővé teszi bizonyos Azure-erőforrások kezelését. A virtuális gépi közreműködő szerepkör például lehetővé teszi a virtuális gépek létrehozását és felügyeletét, de nem engedélyezi a virtuális hálózat vagy az alhálózat azon alhálózatának felügyeletét, amelyhez a virtuális gép csatlakozik.

> További információ: [szerepköralapú hozzáférés-vezérlés kezelése](azure-stack-manage-permissions.md).

## <a name="reporting-usage-data"></a>Jelentési használati adatok

Azure Stack gyűjti és összesíti a használati adatokat az összes erőforrás-szolgáltatónál, és továbbítja azt az Azure-ba az Azure Commerce általi feldolgozáshoz. A Azure Stackban összegyűjtött használati adatok megtekinthetők egy REST API keresztül. Létezik egy Azure-konzisztens bérlői API, valamint a szolgáltató és a delegált szolgáltató API-k, amelyek a használati adatokat az összes bérlői előfizetésben lekérhetik. Ezek az információk a külső eszközzel vagy szolgáltatással való integrálásra használhatók számlázáshoz vagy jóváíráshoz. Miután az Azure Commerce feldolgozta a használatot, az Azure számlázási portálon tekinthető meg.

> További információ a [Azure stack használati adatok Azure-ba történő jelentéskészítéséről](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Következő lépések

[Hasonlítsa össze a Azure stack és a globális Azure-](compare-azure-azure-stack.md)t.

Az [Adminisztráció alapjai](azure-stack-manage-basics.md).

Rövid útmutató [: a Azure stack felügyeleti portál használata](azure-stack-manage-portals.md).