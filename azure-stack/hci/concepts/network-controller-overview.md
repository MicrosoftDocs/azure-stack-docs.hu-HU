---
title: A hálózati vezérlő áttekintése Azure Stack HCI-ben
description: Ebből a témakörből megtudhatja, hogyan lehet a hálózati vezérlőt Azure Stack HCI-ben definiált szoftveres hálózatkezeléshez használni.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: f8c559ff409cb6344f20ffa85a60983724ae5b17
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255044"
---
# <a name="what-is-network-controller"></a>Mi az a hálózati vezérlő?

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A hálózati vezérlő a szoftveres hálózatkezelés (SDN) felügyeletének sarokköve. Ez egy jól méretezhető kiszolgálói szerepkör, amely központosított, programozható automatizálási pontot biztosít a virtuális hálózati infrastruktúra kezeléséhez, konfigurálásához, figyeléséhez és hibakereséséhez.

A hálózati vezérlő segítségével automatizálhatja a hálózati infrastruktúra konfigurációját és felügyeletét a hálózati eszközök és szolgáltatások manuális konfigurálásának végrehajtása helyett.

## <a name="how-network-controller-works"></a>A hálózati vezérlő működése

A hálózati vezérlő egy alkalmazásprogramozási felületet (API-t) biztosít, amely lehetővé teszi, hogy a hálózati vezérlő kommunikáljon a hálózati eszközökkel, szolgáltatásokkal és összetevőkkel (irányú API), valamint egy második API-val, amely lehetővé teszi a felügyeleti alkalmazások számára a hálózati vezérlőnek, hogy milyen hálózati beállításokra és szolgáltatásokra van szükségük (irányú API).

A irányú API-val a hálózati vezérlő felügyelheti a hálózati eszközöket és a hálózati szolgáltatásokat, és összegyűjtheti a hálózattal kapcsolatos összes szükséges információt. A hálózati vezérlő folyamatosan figyeli a hálózati eszközök és szolgáltatások állapotát, és gondoskodik arról, hogy a kívánt állapotból való bármilyen konfigurációs eltolódás szervizelve legyen.

A Hálózati vezérlő magasabb szint felé irányuló API REST interfészként van megvalósítva. Lehetővé teszi az adatközpont-hálózat kezelését a felügyeleti alkalmazásokból. A felügyelethez a felhasználók közvetlenül használhatják a REST API, vagy a REST APIra épülő Windows PowerShellt, vagy egy grafikus felhasználói felülettel (például Windows felügyeleti központtal vagy System Center Virtual Machine Manager) rendelkező felügyeleti alkalmazásokat használhatnak.

## <a name="network-controller-features"></a>Hálózati vezérlő funkciói

A hálózati vezérlővel olyan SDN-funkciókat kezelhet, mint például a virtuális hálózatok, a tűzfalak, a szoftverek Load Balancer és a RAS-átjárók. Az alábbiakban a számos funkcióját ismertetjük.

### <a name="virtual-network-management"></a>Virtuális hálózatok kezelése

Ez a hálózati vezérlő funkció lehetővé teszi a Hyper-V hálózati virtualizálás üzembe helyezését és konfigurálását, a virtuális hálózati adapterek konfigurálását az egyes virtuális gépeken, valamint a virtuális hálózati házirendek tárolását és terjesztését. Ezzel a funkcióval virtuális hálózatokat és alhálózatokat hozhat létre, virtuális gépeket csatlakoztathat ezekhez a hálózatokhoz, és engedélyezheti a kommunikációt az azonos virtuális hálózatban lévő virtuális gépek között.

A hálózati vezérlő támogatja a virtuális helyi hálózat (VLAN) alapú hálózatokat, a hálózati virtualizálási általános útválasztási beágyazást (NVGRE) és a virtuális bővíthető helyi hálózatot (VXLAN).

## <a name="firewall-management"></a>Tűzfalak kezelése

Ezzel a hálózati vezérlő szolgáltatással konfigurálhatja és kezelheti az engedélyezési/megtagadási tűzfalat Access Control a munkaterhelési virtuális gépekre vonatkozó szabályokat a belső (keleti/nyugati) és a külső (észak-/déli) hálózati forgalomhoz az adatközpontban. A tűzfalszabályok a munkaterhelési virtuális gépek vSwitch portjába vannak lefoglalva, így az adatközpontban lévő munkaterhelések között oszlanak el, és a munkaterhelésekkel együtt mozognak.

