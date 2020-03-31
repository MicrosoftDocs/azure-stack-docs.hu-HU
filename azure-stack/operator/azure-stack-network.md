---
title: Azure Stack hub hálózati integrációjának tervezése
description: Ismerje meg, hogyan tervezheti meg az adatközpontok hálózati integrációját Azure Stack hub integrált rendszerekkel.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 121bbc5ff081a6a7773d69294175f979b89bcfc5
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402839"
---
# <a name="network-integration-planning-for-azure-stack"></a>A Azure Stack hálózati integrációjának megtervezése

Ez a cikk Azure Stack hálózati infrastruktúrával kapcsolatos információkat tartalmaz, amelyek segítségével eldöntheti, hogyan integrálhatja a Azure Stackt a meglévő hálózati környezetbe. 

> [!NOTE]
> A külső DNS-nevek Azure Stackból való feloldásához (például a www\.bing.com) a DNS-kérelmek továbbításához meg kell adnia a DNS-kiszolgálókat. Azure Stack DNS-követelményekkel kapcsolatos további információkért lásd: [Azure stack Datacenter Integration-DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Fizikai hálózat kialakítása

Az Azure Stack megoldásnak egy rugalmas és magas rendelkezésre állású fizikai infrastruktúrára van szüksége működése és szolgáltatásai támogatásához. Ahhoz, hogy a Azure Stack integrálva legyen a hálózatra, a rendszer a felső (ToR) kapcsolóktól a legközelebbi kapcsolóhoz vagy útválasztóhoz való kapcsolódást igényli, amely ezen a dokumentáción szegélyként van hivatkozva. A kapcsolati kapcsolat egy vagy több szegélyhez is kapcsolódhat. A ToR-t az Automation-eszköz előre konfigurálta, és legalább egy kapcsolatot vár a ToR és a szegély között, ha BGP-útválasztást használ, és legalább két kapcsolatot (egy-egy ToR-t) a ToR és a szegély között a statikus útválasztás használata esetén, legfeljebb négy kapcsolattal útválasztási beállítások. Ezek a kapcsolatok SFP + vagy SFP28 adathordozóra, valamint egy GB, 10 GB vagy 25 GB sebességre korlátozódnak. A rendelkezésre állás érdekében érdeklődjön az eredeti berendezésgyártó (OEM) hardver gyártójánál. A következő ábra a javasolt kialakítást mutatja be:

![Ajánlott Azure Stack hálózati kialakítás](media/azure-stack-network/physical-network.svg)


## <a name="logical-networks"></a>Logikai hálózatok

A logikai hálózatok a mögöttes fizikai hálózati infrastruktúra absztrakt részét képezik. Ezek a gazdagépek, virtuális gépek és szolgáltatások hálózati hozzárendeléseinek rendszerezésére és leegyszerűsítésére szolgálnak. A logikai hálózat létrehozásának részeként a hálózati telephelyek jönnek létre a virtuális helyi hálózatok (VLAN-ok), az IP-alhálózatok, valamint az egyes fizikai helyeken a logikai hálózathoz társított IP-alhálózatok/VLAN-párok definiálásához.

A következő táblázat azokat a logikai hálózatokat és IPv4-alhálózati tartományokat tartalmazza, amelyeket meg kell terveznie:

| Logikai hálózat | Leírás | Méret | 
| -------- | ------------- | ------------ | 
| Nyilvános VIP | Azure Stack a hálózatból összesen 31 címet használ. A rendszer nyolc nyilvános IP-címet használ a Azure Stack szolgáltatások kis készletéhez, a többi pedig a bérlői virtuális gépeket használja. Ha App Service és az SQL Resource Providers használatát tervezi, 7 további címet használ a rendszer. A fennmaradó 15 IP-cím a jövőbeli Azure-szolgáltatások számára van fenntartva. | /26 (62 gazdagép) –/22 (1022 gazdagép)<br><br>Ajánlott =/24 (254 gazdagép) | 
| Infrastruktúra váltása | Pont-pont típusú IP-címek útválasztási célokra, dedikált kapcsoló felügyeleti felületek és a kapcsolóhoz rendelt visszacsatolási címek. | /26 | 
| Infrastruktúra | A Azure Stack belső összetevőinek kommunikációhoz használatos. | /24 |
| Privát | A Storage Network, a privát VIP-címek, az infrastruktúra-tárolók és egyéb belső függvények esetében használatos. A 1910-től kezdődően az alhálózat mérete a következőre változik:/20, további részletekért lásd a jelen cikk [magánhálózat](#private-network) szakaszát. | /20 | 
| BMC | A fizikai gazdagépeken található bmc való kommunikációhoz használatos. | /26 | 
| | | |

> [!NOTE]
> Ha a rendszer 1910-es verzióra frissül, a portálon lévő riasztás emlékezteti az operátort arra, hogy az új PEP **-parancsmag AzsPrivateNetwork** új/20 magánhálózati IP-cím hozzáadására fogja futtatni. A parancsmag futtatásával kapcsolatos útmutatásért tekintse meg a [1910 kibocsátási megjegyzéseit](release-notes.md) . A/20 privát IP-cím kiválasztásával kapcsolatos további információkért és útmutatásért tekintse meg a jelen cikk [magánhálózat](#private-network) szakaszát.

## <a name="network-infrastructure"></a>Hálózati infrastruktúra

A Azure Stack hálózati infrastruktúrája több, a kapcsolókon konfigurált logikai hálózatból áll. A következő ábra ezeket a logikai hálózatokat és azok integrálását mutatja be a Top-of-rack (TOR), a alaplapi felügyeleti vezérlő (BMC) és a Border (Customer Network) kapcsolókkal.

![Logikai hálózati diagram és kapcsolási kapcsolatok](media/azure-stack-network/networkdiagram.svg)

### <a name="bmc-network"></a>BMC-hálózat

Ez a hálózat dedikált az összes alaplapi-felügyeleti vezérlő (más néven BMC vagy Service Processor) a felügyeleti hálózathoz való csatlakoztatásához. Ilyenek például a következők: iDRAC, iLO, iBMC stb. A BMC-csomópontokkal való kommunikációhoz csak egy BMC-fiók használható. Ha van ilyen, a hardver életciklus-állomása (HLH) ezen a hálózaton található, és OEM-specifikus szoftvereket biztosíthat a hardveres karbantartáshoz vagy monitorozáshoz.

A HLH az üzembe helyezési virtuális gépet (DVM) is üzemelteti. A DVM Azure Stack központi telepítés során használatos, és a telepítés befejeződése után törlődik. A DVM használatához internetkapcsolat szükséges a csatlakoztatott üzembe helyezési forgatókönyvekben több összetevő teszteléséhez, ellenőrzéséhez és eléréséhez. Ezek az összetevők a vállalati hálózaton belül és kívül is lehetnek (például: NTP, DNS és Azure). A kapcsolati követelményekkel kapcsolatos további információkért tekintse [meg a Azure stack tűzfal integrációjának NAT szakaszát](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Magánhálózat

Ez a/20 (4096 IP-cím) hálózat magán a Azure Stack régióban (nem a Azure Stack rendszer szegélyének kapcsoló eszközein halad át), és több alhálózatra van osztva, néhány példa:

- **Storage Network**: egy/25 (128 IP-cím) hálózat, amely a közvetlen tárolóhelyek és az SMB-tárolási forgalom, valamint a virtuális gépek élő áttelepítésének támogatásához használatos.
- **Belső virtuális IP-hálózat**: a szoftveres terheléselosztó számára csak belső VIP-címekre dedikált/25 hálózat.
- **Container Network**: a/23 (512 IP) hálózat, amely az infrastruktúra-szolgáltatásokat futtató tárolók közötti belső forgalomra van kijelölve.

Az 1910-es kiadástól kezdve a Azure Stack hub rendszernek további/20 magánhálózati belső IP-tárterületre **van szüksége** . Ez a hálózat a Azure Stack rendszer számára lesz privát (nem az Azure Stack rendszer szegély-kapcsoló eszközein kívülre), és az adatközponton belül több Azure Stack rendszeren is felhasználható. Amíg a hálózat privát Azure Stack, nem lehet átfedésben az adatközpontban lévő többi hálózattal. A/20 magánhálózati IP-terület több olyan hálózatra oszlik, amelyek lehetővé teszik az Azure Stack hub-infrastruktúra futtatását a tárolókban (az [1905 kibocsátási megjegyzésekben](release-notes.md?view=azs-1905)korábban említettek szerint). Emellett ez az új magánhálózati IP-terület lehetővé teszi, hogy a telepítés előtt csökkentse a szükséges irányítható IP-területet. Az Azure Stack hub-infrastruktúra tárolókban való futtatásának célja a kihasználtság optimalizálása és a teljesítmény javítása. Emellett a/20 magánhálózati IP-terület is lehetővé teszi, hogy a folyamatban lévő erőfeszítéseket a telepítés előtt csökkentse a szükséges irányítható IP-területet. A magánhálózati IP-címekre vonatkozó útmutatásért javasoljuk, hogy kövesse az [RFC 1918-es dokumentumot](https://tools.ietf.org/html/rfc1918).

A 1910 előtt üzembe helyezett rendszerek esetében ez a/20 alhálózat egy további, a 1910-es frissítés után a rendszerbe bekerülő hálózat lesz. A további hálózatot a **set-AzsPrivateNetwork** PEP parancsmaggal kell megadnia a rendszeren.

> [!NOTE]
> A/20 bemenet a 1910 után a következő Azure Stack hub-frissítés előfeltétele. Ha a következő Azure Stack hub frissítése a 1910-es kiadás után, és megpróbálja telepíteni, a frissítés sikertelen lesz, ha nem végezte el a/20 bemenetet a szervizelés lépéseiben leírtak szerint, a következőképpen: A felügyeleti portálon riasztás jelenik meg, amíg a fenti szervizelési lépések be nem fejeződik. Az új privát terület felhasználásának megismeréséhez tekintse meg az [Datacenter hálózati integrációs](azure-stack-network.md#private-network) című cikket.

Javítási **lépések**: a szervizeléshez kövesse az utasításokat a PEP- [munkamenet megnyitásához](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Készítse elő a (z)/20. [magánhálózati belső IP-címtartományt](azure-stack-network.md#logical-networks) , és futtassa a következő parancsmagot (csak 1910-től kezdődően érhető el) a PEP-munkamenetben a következő példa használatával: `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Ha a művelet sikeresen elvégezve, megkapja a **konfigurációhoz hozzáadott belső hálózati AZS**üzenetet. Ha a művelet sikeresen befejeződött, a riasztás be lesz zárva a felügyeleti portálon. A Azure Stack hub rendszer most már frissíthető a következő verzióra.

### <a name="azure-stack-infrastructure-network"></a>Infrastruktúra-hálózat Azure Stack
Ez a/24 hálózat a belső Azure Stack-összetevőkre van kijelölve, így egymás között kommunikálhatnak és cserélhetik az adatcserét. Ez az alhálózat lehet a Azure Stack megoldás kívülről az adatközpontba, ezért nem ajánlott nyilvános vagy internetes útválasztásos IP-címeket használni ezen az alhálózaton. Ezt a hálózatot a rendszer meghirdeti a szegélynek, de az IP-címeinek nagy részét Access Control listák (ACL-ek) védik. A hozzáférésre jogosult IP-címek egy/27 hálózati és gazdagép-szolgáltatásokhoz, például a [privilegizált végponthoz (PEP)](azure-stack-privileged-endpoint.md) és a [Azure stack biztonsági mentéshez](azure-stack-backup-reference.md)megengedett kis hatótávolságon belül vannak.

### <a name="public-vip-network"></a>Nyilvános VIP-hálózat

A nyilvános VIP-hálózat a Azure Stack hálózati vezérlőjéhez van rendelve. Nem logikai hálózat a kapcsolón. A SLB a címek készletét használja, és hozzárendeli/32 hálózatot a bérlői munkaterhelésekhez. A Switch útválasztási táblázatban ezek/32 IP-címek a BGP-n keresztül elérhető útvonalként lesznek meghirdetve. Ez a hálózat a külső számára elérhető vagy nyilvános IP-címeket tartalmazza. A Azure Stack infrastruktúra fenntartja a nyilvános VIP-hálózat első 31 címét, míg a maradékot a bérlői virtuális gépek használják. Az alhálózat hálózati mérete legfeljebb/26 (64 gazdagép) lehet legfeljebb/22 (1022 gazdagép) értékre. Javasoljuk, hogy tervezzen egy/24 hálózatot.

### <a name="switch-infrastructure-network"></a>Infrastruktúra-hálózat váltása

Ez a/26 hálózat az az alhálózat, amely a pont-pont típusú IP/30 (két gazdagép IP-címe) alhálózatokat és a visszacsatolásokat tartalmazza, amelyek dedikált/32 alhálózatok a sávon belüli váltások kezeléséhez és a BGP-útválasztó AZONOSÍTÓhoz. Az IP-címtartomány ezen tartományán kívül kell lennie a Azure Stack megoldáson az adatközponthoz. Lehetnek magán-vagy nyilvános IP-címek.

### <a name="switch-management-network"></a>Váltás a felügyeleti hálózatra

Ez a/29 (hat gazda IP-cím) hálózat a kapcsolók felügyeleti portjainak csatlakoztatására szolgál. Sávon kívüli hozzáférés az üzembe helyezéshez, a felügyelethez és a hibaelhárításhoz. A rendszer a fent említett kapcsoló-infrastruktúra hálózat alapján számítja ki.

## <a name="permitted-networks"></a>Engedélyezett hálózatok

A 1910-es verziótól kezdődően az üzembe helyezési munkalap ezt az új mezőt fogja tartalmazni, amely lehetővé teszi, hogy az operátor módosítson egy hozzáférés-vezérlési listát (ACL), amely lehetővé teszi a hálózati eszközök felügyeleti felületeinek elérését és a hardveres életciklus-gazdagép (HLH) megbízható adatközpont . A hozzáférés-vezérlési lista módosításakor az operátor engedélyezheti, hogy a felügyeleti Jumpbox egy adott hálózati tartományon belül, a HLH operációs rendszer és a HLH BMC használatával hozzáférhessenek. Az operátor egy vagy több alhálózatot is megadhat a listához, ha üresen hagyja, a rendszer alapértelmezés szerint megtagadja a hozzáférést. Ez az új funkció lecseréli az üzembe helyezés utáni manuális beavatkozás szükségességét, ahogy azt a [Azure stack kapcsoló konfigurációjának konkrét beállításainak módosítása](https://docs.microsoft.com/azure-stack/operator/azure-stack-customer-defined#access-control-list-updates)című témakörben ismertette.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a hálózati tervezésről: a [határok közötti kapcsolat](azure-stack-border-connectivity.md).