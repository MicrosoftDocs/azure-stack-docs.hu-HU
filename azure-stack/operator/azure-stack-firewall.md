---
title: Azure Stack hub tűzfal-integráció Azure Stack hub integrált rendszerekhez
description: Ismerkedjen meg az Azure Stack hub integrált rendszereinek Azure Stack hub tűzfal-integrációval.
author: ihenkel
ms.topic: article
ms.date: 11/15/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 752fd1f7887b8de6e9d7808e5c887200da8d6b10
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882307"
---
# <a name="azure-stack-hub-firewall-integration"></a>Azure Stack hub tűzfal-integráció
Javasoljuk, hogy a Azure Stack hub biztonságossá tételéhez használjon tűzfal eszközt. A tűzfalak segítenek megvédeni az olyan műveleteket, mint az elosztott szolgáltatásmegtagadási (DDOS) támadások, a behatolások észlelése és a tartalmi ellenőrzés. Ugyanakkor az Azure Storage-szolgáltatások, például a Blobok, a táblák és a várólisták adatátviteli szűk keresztmetszete is lehetnek.

 Ha a rendszer leválasztott központi telepítési módot használ, közzé kell tennie a AD FS végpontot. További információ: [Datacenter Integration Identity (adatközpont-integrációs identitás](azure-stack-integrate-identity.md)) című cikk.

A Azure Resource Manager (rendszergazda), a felügyeleti portál és a Key Vault (rendszergazda) végpontok nem feltétlenül igényelnek külső közzétételt. Például szolgáltatóként korlátozhatja a támadási felületet úgy, hogy csak Azure Stack hub felügyeletét végzi a hálózaton belülről, és nem az internetről.

A vállalati szervezetek esetében a külső hálózat lehet a meglévő vállalati hálózat. Ebben az esetben a végpontokat közzé kell tenni a Azure Stack hub üzemeltetéséhez a vállalati hálózatról.

### <a name="network-address-translation"></a>Hálózati címfordítás
A hálózati címfordítás (NAT) az ajánlott módszer, amely lehetővé teszi, hogy az üzembe helyezési virtuális gép (DVM) hozzáférjen a külső erőforrásokhoz és az internethez az üzembe helyezés során, valamint a vész-helyreállítási konzol (ERCS) VM-EK vagy privilegizált végpontja (PEP) regisztráció és hibaelhárítás.

A NAT a külső hálózat vagy a nyilvános VIP-címek nyilvános IP-címeinek alternatívája is lehet. Ez azonban nem ajánlott, mert korlátozza a bérlői felhasználói élményt, és növeli a bonyolultságot. Az egyik lehetőség egy olyan NAT lenne, amelyhez felhasználói IP-címenként még egy nyilvános IP-címet kell megadni a készleten. Egy másik lehetőség egy olyan NAT-szabály, amelyhez felhasználónként egy virtuális IP-cím szükséges NAT-szabályt a felhasználók által használt összes porthoz.

A NAT nyilvános VIP-hez való használatának néhány hátránya a következő:
- A NAT a tűzfalszabályok kezelésekor növeli a terhelést, mivel a felhasználók saját végpontokat és saját közzétételi szabályokat irányítanak a szoftveresen definiált hálózatkezelési (SDN) veremben. A felhasználóknak kapcsolatba kell lépniük a Azure Stack hub operátorral a virtuális IP-címek közzétételéhez, valamint a portok listájának frissítéséhez.
- Míg a NAT-használat korlátozza a felhasználói élményt, teljes hozzáférést biztosít az operátornak a közzétételi kérelmekhez.
- Az Azure-ban használt hibrid felhőalapú forgatókönyvek esetében vegye figyelembe, hogy az Azure nem támogatja VPN-alagút beállítását a NAT-t használó végpontok számára.

### <a name="ssl-interception"></a>SSL-lehallgatás
Jelenleg ajánlott letiltani az SSL-elfogást (például a visszafejtés kiszervezését) az összes Azure Stack hub-forgalomon. Ha a jövőbeli frissítések támogatják, útmutatást nyújt a Azure Stack hub SSL-lehallgatásának engedélyezéséhez.

## <a name="edge-firewall-scenario"></a>Peremhálózati tűzfal forgatókönyv
Az Edge-telepítésekben a Azure Stack hub közvetlenül a peremhálózati útválasztó vagy a tűzfal mögött van üzembe helyezve. Ezekben a forgatókönyvekben a tűzfal a szegély felett (1. forgatókönyv) meghaladja az aktív-aktív és az aktív-passzív tűzfal konfigurációját, vagy a szegély eszközként (2. forgatókönyv), ahol csak az aktív-aktív tűzfalat támogatja. a konfiguráció a BGP-vel vagy statikus útválasztással a feladatátvételhez kapcsolódó, egyenlő árú többutas (ECMP) beállításokra támaszkodik.

Nyilvános IP-címek a nyilvános VIP-készlethez a külső hálózatról üzembe helyezéskor vannak megadva. Az Edge-forgatókönyvekben nem ajánlott nyilvános, irányítható IP-címeket használni bármely más hálózaton biztonsági célokra. Ez a forgatókönyv lehetővé teszi a felhasználók számára, hogy a teljes mértékben felügyelt Felhőbeli élményt a nyilvános felhőben, például az Azure-ban is megtapasztalják.  

![Példa Azure Stack hub peremhálózati tűzfalára](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Vállalati intranet vagy peremhálózati tűzfal forgatókönyve
A vállalati intranet vagy peremhálózati környezetekben a Azure Stack hub többzónás tűzfalon, illetve a peremhálózati tűzfal és a belső, vállalati hálózati tűzfal között helyezhető üzembe. Ezután a rendszer a biztonságos, peremhálózati (vagy DMZ) és a nem biztonságos zónák között osztja el a forgalmat, az alábbiak szerint:

- **Biztonságos zóna**: Ez a belső hálózat, amely belső vagy vállalati ÚTVÁLASZTÁSÚ IP-címeket használ. A biztonságos hálózat osztható, a tűzfalon keresztül a NAT-on keresztül érhető el az internetről kimenő hozzáférés, és a belső hálózaton keresztül általában bárhonnan elérhető az adatközponton belül. Minden Azure Stack hub-hálózatnak a biztonságos zónában kell lennie, kivéve a külső hálózat nyilvános VIP-készletét.
- **Peremhálózati zóna**. A peremhálózaton a külső vagy internetes alkalmazások, például a webkiszolgálók jellemzően üzembe helyezhetők. Általában egy tűzfal figyeli, hogy elkerülje a támadásokat (például a DDoS és a behatolást), miközben továbbra is engedélyezi a megadott bejövő forgalmat az internetről. Csak a Azure Stack hub külső hálózati nyilvános VIP-készletének kell lennie a DMZ-zónában.
- Nem **biztonságos zóna**. Ez a külső hálózat, az Internet. Nem ajánlott Azure Stack hubot telepíteni a **nem** biztonságos zónában.

![Azure Stack hub peremhálózati hálózati példa](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>További információk
További információ az [Azure stack hub-végpontok által használt portokról és protokollokról](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Következő lépések
[Azure Stack hub PKI-követelményei](azure-stack-pki-certs.md)

