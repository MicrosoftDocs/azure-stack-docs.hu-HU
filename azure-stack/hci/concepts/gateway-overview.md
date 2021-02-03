---
title: A RAS-átjáró áttekintése Azure Stack HCI-ben és a Windows Serverben
description: Ebből a témakörből megtudhatja, hogyan használható a RAS Gateway a szoftverek által meghatározott hálózatkezeléshez Azure Stack HCI-ben és a Windows Serveren.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/02/2021
ms.openlocfilehash: d0658ab5d000f42a2cc9309a1a298eb62048b980
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510856"
---
# <a name="what-is-ras-gateway-for-software-defined-networking"></a>Mi az RAS-átjáró a szoftveresen definiált hálózatkezeléshez?

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016

A távelérési szolgáltatás (RAS) átjárója egy szoftveres Border Gateway Protocol (BGP) kompatibilis útválasztó, amely a Hyper-V hálózati virtualizálás (HNV) használatával a több-bérlős virtuális hálózatokat üzemeltető vállalatok számára készült. A RAS Gateway segítségével átirányíthatja a hálózati forgalmat egy virtuális hálózat és egy másik hálózat között, akár helyi, akár távoli módon.

A RAS-átjáróhoz [hálózati vezérlőre](network-controller-overview.md)van szükség, amely az átjáró-készletek központi telepítését végzi, konfigurálja a bérlői kapcsolatokat az egyes átjárókban, és a hálózati forgalmat átjáró meghibásodása esetén készenléti átjáróra vált.

  > [!NOTE]
  > A bérlős lehetővé teszi, hogy egy felhőalapú infrastruktúra támogassa a több bérlő virtuális gép (VM) munkaterhelését, mégis elkülöníti azokat egymástól, miközben az összes munkaterhelés ugyanazon az infrastruktúrán fut. Egy adott bérlő több munkaterhelése összekapcsolható és kezelhető távolról, de ezek a rendszerek nem kapcsolódnak össze a többi bérlő munkaterheléseivel, és a többi bérlő nem képes ezek távoli kezelésére.

## <a name="ras-gateway-features"></a>A RAS-átjáró funkciói

A RAS-átjáró számos funkciót kínál a virtuális magánhálózati (VPN), a bújtatási, a továbbítási és a dinamikus útválasztáshoz.

### <a name="site-to-site-ipsec-vpn"></a>Helyek közötti IPsec VPN

Ez a RAS-átjáró funkció lehetővé teszi két hálózat különböző fizikai helyeken való összekapcsolását az interneten keresztül helyek közötti (S2S) virtuális magánhálózati (VPN) kapcsolat használatával. Ez egy titkosított kapcsolat a IKEv2 VPN protokoll használatával.

Az adatközpontban számos bérlőt üzemeltető CSP-hez a RAS Gateway egy több-bérlős átjáró-megoldást biztosít, amely lehetővé teszi a bérlők számára erőforrásaik elérését és kezelését a távoli helyekről a helyek közötti VPN-kapcsolatokon keresztül. A RAS-átjáró lehetővé teszi a hálózati forgalom áramlását az adatközpontban lévő virtuális erőforrások és a fizikai hálózatok között.

### <a name="site-to-site-gre-tunnels"></a>Helyek közötti GRE-alagutak

Az általános útválasztási beágyazás (GRE) alapú alagutak lehetővé teszik a bérlői virtuális hálózatok és a külső hálózatok közötti kapcsolatot. Mivel a GRE protokoll egyszerű és a GRE támogatása a legtöbb hálózati eszközön elérhető, ideális választás az olyan bújtatáshoz, ahol nem szükséges az adattitkosítás.

A GRE-támogatás a S2S-alagutakban megoldja a bérlői virtuális hálózatok és a bérlői külső hálózatok közötti továbbítás problémáját egy több-bérlős átjáró használatával.

### <a name="layer-3-forwarding"></a>3. réteg továbbítása

A 3. rétegbeli (L3) továbbítás lehetővé teszi az adatközpontban található fizikai infrastruktúra és a Hyper-V hálózati virtualizálási felhőben található virtualizált infrastruktúra közötti kapcsolat elérését. Az L3-továbbító kapcsolat használatával a bérlői hálózati virtuális gépek az SDN-átjárón keresztül csatlakozhatnak egy fizikai hálózathoz, amely már konfigurálva van egy szoftveres hálózatkezelési (SDN) környezetben. Ebben az esetben az SDN-átjáró útválasztóként működik a virtualizált hálózat és a fizikai hálózat között.

### <a name="dynamic-routing-with-bgp"></a>Dinamikus útválasztás BGP-vel

A BGP csökkenti a manuális útválasztás-konfigurálás szükségességét az útválasztókon, mert ez egy dinamikus útválasztási protokoll, és automatikusan megtanulja az útvonalakat a webhelyek közötti VPN-kapcsolattal csatlakoztatott helyek között. Ha a szervezet több olyan hellyel rendelkezik, amelyek BGP-kompatibilis útválasztók (például RAS Gateway) használatával csatlakoznak, a BGP lehetővé teszi, hogy az útválasztók a hálózat megszakadása vagy meghibásodása esetén automatikusan kiszámítsanak és használják az érvényes útvonalakat.

A RAS Gateway által tartalmazott BGP Route-tükrözés alternatívát biztosít az útválasztók közötti útvonal-szinkronizáláshoz szükséges BGP teljes hálós topológiára. További információ: [Mi az az Route-tükröző?](route-reflector-overview.md)

## <a name="how-ras-gateway-works"></a>A RAS-átjáró működése

A RAS-átjáró irányítja a hálózati forgalmat a fizikai hálózat és a virtuális gép hálózati erőforrásai között, a helytől függetlenül. A hálózati forgalmat ugyanazon a fizikai helyen vagy számos különböző helyen is átirányíthatja.

A RAS-átjárót olyan magas rendelkezésre állású készletekben helyezheti üzembe, amelyek egyszerre több funkciót használnak. Az átjárók a RAS-átjáró több példányát tartalmazzák a magas rendelkezésre álláshoz és a feladatátvételhez.

Az átjárók készletében lévő virtuális gépek hozzáadásával vagy eltávolításával egyszerűen méretezheti az átjárókat. Az átjárók eltávolítása vagy hozzáadása nem zavarja meg a készlet által biztosított szolgáltatásokat. Az átjárók teljes készletét is hozzáadhatja és eltávolíthatja. További információ: a [RAS-átjáró magas rendelkezésre állása](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-high-availability).

Minden átjáró készlete M + N redundanciát biztosít. Ez azt jelenti, hogy az Active Gateway-beli virtuális gépek számának biztonsági mentése "N" készenléti átjárót használó virtuális gépek száma. Az M + N redundancia nagyobb rugalmasságot biztosít a RAS-átjáró telepítésekor szükséges megbízhatósági szint meghatározásában.

Egyetlen nyilvános IP-címet is hozzárendelhet az összes készlethez vagy a készletek egy részhalmazához. Ez jelentősen csökkenti a használni kívánt nyilvános IP-címek számát, mivel lehetséges, hogy az összes bérlő egyetlen IP-címen csatlakozik a felhőhöz.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [RAS-átjáró telepítési architektúrája](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Hálózati vezérlő – áttekintés](network-controller-overview.md)
- [A hálózati vezérlő üzembe helyezésének megtervezése](network-controller.md)
- [SDN a Azure Stack HCI-ben és a Windows Serverben](software-defined-networking.md)