A irányú API segítségével megadhatja a beérkező és a kimenő forgalomhoz tartozó tűzfalszabályok beállításait a munkaterhelés virtuális gépekről. Úgy is konfigurálhatja az egyes tűzfalszabályokat, hogy naplózzák a szabály által engedélyezett vagy tiltott forgalmat.

## <a name="software-load-balancer-management"></a>Szoftver-Load Balancer felügyelet

A [szoftver Load Balancer](software-load-balancer.md) lehetővé teszi több kiszolgáló üzemeltetését ugyanazon számítási feladatok futtatásához, magas rendelkezésre állást és méretezhetőséget biztosítva. A szoftveres Load Balancer használatával konfigurálhatja és kezelheti a terheléselosztást, a bejövő hálózati címfordítást (NAT) és a kimenő hozzáférést az internethez a hagyományos VLAN-hálózatokhoz és virtuális hálózatokhoz kapcsolódó számítási feladatok esetében.

## <a name="gateway-management"></a>Átjárók kezelése

A [távelérési szolgáltatás (RAS) átjárója](gateway-overview.md) lehetővé teszi olyan virtuális gépek üzembe helyezését, konfigurálását és kezelését, amelyek egy átjáró-készlet tagjai, és amelyek külső hálózati kapcsolatot biztosítanak az ügyfelek munkaterheléséhez. Az átjárókkal a következő kapcsolódási típusok támogatottak a virtuális és a távoli hálózatok között:

- Helyek közötti virtuális magánhálózati (VPN) átjáróval létesített kapcsolat IPsec használatával
- Helyek közötti VPN Gateway-kapcsolat általános útválasztási beágyazás (GRE) használatával
- 3. rétegbeli továbbítási képesség
 
Az átjáró-kapcsolatok támogatják a dinamikus útvonalak felügyeletéhez Border Gateway Protocol (BGP).

## <a name="virtual-appliance-chaining"></a>Virtuális berendezés láncolása

Ez a hálózati vezérlő funkció lehetővé teszi virtuális hálózati berendezések csatlakoztatását a virtuális hálózatokhoz. Ezek a készülékek speciális tűzfalakhoz, terheléselosztáshoz, behatolás-észleléshez és-megelőzéshez, valamint számos más hálózati szolgáltatáshoz használhatók. Hozzáadhat olyan virtuális berendezéseket, amelyek felhasználó által megadott útválasztási és port-tükrözési funkciókat hajtanak végre. A felhasználó által megadott útválasztással a virtuális berendezés a virtuális hálózat virtuális alhálózatai közötti útválasztóként lesz használva. A portok tükrözésével a figyelt portot bejelentkező vagy onnan kilépő hálózati forgalom duplikálva lesz, és a rendszer elküldje őket a virtuális készüléknek elemzés céljából.

A felhasználó által megadott útvonalakkal kapcsolatos további tudnivalókért tekintse [meg a hálózati virtuális berendezések használata Virtual Networkon](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)című témakört.

## <a name="network-controller-deployment-considerations"></a>Hálózati vezérlő üzembe helyezésével kapcsolatos megfontolások

- Ne telepítse a hálózati vezérlő kiszolgálói szerepkört a fizikai gazdagépeken. A hálózati vezérlőt saját dedikált virtuális gépekre kell telepíteni.

- A hálózati vezérlőt tartományi és nem tartományi környezetekben is telepítheti. Tartományi környezetekben a hálózati vezérlő Kerberos használatával hitelesíti a felhasználókat és a hálózati eszközöket. a nem tartományi környezetekben tanúsítványokat kell telepítenie a hitelesítéshez.

- Fontos, hogy a hálózati vezérlő üzembe helyezése magas rendelkezésre állást biztosítson, és hogy az adatközpont igényeinek megfelelően könnyedén méretezhető legyen. Legalább három virtuális gépet használjon, hogy magas rendelkezésre állást biztosítson a hálózati vezérlő alkalmazás számára.

- A magas rendelkezésre állás és a méretezhetőség érdekében a hálózati vezérlő Service Fabricra támaszkodik. A Service Fabric méretezhető, megbízható és könnyen kezelhető alkalmazások kialakítására szolgáló elosztott rendszerplatformot biztosít. [További információ a hálózati vezérlőről Service Fabric alkalmazásként](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability#network-controller-as-a-service-fabric-application).


## <a name="next-steps"></a>További lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [A hálózati vezérlő üzembe helyezésének megtervezése](network-controller.md)
- [Hálózati vezérlő telepítése a Windows PowerShell használatával](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts)
- [SDN Azure Stack HCI-ben](software-defined-networking.md)