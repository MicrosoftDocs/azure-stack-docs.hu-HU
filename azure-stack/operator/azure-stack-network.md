---
title: Azure Stack hub hálózati integrációjának tervezése
description: Ismerje meg, hogyan tervezheti meg az adatközpontok hálózati integrációját Azure Stack hub integrált rendszerekkel.
author: ihenkel
ms.topic: article
ms.date: 10/23/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 42529e5757100ed2ad1334b62bf4e58e1266f561
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881697"
---
# <a name="network-integration-planning-for-azure-stack-hub"></a>Azure Stack hub hálózati integrációjának tervezése

Ez a cikk Azure Stack hub hálózati infrastruktúrával kapcsolatos információkat tartalmaz, amelyek segítségével eldöntheti, hogyan integrálhatja a Azure Stack hub-t a meglévő hálózati környezetbe. 

> [!NOTE]
> Ha Azure Stack hub külső DNS-neveit szeretné feloldani (például a www\.bing.com), a DNS-kérések továbbításához meg kell adnia a DNS-kiszolgálókat. Azure Stack hub DNS-követelményeivel kapcsolatos további információkért lásd: [Azure stack hub Datacenter Integration-DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Fizikai hálózat kialakítása

Az Azure Stack hub-megoldás rugalmas és magasan elérhető fizikai infrastruktúrát igényel a működésének és szolgáltatásainak támogatásához. A ToR és a Border kapcsolók közötti Kikapcsolások SFP + vagy SFP28 adathordozóra, illetve 1 GB, 10 GB vagy 25 GB sebességre korlátozódnak. A rendelkezésre állás érdekében érdeklődjön az eredeti berendezésgyártó (OEM) hardver gyártójánál. A következő ábra a javasolt kialakítást mutatja be:

![Ajánlott Azure Stack hub hálózati kialakítás](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logikai hálózatok

A logikai hálózatok a mögöttes fizikai hálózati infrastruktúra absztrakt részét képezik. Ezek a gazdagépek, virtuális gépek és szolgáltatások hálózati hozzárendeléseinek rendszerezésére és leegyszerűsítésére szolgálnak. A logikai hálózat létrehozásának részeként a hálózati telephelyek jönnek létre a virtuális helyi hálózatok (VLAN-ok), az IP-alhálózatok, valamint az egyes fizikai helyeken a logikai hálózathoz társított IP-alhálózatok/VLAN-párok definiálásához.

A következő táblázat azokat a logikai hálózatokat és IPv4-alhálózati tartományokat tartalmazza, amelyeket meg kell terveznie:

| Logikai hálózat | Leírás | Méret | 
| -------- | ------------- | ------------ | 
| Nyilvános VIP | Azure Stack hub a hálózatból összesen 31 címet használ. A rendszer nyolc nyilvános IP-címet használ a Azure Stack hub-szolgáltatások kis készletéhez, a többi pedig a bérlői virtuális gépeket használja. Ha App Service és az SQL Resource Providers használatát tervezi, 7 további címet használ a rendszer. A fennmaradó 15 IP-cím a jövőbeli Azure-szolgáltatások számára van fenntartva. | /26 (62 gazdagép) –/22 (1022 gazdagép)<br><br>Ajánlott =/24 (254 gazdagép) | 
| Infrastruktúra váltása | Pont-pont típusú IP-címek útválasztási célokra, dedikált kapcsoló felügyeleti felületek és a kapcsolóhoz rendelt visszacsatolási címek. | /26 | 
| Infrastruktúra | A Azure Stack hub belső összetevőinek kommunikációhoz használatos. | /24 |
| Saját | A Storage Network, a privát VIP-címek, az infrastruktúra-tárolók és egyéb belső függvények esetében használatos. A 1910-től kezdődően az alhálózat mérete a/20 értékre változik. További információkért lásd a jelen cikk [magánhálózat](#private-network) című szakaszát. | /20 | 
| BMC | A fizikai gazdagépeken található bmc való kommunikációhoz használatos. | /26 | 
| | | |

> [!NOTE]
> Ha a rendszer a 1910-es verzióra frissül, a portálon lévő riasztás emlékezteti az operátort arra, hogy a **set-AzsPrivateNetwork** új PEP-parancsmagot egy új/20 magánhálózati IP-terület hozzáadására fogja futtatni. A parancsmag futtatásával kapcsolatos útmutatásért tekintse meg a [1910 kibocsátási megjegyzéseit](release-notes.md) . További információt és útmutatást a/20 privát IP-cím kiválasztásáról a jelen cikk [magánhálózat](#private-network) című szakaszában talál.

## <a name="network-infrastructure"></a>Hálózati infrastruktúra

Azure Stack hub hálózati infrastruktúrája több, a kapcsolókon konfigurált logikai hálózatból áll. A következő ábra ezeket a logikai hálózatokat és azok integrálását mutatja be a Top-of-rack (TOR), a alaplapi felügyeleti vezérlő (BMC) és a Border (Customer Network) kapcsolókkal.

![Logikai hálózati diagram és kapcsolási kapcsolatok](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC-hálózat

Ez a hálózat dedikált az összes alaplapi-felügyeleti vezérlő (más néven BMC vagy Service Processor) a felügyeleti hálózathoz való csatlakoztatásához. Ilyenek például a következők: iDRAC, iLO, iBMC stb. A BMC-csomópontokkal való kommunikációhoz csak egy BMC-fiók használható. Ha van ilyen, a hardver életciklus-állomása (HLH) ezen a hálózaton található, és OEM-specifikus szoftvereket biztosíthat a hardveres karbantartáshoz vagy monitorozáshoz.

A HLH az üzembe helyezési virtuális gépet (DVM) is üzemelteti. A DVM Azure Stack hub központi telepítése során használatos, és a telepítés befejeződése után törlődik. A DVM használatához internetkapcsolat szükséges a csatlakoztatott üzembe helyezési forgatókönyvekben több összetevő teszteléséhez, ellenőrzéséhez és eléréséhez. Ezek az összetevők a vállalati hálózaton belül és kívül is lehetnek (például: NTP, DNS és Azure). A kapcsolati követelményekkel kapcsolatos további információkért tekintse [meg a Azure stack hub tűzfal-integrációjának NAT szakaszát](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Magánhálózat

Ez a/20 (4096 IP-cím) hálózat az Azure Stack hub-régióhoz tartozik (nem az Azure Stack hub rendszer szegélyének kapcsoló eszközein halad át), és több alhálózatra van osztva, néhány példa:

- **Storage Network**: egy/25 (128 IP-cím) hálózat, amely a közvetlen tárolóhelyek és az SMB-tárolási forgalom, valamint a virtuális gépek élő áttelepítésének támogatásához használatos.
- **Belső virtuális IP-hálózat**: a szoftveres terheléselosztó számára csak belső VIP-címekre dedikált/25 hálózat.
- **Container Network**: a/23 (512 IP) hálózat, amely az infrastruktúra-szolgáltatásokat futtató tárolók közötti belső forgalomra van kijelölve.

A 1910-től kezdődően a magánhálózat mérete a magánhálózati IP-címek egy/20 (4096-es IP-címei) értékre változik. Ez a hálózat a Azure Stack hub rendszer számára (nem az Azure Stack hub rendszer szegély-kapcsoló eszközein kívülről) lesz, és az adatközponton belül több Azure Stack hub-rendszeren is felhasználható. Amíg a hálózat Azure Stack hub-ra van csatlakoztatva, az nem lehet átfedésben az adatközpont többi hálózatával. Átfedés esetén előfordulhat, hogy Azure Stack hub nem irányítja kívül a vállalati hálózati forgalmat. A magánhálózati IP-területtel kapcsolatos útmutatásért javasoljuk, hogy kövesse az [RFC 1918-es dokumentumot](https://tools.ietf.org/html/rfc1918).

Ez a/20 magánhálózati IP-terület több hálózatra lesz osztva, amely lehetővé teszi az Azure Stack hub rendszer belső infrastruktúrájának a későbbi kiadásokban lévő tárolókban való futtatását. További információ: [1910 kibocsátási megjegyzések](release-notes.md). Emellett ez az új magánhálózati IP-terület lehetővé teszi, hogy a telepítés előtt csökkentse a szükséges irányítható IP-területet.

A 1910 előtt üzembe helyezett rendszerek esetében ez a/20 alhálózat egy további, a 1910-es frissítés után a rendszerbe bekerülő hálózat lesz. A további hálózatot a **set-AzsPrivateNetwork** PEP parancsmaggal kell megadnia a rendszeren. A parancsmaggal kapcsolatos útmutatásért tekintse meg a [1910 kibocsátási megjegyzéseit](release-notes.md).

### <a name="azure-stack-hub-infrastructure-network"></a>Azure Stack hub infrastruktúra hálózata

Ez a/24 hálózat a belső Azure Stack hub-összetevőkre van kijelölve, így egymás között kommunikálhatnak és cserélhetik az adatcserét. Ez az alhálózat az Azure Stack hub-megoldáshoz az adatközponthoz külsőleg irányítható. Ezen az alhálózaton nem ajánlott nyilvános vagy internetes útválasztásos IP-címeket használni. Ezt a hálózatot a rendszer meghirdeti a szegélynek, de a legtöbb IP-címet Access Control listák (ACL-ek) védik. A hozzáférésre jogosult IP-címek kis hatótávolságon belül vannak, a/27 hálózat és a gazdagép-szolgáltatások, például a [privilegizált végpont (PEP)](azure-stack-privileged-endpoint.md) és az [Azure stack hub biztonsági mentések](azure-stack-backup-reference.md)mérete szerint.

### <a name="public-vip-network"></a>Nyilvános VIP-hálózat

A nyilvános VIP-hálózat Azure Stack hub hálózati vezérlőjéhez van rendelve. Nem logikai hálózat a kapcsolón. A SLB a címek készletét használja, és hozzárendeli/32 hálózatot a bérlői munkaterhelésekhez. A Switch útválasztási táblázatban ezek/32 IP-címek a BGP-n keresztül elérhető útvonalként lesznek meghirdetve. Ez a hálózat a külső számára elérhető vagy nyilvános IP-címeket tartalmazza. A Azure Stack hub-infrastruktúra megtartja a nyilvános VIP-hálózat első 31 címét, míg a maradékot a bérlői virtuális gépek használják. Az alhálózat hálózati mérete legfeljebb/26 (64 gazdagép) lehet legfeljebb/22 (1022 gazdagép) értékre. Javasoljuk, hogy tervezzen egy/24 hálózatot.

### <a name="switch-infrastructure-network"></a>Infrastruktúra-hálózat váltása

Ez a/26 hálózat az az alhálózat, amely a pont-pont típusú IP/30 (két gazdagép IP-címe) alhálózatokat és a visszacsatolásokat tartalmazza, amelyek dedikált/32 alhálózatok a sávon belüli váltások kezeléséhez és a BGP-útválasztó AZONOSÍTÓhoz. Ezt az IP-címtartományt az Azure Stack hub-megoldáson kívül kell irányítani az adatközpontban. Lehetnek magán-vagy nyilvános IP-címek.

### <a name="switch-management-network"></a>Váltás a felügyeleti hálózatra

Ez a/29 (hat gazda IP-cím) hálózat a kapcsolók felügyeleti portjainak csatlakoztatására szolgál. Sávon kívüli hozzáférés az üzembe helyezéshez, a felügyelethez és a hibaelhárításhoz. A rendszer a fent említett kapcsoló-infrastruktúra hálózat alapján számítja ki.

## <a name="permitted-networks"></a>Engedélyezett hálózatok

A 1910-es verziótól kezdődően az üzembe helyezési munkalap egy új mezővel fog rendelkezni, amely lehetővé teszi, hogy az operátor megváltoztasson bizonyos hozzáférés-vezérlési listákat (ACL-eket), hogy engedélyezze a hozzáférést a hálózati eszközök felügyeleti felületéhez és a hardver életciklus-gazdagépéhez (HLH A hozzáférés-vezérlési lista módosításakor az operátor engedélyezheti, hogy a felügyeleti Jumpbox egy adott hálózati tartományon belül, a HLH operációs rendszer és a HLH BMC használatával hozzáférhessenek. Az operátor egy vagy több alhálózatot is megadhat a listához, ha üresen hagyja, a rendszer alapértelmezés szerint megtagadja a hozzáférést. Ez az új funkció lecseréli az üzembe helyezés utáni manuális beavatkozás szükségességét, ahogy azt a [Azure stack hub kapcsoló konfigurációjának konkrét beállításainak módosítása](azure-stack-customer-defined.md#access-control-list-updates)című témakörben ismertette.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a hálózati tervezésről: a [határok közötti kapcsolat](azure-stack-border-connectivity.md).
