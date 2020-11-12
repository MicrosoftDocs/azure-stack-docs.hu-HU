---
title: Virtuális hálózati társítás Azure Stack hub-ban
description: Ismerje meg, hogyan csatlakoztathatja a virtuális hálózatokat a virtuális hálózatok Azure Stack hub-beli összekapcsolásához.
author: sethmanheim
ms.author: sethm
ms.date: 11/11/2020
ms.topic: conceptual
ms.reviewer: sranthar
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 16d7701161b8ec8c16aa3caecf5d5e291d6c0e99
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548665"
---
# <a name="virtual-network-peering"></a>Virtual Network peering

A virtuális hálózatok közötti kapcsolat lehetővé teszi, hogy a virtuális hálózatokat zökkenőmentesen összekapcsolja egy Azure Stack hub-környezetben. A virtuális hálózatok a kapcsolati céloknak megfelelően jelennek meg. A virtuális gépek közötti forgalom a mögöttes SDN-infrastruktúrát használja. Az azonos hálózatban lévő virtuális gépek közötti forgalomhoz hasonlóan a forgalom csak az Azure Stack hub magánhálózati hálózatán keresztül történik.

Azure Stack hub nem támogatja a globális társítást, mivel a "régiók" fogalma nem érvényes.

A virtuális hálózati társítás használatának előnyei a következők:

- Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
- Egy virtuális hálózat erőforrásainak egy másik virtuális hálózat erőforrásaival való kommunikációjának képessége.
- Az adatok átvitelének lehetősége a virtuális hálózatok között különböző előfizetésekben és Azure Active Directory bérlők között.
- Ez nem okoz leállást egyik virtuális hálózat erőforrásaiban sem a társításkor és azt követően sem.

A társított virtuális hálózatok közti hálózati adatforgalom nem nyilvános. A virtuális hálózatok közötti adatforgalmat az infrastruktúra-réteg tárolja. A virtuális hálózatok közötti kommunikációhoz nincs szükség nyilvános internetre, átjáróra vagy titkosításra.

## <a name="connectivity"></a>Kapcsolat

A egyenrangú virtuális hálózatok esetében bármelyik virtuális hálózat erőforrásai közvetlenül kapcsolódhatnak a virtuális hálózatban lévő erőforrásokhoz.

Az azonos régióban lévő virtuális társhálózaton belüli virtuális gépek közötti hálózati késés megegyezik az egyetlen virtuális hálózaton belüli hálózati késéssel. A hálózat átbocsátóképessége attól függ, hogy milyen, a virtuális gépek méretével arányos sávszélesség van engedélyezve. A társviszonyon belül más korlátozás nem vonatkozik a sávszélességre.

A virtuális gépek közötti forgalmat a rendszer közvetlenül az SDN-rétegen keresztül irányítja át, nem átjárón vagy a nyilvános interneten keresztül.

Bármelyik virtuális hálózaton hálózati biztonsági csoportokat is alkalmazhat, hogy letiltsa a hozzáférést más virtuális hálózatokhoz vagy alhálózatokhoz. A virtuális hálózati kapcsolatok konfigurálásakor nyissa meg vagy zárjunk be a hálózati biztonsági csoport szabályait a virtuális hálózatok között. Ha megnyitja a teljes kapcsolatot a kihelyezett virtuális hálózatok között, akkor a hálózati biztonsági csoportok alkalmazásával blokkolhatja vagy megtagadhatja a megadott hozzáférést. A teljes kapcsolat az alapértelmezett beállítás. A hálózati biztonsági csoportokkal kapcsolatos további tudnivalókért tekintse meg a [biztonsági csoportok](/azure/virtual-network/security-overview)című témakört.

## <a name="service-chaining"></a>Szolgáltatásláncolás

A szolgáltatás-láncolás lehetővé teszi, hogy az egyik virtuális hálózatról egy virtuális készülékre vagy átjáróra irányítsa át a forgalmat a felhasználó által megadott útvonalakon keresztül.

A szolgáltatások láncolásának engedélyezéséhez olyan felhasználó által megadott útvonalakat konfigurálhat, amelyek a *következő ugrás* IP-címének megfelelően a virtuális gépekre mutatnak.

