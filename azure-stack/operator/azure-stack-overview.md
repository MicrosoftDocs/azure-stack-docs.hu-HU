---
title: Azure Stack hub áttekintése
description: Áttekintés arról, hogy mi Azure Stack hub, és hogyan teszi lehetővé az Azure-szolgáltatások futtatását az adatközpontban.
author: justinha
ms.topic: overview
ms.date: 04/20/2020
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 11/08/2019
ms.openlocfilehash: dc5fa60f0ab1460a98ec21a0501c3cdcec6a6637
ms.sourcegitcommit: a3ae6dd8670f8fb24224880df7eee256ebbcc4ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772640"
---
# <a name="azure-stack-hub-overview"></a>Azure Stack hub áttekintése

Az Azure Stack hub az Azure egy bővítménye, amely lehetővé teszi az alkalmazások helyszíni környezetben való futtatását, valamint az adatközpontban elérhető Azure-szolgáltatások nyújtását. Az egységes felhőalapú platformmal a szervezetek az üzleti követelmények alapján, a technológiai korlátozások alapján nem üzleti döntéseket hozhatnak.

## <a name="why-use-azure-stack-hub"></a>Miért érdemes Azure Stack hubot használni?

Az Azure sokoldalú platformot biztosít a fejlesztők számára modern alkalmazások létrehozásához. Egyes felhőalapú alkalmazások azonban olyan akadályokba ütköznek, mint a késés, az időszakos kapcsolat és a szabályozás. Az Azure és a Azure Stack hub új hibrid felhőalapú használati eseteket is felhasználhat az ügyfelek és a belső üzletági alkalmazások esetében:

- **Edge és leválasztott megoldások**. A késéssel és a kapcsolattal kapcsolatos követelmények a Azure Stack hub-ban helyileg feldolgozott adatfeldolgozással, majd az Azure-ban való összesítésével további elemzések céljából, a közös alkalmazás-logikával együtt. Az internetről az Azure-hoz való csatlakozás nélkül is üzembe helyezheti az Azure Stack hubot. Példaként tekintse meg a gyári padlókat, a tengerjáró hajókat és a bánya-aknákat.

- **A különböző szabályozásoknak megfelelő felhőalapú alkalmazások**. Alkalmazások fejlesztése és üzembe helyezése az Azure-ban teljes körű rugalmassággal a helyszíni üzembe helyezéshez Azure Stack hub-mel a szabályozási és a szabályzati követelmények teljesítése érdekében. Nincs szükség kód módosítására. Az alkalmazás példái közé tartozik a globális audit, a pénzügyi jelentéskészítés, a deviza-kereskedelem, az online játékok és a költségek bejelentése.

- Helyszíni **Cloud app Model**. Az Azure-szolgáltatások, tárolók, kiszolgáló nélküli és a Service-architektúrák használatával frissítheti és bővítheti a meglévő alkalmazásokat, illetve újakat építhet ki. A felhőben az Azure-ban konzisztens DevOps-folyamatokat használhat, és Azure Stack a helyszínen, hogy felgyorsítsa az alkalmazások modernizációját a legfontosabb alapvető fontosságú alkalmazásokhoz.

## <a name="azure-stack-hub-architecture"></a>Azure Stack hub-architektúra

Az Azure Stack hub integrált rendszerek megbízható hardveres partnerek által készített, és közvetlenül az adatközpontba szállított 4-16-es kiszolgálók állványait alkotják. A kézbesítést követően a megoldás-szolgáltató együttműködve telepíti az integrált rendszerét, és gondoskodik arról, hogy az Azure Stack hub-megoldás megfeleljen az üzleti követelményeinek. Az adatközpont előkészíthető az összes szükséges energiaellátási és hűtési, határ-és egyéb szükséges adatközpont-integrációs követelmény biztosításával.

> További információ az Azure Stack hub Datacenter integrációs felületéről: [Azure stack hub Datacenter Integration](azure-stack-customer-journey.md).

Az Azure Stack hub az iparági szabványoknak megfelelő hardverre épül, és ugyanazokkal az eszközökkel felügyelhető, mint amelyeket az Azure-előfizetések kezeléséhez már használ. Ennek eredményeképpen konzisztens DevOps-folyamatokat alkalmazhat, függetlenül attól, hogy csatlakozik-e az Azure-hoz.

Az Azure Stack hub architektúrája lehetővé teszi, hogy az Azure-szolgáltatásokat a távoli helyekhez vagy időszakos kapcsolatokhoz, az internetről leválasztva biztosítson. Olyan hibrid megoldásokat hozhat létre, amelyek helyileg dolgozzák fel az adatokat Azure Stack hub-ban, majd az Azure-ban összesítve további feldolgozást és elemzést végeznek. Végezetül, mivel Azure Stack hub a helyszínen van telepítve, az egyes szabályozási vagy házirend-követelmények kielégítésével a felhőalapú alkalmazások helyszíni üzembe helyezése a kód módosítása nélkül is megváltoztatható.

## <a name="deployment-options"></a>Üzembe helyezési beállítások

