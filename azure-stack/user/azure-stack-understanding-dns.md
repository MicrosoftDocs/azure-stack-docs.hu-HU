---
title: IDNS használata a Azure Stackban | Microsoft Docs
description: Ismerje meg, hogyan használhatja a iDNS szolgáltatásait és funkcióit Azure Stackban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 6dda8562e47f17c97da5e0597a2ed88865bc6425
ms.sourcegitcommit: 82d09bbae3e5398d2fce7e2f998dfebff018716c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497856"
---
# <a name="use-idns-in-azure-stack"></a>IDNS használata Azure Stack 

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

a iDNS egy Azure stack hálózati szolgáltatás, amely lehetővé teszi a külső DNS-nevek (például https:\//www.Bing.com) feloldását. Lehetővé teszi a belső virtuális hálózatok nevének regisztrálását is. Ezzel az IP-cím helyett a virtuális gépeket (VM-ket) az ugyanazon a virtuális hálózaton lehet feloldani. Ez a megközelítés eltávolítja az egyéni DNS-kiszolgáló bejegyzéseinek megadásához szükséges adatokat. További információ a DNS-ről: [Azure DNS Overview (áttekintés](https://docs.microsoft.com/azure/dns/dns-overview)).

## <a name="what-does-idns-do"></a>Mit tesz a iDNS?

A Azure Stackban a iDNS a következő képességeket kapja, anélkül, hogy egyéni DNS-kiszolgáló bejegyzéseket kellene megadnia:

- Megosztott DNS-névfeloldási szolgáltatások bérlői számítási feladatokhoz.
- Mérvadó DNS-szolgáltatás a névfeloldáshoz és a DNS-regisztrációhoz a bérlői virtuális hálózaton belül.
- Rekurzív DNS szolgáltatás a bérlői virtuális gépek internetes neveinek feloldásához. A bérlőknek többé nem kell egyéni DNS-bejegyzéseket megadniuk az internetes nevek feloldásához (\.például www-Bing.com).

Továbbra is használhatja a saját DNS-t, és használhat egyéni DNS-kiszolgálókat. A iDNS használatával azonban megoldhatók az internetes DNS-nevek, és az azonos virtuális hálózatban lévő más virtuális gépekhez is csatlakozhatnak anélkül, hogy egyéni DNS-bejegyzéseket kellene létrehoznia.

## <a name="what-doesnt-idns-do"></a>Mi nem a iDNS?

a iDNS nem teszi lehetővé, hogy olyan nevekhez hozzon létre DNS-rekordot, amelyek a virtuális hálózaton kívülről oldhatók fel.

Az Azure-ban lehetősége van egy nyilvános IP-címhez társított DNS-név címke megadására. Kiválaszthatja a címkét (előtag), de az Azure kiválasztja az utótagot, amely azon a régión alapul, amelyben a nyilvános IP-címet létrehozta.

![DNS-név címkéje – példa](media/azure-stack-understanding-dns-in-tp2/image3.png)

Ahogy az előző képen is látható, az Azure létrehoz egy "A" rekordot a DNS-ben a zóna **westus.cloudapp.Azure.com**megadott DNS-név címkéjén. Az előtag és az utótag össze van egyesítve egy [teljes tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), amely a nyilvános internetről bárhonnan oldható fel.

Azure Stack csak a belső nevek regisztrálásához támogatja a iDNS-t, így a következő dolgok nem hajthatók végre:

- DNS-rekord létrehozása meglévő üzemeltetett DNS-zónában (például local. azurestack. external)
- Hozzon létre egy DNS-zónát (például Contoso.com.)
- Hozzon létre egy rekordot a saját egyéni DNS-zónájában.
- Támogatja a tartománynevek megvásárlását.

## <a name="next-steps"></a>További lépések

[DNS használata az Azure Stackben](azure-stack-dns.md)