Központi *és küllős* hálózatokat is telepíthet, ahol a hub virtuális hálózat infrastruktúra-összetevőket, például hálózati virtuális berendezést vagy VPN-átjárót üzemeltet. Az ágakon lévő virtuális hálózatok társhálózatai lehetnek a középponti hálózatnak. A forgalom a hub virtuális hálózatán keresztül áramlik a hálózati virtuális berendezéseken vagy a VPN-átjárón keresztül.

A virtuális hálózati társítás lehetővé teszi, hogy a következő ugrás egy felhasználó által megadott útvonalon legyen a virtuális gép IP-címe a társ virtuális hálózaton. A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd a [felhasználó által megadott útvonalak áttekintését](/azure/virtual-network/virtual-networks-udr-overview#user-defined). A sugaras hálózati topológia létrehozásával kapcsolatos információkért lásd: [sugaras hálózati topológia az Azure-ban](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Átjárók és kapcsolat helyszíni rendszerekkel

Minden virtuális hálózatnak, beleértve a társ virtuális hálózatot, saját átjáróval is rendelkezhet. A virtuális hálózatok használhatják az átjárót a helyszíni hálózathoz való kapcsolódáshoz. Tekintse át a [Virtual Network átjáró dokumentációját](/azure/vpn-gateway/).

Azt is megteheti, hogy az átjárót a társ virtuális hálózatban is konfigurálhatja egy helyszíni hálózatra irányuló továbbítási pontként. Ebben az esetben a távoli átjárót használó virtuális hálózatnak nem lehet saját átjárója. Egy virtuális hálózatnak csak egy átjárója van. Az átjáró vagy egy helyi vagy távoli átjáró a társ virtuális hálózaton, az alábbi ábrán látható módon:

:::image type="content" source="media/virtual-network-peering/virtual-network-gateway.png" alt-text="VPN Gateway-topológia":::

Vegye figyelembe, hogy a **kapcsolati** objektumot a VPN-átjárón kell létrehozni, mielőtt engedélyezi a **UseRemoteGateways** beállításait a társításban.

## <a name="virtual-network-peering-configuration"></a>Virtuális hálózat társításának konfigurációja

**Virtuális hálózati hozzáférés engedélyezése:** A virtuális hálózatok közötti kommunikáció engedélyezése lehetővé teszi, hogy a virtuális hálózathoz csatlakoztatott erőforrások ugyanazzal a sávszélességgel és késéssel kommunikáljanak egymással, mintha ugyanahhoz a virtuális hálózathoz csatlakoznak. A két virtuális hálózat erőforrásai közötti összes kommunikáció a belső SDN-rétegen keresztül történik.  

A hálózati hozzáférés használatának egyik oka az lehet, hogy a virtuális hálózatot egy másik virtuális hálózattal társítják, de alkalmanként le szeretné tiltani a két virtuális hálózat közötti forgalmat. Előfordulhat, hogy az engedélyezés/letiltás sokkal kényelmesebb, mint a társítások törlése és újbóli létrehozása. Ha ez a beállítás le van tiltva, a forgalom nem áramlik a kiszolgált virtuális hálózatok között.

**Továbbított forgalom engedélyezése:** Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné, hogy egy virtuális hálózat (amely nem a virtuális hálózatból származik) hálózati virtuális berendezése *továbbítsa* a forgalmat a virtuális hálózatra egy társon keresztül. Tegyük fel például, hogy három virtuális hálózat neve Spoke1, Spoke2 és hub. Az egyes küllős virtuális hálózatok és a hub virtuális hálózat között egy társítás létezik, de a küllős virtuális hálózatok között nem léteznek társítások. Egy hálózati virtuális berendezést helyeznek üzembe a központi virtuális hálózaton, és a felhasználó által megadott útvonalak minden olyan küllős virtuális hálózatra vonatkoznak, amely az alhálózatok közötti forgalmat a hálózati virtuális berendezésen keresztül irányítja. Ha ez a jelölőnégyzet nincs bejelölve az egyes küllős virtuális hálózatok és a hub virtuális hálózat között, a forgalom nem áramlik a küllős virtuális hálózatok között, mert a hub nem továbbítja a forgalmat a virtuális hálózatok között. Ha engedélyezi ezt a lehetőséget, a továbbított forgalom a társításon keresztül is lehetővé válik, nem hoz létre felhasználó által megadott útvonalakat vagy hálózati virtuális berendezéseket. A felhasználó által megadott útvonalak és hálózati virtuális berendezések külön jönnek létre. Ismerje meg a [felhasználó által megadott útvonalakat](/azure/virtual-network/virtual-networks-udr-overview#user-defined). Ezt a beállítást nem kell megadnia, ha a forgalmat egy VPN Gateway keresztül továbbítják a virtuális hálózatok között.

**Átjáró továbbításának engedélyezése:** Jelölje be ezt a jelölőnégyzetet, ha egy virtuális hálózati átjáró csatlakozik ehhez a virtuális hálózathoz, és engedélyezni szeretné, hogy a virtuális hálózatról érkező forgalom áthaladjon az átjárón keresztül. Ezt a virtuális hálózatot például egy virtuális hálózati átjárón keresztül lehet csatlakoztatni egy helyszíni hálózathoz. Ha bejelöli ezt a jelölőnégyzetet, a rendszer átirányítja a virtuális hálózatról a helyszíni hálózatra csatlakozó átjárón keresztül érkező forgalmat. Ha bejelöli ezt a jelölőnégyzetet, a társ virtuális hálózat nem rendelkezhet konfigurált átjáróval. A társ virtuális hálózatnak be kell jelölnie a **távoli átjárók használata** jelölőnégyzetet a másik virtuális hálózatról a virtuális hálózatra való társítás beállításakor. Ha a jelölőnégyzet nincs bejelölve (az alapértelmezett érték), a virtuális hálózatról érkező forgalom továbbra is erre a virtuális hálózatra kerül, de a virtuális hálózathoz csatlakoztatott virtuális hálózati átjárón keresztül nem végezhető el folyamat.

**Távoli átjárók használata:** Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné, hogy a virtuális hálózatról érkező forgalom olyan virtuális hálózati átjárón keresztül folyjon, amelyhez a társítást végzi. Például a társításhoz használt virtuális hálózat rendelkezik egy olyan VPN-átjáróval, amely lehetővé teszi a kommunikációt a helyszíni hálózattal. Ha bejelöli ezt a jelölőnégyzetet, a virtuális hálózatról érkező forgalom áthaladhat a társ virtuális hálózathoz csatlakoztatott VPN-átjárón keresztül. Ha bejelöli ezt a jelölőnégyzetet, a csatlakoztatott virtuális hálózatnak hozzá kell rendelnie egy virtuális hálózati átjárót, és be kell jelölnie az **átjáró engedélyezése** jelölőnégyzetet. Ha nem jelöli be ezt a jelölőnégyzetet (az alapértelmezett érték), a kihelyezett virtuális hálózatról érkező forgalom továbbra is a virtuális hálózatra áramlik, de a virtuális hálózathoz csatlakoztatott virtuális hálózati átjárón keresztül nem végezhető el folyamat.

Távoli átjárók nem használhatók, ha már van konfigurálva átjáró a virtuális hálózaton.

### <a name="permissions"></a>Engedélyek

Győződjön meg arról, hogy a különböző előfizetésekben és az Azure AD-bérlőben található virtuális hálózatok rendelkező összevonások létrehozásakor a fiókokhoz hozzá van rendelve a **közreműködői** szerepkör. Emellett nincs felhasználói felületi képesség a különböző Azure AD-bérlők közötti együttműködéshez. A társítások létrehozásához használhatja az Azure CLI-t és a PowerShellt.

## <a name="virtual-network-peering-frequently-asked-questions-faq"></a>Virtuális hálózatokkal kapcsolatos gyakran ismételt kérdések (GYIK)

### <a name="what-is-virtual-network-peering"></a>Mi az a Virtual Network peering?

A virtuális hálózatok közötti kapcsolat lehetővé teszi a virtuális hálózatok összekapcsolását. A virtuális hálózatok közötti VNet-társítási kapcsolat lehetővé teszi, hogy a forgalmat az IPv4-címeken keresztül továbbítsa egymás között. A társ-virtuális hálózatok található virtuális gépek kommunikálhatnak egymással, mintha ugyanazon a hálózaton belül vannak. A VNet-társítási kapcsolatok több előfizetésben is létrehozhatók.

### <a name="does-azure-stack-hub-support-global-vnet-peering"></a>Támogatja a Azure Stack hub a globális VNET-társítást?

Azure Stack hub nem támogatja a globális társítást, mivel a "régiók" fogalma nem érvényes.

### <a name="on-which-azure-stack-hub-update-will-virtual-network-peering-be-available"></a>Mely Azure Stack hub-frissítés lesz elérhető a virtuális hálózatok között?

a virtuális hálózati társítás a 2008-es frissítéstől kezdődően Azure Stack központban érhető el.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub-to-a-virtual-network-in-azure"></a>Használhatom a virtuális hálózatot Azure Stack hub-ban egy Azure-beli virtuális hálózatra?

Nem, az Azure és az Azure Stack hub közötti társítás jelenleg nem támogatott.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub1-to-a-virtual-network-in-azure-stack-hub2"></a>Használhatom a virtuális hálózatot Azure Stack Hub1 egy Azure Stack Hub2 lévő virtuális hálózatra?

Nem, a társítás csak egy Azure Stack hub-rendszeren lévő virtuális hálózatok között hozható létre. További információ a két virtuális hálózat különböző bélyegzőből való csatlakoztatásáról: [VNET létrehozása VNET-kapcsolathoz Azure stack hub-ban](azure-stack-network-howto-vnet-to-vnet-stacks.md).

### <a name="can-i-enable-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Engedélyezhető a társítás, ha a virtuális hálózatok a különböző Azure Active Directory bérlőn belüli előfizetésekhez tartoznak?

Igen. Ha az előfizetések különböző Azure Active Directory bérlőhöz tartoznak, VNet-társítást lehet létrehozni. Ezt megteheti a PowerShell vagy a parancssori felület használatával. A portál még nem támogatott.

### <a name="can-i-peer-my-virtual-network-with-a-virtual-network-in-a-different-subscription"></a>Használhatom a virtuális hálózatot egy másik előfizetésben található virtuális hálózattal?

Igen. Az előfizetések között egyenrangú virtuális hálózatokat is használhat.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Van-e sávszélességre vonatkozó korlátozás a társi kapcsolatok esetében?

Nem. A virtuális hálózat társítása semmilyen sávszélesség-korlátozást nem ír elő. A sávszélességet csak a virtuális gép vagy a számítási erőforrás korlátozza.

### <a name="my-virtual-network-peering-connection-is-in-an-initiated-state-why-cant-i-connect"></a>A virtuális hálózati társítási kapcsolat egy *kezdeményezett* állapotban van, miért nem tudok csatlakozni?

Ha a társ-kapcsolat **kezdeményezett** állapotban van, akkor az azt jelenti, hogy csak egy hivatkozást hozott létre. Sikeres kapcsolat létrehozásához kétirányú hivatkozást kell létrehozni. Ha például a VNet-VNet A B csoportba kívánja kapcsolni, létre kell hoznia egy hivatkozást az A VNet-VNet B-re, a B VNet pedig a VNet. **Connected**

### <a name="my-virtual-network-peering-connection-is-in-a-disconnected-state-why-cant-i-create-a-peering-connection"></a>A virtuális hálózati társítás kapcsolata *leválasztott* állapotban van, miért nem hozható létre egyenrangú kapcsolat?

Ha a virtuális hálózati társítás kapcsolata **leválasztott** állapotban van, az azt jelenti, hogy az egyik létrehozott hivatkozás törölve lett. Ha újra létre szeretne hozni egy társ-kapcsolatot, törölje a hivatkozást, és hozza létre újból.

### <a name="is-virtual-network-peering-traffic-encrypted"></a>Titkosítva van-e a virtuális hálózati partneri forgalom?

Nem. A megosztott virtuális hálózatok erőforrásai közötti forgalom magán-és elkülönített. Teljes egészében az Azure Stack hub System SDN rétegében marad.

### <a name="if-i-peer-vnet-a-to-vnet-b-and-i-peer-vnet-b-to-vnet-c-does-that-mean-vnet-a-and-vnet-c-are-peered"></a>Ha a társam VNet A B VNet, és a B társ VNet a C VNet, akkor ez azt jelenti, hogy az A és A VNet C?

Nem. A tranzitív peering nem támogatott. Az A és A VNet C VNet kell lennie.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Virtual Network bemutatása](/azure/virtual-network/virtual-networks-overview)
- [A felhasználó által megadott útvonalak áttekintése](/azure/virtual-network/virtual-networks-udr-overview#user-defined)
- [Küllős hálózati topológia az Azure-ban](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
