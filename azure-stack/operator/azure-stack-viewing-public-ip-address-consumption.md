---
title: Nyilvános IP-címek használatának megtekintése az Azure Stackben |} A Microsoft Docs
description: A rendszergazdák megtekinthetik a használatalapú nyilvános IP-címek a régióban
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 35dc40fc3539038ab3c44318374e8c1fb327e6e4
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782435"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Nyilvános IP-címek használatának megtekintése az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Felhő-rendszergazdák tekintheti meg:
 - A bérlők számára lefoglalt nyilvános IP-címek száma.
 - Továbbra is elérhető kiosztható nyilvános IP-címek száma.
 - A százalékos aránya, amelyek az adott helyen lefoglalt nyilvános IP-címek.

A **nyilvános IP-készletek használati** csempe több nyilvános IP-címkészletek felhasznált nyilvános IP-címek számát jeleníti meg. Az egyes IP-címet a csempe bérlő IaaS virtuális gépek használati mutatja, példány, a fabric infrastruktúra-szolgáltatások és a bérlők által explicit módon létrehozott nyilvános IP-cím erőforrások.

A csempe célja biztosíthat az Azure Stack-operátorok érteni, ezen a helyen használt nyilvános IP-címek számát. A szám segítségével a rendszergazda határozza meg, hogy, kevés a ehhez az erőforráshoz.

A **nyilvános IP-címek** menüpont alatt **bérlői erőforrások** csak ezen nyilvános IP-címek listája, amelyeket *explicit módon a bérlők által létrehozott*. A menüelem megtalálhatja a **erőforrás-szolgáltatók**, **hálózati** ablaktáblán. Száma **használható** nyilvános IP-címek a a **nyilvános IP-készletek használati** csempe mindig eltér a (nagyobb) a száma a **nyilvános IP-címek** alatt csempe **A bérlői erőforrások**.

## <a name="view-the-public-ip-address-usage-information"></a>A nyilvános IP-cím használati adatok megtekintése

A régió felhasznált nyilvános IP-címek teljes száma megtekintése:

1. Válassza ki az Azure Stack rendszergazdai portálon **minden szolgáltatás**. Ezt követően a **felügyeleti** kategória kiválasztása **hálózati**.
1. A **hálózati** ablaktáblán megjelennek azok a **nyilvános IP-készletek használati** csempéje a **áttekintése** szakaszban.

![Hálózati erőforrás-szolgáltató panel](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

A **használható** számot jelöli száma hozzárendelt nyilvános IP-címeket nyilvános IP-címkészleteket. A **ingyenes** szám azt jelzi, a nyilvános IP-címek számának a nyilvános IP-cím címkészletek, amely nincs hozzárendelve, és továbbra is elérhető. A **% használható** szám számát jelöli, használt vagy címek hozzárendelve százalékos arányában nyilvános IP-címkészletek az adott helyen lévő nyilvános IP-címek teljes száma.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Bérlői előfizetések által létrehozott nyilvános IP-címek megtekintése

Válassza ki **nyilvános IP-címek** alatt **bérlői erőforrások**. Tekintse át az explicit módon egy adott régióban bérlői előfizetések által létrehozott nyilvános IP-címek listáját.

![Bérlő nyilvános IP-címek](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Észreveheti, hogy néhány dinamikusan lefoglalt nyilvános IP-címek szerepelnek a listán. Azonban egy cím még nem hozzájuk tartozó még. A cím erőforrás létrejött, a hálózati erőforrás-szolgáltató, de még nem a hálózatvezérlő.

A hálózati vezérlő nem rendel hozzá egy címet az erőforrás mindaddig, amíg egy felületet, a hálózati kártya (NIC), egy terheléselosztó vagy a virtuális hálózati átjáró van kötve. Ha egy felület nyilvános IP-cím van kötve, a hálózati vezérlő IP-címet foglal le. A cím jelenik meg a **cím** mező.

## <a name="view-the-public-ip-address-information-summary-table"></a>A nyilvános IP-információk összegzése címtábláján megtekintése

Eltérő esetben, amelyek meghatározzák, hogy a cím megjelenik egy listában, vagy egy másik nyilvános IP-címek vannak hozzárendelve.

| **Nyilvános IP-cím hozzárendelés eset** | **Megjelenik a használat összegzése** | **A bérlő nyilvános IP-címek listája jelenik meg** |
| --- | --- | --- |
| Még nem rendelt (ideiglenes) egy hálózati adapter vagy terheléselosztó dinamikus nyilvános IP-cím |Nem |Igen |
| A dinamikus nyilvános IP-cím rendelve egy hálózati adapter vagy a load balancert. |Igen |Igen |
| Statikus nyilvános IP-címet egy bérlő hálózati adapter vagy terheléselosztó rendelve. |Igen |Igen |
| Statikus nyilvános IP-cím rendelve egy fabric infrastruktúra végpontot. |Igen |Nem |
| Nyilvános IP-cím implicit módon hoztunk létre IaaS Virtuálisgép-példányok és kimenő NAT-hoz a virtuális hálózaton. Jön létre a háttérben, amikor egy bérlő létrehoz egy Virtuálisgép-példánnyal, hogy a virtuális gépek képes információkat küldeni az internethez. |Igen |Nem |

## <a name="next-steps"></a>További lépések

[Az Azure Stack Tárfiókok kezelése](azure-stack-manage-storage-accounts.md)