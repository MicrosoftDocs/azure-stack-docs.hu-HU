---
title: Különbségek és szempontok hálózat az Azure Stack |} A Microsoft Docs
description: Különbségek és szempontok ismerje meg az Azure Stack a hálózatkezelés használatakor.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 06/17/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: a59b716df7e8bf7c9a76abbfcdbe6b300c985c9f
ms.sourcegitcommit: c4507a100eadd9073aed0d537d054e394b34f530
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67198821"
---
# <a name="considerations-for-azure-stack-networking"></a>Azure Stack hálózati szempontjai

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack-hálózatok számos, az Azure-hálózatok által kínált szolgáltatásokkal rendelkezik. Vannak azonban néhány fontos különbség, hogy az Azure Stack hálózat üzembe helyezése előtt tisztában kell lennie.

Ez a cikk az Azure Stack hálózati és annak szolgáltatásait egyedi szempontjai áttekintést nyújt. Azure Stack és az Azure magas szintű különbségeit kapcsolatos további információkért tekintse meg a [szempontok kulcs](azure-stack-considerations.md) cikk.

## <a name="cheat-sheet-networking-differences"></a>Hasznos tanácsok: Hálózatkezelés különbségek

| Szolgáltatás | Funkció | Azure (globális) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Multi-tenant DNS | Támogatott | Még nem támogatott |
|  | DNS AAAA records | Támogatott | Nem támogatott |
|  | DNS-zónák előfizetésenként | 100 (alapértelmezett)<br>Kérésre növelhető. | 100 |
|  | DNS-rekord zónánként beállítása | 5000 (alapértelmezett)<br>Kérésre növelhető. | 5000 |
|  | Name servers for zone delegation | Az Azure biztosít minden felhasználó (bérlő) zónában jön létre, a négy névkiszolgálói nevet. | Az Azure Stack két névkiszolgálókat biztosít minden egyes létrehozott felhasználó (bérlő) zóna. |
| Azure Firewall | Hálózati biztonsági szolgáltatás | Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. | Még nem támogatott. |
| Virtuális hálózat | Társviszony létesítése virtuális hálózatok között | Az azonos régióban található két virtuális hálózat csatlakoztatása az Azure gerinchálózatán keresztül. | Még nem támogatott |
|  | IPv6-címek | IPv6-címet rendelhet részeként a [hálózatiadapter-konfigurációjában](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Kizárólag az IPv4 használata támogatott. |
|  | DDoS Protection-Díjcsomag | Támogatott | Még nem támogatott. |
|  | Méretezési csoport IP-konfigurációk | Támogatott | Még nem támogatott. |
|  | Személyes elérési szolgáltatások (alhálózat) | Támogatott | Még nem támogatott. |
|  | Service Endpoints – szolgáltatásvégpont | Belső (nem Internet) kapcsolatot Azure-szolgáltatások esetében támogatott. | Még nem támogatott. |
|  | Szolgáltatásvégpont-szabályzatok | Támogatott | Még nem támogatott. |
|  | Szolgáltatás-alagút | Támogatott | Még nem támogatott.  |
| Network Security Groups (Hálózati biztonsági csoportok) | Kibővített biztonsági szabályok | Támogatott | Még nem támogatott. |
|  | Érvényes biztonsági szabályok | Támogatott | Még nem támogatott. |
|  | Alkalmazásbiztonsági csoportok | Támogatott | Még nem támogatott. |
| Virtuális hálózati átjárók | Pont – hely VPN-átjáró | Támogatott | Még nem támogatott. |
|  | Vnet – Vnet-átjáró | Támogatott | Még nem támogatott. |
|  | Virtuális hálózati átjáró típusa | Az Azure támogatja a VPN<br> Express Route <br> A Hyper-Net | Az Azure Stack jelenleg csak a VPN-típust támogatja. |
|  | VPN-átjáró-termékváltozatok | Alapszintű, GW1, GW2, GW3, Standard, nagy teljesítményű, ultramagas szintű teljesítmény támogatása. | Alapszintű, Standard és a nagy teljesítményű termékváltozatok támogatása. |
|  | VPN-típust | Az Azure támogatja a csoportházirend-alapú és útvonal alapján. | Az Azure Stack-alapú útvonal csak támogatja. |
|  | BGP-beállítások | Az Azure támogatja a BGP-társviszony-létesítés címét és a Társviszony súlyozása konfigurációját. | BGP-társviszony-létesítés címét és a Társviszony súlyozása is, automatikusan konfigurált az Azure Stackben. Nincs lehetőség a saját értékekkel a beállítások konfigurálása a felhasználó számára. |
|  | Alapértelmezett átjáró hely | Az Azure támogatja az alapértelmezett hely konfigurációs a kényszerített bújtatás. | Még nem támogatott. |
|  | Átjáró átméretezése | Az Azure támogatja az átjáró átméretezése az üzembe helyezés után. | Újra méretezése nem támogatott. |
|  | Rendelkezésre állás konfigurálása | Active/Active | Aktív/passzív |
|  | UsePolicyBasedTrafficSelectors | Az Azure támogatja a szabályzatalapú forgalomválasztóinak útvonalalapú átjárót kapcsolatokkal. | Még nem támogatott. |
| Terheléselosztó | SKU | Alapszintű és Standard Load Balancer Terheléselosztók támogatottak. | Csak az alapszintű Load Balancer használata támogatott.  Az SKU tulajdonság nem támogatott. |
|  | Zóna | Rendelkezésre állási zónák használata támogatott. | Még nem támogatott |
|  | A Szolgáltatásvégpontok bejövő NAT-szabályok támogatása | Az Azure támogatja a késleltetve Szolgáltatásvégpontok bejövő NAT-szabályok. | Az Azure Stack még nem támogatja a Szolgáltatásvégpontokat, így ezek nem adhatók meg. |
|  | Protocol | Az Azure támogatja a GRE- vagy ESP megadására. | Protokoll osztály nem támogatott az Azure Stackben. |
| Nyilvános IP-cím | Nyilvános IP-cím verziója | Az Azure támogatja az IPv6 és IPv4 | Kizárólag az IPv4 használata támogatott. |
| Hálózati adapter | Hatályos útvonaltábla beolvasása | Támogatott | Még nem támogatott. |
|  | Hatékony hozzáférés-vezérlési listák beolvasása | Támogatott | Még nem támogatott. |
|  | Gyorsított hálózatkezelés engedélyezéséhez | Támogatott | Még nem támogatott. |
|  | IP-továbbítás | Alapértelmezés szerint le van tiltva.  Engedélyezhető. | Ezzel a beállítással vizualizációtól nem támogatott.  Az alapértelmezés szerint. |
|  | Alkalmazásbiztonsági csoportok | Támogatott | Még nem támogatott. |
|  | Belső DNS-névcímke | Támogatott | Még nem támogatott. |
|  | Magánhálózati IP-cím verziója | Az IPv6 és IPv4 használata támogatott. | Kizárólag az IPv4 használata támogatott. |
|  | Statikus MAC-cím | Nem támogatott | Nem támogatott. Minden egyes Azure Stack rendszert használja az azonos MAC-címkészlet. |
| Network Watcher | Network Watcher bérlői hálózathoz figyelési képességek | Támogatott | Még nem támogatott. |
| Tartalomkézbesítési hálózat (CDN) | Content Delivery Network-profilok | Támogatott | Még nem támogatott. |
| Alkalmazásátjáró | 7\. rétegbeli terheléselosztási | Támogatott | Még nem támogatott. |
| Traffic Manager | A bejövő forgalom irányítása az optimális alkalmazásteljesítmény és megbízhatóság. | Támogatott | Még nem támogatott. |
| Express Route | Állítsa be egy gyors privát kapcsolat a helyszíni infrastruktúrát vagy közös elhelyezési létesítményből származó Microsoft-felhőszolgáltatásokhoz. | Támogatott | Támogatás az Azure Stack csatlakoztatása egy Expressroute-kapcsolatcsoportot. |

## <a name="next-steps"></a>További lépések

[DNS az Azure Stackben](azure-stack-dns.md)