A Azure Stack hub integrált rendszerek a Microsoft és a hardveres partnerek közötti partneri kapcsolaton keresztül érhetők el, így olyan megoldások hozhatók létre, amelyek a Felhőbeli innováció és a számítástechnika egyszerűségét biztosítják. Mivel az Azure Stack hub integrált hardver-és szoftveres rendszerként van felkínálva, rugalmasan és szabályozva van, valamint a felhőből való innováció lehetőségével.

Az Azure Stack hub integrált rendszerének mérete 4-16-kiszolgálókról, *méretezési egységnek*nevezzük. Az integrált rendszereket a hardveres partner és a Microsoft közösen támogatja. Az alábbi ábrán egy méretezési egység látható. 

![Azure Stack hub integrált rendszerét bemutató ábra](./media/azure-stack-overview/azure-stack-integrated-system.svg). 

<!---add info and image on regions, etc--->

### <a name="connection-models"></a>Kapcsolatok modelljei

Dönthet úgy is, hogy Azure Stack hubot az internethez (és az Azure-hoz) **csatlakozik** , vagy **leválasztja** azt. 

> További információ: a [csatlakoztatott](azure-stack-connected-deployment.md) és a [leválasztott](azure-stack-disconnected-deployment.md) üzembe helyezési modellek szempontjai.

### <a name="identity-provider"></a>Identitásszolgáltató 

Azure Stack hub Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) protokollt használ. Az Azure AD a Microsoft felhőalapú, több-bérlős identitás-szolgáltatója. Az internethez csatlakoztatott központi telepítésekkel kapcsolatos legtöbb hibrid forgatókönyv az Azure AD-t használja identitás-tárolóként.

Azure Stack hub leválasztott központi telepítéséhez AD FS kell használnia. Azure Stack hub erőforrás-szolgáltató és más alkalmazások ugyanúgy működnek, mint AD FS vagy az Azure AD. Azure Stack hub a saját Active Directory példányát és egy Active Directory Graph API tartalmaz.


## <a name="how-is-azure-stack-hub-managed"></a>Hogyan történik a Azure Stack hub kezelése?

Azure Stack hub ugyanazt az operatív modellt használja, mint az Azure. Egy Azure Stack hub-operátor számos szolgáltatást és alkalmazást biztosíthat a bérlői felhasználók számára, hasonlóan ahhoz, ahogyan a Microsoft Azure-szolgáltatásokat nyújt a bérlői felhasználók számára. 

![Azure Stack hub-feladatok szerepköreit bemutató diagram](./media/azure-stack-overview/azure-stack-job-roles.png)

Azure Stack hub a felügyeleti portál, a felhasználói portál vagy a [PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)segítségével kezelhető. Az Azure Stack hub-portálok mindegyike Azure Resource Manager különálló példányaival támogatott. Az **Azure stack hub-operátor** a felügyeleti portál használatával felügyeli az Azure stack hub-t, és olyan műveleteket hajthat végre, mint például a bérlői ajánlatok létrehozása és az integrált rendszer állapotának és figyelésének állapota. A felhasználói portál önkiszolgáló élményt biztosít a felhőalapú erőforrások, például a virtuális gépek, a Storage-fiókok és a webalkalmazások felhasználásához.

> Az Azure Stack hub felügyeleti portál használatával történő kezelésével kapcsolatos további információkért tekintse meg a [Azure stack hub felügyeleti portáljának](azure-stack-manage-portals.md)használatának első lépéseit.

