---
title: IDNS használata Azure Stack hub-ban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a iDNS szolgáltatásait és képességeit Azure Stack hub-ban.
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: Justinha
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 8bdadb4e0cd36c6e650b001585aa3519293a9247
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883252"
---
# <a name="use-idns-in-azure-stack-hub"></a>IDNS használata Azure Stack központban 

a iDNS egy Azure Stack hub hálózati szolgáltatás, amely lehetővé teszi a külső DNS-nevek (például https:\//www.bing.com) feloldását. Lehetővé teszi a belső virtuális hálózatok nevének regisztrálását is. Ezzel az IP-cím helyett a virtuális gépeket (VM-ket) az ugyanazon a virtuális hálózaton lehet feloldani. Ez a megközelítés eltávolítja az egyéni DNS-kiszolgáló bejegyzéseinek megadásához szükséges adatokat. További információ a DNS-ről: [Azure DNS Overview (áttekintés](https://docs.microsoft.com/azure/dns/dns-overview)).

## <a name="what-does-idns-do"></a>Mit tesz a iDNS?

Az Azure Stack hub iDNS szolgáltatásában a következő képességeket kapja, anélkül, hogy egyéni DNS-kiszolgáló bejegyzéseket kellene megadnia:

- Megosztott DNS-névfeloldási szolgáltatások bérlői számítási feladatokhoz.
- Mérvadó DNS-szolgáltatás a névfeloldáshoz és a DNS-regisztrációhoz a bérlői virtuális hálózaton belül.
- Rekurzív DNS szolgáltatás a bérlői virtuális gépek internetes neveinek feloldásához. A bérlőknek többé nem kell egyéni DNS-bejegyzéseket megadniuk az internetes nevek feloldásához (például: www\.bing.com.)

Továbbra is használhatja a saját DNS-t, és használhat egyéni DNS-kiszolgálókat. A iDNS használatával azonban megoldhatók az internetes DNS-nevek, és az azonos virtuális hálózatban lévő más virtuális gépekhez is csatlakozhatnak anélkül, hogy egyéni DNS-bejegyzéseket kellene létrehoznia.

## <a name="what-doesnt-idns-do"></a>Mi nem a iDNS?

a iDNS nem teszi lehetővé, hogy olyan nevekhez hozzon létre DNS-rekordot, amelyek a virtuális hálózaton kívülről oldhatók fel.

Az Azure-ban lehetősége van egy nyilvános IP-címhez társított DNS-név címke megadására. Kiválaszthatja a címkét (előtag), de az Azure kiválasztja az utótagot, amely azon a régión alapul, amelyben a nyilvános IP-címet létrehozta.

![DNS-név címkéje – példa](media/azure-stack-understanding-dns-in-tp2/image3.png)

Ahogy az előző képen is látható, az Azure létrehoz egy "A" rekordot a DNS-ben a zóna **westus.cloudapp.Azure.com**megadott DNS-név címkéjén. Az előtag és az utótag össze van egyesítve egy [teljes tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), amely a nyilvános internetről bárhonnan oldható fel.

Azure Stack hub csak a belső nevek regisztrálására szolgáló iDNS-t támogatja, így a következő dolgok nem hajthatók végre:

- DNS-rekord létrehozása meglévő üzemeltetett DNS-zónában (például local. azurestack. external)
- Hozzon létre egy DNS-zónát (például Contoso.com.)
- Hozzon létre egy rekordot a saját egyéni DNS-zónájában.
- Támogatja a tartománynevek megvásárlását.

## <a name="demo-of-how-idns-works"></a>A iDNS működésének bemutatója

A virtuális hálózatokban lévő virtuális gépek összes állomásneve ugyanabban a zónában található DNS-erőforrásrekordokként van tárolva, azonban a saját egyedi rekeszük alapján, amely az SDN-infrastruktúra azon VNET-azonosítójával összefügg, amelyet a virtuális gép üzembe helyezett. A bérlői virtuális gépek teljes tartományneve (FQDN) a számítógép nevét és a DNS-utótag karakterláncát tartalmazza a Virtual Network GUID formátumban.

<!--- what does compartment mean? Add a screenshot? can we clarify what we mean by host name and computer name. the description doesn't match the example in the table.--->
 
A következő egy egyszerű labor, amely bemutatja, hogyan működik ez a funkció. Három virtuális gépet hoztunk létre egy VNet és egy másik virtuális gépen egy külön VNet:

<!--- Is DNS Label the right term? If so, we should define it. The column lists FQDNs, afaik. Where does the domain suffix come from? --->
 
|VM    |vNet    |Magánhálózati IP-cím   |Nyilvános IP-cím    | DNS-címke                                |
|------|--------|-------------|-------------|------------------------------------------|
|VM – A1 |Társviszonyban áll   | 10.0.0.5    |172.31.12.68 |VM-a1-Label. lnv1. cloudapp. azscss. external |
|VM-A2 |Társviszonyban áll   | 10.0.0.6    |172.31.12.76 |VM-a2-Label. lnv1. cloudapp. azscss. external |
|VM – A3 |Társviszonyban áll   | 10.0.0.7    |172.31.12.49 |VM-a3-Label. lnv1. cloudapp. azscss. external |
|VIRTUÁLIS GÉP – B1 |B   | 10.0.0.4    |172.31.12.57 |VM-B1-Label. lnv1. cloudapp. azscss. external |
 
 
|VNet  |GUID                                 |DNS-utótag karakterlánca                                                  |
|------|-------------------------------------|-------------------------------------------------------------------|
|Társviszonyban áll |e71e1db5-0a38-460d-8539-705457a4cf75 |e71e1db5-0a38-460d-8539-705457a4cf75. internal. lnv1. azurestack. local|
|B |e8a6e386-bc7a-43e1-a640-61591b5c76dd |e8a6e386-bc7a-43e1-a640-61591b5c76dd. internal. lnv1. azurestack. local|
 
 
Megteheti a névfeloldási teszteket, hogy jobban megértse, hogyan működik az iDNS:

<!--- why Linux?--->

Virtuális gép – a1 (Linux rendszerű virtuális gép): a virtuális gép (a2) megkeresése. Láthatja, hogy a rendszer hozzáadja a Társviszonyban áll DNS-utótagját, és a nevet feloldja a magánhálózati IP-címhez:
 
```console
carlos@VM-A1:~$ nslookup VM-A2
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2.e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local
Address: 10.0.0.6
```
 
A virtuális gép-a2-Label megkeresése a teljes tartománynév megadása nélkül sikertelen, a várt módon:

```console 
carlos@VM-A1:~$ nslookup VM-A2-Label
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-A2-Label: SERVFAIL
```

Ha a DNS-címke teljes tartománynevét adja meg, a név a nyilvános IP-címhez lesz feloldva:

```console
carlos@VM-A1:~$ nslookup VM-A2-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.76
```
 
A virtuális gép (azaz egy másik VNet származó) feloldására tett kísérlet sikertelen, mert ez a rekord nem létezik ezen a zónán.

```console
carlos@caalcobi-vm4:~$ nslookup VM-B1
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1: SERVFAIL
```

A virtuális gép teljes tartománynevének használata – a B1 nem segít, mivel ez a rekord egy másik zónából származik.

```console 
carlos@VM-A1:~$ nslookup VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local: SERVFAIL
```
 
Ha a DNS-címke teljes tartománynevét használja, a rendszer sikeresen feloldja a következőket:

``` 
carlos@VM-A1:~$ nslookup VM-B1-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-B1-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.57
```
 
VIRTUÁLIS gépről – a3 (Windows virtuális gép). Figyelje meg a mérvadó és nem mérvadó válaszok közötti különbséget.

Belső rekordok:

```console
C:\Users\carlos>nslookup
Default Server:  UnKnown
Address:  168.63.129.16
 
> VM-A2
Server:  UnKnown
Address:  168.63.129.16
 
Name:    VM-A2.e71e1db5-0a38-460d-8539-705457ª4cf75.internal.lnv1.azurestack.local
Address:  10.0.0.6
```

Külső rekordok:

```console
> VM-A2-Label.lnv1.cloudapp.azscss.external
Server:  UnKnown
Address:  168.63.129.16
 
Non-authoritative answer:
Name:    VM-A2-Label.lnv1.cloudapp.azscss.external
Address:  172.31.12.76
``` 
 
Röviden, a fentiek közül a következőt láthatja:
 
*   Mindegyik VNet saját zónával rendelkezik, amely minden magánhálózati IP-címhez tartalmaz egy rekordot, amely a virtuális gép nevét és a VNet DNS-utótagját (a GUID azonosítóját) tartalmazza.
    *   \<vmname >.\<vnetGUID\>. internal.\<régió >.\<stackinternalFQDN >
    *   Ez automatikusan történik
*   Ha nyilvános IP-címeket használ, létrehozhat DNS-címkéket is. Ezek a többi külső címnek hasonlóan oldhatók meg.
 
 
- az iDNS-kiszolgálók a belső DNS-zónákhoz tartozó mérvadó kiszolgálók, és a nyilvános nevek feloldására is használhatók, amikor a bérlői virtuális gépek külső erőforrásokhoz próbálnak csatlakozni. Ha van lekérdezés egy külső erőforráshoz, a iDNS-kiszolgálók továbbítják a kérést a mérvadó DNS-kiszolgálókra a feloldáshoz.
 
Ahogy a labor eredményei közül láthatja, megadhatja, hogy milyen IP-címet használ. Ha a virtuális gép nevét használja, akkor megkapja a magánhálózati IP-címet, és ha a DNS-címkét használja, akkor a nyilvános IP-címet kapja meg.

## <a name="next-steps"></a>Következő lépések

[DNS használata Azure Stack központban](azure-stack-dns.md)
