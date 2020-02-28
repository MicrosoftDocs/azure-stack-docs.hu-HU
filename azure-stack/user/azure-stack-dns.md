---
title: DNS Azure Stack központban
description: Ismerkedjen meg a DNS-sel Azure Stack központban, valamint hogyan hozhat létre és kezelhet DNS-zónákat.
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 01/05/2020
ms.openlocfilehash: b9afba6322b80d487f4ba7d4324adcd42a8fd85a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703009"
---
# <a name="use-dns-in-azure-stack-hub"></a>DNS használata Azure Stack központban

Azure Stack hub a következő Azure DNS funkciókat támogatja:

* DNS-állomásnév feloldása.
* DNS-zónák és-rekordok létrehozása és kezelése az API használatával.

## <a name="support-for-dns-hostname-resolution"></a>DNS-állomásnév feloldásának támogatása

Megadhatja a nyilvános IP-erőforrásokhoz tartozó DNS-tartománynevek címkéjét. Azure Stack hub a **domainnamelabel értékkel. location. cloudapp. azurestack. external** nevet használja a címke nevéhez, és leképezi azt a Azure stack hub által FELÜGYELt DNS-KISZOLGÁLÓK nyilvános IP-címére.

Ha például egy nyilvános IP-erőforrást hoz létre a **contoso** névvel a helyi Azure stack központ helyének tartományneve címkéjével, a [teljes tartománynév (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) **contoso. local. cloudapp. azurestack. external** az erőforrás nyilvános IP-címére kerül feloldásra. Ezzel a teljes tartománynévvel létrehozhat egy egyéni tartományi CNAME rekordot, amely a Azure Stack hub nyilvános IP-címére mutat.

A névfeloldással kapcsolatos további tudnivalókért tekintse meg a [DNS-megoldással](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) foglalkozó cikket.

> [!IMPORTANT]
> Minden létrehozott tartománynév-címkének egyedinek kell lennie az Azure Stack hub-helyen belül.

A következő képernyőfelvétel a nyilvános IP **-cím létrehozása** párbeszédpanelt mutatja be a nyilvános IP-cím létrehozásához a portál használatával:

![Nyilvános IP-cím létrehozása](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Példaforgatókönyv

Van egy webalkalmazásból származó terheléselosztó feldolgozási kérelme. A terheléselosztó mögött egy webhely fut egy vagy több virtuális gépen. IP-cím helyett DNS-név használatával érheti el a terheléselosztási webhelyet.

## <a name="create-and-manage-dns-zones-and-records-using-the-apis"></a>DNS-zónák és-rekordok létrehozása és kezelése az API-k használatával

A DNS-zónák és-rekordok a Azure Stack hub-ban hozhatók létre és kezelhetők.

Az Azure Stack hub az Azure-hoz hasonló DNS-szolgáltatást biztosít, amely a Azure DNS API-kkal konzisztens API-kat használ.  Ha a tartományait Azure Stack hub DNS-ben üzemelteti, a DNS-rekordokat ugyanazzal a hitelesítő adatokkal, API-kkal és eszközökkel kezelheti. Ugyanezt a számlázási és támogatási szolgáltatásokat is használhatja a többi Azure-szolgáltatáshoz.

Az Azure Stack hub DNS-infrastruktúrája sokkal kisebb, mint az Azure. Egy Azure Stack hub központi telepítésének mérete és helye befolyásolja a DNS hatókörét, méretezését és teljesítményét. Ez azt is jelenti, hogy a teljesítmény, a rendelkezésre állás, a globális terjesztés és a magas rendelkezésre állás eltérő lehet az üzembe helyezéstől az üzembe helyezésig.

## <a name="comparison-with-azure-dns"></a>Összehasonlítás a Azure DNS

A Azure Stack hub DNS-je hasonló a DNS-hez az Azure-ban, de van néhány fontos kivétel:

* **Nem támogatja az AAAA-rekordokat**: Azure stack hub nem támogatja az AAAA-rekordokat, mert az Azure stack hub nem támogatja az IPv6-címeket. Ez az Azure-beli DNS-ben és Azure Stack hub-ban kulcsfontosságú különbség.

* **Nem több-bérlő**: a Azure stack hub DNS szolgáltatása nem több-bérlős. A bérlők nem tudják létrehozni ugyanazt a DNS-zónát. Csak az első előfizetés, amely a zóna létrehozását kísérli meg, és a későbbi kérelmek sikertelenek lesznek. Ez az Azure és a Azure Stack hub DNS közötti különbség.

* **Címkék, metaadatok és etagek**: kisebb különbségek vannak abban, hogy Azure stack hub hogyan kezelje a címkéket, a metaadatokat, a etagek és a korlátokat.

További információ a Azure DNSről: [DNS-zónák és-rekordok](/azure/dns/dns-zones-records).

### <a name="tags"></a>Címkék

Azure Stack hub DNS támogatja Azure Resource Manager címkék használatát a DNS-zónák erőforrásain. Nem támogatja a címkéket a DNS-rekordhalmazokon. Alternatív megoldásként a **metaadatokat** a DNS-rekordhalmazok támogatják, a következő szakaszban leírtak szerint.

### <a name="metadata"></a>Metaadatok

A címkék rekordhalmazának alternatívájaként a Azure Stack hub DNS a *metaadatok*használatával támogatja a rekordok megjegyzéseit. A címkékhez hasonlóan a metaadatok lehetővé teszik a név-érték párok hozzárendelését az egyes bejegyzéstípusokhoz. A metaadatok például az egyes bejegyzéstípusok céljának rögzítéséhez hasznosak. A címkéktől eltérően a metaadatok nem használhatók az Azure-számla szűrt nézetének megadásához, és a metaadatok nem adhatók meg Azure Resource Manager házirendben.

### <a name="etags"></a>Etagek

Tegyük fel, hogy két személy vagy két folyamat egyszerre próbálkozik a DNS-rekordok módosításával. Melyik nyer? És a nyertes is tudja, hogy felülírták-e valaki más által létrehozott módosításokat?

Azure Stack hub DNS a *etagek* -t használja az egyazon erőforrás egyidejű változásainak biztonságos kezelésére. A etagek különböznek Azure Resource Manager *címkéktől*. Minden DNS-erőforráshoz (zónához vagy rekordhoz) társítva van egy ETAG. Erőforrás lekérése esetén a rendszer a ETAG is beolvassa. Amikor frissít egy erőforrást, dönthet úgy, hogy visszaadja a ETAG, így Azure Stack hub DNS ellenőrizheti a ETAG a kiszolgálói egyezéseken. Mivel az erőforrás minden frissítése újra létrehozza a ETAG, egy ETAG eltérés azt jelzi, hogy egyidejű változás történt. A etagek akkor is használható, ha új erőforrást hoz létre annak biztosítására, hogy az erőforrás még nem létezik.

Alapértelmezés szerint a Azure Stack hub DNS PowerShell-parancsmagjai a Etagek használatával blokkolják a zónák és a rekordhalmazok egyidejű módosításait. Az opcionális `-Overwrite` kapcsolóval tilthatja le a ETAG-ellenőrzéseket. A ETAG-ellenőrzések nélkül az egyidejű változások felülírva vannak.

Az Azure Stack hub DNS-REST API szintjén a Etagek HTTP-fejlécek használatával vannak megadva. A viselkedést az alábbi táblázat ismerteti:

| Fejléc | Viselkedés|
|--------|---------|
| Nincs   | A mindig sikeres (nincs ETAG-ellenőrzés).|
| If-Match| A csak akkor lesz sikeres, ha az erőforrás létezik, és a ETAG egyezik.|
| If-Match *| Az üzembe helyezés csak akkor sikeres, ha az erőforrás létezik.|
| If-None-Match *| A csak akkor lesz sikeres, ha az erőforrás nem létezik.|

### <a name="limits"></a>Korlátok

Azure Stack hub DNS használatakor a következő alapértelmezett korlátozások érvényesek:

| Erőforrás| Alapértelmezett korlát|
|---------|--------------|
| Zónák/előfizetés| 100|
| Rekordhalmazok száma zónában| 5000|
| Rekord/rekordhalmaz| 20|

## <a name="next-steps"></a>Következő lépések

* [Az Azure Stack hub iDNS bemutatása](azure-stack-understanding-dns.md)
