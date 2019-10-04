---
title: Azure Stack tűzfal a Azure Stack integrált rendszerek tervezéséhez | Microsoft Docs
description: A többcsomópontos Azure Stack Azure-beli üzemelő példányok Azure Stack tűzfallal kapcsolatos szempontjait ismerteti.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ec8d45add2b2d0312d82d2a7e2d71fbd2a2b41d6
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909193"
---
# <a name="azure-stack-firewall-integration"></a>Azure Stack tűzfal-integráció
Javasoljuk, hogy használjon egy tűzfal eszközt a Azure Stack biztonságossá tételéhez. A tűzfalak segítenek megvédeni az olyan műveleteket, mint az elosztott szolgáltatásmegtagadási (DDOS) támadások, a behatolások észlelése és a tartalmi ellenőrzés. Ugyanakkor az Azure Storage-szolgáltatások, például a Blobok, a táblák és a várólisták adatátviteli szűk keresztmetszete is lehetnek.

 Ha a rendszer leválasztott központi telepítési módot használ, közzé kell tennie a AD FS végpontot. További információ: [Datacenter Integration Identity (adatközpont-integrációs identitás](azure-stack-integrate-identity.md)) című cikk.

A Azure Resource Manager (rendszergazda), a felügyeleti portál és a Key Vault (rendszergazda) végpontok nem feltétlenül igényelnek külső közzétételt. Például szolgáltatóként korlátozhatja a támadási felületet úgy, hogy csak a hálózatán belüli Azure Stack felügyeli, és nem az internetről.

A vállalati szervezetek esetében a külső hálózat lehet a meglévő vállalati hálózat. Ebben a forgatókönyvben a végpontokat közzé kell tenni a vállalati hálózatról Azure Stack működéséhez.

### <a name="network-address-translation"></a>Hálózati címfordítás
A hálózati címfordítás (NAT) az ajánlott módszer, amely lehetővé teszi a telepítési virtuális gép (DVM) számára a külső erőforrások és az internet elérését az üzembe helyezés során, valamint a vész-helyreállítási konzol (ERCS) VM-EK vagy privilegizált végpontja (PEP) regisztráció és hibaelhárítás.

A NAT a külső hálózat vagy a nyilvános VIP-címek nyilvános IP-címeinek alternatívája is lehet. Ez azonban nem ajánlott, mert korlátozza a bérlői felhasználói élményt, és növeli a bonyolultságot. Az egyik lehetőség egy olyan NAT lenne, amelyhez felhasználói IP-címenként még egy nyilvános IP-címet kell megadni a készleten. Egy másik lehetőség egy olyan NAT-szabály, amelyhez felhasználónként egy virtuális IP-cím szükséges NAT-szabályt a felhasználók által használt összes porthoz.

A NAT nyilvános VIP-hez való használatának néhány hátránya a következő:
- A NAT a tűzfalszabályok kezelésekor növeli a terhelést, mivel a felhasználók saját végpontokat és saját közzétételi szabályokat irányítanak a szoftveresen definiált hálózatkezelési (SDN) veremben. A felhasználóknak kapcsolatba kell lépniük a Azure Stack operátorral a virtuális IP-címek közzétételéhez, valamint a portok listájának frissítéséhez.
- Míg a NAT-használat korlátozza a felhasználói élményt, teljes hozzáférést biztosít az operátornak a közzétételi kérelmekhez.
- Az Azure-ban használt hibrid felhőalapú forgatókönyvek esetében vegye figyelembe, hogy az Azure nem támogatja VPN-alagút beállítását a NAT-t használó végpontok számára.

### <a name="ssl-decryption"></a>SSL-visszafejtés
Jelenleg ajánlott letiltani az SSL-visszafejtést az összes Azure Stack-forgalom esetében. Ha a jövőbeli frissítések támogatják, útmutatást nyújt a Azure Stack SSL-visszafejtésének engedélyezéséhez.

## <a name="edge-firewall-scenario"></a>Peremhálózati tűzfal forgatókönyv
Az Edge-telepítésekben a Azure Stack közvetlenül a peremhálózati útválasztó vagy a tűzfal mögött helyezhető üzembe. Ezekben a forgatókönyvekben a tűzfal a szegély felett (1. forgatókönyv) meghaladja az aktív-aktív és az aktív-passzív tűzfal konfigurációját, vagy a szegély eszközként (2. eset), ahol csak az aktív-aktív tűzfalat támogatja. a konfiguráció a BGP-vel vagy statikus útválasztással, a feladatátvételhez kapcsolódóan egyenlő árú, több útvonalon (ECMP) alapul.

Nyilvános IP-címek a nyilvános VIP-készlethez a külső hálózatról üzembe helyezéskor vannak megadva. Az Edge-forgatókönyvek esetében nem ajánlott nyilvános útválasztású IP-címeket használni bármely más hálózaton biztonsági célokra. Ez a forgatókönyv lehetővé teszi a felhasználók számára, hogy a teljes mértékben felügyelt Felhőbeli élményt a nyilvános felhőben, például az Azure-ban is megtapasztalják.  

![Azure Stack peremhálózati tűzfal – példa](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Vállalati intranet vagy peremhálózati tűzfal forgatókönyve
A vállalati intranet vagy peremhálózati környezetekben Azure Stack több zónás tűzfalon, illetve a peremhálózati tűzfal és a belső, vállalati hálózati tűzfal között helyezhető üzembe. Ezután a rendszer a biztonságos, peremhálózati (vagy DMZ) és a nem biztonságos zónák között osztja el a forgalmat, az alábbiak szerint:

- **Biztonságos zóna**: Ez az a belső hálózat, amely belső vagy vállalati útválasztású IP-címeket használ. A biztonságos hálózat osztható, a tűzfalon keresztül a NAT-on keresztül érhető el az internetről kimenő hozzáférés, és a belső hálózaton keresztül általában bárhonnan elérhető az adatközponton belül. Minden Azure Stack hálózatnak a biztonságos zónában kell lennie, kivéve a külső hálózat nyilvános VIP-készletét.
- **Peremhálózati zóna**. A peremhálózaton a külső vagy az internetkapcsolattal rendelkező alkalmazások, például a webkiszolgálók jellemzően üzembe helyezhetők. Általában egy tűzfal figyeli, hogy elkerülje a támadásokat (például a DDoS és a behatolást), miközben továbbra is engedélyezi a megadott bejövő forgalmat az internetről. Csak a Azure Stack külső hálózat nyilvános VIP-készletének kell lennie a DMZ-zónában.
- Nem **biztonságos zóna**. Ez a külső hálózat, az Internet. A **nem** biztonságos zónában nem ajánlott Azure stack üzembe helyezése.

![Azure Stack peremhálózati hálózati példa](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról
További információ az [Azure stack-végpontok által használt portokról és protokollokról](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>További lépések
[PKI-követelmények Azure Stack](azure-stack-pki-certs.md)

