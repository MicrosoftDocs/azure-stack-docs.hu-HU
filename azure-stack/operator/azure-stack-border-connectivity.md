---
title: A Border connectivity és a hálózati integráció Azure Stack hub integrált rendszerek esetében
description: Ismerje meg, hogyan tervezheti meg az adatközpontok szegélyének hálózati kapcsolatát Azure Stack hub integrált rendszerekben.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: a1faf66aa6bd3195ceece035b4c67234673359bf
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366219"
---
# <a name="border-connectivity"></a>Szegély kapcsolat 
A hálózati integráció megtervezése a sikeres Azure Stack hub integrált rendszerek üzembe helyezésének, működtetésének és felügyeletének fontos előfeltétele. A határok közötti kapcsolat megtervezése megkezdődik, ha a dinamikus útválasztást a Border Gateway Protocol (BGP) használatával szeretné használni. Ehhez hozzá kell rendelni egy 16 bites BGP autonóm rendszerszámot (nyilvános vagy privát) vagy statikus útválasztást, ahol a rendszer statikus alapértelmezett útvonalat rendel hozzá a szegélyhez tartozó eszközökhöz.

> [!IMPORTANT]
> A (z) Top of rack (TOR) kapcsolókhoz 3. rétegbeli, pont – pont típusú IP-címek (/30 hálózat) szükségesek, amelyek a fizikai interfészeken vannak konfigurálva. A 2. rétegbeli, Azure Stack hub-műveleteket támogató TOR-kapcsolók nem támogatottak.

## <a name="bgp-routing"></a>BGP-Útválasztás
Egy dinamikus útválasztási protokoll, például a BGP garantálja, hogy a rendszer mindig tisztában van a hálózati változásokkal, és megkönnyíti a felügyeletet. A fokozott biztonság érdekében a a TOR és a szegély közötti BGP-társra vonatkozó jelszót lehet beállítani.

Az alábbi ábrán látható módon a TOR-kapcsolón lévő magánhálózati IP-terület hirdetése le van tiltva egy előtag-lista használatával. Az előtag lista megtagadja a magánhálózat hirdetményét, és a TOR és a szegély közötti kapcsolaton útvonal-térképként alkalmazza.

A szoftver Load Balancer (SLB), amely az Azure Stack hub-megoldási partnereken belül fut a TOR-eszközökön, így dinamikusan képes a VIP-címek reklámozására.

Annak biztosítása érdekében, hogy a felhasználói forgalom azonnal és transzparens módon helyreállítható legyen a meghibásodástól, a TOR-eszközök között konfigurált VPC vagy MLAG lehetővé teszi a többplatformos kapcsolatok összesítésének használatát a gazdagépeken és a HSRP, illetve a VRRP, amelyek hálózati redundanciát biztosítanak az IP-hálózatokhoz.

![BGP-Útválasztás](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Statikus útválasztás
A statikus útválasztáshoz további konfiguráció szükséges a szegély eszközeihez. További manuális beavatkozásra és felügyeletre, valamint a változás előtt alapos elemzésre van szükség. A konfigurációs hiba által okozott problémák több időt vehetnek igénybe a módosításoktól függően. Ez az útválasztási módszer nem ajánlott, de támogatott.

Az Azure Stack hub statikus útválasztást használó hálózati környezetbe való integrálásához a szegély és a TOR-eszköz közötti négy fizikai kapcsolatnak csatlakoztatva kell lennie. A statikus útválasztás működése miatt nem garantálható a magas rendelkezésre állás.

A szegélyt tartalmazó eszközt statikus útvonalakkal kell konfigurálni, amelyek a TOR és az Azure Stack hub-on belüli hálózatra irányuló forgalom határa, de csak a *külső* vagy a nyilvános VIP-hálózat szükséges a működéshez. Az első telepítéshez statikus útvonalak szükségesek a *bmc* -hez és a *külső* hálózatokhoz. A kezelők dönthetnek úgy, hogy statikus útvonalakat hagynak a szegélyben a *bmc* -ben és az *infrastruktúra* -hálózaton található felügyeleti erőforrások eléréséhez. Nem kötelező statikus útvonalakat hozzáadni az infrastruktúra és a *switch felügyeleti* hálózatok *váltásához* .

A TOR-eszközök statikus alapértelmezett útvonalon vannak konfigurálva, és a teljes adatforgalmat a szegély eszközeire küldik. Az alapértelmezett szabály alóli egyetlen forgalmi kivétel a privát területre vonatkozik, amely le van tiltva a TOR-ra vonatkozó Access Control lista használatával.

A statikus útválasztás csak a TOR és a Border kapcsolók közötti kapcsolatokra vonatkozik. A BGP dinamikus útválasztás az állványon belül van használatban, mivel ez nélkülözhetetlen eszköz a SLB és más összetevők számára, és nem tiltható le és nem távolítható el.

![Statikus útválasztás](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> Az üzembe helyezés után a BMC-hálózat nem kötelező.

<sup>\*\*</sup> A kapcsoló-infrastruktúra hálózata nem kötelező, mivel a teljes hálózat belefoglalható a váltási felügyeleti hálózatba.

<sup>\*\*\*</sup> A kapcsoló-felügyeleti hálózatot kötelező megadni, és a kapcsoló infrastruktúra hálózata külön is felvehető.

## <a name="transparent-proxy"></a>Transzparens proxy
Ha az adatközpontban az összes forgalom proxy használatára van szüksége, egy *transzparens proxyt* kell konfigurálnia az állványról a szabályzatnak megfelelően kezelendő összes forgalom feldolgozásához, a hálózati zónák közötti forgalom elkülönítésével.

> [!IMPORTANT]
> Az Azure Stack hub-megoldás nem támogatja a normál webproxykat.  

Egy transzparens proxy (más néven lehallgatás, beágyazott vagy kényszerített proxy) elfogja a normál kommunikációt a hálózati rétegben anélkül, hogy speciális ügyfél-konfigurációra lenne szükség. Az ügyfeleknek nem kell megismerniük a proxy létezését.

![Transzparens proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

Az SSL-forgalom elfogása [nem támogatott](azure-stack-firewall.md#ssl-interception) , és a végpontok elérésekor a szolgáltatás meghibásodásához vezethet. Az identitáshoz szükséges végpontokkal folytatott kommunikáció maximális támogatott időtúllépése 60 – 3 újrapróbálkozási kísérlet.

## <a name="next-steps"></a>Következő lépések
[DNS-integráció](azure-stack-integrate-dns.md)
