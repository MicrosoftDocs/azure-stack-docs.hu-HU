---
title: DNS a Azure Stackban | Microsoft Docs
description: Ismerkedjen meg a DNS-sel Azure Stack és a DNS-zónák létrehozásával és kezelésével.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 8bfe15ad19e4aaec45492aa98cfb2ef02294742a
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658478"
---
# <a name="use-dns-in-azure-stack"></a>DNS használata Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack a következő Azure DNS funkciókat támogatja:

* DNS-állomásnév feloldása.
* DNS-zónák és-rekordok létrehozása és kezelése az API használatával.

## <a name="support-for-dns-hostname-resolution"></a>DNS-állomásnév feloldásának támogatása

Megadhatja a nyilvános IP-erőforrásokhoz tartozó DNS-tartománynevek címkéjét. Azure Stack a **domainnamelabel értékkel. location. cloudapp. azurestack. external** nevet használja a címke nevéhez, és leképezi azt a Azure stack által FELÜGYELt DNS-KISZOLGÁLÓK nyilvános IP-címére.

Ha például egy nyilvános IP-erőforrást hoz létre a **contoso** tartománynév-címkével a helyi Azure stack helyen, a **contoso. local. cloudapp. azurestack. external** [nevű teljes tartománynevet (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) a nyilvános IP-címhez. az erőforrás címe. Ezzel a teljes tartománynévvel létrehozhat egy egyéni tartományi CNAME rekordot, amely a Azure Stack nyilvános IP-címére mutat.

A névfeloldással kapcsolatos további tudnivalókért tekintse meg a [DNS-megoldással](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) foglalkozó cikket.

> [!IMPORTANT]
> Minden létrehozott tartománynév-címkének egyedinek kell lennie a Azure Stack helyen belül.

A következő képernyőfelvétel a nyilvános IP **-cím létrehozása** párbeszédpanelt mutatja be a nyilvános IP-cím létrehozásához a portál használatával:

![Nyilvános IP-cím létrehozása](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Példaforgatókönyv

Van egy webalkalmazásból származó terheléselosztó feldolgozási kérelme. A terheléselosztó mögött egy webhely fut egy vagy több virtuális gépen. IP-cím helyett DNS-név használatával érheti el a terheléselosztási webhelyet.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>DNS-zónák és-rekordok létrehozása és kezelése az API használatával

A Azure Stack DNS-zónákat és-rekordokat hozhat létre és kezelhet.

A Azure Stack az Azure-hoz hasonló DNS-szolgáltatást biztosít a Azure DNS API-kkal konzisztens API-k használatával.  Ha a tartományait Azure Stack DNS-ben üzemelteti, a DNS-rekordokat ugyanazzal a hitelesítő adatokkal, API-kkal és eszközökkel kezelheti. Ugyanezt a számlázási és támogatási szolgáltatásokat is használhatja a többi Azure-szolgáltatáshoz.

Az Azure Stack DNS-infrastruktúra sokkal rugalmasabb, mint az Azure. Egy Azure Stack üzemelő példány mérete és helye befolyásolja a DNS hatókörét, méretezését és teljesítményét. Ez azt is jelenti, hogy a teljesítmény, a rendelkezésre állás, a globális terjesztés és a magas rendelkezésre állás eltérő lehet az üzembe helyezéstől az üzembe helyezésig.

## <a name="comparison-with-azure-dns"></a>Összehasonlítás a Azure DNS

A Azure Stack DNS-ben hasonló a DNS az Azure-ban, de van néhány fontos kivétel:

* **Nem támogatja az AAAA**-rekordokat: A Azure Stack nem támogatja az AAAA-rekordokat, mert Azure Stack nem támogatja az IPv6-címeket. Ez az Azure-beli DNS-ben és a Azure Stackban megjelenő fő különbség.

* **Nem több-bérlős**: A Azure Stack DNS szolgáltatása nem több-bérlős. A bérlők nem tudják létrehozni ugyanazt a DNS-zónát. Csak az első előfizetés, amely a zóna létrehozását kísérli meg, és a későbbi kérelmek sikertelenek lesznek. Ez az Azure és a Azure Stack DNS között egy másik fő különbség.

* **Címkék, metaadatok és etagek**: A Azure Stack a címkék, a metaadatok, a Etagek és a korlátok kezelésével kapcsolatban kisebb különbségek vannak.

További információ a Azure DNSről: [DNS-zónák és-rekordok](/azure/dns/dns-zones-records).

### <a name="tags"></a>Tags

Azure Stack DNS támogatja Azure Resource Manager címkék használatát a DNS-zónák erőforrásain. Nem támogatja a címkéket a DNS-rekordhalmazokon. Alternatív megoldásként a **metaadatokat** a DNS-rekordhalmazok támogatják, a következő szakaszban leírtak szerint.

### <a name="metadata"></a>Metaadatok

A címkék rekordhalmazának alternatívájaként a Azure Stack DNS a *metaadatok*használatával támogatja a rekordok megjegyzéseit. A címkékhez hasonlóan a metaadatok lehetővé teszik a név-érték párok hozzárendelését az egyes bejegyzéstípusokhoz. A metaadatok például az egyes bejegyzéstípusok céljának rögzítéséhez hasznosak. A címkéktől eltérően a metaadatok nem használhatók az Azure-számla szűrt nézetének megadásához, és a metaadatok nem adhatók meg Azure Resource Manager házirendben.

### <a name="etags"></a>Etagek

Tegyük fel, hogy két személy vagy két folyamat egyszerre próbálkozik a DNS-rekordok módosításával. Melyik nyer? És a nyertes is tudja, hogy felülírták-e valaki más által létrehozott módosításokat?

Azure Stack a DNS a *etagek* -t használja az egyazon erőforrás egyidejű változásainak biztonságos kezelésére. A etagek különböznek Azure Resource Manager *címkéktől*. Minden DNS-erőforráshoz (zónához vagy rekordhoz) társítva van egy ETAG. Erőforrás lekérése esetén a rendszer a ETAG is beolvassa. Amikor frissít egy erőforrást, dönthet úgy, hogy visszaadja a ETAG, így Azure Stack DNS ellenőrizheti a ETAG a kiszolgálói egyezéseken. Mivel az erőforrás minden frissítése újra létrehozza a ETAG, egy ETAG eltérés azt jelzi, hogy egyidejű változás történt. A etagek akkor is használható, ha új erőforrást hoz létre annak biztosítására, hogy az erőforrás még nem létezik.

Alapértelmezés szerint a Azure Stack DNS PowerShell-parancsmagok a Etagek használatával blokkolják a zónák és a rekordhalmazok egyidejű módosításait. A választható `-Overwrite` kapcsoló használatával elkerülheti a ETAG-ellenőrzéseket. A ETAG-ellenőrzések nélkül az egyidejű változások felülírva vannak.

A Azure Stack DNS-REST API szintjén a Etagek HTTP-fejlécek használatával vannak megadva. A viselkedést az alábbi táblázat ismerteti:

| Fejléc | Viselkedés|
|--------|---------|
| None   | A mindig sikeres (nincs ETAG-ellenőrzés).|
| If-Match| A csak akkor lesz sikeres, ha az erőforrás létezik, és a ETAG egyezik.|
| If-Match *| Az üzembe helyezés csak akkor sikeres, ha az erőforrás létezik.|
| If-None-Match *| A csak akkor lesz sikeres, ha az erőforrás nem létezik.|

### <a name="limits"></a>Korlátok

Azure Stack DNS használatakor a következő alapértelmezett korlátok érvényesek:

| Resource| Alapértelmezett korlát|
|---------|--------------|
| Zónák/előfizetés| 100|
| Rekordhalmazok száma zónában| 5000|
| Rekord/rekordhalmaz| 20|

## <a name="next-steps"></a>További lépések

* [Bemutató: iDNS az Azure Stackhez](azure-stack-understanding-dns.md)