Azure Stack hub-kezelőként a [virtuális gépeket](azure-stack-tutorial-tenant-vm.md), [webalkalmazásokat](azure-stack-app-service-overview.md), valamint a rendelkezésre álló [SQL Server](azure-stack-tutorial-sql.md)és a [MySQL-kiszolgáló](azure-stack-tutorial-mysql.md) adatbázisait is továbbíthatja. A SharePoint, az Exchange és más rendszereken a Azure Stack hub gyors üzembe helyezési [Azure Resource Manager sablonjaival](https://github.com/Azure/AzureStack-QuickStart-Templates) is elvégezheti a telepítést.

Az operátorok a [felügyeleti portál](azure-stack-manage-portals.md) vagy a [PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)használatával felügyelhetik Azure stack hubot. Azure Stack hub-t úgy konfigurálhatja, hogy a szolgáltatásokat a bérlők számára a csomagok, kvóták, ajánlatok és előfizetések használatával [kézbesítse](service-plan-offer-subscription-overview.md) . A bérlői felhasználók több ajánlatra is fizethetnek. Az ajánlatok egy vagy több csomaggal rendelkezhetnek, a csomagok pedig egy vagy több szolgáltatással rendelkezhetnek. A kezelők is kezelhetik a kapacitást, és reagálnak a riasztásokra.

A felhasználók a kezelő által kínált szolgáltatásokat használják. A felhasználók üzembe helyezhetik, felügyelhetik és kezelhetik a előfizetett szolgáltatásokat, például a webalkalmazásokat, a tárolókat és a virtuális gépeket. A felhasználók a felhasználói portál vagy a PowerShell használatával felügyelhetik Azure Stack hubot.

> Ha többet szeretne megtudni az Azure Stack hub kezelésével kapcsolatban, beleértve azokat a fiókokat, amelyek esetében a jellemző operátori feladatok, a felhasználók tájékoztatása és a Súgó beszerzése, tekintse át a [Azure stack hub adminisztrációs alapjai](azure-stack-manage-basics.md)című témakört.

## <a name="resource-providers"></a>Erőforrás-szolgáltatók

Az erőforrás-szolgáltatók olyan webszolgáltatások, amelyek az összes Azure Stack hub-IaaS és a Péter-szolgáltatás alapjait alkotják. Azure Resource Manager a különböző erőforrás-szolgáltatókra támaszkodik, hogy hozzáférést biztosítson a szolgáltatásokhoz. Minden erőforrás-szolgáltató segíti a megfelelő erőforrások konfigurálását és felügyeletét. A szolgáltatás-rendszergazdák hozzáadhatnak új egyéni erőforrás-szolgáltatókat is.

### <a name="foundational-resource-providers"></a>Alapvető erőforrás-szolgáltatók

Három alapvető IaaS erőforrás-szolgáltató létezik:

- **Számítás**: a számítási erőforrás-szolgáltató lehetővé teszi, hogy Azure stack hub-bérlők saját virtuális gépeket hozzanak létre. A számítási erőforrás-szolgáltató a virtuális gépek és a virtuálisgép-bővítmények létrehozását is lehetővé teszi. A virtuálisgép-bővítmény szolgáltatás a Windows-és Linux-alapú virtuális gépek IaaS funkcióit segíti elő. A számítási erőforrás-szolgáltató segítségével például Linux rendszerű virtuális gépet építhet ki, és a telepítés során bash-parancsfájlokat futtathat a virtuális gép konfigurálásához.
- **Hálózati erőforrás-szolgáltató**: a hálózati erőforrás-szolgáltató számos szoftver által meghatározott hálózatkezelési (Sdn) és hálózati Function VIRTUALIZATION (NFV) funkciót biztosít a privát felhőhöz. A hálózati erőforrás-szolgáltató használatával olyan erőforrásokat hozhat létre, mint például a szoftveres terheléselosztó, a nyilvános IP-címek, a hálózati biztonsági csoportok és a virtuális hálózatok.
- **Tárolási erőforrás-szolgáltató**: a tárolási erőforrás-szolgáltató négy Azure-konzisztens tárolási szolgáltatást biztosít: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [üzenetsor](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tábla](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)és [Key Vault](https://docs.microsoft.com/azure/key-vault/) Fiókkezelés a titkok felügyeletét és naplózását, például jelszavakat és tanúsítványokat. A Storage erőforrás-szolgáltató egy tárolási Felhőbeli felügyeleti szolgáltatást is kínál, amely megkönnyíti az Azure-konzisztens tárolási szolgáltatások szolgáltatói felügyeletét. Az Azure Storage rugalmasságot biztosít a nagy mennyiségű strukturálatlan adat tárolásához és lekéréséhez, például dokumentumok és médiafájlok Azure-Blobokkal való ellátásához, valamint strukturált NoSQL-alapú adattároláshoz az Azure Tables használatával.

### <a name="optional-resource-providers"></a>Választható erőforrás-szolgáltatók

A Azure Stack hub szolgáltatásban három opcionális, a (z) rendszerbe telepíthető és használható.

- **App Service**: [a Azure stack hub](azure-stack-app-service-overview.md) -beli Azure app Service a Azure Stack hub számára elérhető Microsoft Azure. A szolgáltatás lehetővé teszi, hogy a belső vagy külső ügyfelei webes, API-és Azure Functions alkalmazásokat hozzanak létre bármely platformra vagy eszközre.
- **SQL Server**: a [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider.md) használatával Azure stack hub szolgáltatásként kínál SQL-adatbázisokat. Miután telepítette az erőforrás-szolgáltatót, és összekapcsolta egy vagy több SQL Server példánnyal, Ön és a felhasználók létrehozhatnak adatbázisokat a Felhőbeli natív alkalmazásokhoz, az SQL-t használó webhelyekhez és az SQL-t használó egyéb munkaterhelésekhez.
- **MySQL-kiszolgáló**: a MySQL- [kiszolgáló erőforrás-szolgáltatójának](azure-stack-mysql-resource-provider-deploy.md) használatával tegye elérhetővé a mysql-adatbázisokat Azure stack hub-szolgáltatásként. A MySQL erőforrás-szolgáltató szolgáltatásként fut egy Windows Server 2019 Server Core virtuális gépen.

## <a name="next-steps"></a>További lépések

[Az Azure Stack hub-portfólió összehasonlítása](compare-azure-azure-stack.md)

[Az adminisztráció alapjai](azure-stack-manage-basics.md)

[Gyors útmutató: az Azure Stack hub felügyeleti portáljának használata](azure-stack-manage-portals.md)

[A használat és a számlázás megismerése](azure-stack-usage-reporting.md).
