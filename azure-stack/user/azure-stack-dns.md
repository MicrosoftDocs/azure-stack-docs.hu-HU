---
title: DNS az Azure Stackben |} A Microsoft Docs
description: DNS-sel az Azure Stackben
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
ms.openlocfilehash: 278b010cd1883043549217d657e1315ea697a303
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691963"
---
# <a name="using-dns-in-azure-stack"></a>DNS-sel az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack a következő funkciók az Azure DNS támogatja:

* DNS-állomásnév feloldása
* DNS-zónák és -rekordjait az API létrehozása és kezelése

## <a name="support-for-dns-hostname-resolution"></a>DNS-állomásnév feloldása támogatása

You can specify a DNS domain name label for public IP resources. Használja az Azure Stack **domainnamelabel.location.cloudapp.azurestack.external** a címke nevét vagy a térképek, hogy a nyilvános IP-cím az Azure Stack által felügyelt DNS-kiszolgálók.

Például, ha létrehoz egy nyilvános IP-erőforráshoz az **contoso** egy tartománynévcímkét a helyi Azure Stack-helyen, mint a [teljesen minősített tartománynevét (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)  **contoso.local.cloudapp.azurestack.external** feloldja az erőforrás nyilvános IP-címét. Ez a teljes tartománynév használatával hozzon létre egy CNAME rekordot a nyilvános IP-címre az Azure Stackben, egyéni tartományt.

A névfeloldással kapcsolatos további tudnivalókért tekintse meg a [DNS-feloldás](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) cikk.

> [!IMPORTANT]
> Minden egyes tartománynév címkéje hoz létre az Azure Stack helyen belül egyedinek kell lennie.

Az alábbi képernyőfelvételen a **nyilvános IP-cím létrehozása** egy nyilvános IP-címet, a portál használatával létrehozására szolgáló párbeszédpanel:

![Create public IP address](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Példaforgatókönyv

Rendelkezik egy terheléselosztó-webalkalmazások kérelmek feldolgozásához. A terhelés mögött terheléselosztó egy vagy több virtual machines szolgáltatásban futó webhelyek. Az elosztott terhelésű webhely IP-cím helyett egy DNS-név használatával érheti el.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>DNS-zónák és -rekordjait az API létrehozása és kezelése

Hozzon létre, és kezelheti a DNS-zónák és -rekordok az Azure Stackben.

Az Azure Stack egy hasonló Azure-bA DNS szolgáltatást kínál, API-k konzisztensek legyenek az Azure DNS API-k használatával.  A tartományok Azure Stack DNS-ben üzemelteti, DNS-rekordjait a azonos hitelesítő adatokkal, API-k és eszközök használatával is kezelheti. Használja ugyanazt a számlázás és támogatják, mint a többi Azure-szolgáltatásokat is.

Az Azure Stack DNS-infrastruktúra képes legyen tömörebb, mint az Azure. A méret- és az Azure Stack központi hatással van a DNS hatókör, méretezést és teljesítményt. Ez azt is jelenti, hogy teljesítmény, a rendelkezésre állási, a globális terjesztés és a magas rendelkezésre állású telepítésről a másikra változhat.

## <a name="comparison-with-azure-dns"></a>Az Azure DNS összehasonlítása

Az Azure DNS DNS az Azure Stackben hasonlít, de néhány fontos kivételek:

* **Nem támogatja a AAAA típusú rekord**: Az Azure Stack nepodporuje AAAA típusú rekord, mert az Azure Stack nem támogatja az IPv6-címek. Ez a fő különbség a DNS az Azure-ban és az Azure Stackben.

* **Nem több-bérlős**: A DNS-szolgáltatás, az Azure Stackben ne legyen több-bérlős. Mindegyik bérlő ugyanazt a DNS-zóna nem hozható létre. Csak az első előfizetés, amely megpróbálja a zóna létrehozása sikeres volt, és a későbbi kérelmeket. Ez az Azure és az Azure Stack DNS egy másik fő különbsége.

* **A címkék, metaadatokat és ETag**: Hogyan kezeli az Azure Stack a címkéket, metaadatok, ETag és korlátokat kisebb különbségek vannak.

Azure DNS szolgáltatással kapcsolatos további tudnivalókért lásd: [DNS-zónák és rekordok](/azure/dns/dns-zones-records).

### <a name="tags"></a>Tags

Az Azure Stack DNS támogatja az Azure Resource Manager-címkék használatát a DNS-zóna erőforrás. Nem támogatja a címkék a DNS-rekordhalmazok, bár lehetőségként **metaadatok** a DNS-rekordhalmazok, támogatott a következő szakaszban leírtak szerint.

### <a name="metadata"></a>Metaadatok

Rekordhalmaz címkékre alternatív megoldásként az Azure Stack DNS támogatja a jegyzetkészítés rekordhalmazok használatával *metaadatok*. Címkék hasonlóan, metaadatok lehetővé teszi minden rekordhalmaz név-érték párok társítani. Például ez lehet hasznos, jegyezze fel minden rekordhalmaz célját. Ellentétben a címkék és metaadatok használatával nem adja meg az Azure-elszámolások szűrt nézetét, és egy Azure Resource Manager-szabályzatban nem adható meg a metaadatok.

### <a name="etags"></a>ETag

Tegyük fel, hogy a két személynek vagy két folyamatok próbálja meg módosítani a DNS-rekord egy időben. Melyik wins? És a győztes nem tudja, hogy azok már módosításait mások által létrehozott felülírja?

Használja az Azure Stack DNS *ETag* biztonságosan kezelje az egyidejű módosításokat ugyanarra az erőforrásra. ETag nem ugyanaz az Azure Resource Manager *címkék*. Minden DNS-erőforrás (zóna vagy rekordhalmaz) egy hozzá társított ETag címkével rendelkezik. Ha egy erőforrás lekérdezése van, az ETag címkéje is lekéri. Egy erőforrás frissítésekor kiválaszthatja adja át vissza az ETag címke, az Azure Stack DNS-ben is ellenőrizze, hogy az Etag a kiszolgáló megfelel. Minden frissítés egy erőforráshoz újragenerálása folyamatban van az Etag eredményez, mivel az Etag nem egyezik azt jelzi, egyidejű változás történt. ETag is segítségével új erőforrás létrehozásakor győződjön meg arról, hogy az erőforrás még nem létezik.

Alapértelmezés szerint az Azure Stack DNS PowerShell-parancsmagok használatával ETag egyidejű letiltása a zónák és -rekordhalmazok. Használhatja a választható `-Overwrite` váltson le Etag-ellenőrzéseket, emiatt felülírásának történt egyidejű módosításokat.

Az Azure Stack DNS REST API a szintjén ETag megadott HTTP-fejlécek használata. Az alábbi táblázatban ismertetett viselkedésük:

| Fejléc | Viselkedés|
|--------|---------|
| Egyik sem   | PUT mindig sikeres (nincs Etag-ellenőrzések)|
| IF-match| PUT csak akkor sikeres, ha az erőforrás létezik, és Etagje megegyezik|
| IF-match *| PUT csak akkor sikeres, ha az erőforrás létezik|
| If-none-match *| PUT csak akkor sikeres, ha az erőforrás nem létezik.|

### <a name="limits"></a>Limits

Az alábbi alapértelmezett korlátok vonatkoznak az Azure Stack DNS használata esetén:

| Resource| Alapértelmezett korlát|
|---------|--------------|
| Zónák előfizetésenként| 100|
| Zónánként rekordhalmazok| 5000|
| Rekordok száma rekordhalmaz| 20|

## <a name="next-steps"></a>További lépések

* [Bemutató: iDNS az Azure Stackhez](azure-stack-understanding-dns.md)
