---
title: Azure Stack hálózati különbségek | Microsoft Docs
description: A hálózatkezeléssel kapcsolatos különbségek és megfontolások a Azure Stackban.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 10/10/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 09e75656fc6a00181ffb31087e19e80b92760ed2
ms.sourcegitcommit: b72729305234e13c65de3771cb08678d46ba1348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72543674"
---
# <a name="differences-and-considerations-for-azure-stack-networking"></a>Különbségek és szempontok Azure Stack hálózatkezeléshez

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack hálózatkezelés az Azure-hálózatkezelés számos szolgáltatásával rendelkezik. Van azonban néhány fontos különbség, amelyet érdemes megismernie a Azure Stack hálózat telepítése előtt.

Ez a cikk áttekintést nyújt a Azure Stack hálózatkezeléssel és szolgáltatásaival kapcsolatos egyedi szempontokról. A Azure Stack és az Azure közötti magas szintű különbségek megismeréséhez tekintse meg a [legfontosabb szempontokat](azure-stack-considerations.md) ismertető cikket.

## <a name="cheat-sheet-networking-differences"></a>Cheat Sheet: hálózati különbségek

| Szolgáltatás | Szolgáltatás | Azure (globális) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Több-bérlős DNS | Támogatott | Még nem támogatott |
|  | DNS AAAA-rekordok | Támogatott | Nem támogatott |
|  | DNS-zónák/előfizetés | 100 (alapértelmezett)<br>Igény szerint növelhető. | 100 |
|  | DNS-rekordhalmazok száma zónában | 5000 (alapértelmezett)<br>Igény szerint növelhető. | 5000 |
|  | Névkiszolgálók a zónák delegálásához | Az Azure négy névszervert biztosít minden létrehozott felhasználói (bérlői) zónához. | Azure Stack két névszervert biztosít minden létrehozott felhasználói (bérlői) zónához. |
| Azure Firewall | Hálózati biztonsági szolgáltatás | Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. | Még nem támogatott. |
| Virtual Network (Virtuális hálózat) | Virtuális hálózatok közötti társviszony létesítése | Két virtuális hálózat összekötése ugyanabban a régióban az Azure gerinc hálózatán keresztül. | Még nem támogatott |
|  | IPv6-címek | A [hálózati adapter konfigurációjának](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)részeként egy IPv6-cím is hozzárendelhető. | Kizárólag az IPv4 használata támogatott. |
|  | DDoS Protection terv | Támogatott | Még nem támogatott. |
|  | Méretezési csoport IP-konfigurációi | Támogatott | Még nem támogatott. |
|  | Magánhálózati hozzáférési szolgáltatások (alhálózat) | Támogatott | Még nem támogatott. |
|  | Service Endpoints – szolgáltatásvégpont | Az Azure-szolgáltatásokhoz való belső (nem internetes) kapcsolódás esetén támogatott. | Még nem támogatott. |
|  | Szolgáltatásvégpont-szabályzatok | Támogatott | Még nem támogatott. |
|  | Szolgáltatási alagutak | Támogatott | Még nem támogatott.  |
| Hálózati biztonsági csoportok | Kibővített biztonsági szabályok | Támogatott | Még nem támogatott. |
|  | Érvényes biztonsági szabályok | Támogatott | Még nem támogatott. |
|  | Alkalmazásbiztonsági csoportok | Támogatott | Még nem támogatott. |
| Virtuális hálózati átjárók | Pont – hely VPN Gateway | Támogatott | Még nem támogatott. |
|  | Vnet – vnet átjáró | Támogatott | Még nem támogatott. |
|  | Virtual Network átjáró típusa | Az Azure támogatja a VPN-t<br> ExpressRoute <br> Hyper-háló. | Azure Stack jelenleg csak a VPN-típust támogatja. |
|  | SKU VPN Gateway | Az alapszintű, a GW1, a GW2, a GW3, a standard nagy teljesítményű, rendkívül nagy teljesítményű funkciók támogatása. | Az alapszintű, a standard és a nagy teljesítményű SKU támogatása. |
|  | VPN-típus | Az Azure a házirend-alapú és a Route-alapú rendszert is támogatja. | A Azure Stack csak a Route-alapú használatát támogatja. |
|  | BGP-beállítások | Az Azure támogatja a BGP-társas címek és a társak súlyozásának konfigurációját. | A BGP-társak címzési címe és a társ súlya automatikusan konfigurálva van Azure Stackban. A felhasználó nem állíthatja be ezeket a beállításokat a saját értékeivel. |
|  | Alapértelmezett átjáró helye | Az Azure támogatja az alapértelmezett hely konfigurációját a kényszerített bújtatáshoz. | Még nem támogatott. |
|  | Átjáró átméretezése | Az Azure támogatja az átjáró átméretezését az üzembe helyezés után. | Az átméretezés nem támogatott. |
|  | Rendelkezésre állási konfiguráció | Aktív/aktív | Aktív/passzív |
|  | UsePolicyBasedTrafficSelectors | Az Azure támogatja a házirend-alapú forgalom-választókat az Útválasztás-alapú átjáró kapcsolataival. | Még nem támogatott. |
| Load Balancer | SKU (Cikkszám) | Az alapszintű és a standard Load Balancer támogatott | Csak az alapszintű Load Balancer támogatott.<br>Az SKU tulajdonság nem támogatott.<br>Az alapszintű SKU Load Balancer/Path/legfeljebb 5 előtér-IP-konfigurációval rendelkezhet.  |
|  | Zóna | A Availability Zones támogatottak. | Még nem támogatott |
|  | Bejövő NAT-szabályok támogatása szolgáltatási végpontok számára | Az Azure támogatja a szolgáltatási végpontok megadását a bejövő NAT-szabályokhoz. | A Azure Stack még nem támogatja a szolgáltatási végpontokat, így ezeket nem lehet megadni. |
|  | Protocol (Protokoll) | Az Azure támogatja a GRE vagy az ESP megadását. | A protokoll osztály nem támogatott Azure Stackban. |
| Nyilvános IP-cím | Nyilvános IP-cím verziója | Az Azure az IPv6 és az IPv4 protokollt is támogatja. | Kizárólag az IPv4 használata támogatott. |
| Hálózati adapter | Hatékony útválasztási táblázat beolvasása | Támogatott | Még nem támogatott. |
|  | Érvényes ACL-ek lekérése | Támogatott | Még nem támogatott. |
|  | Gyorsított hálózatkezelés engedélyezése | Támogatott | Még nem támogatott. |
|  | IP-továbbítás | Alapértelmezés szerint letiltva.  Engedélyezve lehet. | Ez a beállítás nem támogatott.  Alapértelmezés szerint bekapcsolva. |
|  | Alkalmazásbiztonsági csoportok | Támogatott | Még nem támogatott. |
|  | Belső DNS-név címkéje | Támogatott | Még nem támogatott. |
|  | Magánhálózati IP-cím verziója | Az IPv6 és az IPv4 is támogatott. | Kizárólag az IPv4 használata támogatott. |
|  | Statikus MAC-címek | Nem támogatott | Nem támogatott. Az egyes Azure Stack rendszerek ugyanazt a MAC-címkészletet használják. |
| Network Watcher | Network Watcher bérlői hálózat figyelési képességei | Támogatott | Még nem támogatott. |
| CDN | Content Delivery Network profilok | Támogatott | Még nem támogatott. |
| Alkalmazásátjáró | 7\. rétegbeli terheléselosztás | Támogatott | Még nem támogatott. |
| Forgalomkezelő | A bejövő forgalom irányítása az alkalmazások optimális teljesítményének és megbízhatóságának megfelelően. | Támogatott | Még nem támogatott. |
| ExpressRoute | Hozzon létre egy gyors, privát kapcsolatokat a Microsoft Cloud Services szolgáltatással a helyszíni infrastruktúráról vagy a helyi létesítményből. | Támogatott | Azure Stack csatlakoztatásának támogatása Express Route-áramkörhöz. |

## <a name="api-versions"></a>API-verziók 

Azure Stack hálózatkezelés a következő API-verziókat támogatja: 

- 2018-11-01
- 2018-10-01
- 2018-08-01
- 2018-07-01
- 2018-06-01
- 2018-05-01
- 2018-04-01
- 2018-03-01
- 2018-02-01
- 2018-01-01
- 2017-11-01
- 2017-10-01

## <a name="next-steps"></a>Következő lépések

[DNS az Azure Stackben](azure-stack-dns.md)
