---
title: Az Azure Stack-tűzfal tervezése az Azure Stack integrált rendszerek |} A Microsoft Docs
description: A több csomópontos, az Azure Stack Azure-kapcsolattal rendelkező üzemelő példányok az Azure Stack tűzfal szempontokat ismerteti.
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
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: a98f89866fd7679a0d6ce7c6ecb40d7e8e8b51e7
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618685"
---
# <a name="azure-stack-firewall-integration"></a>Az Azure Stack tűzfal integrációja
Javasoljuk, hogy egy tűzfal eszköz segítségével biztonságos Azure Stack segítségével. Tűzfalak segítségével többek között elosztott--szolgáltatásmegtagadásos (DDOS) támadások, behatolásérzékelési és a tartalomvizsgálat szemben. Azonban akkor is válhat az Azure storage-szolgáltatásokhoz hasonlóan a blobok, táblák és üzenetsorok átviteli szűk keresztmetszetté.

 Ha a kapcsolat nélküli telepítési módban használja, az AD FS-végpont közzé kell tennie. További információkért lásd: a [adatközpont integrációja identitás cikk](azure-stack-integrate-identity.md).

Az Azure Resource Manager (rendszergazda), a felügyeleti portál és a Key Vault (rendszergazda) végpontok nem feltétlenül szükséges külső közzétételét. Például szolgáltató, korlátozhatja a támadási felület által csak felügyelete az Azure Stack, a hálózaton belül, és nem az internetről.

A vállalati szervezetek számára a külső hálózat lehet a meglévő vállalati hálózaton. Ebben a forgatókönyvben közzé kell tennie a végpontok Azure Stack működjön a vállalati hálózatról.

### <a name="network-address-translation"></a>Hálózati címfordítás
Hálózati címfordítás (NAT), hogy a központi telepítés a virtuális gép (DVM) külső erőforrások hozzáféréséhez és üzembe helyezése során az interneten, valamint a válságkezelési helyreállítási konzol (ERCS) virtuális gépek vagy a kiemelt jogosultságú a végpontot (EGP) a során a javasolt módszer a regisztráció és a hibaelhárítás.

NAT is lehet a külső hálózaton vagy a nyilvános virtuális IP-cím nyilvános IP-címek helyett. Azonban nem ajánlott, mert azt korlátozza a bérlő felhasználói élményt, és növeli az összetettséget. Az egyik lehetőség, amely itt is szükség van egy nyilvános IP-Címek száma a felhasználói IP-cím használatát a készletben egy az egyhez NAT lenne. Egy másik lehetőség, több-több NAT egy NAT-szabályt egy felhasználói virtuális IP-cím szükséges összes port, a felhasználó használhatja.

A nyilvános virtuális IP-CÍMEK a NAT használatával több szempontból sem ajánlott a következők:
- NAT többletterheléssel tűzfalszabályok kezelésekor, mert a felhasználók saját végpontokat és a saját közzététel szabályai a szoftveralapú hálózatkezelési (SDN) verem szabályozhatja. Felhasználók kapcsolatba kell lépnie a VIP-k közzététele, és a port listájának frissítése az Azure Stack-operátorokról.
- NAT-használat korlátozza a felhasználói élmény, miközben nyújt teljes hozzáférés az operátor keresztül közzétételi kérelmeket.
- Hibrid felhős rendszerekben az Azure-ral fontolja meg, hogy az Azure nem támogatja a VPN-alagúton NAT használata a végpont beállítása

### <a name="ssl-decryption"></a>Az SSL visszafejtési
Jelenleg javasoljuk, hogy az összes Azure Stack-forgalom SSL visszafejtési letiltása. A jövőbeli frissítések támogatott, ha útmutatást nyújtanak az Azure Stack SSL visszafejtési engedélyezésével kapcsolatos.

## <a name="edge-firewall-scenario"></a>Peremhálózati tűzfal forgatókönyv
Az edge üzemelő példány, az Azure Stack közvetlenül a peremhálózati útválasztó vagy a tűzfal mögött van telepítve. Ezekben az esetekben támogatott meghaladta a szegély (1. forgatókönyv esetében), ahol támogatja mindkét aktív-aktív és az aktív-passzív Tűzfalkonfiguráció, vagy a szegély eszköz (2. forgatókönyv) átjáróként, csak a támogatott aktív-aktív tűzfal tűzfal függő a egyenlő költség több elérési út (ECMP) BGP vagy a statikus útválasztás feladatátvételi konfiguráció.

Nyilvános irányítható IP-címek üzembe helyezéskor megváltozhatja a nyilvános VIP-készletet a külső hálózathoz vannak megadva. Az edge a forgatókönyvben nem ajánlott bármilyen más hálózati biztonsági okokból nyilvános irányítható IP-címek használatát. Ebben a forgatókönyvben lehetővé teszi, hogy a felhasználó élmény, mint például az Azure nyilvános felhő mint a teljes körű önálló felügyelt felhőbeli élményt.  

![Azure Stack peremhálózati tűzfal – példa](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Vállalati intranet vagy peremhálózati tűzfal forgatókönyve
Vállalati intranetes vagy szegélyhálózat-alapú üzembe helyezés Azure Stack üzembe lesz helyezve a többszörös zónázva tűzfalon, vagy a peremhálózati tűzfalon, és a belső, vállalati hálózati tűzfal között. Ezután ossza a biztonságos, szegélyhálózaton (vagy a DMZ) között a forgalmat, és nem biztonságos zónák, az alábbiakban:

- **Biztonságos zóna**: Ez az a belső hálózat, amely a belső vagy vállalati irányítható IP-címet használ. A biztonságos hálózati oszthatók, a tűzfalon keresztül, NAT kimenő internet-hozzáféréssel rendelkezik, és általában érhető el, bárhol az adatközpontban, a belső hálózaton belül. Az összes Azure Stack-hálózatok a külső hálózati nyilvános VIP-címkészlet kivételével a biztonságos zóna kell lennie.
- **Szegélyhálózat-alapú zóna**. A szegélyhálózaton van, ha külső vagy internetkapcsolattal rendelkező alkalmazás, például webkiszolgálók általában vannak telepítve. Egy tűzfal, például DDoS és a behatolás (feltörés) miközben továbbra is lehetővé teszi a megadott bejövő forgalom az internetről érkező támadások elkerülése érdekében általában figyelhető. Csak a külső hálózati nyilvános VIP-készletet az Azure Stack kell lennie a DMZ zónában.
- **Nem biztonságos zóna**. Ez az a külső hálózat, az interneten. Ez **nem** javasoljuk, hogy a nem biztonságos zónában az Azure Stack üzembe helyezése.

![Azure Stack szegélyhálózat-alapú hálózat – példa](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról
Tudjon meg többet [Azure Stack-végpontok által használt portokról és protokollokról](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>További lépések
[Az Azure Stack nyilvános kulcsokra épülő infrastruktúra követelményei](azure-stack-pki-certs.md)

