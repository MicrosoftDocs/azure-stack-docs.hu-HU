---
title: Hálózati integráció szempontok az Azure Stack integrált rendszerek |} A Microsoft Docs
description: Ismerje meg, mi mindent adatközpont hálózati integrálása az Azure Stack több csomópontos tervezése.
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
ms.openlocfilehash: 6286b8531a85abe6378b7ada419af7eb9f86b40e
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985000"
---
# <a name="network-connectivity"></a>Hálózati kapcsolat
Ez a cikk segít eldönteni, hogy a legjobb integrálása az Azure Stack a meglévő hálózati környezetbe az Azure Stack hálózati infrastruktúra információkat nyújt. 

> [!NOTE]
> Az Azure Stack a külső DNS-neveket (például: www\.bing.com), meg kell adnia a DNS-kiszolgálók DNS-kérelmeket továbbítsa. Az Azure Stack DNS követelményeivel kapcsolatos további információkért lásd: [adatközpontban Azure Stack - integrációs DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Fizikai hálózati terv
Az Azure Stack megoldásnak egy rugalmas és magas rendelkezésre állású fizikai infrastruktúrára van szüksége működése és szolgáltatásai támogatásához. ToR böngészőből szegély kapcsolókhoz kimenő portok korlátozva, SFP + vagy SFP28 adathordozó és 1 GB-os, 10 GB-os vagy 25 GB megbízhatóbbak. Ellenőrizze a számítógépgyártó (OEM) hardver szállítójával a rendelkezésre állás érdekében. Az alábbi diagram bemutatja a javasolt tervezési:

![Ajánlott az Azure Stack hálózati terv](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logikai hálózatok
Logikai hálózatok jelentik az alapul szolgáló fizikai hálózati infrastruktúra absztrakciója. Szervezése és egyszerűsítése érdekében a gazdagépek, virtuális gépek és szolgáltatások hálózati feladatok használhatók. Logikai hálózat létrehozása részeként a hálózati telephelyek jönnek létre adható meg virtuális helyi hálózatokat (VLAN), IP-alhálózatok és a logikai hálózatra valamennyi fizikai helyen a társított IP-alhálózat/VLAN párokat.

Az alábbi táblázat a logikai hálózatok és társított, meg kell tervezni az IPv4 alhálózati tartományok:

| Logikai hálózat | Leírás | Méret | 
| -------- | ------------- | ------------ | 
| Nyilvános virtuális IP-cím | Az Azure Stack használja ezt a hálózatot 31 címeket összesen. Az Azure Stack-szolgáltatások egy kis készletét nyolc nyilvános IP-címeket használja, és a többi bérlő virtuális gépek által használt. Ha azt tervezi, használja az App Service-ben és az SQL erőforrás-szolgáltatók, 7 további címeket használják. A fennmaradó 15 IP-címek későbbi Azure-szolgáltatások számára vannak fenntartva. | / 26 (62 gazdagépek) – /22 (1022 gazdagép)<br><br>Ajánlott = /24 (254 gazdagép) | 
| Kapcsoló-infrastruktúra | Az Útválasztás megállapítása, dedikált IP-címek pont-pont típusú váltson a felügyeleti felületek és a kapcsoló visszacsatolási címeket. | /26 | 
| Infrastruktúra | Azure Stack belső összetevőinek való kommunikációhoz használt. | /24 |
| Privát | A tárolóhálózat és a privát virtuális IP-cím használható. | /24 | 
| BMC | A bmc-k a fizikai gazdagépeken folytatott kommunikáció során használt. | /26 | 
| | | |

## <a name="network-infrastructure"></a>Hálózati infrastruktúra
Az Azure Stack a hálózati infrastruktúra több logikai hálózatok, a kapcsolók konfigurált áll. Az alábbi ábrán látható, ezeket a logikai hálózatokat, és hogyan integrálható a top-of-rack (TOR), alaplapi felügyeleti vezérlőnek (BMC), és a szegély (ügyfélhálózat) kapcsolók.

![Logikai hálózati diagram és kapcsoló kapcsolatok](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC-hálózat
Ezt a hálózatot a felügyeleti hálózathoz csatlakozó összes az alaplapi felügyeleti vezérlők (más néven szolgáltatás processzorok, például iDRAC, iLO, iBMC, stb.) van kijelölve. Ilyen esetekben az életciklus állomás (HLH) ezen a hálózaton található, és rendelkezhetnek OEM-specifikus szoftver hardver karbantartás és a figyelés. 

A HLH is találhatók a virtuális gép (DVM üzembe helyezés). A DVM Azure Stack üzembe helyezése során használja, és üzembe helyezés befejezése után a rendszer eltávolítja. A DVM internet-hozzáféréssel a csatlakoztatott központi telepítési forgatókönyv teszteléséhez, érvényesítése és több összetevőből eléréséhez szükséges. Ezek az összetevők belüli és kívüli a vállalati hálózathoz; lehet. Ha például NTP, DNS és az Azure. Kapcsolódási követelményeivel kapcsolatos további információkért lásd: a [NAT szakaszban az Azure Stack tűzfal integrációs](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Magánhálózat
Ez /24 (254 gazdagép IP-címek) hálózat magánjellegű (nem bővíti ki a szegély kapcsoló eszközök az Azure Stack régió túli) az Azure Stack-régió, és két alhálózatra oszlik:

- **Tárolóhálózat**. Egy /25 (126 gazdagép IP-címek) a közvetlen tárolóhelyek és a Server Message Block (SMB) tárolási forgalom és a virtuális gép élő áttelepítésének támogatásához használt hálózati. 
- **Belső virtuális IP-hálózat**. A/25-ös hálózati a szoftveres terheléselosztó virtuális csak belső dedikált IP-cím.

### <a name="azure-stack-infrastructure-network"></a>Az Azure Stack infrastruktúra-hálózathoz.
Ez/24 hálózati Azure Stack belső összetevőkre van kijelölve, így kommunikál, és adatokat egymás között. Ez az alhálózat elérhető IP-címet igényel, de titokban a megoldás hozzáférés-vezérlési listák (ACL-ek) használatával. Legyen irányítva, / 27-es méretű egyenértékű kis számos kivételével a szegély kapcsolók túli várhatóan nem hálózati ezen szolgáltatások által használt fel, ha a külső erőforrásokat és/vagy az internethez való hozzáférést igényelnek. 

### <a name="public-vip-network"></a>Nyilvános VIP-hálózat
A nyilvános VIP-hálózat van rendelve a hálózati vezérlő az Azure Stackben. Nem áll a logikai hálózatot, a kapcsolóhoz. A szoftveres Terheléselosztó használja a címkészletet, és hozzárendeli/32 hálózatok bérlői számítási feladatok esetében. A kapcsoló útválasztási tábla 32-címeket, BGP-n keresztül a rendelkezésre álló láncot hirdesse meg. Ehhez a hálózathoz külső elérhető vagy nyilvános IP-címeket tartalmazza. Az Azure Stack-infrastruktúra fenntartja az első 31 címeket a nyilvános VIP-hálózat, míg a többi bérlő virtuális gépek által használt. Az alhálózat hálózati mérete legalább/26-os (64 gazdagép) terjedhet maximális /22 (1022 gazdagép), azt javasoljuk, hogy egy/24 tervezi hálózati.

### <a name="switch-infrastructure-network"></a>Infrastruktúra-hálózati kapcsoló
Ez/26 hálózati az alhálózatot, amely tartalmazza a point-to-point irányítható IP/30 (2 gazdagép IP-címek) alhálózatot és a loopbacks, amelyek dedikált/32 alhálózatokat a sávon kívüli kapcsoló kezelése és a BGP-útválasztó azonosítója. Ez az IP-címek kívülről, a helyi adatközpontban az Azure Stack megoldás irányítható, magán- vagy nyilvános IP-címei lehetnek.

### <a name="switch-management-network"></a>Kapcsoló felügyeleti hálózat
Ez akár/29 méretű (6 gazdagép IP-címek) hálózati csatlakozás a felügyeleti portokat, a kapcsolók van kijelölve. Engedélyezi a sávon kívüli hozzáférést az üzembe helyezés, a felügyelet és hibaelhárítás. A fent említett hálózati kapcsoló infrastruktúra kiszámítása történik.

## <a name="publish-azure-stack-services"></a>Közzététel az Azure Stack-szolgáltatások
A felhasználók számára elérhetővé tenni az Azure Stack-szolgáltatások, a külső Azure Stack kell. Az Azure Stack állít be az infrastruktúra-szerepkörök különböző végpontjait. Ezeket a végpontokat hozzárendelt virtuális IP-cím a nyilvános IP-címkészletből. Minden végpont az üzembe helyezéskor megadott külső DNS-zóna egy DNS-bejegyzés jön létre. Ha például a felhasználói portál hozzá van rendelve a DNS állomásbejegyzéssel portál.  *&lt;régió >.&lt; teljesen minősített tartományneve >*.

### <a name="ports-and-urls"></a>Portok és URL-címek
Azure Stack-szolgáltatásokat (például a portálok, az Azure Resource Manager-, DNS, stb.) elérhető külső hálózatokhoz, engedélyeznie kell a bejövő forgalmat a fenti végpontokkal az adott URL-címeket, portokat és protokollokat.
 
A központi telepítés, ha egy transzparens proxy kimenő portokhoz hagyományos proxykiszolgálónak engedélyeznie kell a adott portok és URL-címek is [bejövő](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) és [kimenő](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) kommunikációt. Ezek közé tartozik a portok és URL-címek identitás, a Marketplace-en, javítási és frissítési, regisztrációs és használati adatok.

## <a name="next-steps"></a>További lépések
[Szegély kapcsolat](azure-stack-border-connectivity.md)
