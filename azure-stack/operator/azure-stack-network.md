---
title: Hálózati integrációs megfontolások Azure Stack integrált rendszerek esetében | Microsoft Docs
description: Megtudhatja, mit tehet az adatközpontok hálózati integrációjának tervezéséhez több csomópontos Azure Stack használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: b2b53edaba6a6cb180ae617740fd4695b1a86187
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842728"
---
# <a name="network-connectivity"></a>Hálózati kapcsolat
Ez a cikk Azure Stack hálózati infrastruktúrával kapcsolatos információkat tartalmaz, amelyek segítségével eldöntheti, hogyan integrálhatja a Azure Stackt a meglévő hálózati környezetbe. 

> [!NOTE]
> A külső DNS-nevek Azure Stackból (például a www\.Bing.com) való feloldásához DNS-kiszolgálókat kell megadnia a DNS-kérések továbbításához. Azure Stack DNS-követelményekkel kapcsolatos további információkért lásd: [Azure stack Datacenter Integration-DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Fizikai hálózat kialakítása
Az Azure Stack megoldásnak egy rugalmas és magas rendelkezésre állású fizikai infrastruktúrára van szüksége működése és szolgáltatásai támogatásához. A ToR és a Border kapcsolók közötti Kikapcsolások SFP + vagy SFP28 adathordozóra, illetve 1 GB, 10 GB vagy 25 GB sebességre korlátozódnak. A rendelkezésre állás érdekében érdeklődjön az eredeti berendezésgyártó (OEM) hardver gyártójánál. A következő ábra a javasolt kialakítást mutatja be:

![Ajánlott Azure Stack hálózati kialakítás](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logikai hálózatok
A logikai hálózatok a mögöttes fizikai hálózati infrastruktúra absztrakt részét képezik. Ezek a gazdagépek, virtuális gépek és szolgáltatások hálózati hozzárendeléseinek rendszerezésére és leegyszerűsítésére szolgálnak. A logikai hálózat létrehozásának részeként a hálózati telephelyek jönnek létre a virtuális helyi hálózatok (VLAN-ok), az IP-alhálózatok, valamint az egyes fizikai helyeken a logikai hálózathoz társított IP-alhálózatok/VLAN-párok definiálásához.

A következő táblázat azokat a logikai hálózatokat és IPv4-alhálózati tartományokat tartalmazza, amelyeket meg kell terveznie:

| Logikai hálózat | Leírás | Size | 
| -------- | ------------- | ------------ | 
| Nyilvános VIP | Azure Stack a hálózatból összesen 31 címet használ. A rendszer nyolc nyilvános IP-címet használ a Azure Stack szolgáltatások kis készletéhez, a többi pedig a bérlői virtuális gépeket használja. Ha App Service és az SQL Resource Providers használatát tervezi, 7 további címet használ a rendszer. A fennmaradó 15 IP-cím a jövőbeli Azure-szolgáltatások számára van fenntartva. | /26 (62 gazdagép) –/22 (1022 gazdagép)<br><br>Ajánlott =/24 (254 gazdagép) | 
| Infrastruktúra váltása | Pont-pont típusú IP-címek útválasztási célokra, dedikált kapcsoló felügyeleti felületek és a kapcsolóhoz rendelt visszacsatolási címek. | /26 | 
| Infrastruktúra | A Azure Stack belső összetevőinek kommunikációhoz használatos. | /24 |
| Magánjellegű | A Storage Network és a privát VIP-címek esetében használatos. | /24 | 
| BMC | A fizikai gazdagépeken található bmc való kommunikációhoz használatos. | /26 | 
| | | |

## <a name="network-infrastructure"></a>Hálózati infrastruktúra
A Azure Stack hálózati infrastruktúrája több, a kapcsolókon konfigurált logikai hálózatból áll. A következő ábra ezeket a logikai hálózatokat mutatja be, valamint azt, hogyan integrálható a Top-of-rack (TOR), a alaplapi felügyeleti vezérlővel (BMC) és a Border (Customer Network) kapcsolókkal.

![Logikai hálózati diagram és kapcsolási kapcsolatok](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC-hálózat
Ez a hálózat dedikált az összes alaplapi-felügyeleti vezérlő (más néven szolgáltatási processzor, például iDRAC, iLO, iBMC stb.) összekapcsolásához a felügyeleti hálózathoz. Ha van ilyen, a hardver életciklus-állomása (HLH) ezen a hálózaton található, és OEM-specifikus szoftvereket biztosíthat a hardveres karbantartáshoz vagy monitorozáshoz. 

A HLH az üzembe helyezési virtuális gépet (DVM) is üzemelteti. A DVM Azure Stack központi telepítés során használatos, és a telepítés befejeződése után törlődik. A DVM használatához internetkapcsolat szükséges a csatlakoztatott üzembe helyezési forgatókönyvekben több összetevő teszteléséhez, ellenőrzéséhez és eléréséhez. Ezek az összetevők a vállalati hálózaton belül és kívül is lehetnek; például: NTP, DNS és Azure. A kapcsolati követelményekkel kapcsolatos további információkért tekintse [meg a Azure stack tűzfal integrációjának NAT szakaszát](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Magánhálózat
Ez a/24 (254-es gazda IP-címek) hálózat a Azure Stack régióhoz tartozik (nem terjed ki a Azure Stack régió szegély-kapcsoló eszközeire), és két alhálózatra van osztva:

- **Storage Network**. A közvetlen tárolóhelyek és a virtuális gépek élő áttelepítésének támogatásához használt a/25 (126-es gazdagépi IP-címek) hálózat. 
- **Belső virtuális IP-hálózat**. Egy/25 hálózat, amely csak belső VIP-címekhez van hozzárendelve a szoftveres terheléselosztó számára.

### <a name="azure-stack-infrastructure-network"></a>Infrastruktúra-hálózat Azure Stack
Ez a/24 hálózat a belső Azure Stack-összetevőkre van kijelölve, így egymás között kommunikálhatnak és cserélhetik az adatcserét. Ehhez az alhálózathoz irányítható IP-címek szükségesek, de a Access Control listák (ACL-ek) használatával a megoldás magánjellegű marad. Nem várható, hogy a rendszer nem irányítja át a határon túli kapcsolókon, kivéve, ha az egyes szolgáltatások által használt, illetve a külső erőforrásokhoz és/vagy az internethez való hozzáférést igénylő, kis hatótávolságú, mérettől megegyező méretű tartományt használ. 

### <a name="public-vip-network"></a>Nyilvános VIP-hálózat
A nyilvános VIP-hálózat a Azure Stack hálózati vezérlőjéhez van rendelve. Nem logikai hálózat a kapcsolón. A SLB a címek készletét használja, és hozzárendeli/32 hálózatot a bérlői munkaterhelésekhez. A Switch útválasztási táblázatban ezek/32 IP-címek a BGP-n keresztül elérhető útvonalként lesznek meghirdetve. Ez a hálózat a külső számára elérhető vagy nyilvános IP-címeket tartalmazza. A Azure Stack infrastruktúra fenntartja a nyilvános VIP-hálózat első 31 címét, míg a maradékot a bérlői virtuális gépek használják. Az alhálózat hálózati mérete legfeljebb/26 (64 gazdagép) lehet legfeljebb/22 (1022 gazdagépre), ezért azt javasoljuk, hogy a/24 hálózatot tervezze meg.

### <a name="switch-infrastructure-network"></a>Infrastruktúra-hálózat váltása
Ez a/26 hálózat az az alhálózat, amely a pont-pont típusú IP/30 (2 gazdagép IP-címek) alhálózatokat és a visszacsatolásokat tartalmazza, amelyek dedikált/32 alhálózatok a sávon belüli váltások kezeléséhez és a BGP-útválasztó AZONOSÍTÓhoz. Az IP-címtartomány ezen tartományának kívül kell, hogy legyen a Azure Stack megoldásnak az adatközponthoz való átirányítása, lehetnek magán vagy nyilvános IP-címek.

### <a name="switch-management-network"></a>Váltás a felügyeleti hálózatra
Ez a/29 (6 gazdagépi IP-cím) hálózat a kapcsolók felügyeleti portjainak csatlakoztatására van kijelölve. Sávon kívüli hozzáférés az üzembe helyezéshez, a felügyelethez és a hibaelhárításhoz. A számítást a fent említett kapcsoló-infrastruktúra hálózatból számítjuk ki.




## <a name="next-steps"></a>További lépések
[Szegélyek közötti kapcsolat](azure-stack-border-connectivity.md)
