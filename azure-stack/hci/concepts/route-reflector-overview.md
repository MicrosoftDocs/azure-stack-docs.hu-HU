---
title: A BGP Route-tükrözés áttekintése Azure Stack HCI-ben és a Windows Serverben
description: Ebből a témakörből megtudhatja, hogyan használható a BGP Route-tükröző a szoftverek által meghatározott hálózatkezeléshez Azure Stack HCI-ben és a Windows Serverben.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/02/2021
ms.openlocfilehash: 836931b0efa6c3629123602c141ec5ea35f8aa40
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510839"
---
# <a name="what-is-route-reflector"></a>Mi az az útvonal-tükröző?

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016

A Border Gateway Protocol (BGP) útvonal-tükröző tartalmazza a [távelérési szolgáltatás (RAS) átjáróját](gateway-overview.md) , és alternatívát biztosít az útválasztók közötti útvonal-szinkronizáláshoz szükséges BGP teljes hálós topológiához. A szoftveresen definiált hálózati telepítés útvonal-tükröző egy olyan logikai entitás, amely az RAS-átjárók és a [hálózati vezérlő](network-controller-overview.md)közötti vezérlési síkon helyezkedik el. Azonban nem vesz részt az adatsík-útválasztásban.

## <a name="how-route-reflector-works"></a>Az útvonal-tükrözés működése

A teljes hálós szinkronizálással minden BGP-útválasztónak kapcsolódnia kell az útválasztási topológia összes többi útválasztójának. Az útvonal-tükröző használatakor azonban az útvonal-tükröző az egyetlen olyan útválasztó, amely az összes többi útválasztóhoz csatlakozik, az úgynevezett BGP-útvonal-tükrözési ügyfelekkel, így leegyszerűsíti az útvonal-szinkronizálást és csökkenti a hálózati forgalmat. Az útvonal-tükröző megtanulja az összes útvonalat, kiszámítja a legjobb útvonalakat, és újraterjeszti a legjobb útvonalakat a BGP-ügyfelekre.

Az egyes bérlők távelérési alagutakat úgy is konfigurálhatja, hogy egynél több RAS Gateway virtuális gépen (VM) legyenek lemondva. Ez nagyobb rugalmasságot biztosít a felhőalapú szolgáltatók (CSP-EK) számára olyan helyzetekben, amikor az egyik RAS-átjáró virtuális gép nem tudja kielégíteni a bérlői kapcsolatok sávszélességre vonatkozó követelményeit.

Ez a képesség azonban bevezeti az útvonal-kezelés és az útvonalak hatékony szinkronizálásának további bonyolultságát a bérlői távoli helyek és a felhőalapú adatközpontban lévő virtuális erőforrásaik között. Ha a bérlők több RAS-átjáróval létesített kapcsolattal rendelkeznek, a vállalati végén további bonyolultságot is biztosít a konfigurációban, ahol minden egyes bérlői hely külön útválasztási szomszédokkal fog rendelkezni.

Az ellenőrzési síkon található BGP Route-tükröző kezeli ezeket a problémákat, és a CSP belső háló üzembe helyezését a vállalati bérlők számára transzparensvé teszi.

- Amikor új bérlőt ad hozzá az adatközponthoz, a hálózati vezérlő automatikusan konfigurálja az első bérlői RAS-átjárót útvonal-tükröző.

- Az egyes bérlők megfelelő útvonal-tükrözéssel rendelkeznek, és az adott bérlőhöz társított RAS Gateway-beli virtuális gépek egyikén találhatók.

- A bérlői útvonal-tükröző a bérlőhöz társított összes RAS Gateway-beli virtuális gép útvonal-fényvisszaverő működik. A RAS Gateway útvonal-tükrözésen kívüli bérlői átjárók az útvonal-tükrözési ügyfelek. Az útvonal-tükröző átirányítja az útvonal-szinkronizálást az összes útvonal-tükrözési ügyfél között, így a tényleges adatútvonal-útválasztás is megjelenhet.

- Az útvonal-tükröző nem biztosít szolgáltatásokat azon RAS-átjárón, amelyen konfigurálva van.

- Az útvonal-tükröző frissíti a hálózati vezérlőt a bérlő vállalati helyeinek megfelelő vállalati útvonalakkal. Ez lehetővé teszi, hogy a hálózati vezérlő konfigurálja a szükséges Hyper-V hálózati virtualizálási házirendeket a bérlői virtuális hálózaton a végpontok közötti adatelérési út eléréséhez.

- Ha a vállalati ügyfelek BGP-útválasztást használnak az ügyfél címterület területén, a RAS Gateway Route-tükröző az egyetlen külső BGP-(eBGP-) szomszéd a megfelelő bérlő összes helyéhez. Ez a vállalati bérlő alagút-megszakítási pontjaitól függetlenül igaz. Más szóval, függetlenül attól, hogy a CSP-adatközpontban lévő RAS Gateway virtuális gép leáll-e a helyek közötti VPN-alagút a bérlői helyhez, az összes bérlői hely eBGP-társa az útvonal-tükröző.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [A RAS-átjáró áttekintése](gateway-overview.md)
- [RAS-átjáró telepítési architektúrája](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Az Internet Engineering Task Force (IETF) kérelme megjegyzésekkel foglalkozó témakör RFC 4456 BGP Route Reflection: a teljes Mesh belső BGP alternatívája](https://tools.ietf.org/html/rfc4456)