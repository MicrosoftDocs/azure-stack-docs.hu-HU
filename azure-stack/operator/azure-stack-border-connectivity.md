---
title: Szegély kapcsolat hálózati integráció kapcsolatos szempontok az Azure Stack integrált rendszerek |} A Microsoft Docs
description: Ismerje meg, mi mindent adatközpont szegély hálózati kapcsolatot az Azure Stack több csomópontos tervezése.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: ce04d458817362c79f2fb990702ab4eee399653f
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985954"
---
# <a name="border-connectivity"></a>Szegély kapcsolat 
Hálózati integráció megtervezése egy fontos előfeltétel sikeres Azure Stackkel integrált rendszerek üzembe helyezés, a művelet és felügyeletéhez. Szegély kapcsolat tervezési kezdődik-e a border gateway protocol (BGP) dinamikus útválasztást használnak a kiválasztásával. A 16 bites BGP autonóm rendszer száma, (nyilvános vagy magán) hozzárendelése ehhez, vagy használja a statikus útválasztást, ahol az alapértelmezett statikus útvonal hozzá van rendelve a border eszközökhöz.

> [!IMPORTANT]
> Felső részén rack (TOR) kapcsoló szükséges a pont-pont típusú IP-címek a 3. rétegbeli kimenő kapcsolatok (/ 30 hálózatok) a fizikai adapteren konfigurált. 2. rétegbeli kimenő portok használata az Azure Stack műveleteket TOR-kapcsolók nem támogatott. 

## <a name="bgp-routing"></a>A BGP-Útválasztás
Egy dinamikus útválasztási protokoll, mint a BGP használata garantálja, hogy a rendszer mindig ismeri a hálózati változásokat, és megkönnyíti a felügyeletet. A fokozott biztonság érdekében jelszó alapján lehet beállítani a BGP-t a TOR és szegélye közötti társviszony-létesítés. 

Az alábbi ábrán látható, a privát IP-cím hirdetési a TOR-kapcsoló a hely le van tiltva előtag-lista használatával. Az előtagok listáját a hirdetés a privát hálózat megtagadja, és a TOR és szegélye közötti kapcsolat az útvonal-térkép alkalmazásának.

A szoftveres terheléselosztó (SLB) az Azure Stack megoldás belül futó a tor-alapú eszközökre is társul, így dinamikusan meghirdethet, hogy a VIP-címek.

Annak érdekében, hogy azonnal, és transzparens módon történt hiba után helyreállítja a felhasználói adatforgalmat, a VPC vagy konfigurálni a tor-alapú eszközök közötti MLAG lehetővé teszi, hogy több váz hivatkozás használata a gazdagépek és a HSRP vagy biztosító VRRP összesítési hálózati redundancia az IP-hálózatok.

![A BGP-Útválasztás](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Statikus útválasztás
Statikus útválasztás, a peremátjáró eszközök további konfigurálására van szükség. További manuális beavatkozásra van szükség, azt, és a felügyeleti, valamint minden olyan változás- és konfigurációs hiba okozta problémák előtt alapos elemzés előfordulhat, hogy több időt vesz igénybe, attól függően, a módosítások visszaállítása. Nem az ajánlott útválasztási módszer, de támogatja azt.

A statikus útválasztás használatával hálózati környezetébe integrálhatja az Azure Stacket, szegélye és a tor-alapú eszközök közötti összes négy fizikai hivatkozás csatlakoztatva kell lennie, és magas rendelkezésre állás nem garantálható, hogyan statikus útválasztási működése miatt.

A szegély eszköz kell konfigurálni a tor-alapú eszközök P2P mutató, a felé irányuló statikus útvonalak a *külső* hálózati vagy a nyilvános virtuális IP-címek és a *infrastruktúra* hálózati. Ehhez szükség lesz a statikus útvonalakat a *BMC* és a *külső* hálózatok központi telepítésére vonatkozóan. Operátorok lehet váltani, hagyja a statikus útvonalakat a szegély, amelyek megtalálhatók a felügyeleti erőforrások eléréséhez a *BMC* hálózati. A statikus útvonalak hozzáadása *kapcsoló-infrastruktúra* és *felügyeleti kapcsoló* hálózatok nem kötelező.

A tor-alapú eszközök az összes adatforgalmat küldő szegély eszközök alapértelmezett statikus útvonal konfigurált származnak. Az alapértelmezett szabályhoz forgalom kivételt jelent a privát területet, amely le van tiltva, egy hozzáférés-vezérlési lista, a TOR szegély kapcsolat a alkalmazni a.

Statikus útválasztás csak azokra a kimenő kapcsolatok a TOR- és szegélytulajdonságok kapcsolók között. Dinamikus útválasztás a BGP használata az állványra szerelt belül, a fontos eszközzel a szoftveres Terheléselosztó és más összetevőket, és nem letiltása vagy eltávolítása.

![Statikus útválasztás](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> Üzembe helyezés után a BMC-hálózat nem kötelező.

<sup>\*\*</sup> A kapcsoló-infrastruktúra-hálózat megadása nem kötelező, a teljes hálózati kapcsolók kezelése hálózatban részeként.

<sup>\*\*\*</sup> A kapcsoló felügyeleti hálózat megadása kötelező, és a hálózati kapcsoló-infrastruktúra külön adhatók hozzá.

## <a name="transparent-proxy"></a>Transzparens proxy
Ha az Adatközpont igényel minden forgalom a proxy használatát, konfigurálnia kell egy *transzparens proxy* kezelni, a hálózaton a zónák közötti forgalom szétválasztása házirend szerint az állványra szerelt érkező minden feldolgozásához.

> [!IMPORTANT]
> Az Azure Stack megoldás nem támogatja a normál webes proxykat.  

Transzparens proxy (más néven egy elfogja, beágyazott vagy kényszerített proxy) elfogja a normál kommunikáció, a hálózati rétegen anélkül, hogy minden olyan speciális ügyfél-konfigurációt. Az ügyfelek nem létezik-e a proxy tisztában lennie kell.

![Transzparens proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>További lépések
[DNS-integráció](azure-stack-integrate-dns.md)
