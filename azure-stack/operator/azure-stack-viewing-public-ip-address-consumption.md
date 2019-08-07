---
title: Hálózati osztályalapú kezelése Azure Stackban | Microsoft Docs
description: A rendszergazdák kezelhetik a hálózati erőforrásokat, beleértve a MAC-címkészletet és a nyilvános IP-címek felhasználását egy adott régióban.
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
ms.openlocfilehash: d056cbf73e2417bd826fba7a7de263cc8e015b7d
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842910"
---
# <a name="manage-network-resources"></a>Hálózati erőforrások kezelése

## <a name="mac-address-pool"></a>MAC-címkészlet

A Azure Stack statikus MAC-címkészletet használ a MAC-címek virtuális gépekhez való automatikus létrehozásához és hozzárendeléséhez.
Az üzembe helyezés során a rendszer automatikusan létrehozza ezt a MAC-címkészletet, és a következő tartományt használja:

- StartMacAddress: 00-1D-D8-B7-00-00
- EndMacAddress : 00-1D-D8-F4-FF-FF

> [!Note]  
> Ez a MAC-címkészlet minden Azure Stack rendszeren azonos, és nem konfigurálható.

Attól függően, hogy a virtuális hálózatok hogyan kapcsolódnak a meglévő vállalati hálózatokhoz, a virtuális gépek duplikált MAC-címei várhatók.

További információ a MAC-címkészlet kihasználtságáról: a [Get-AzsMacAddressPool](https://docs.microsoft.com/powershell/module/azs.fabric.admin/get-azsmacaddresspool) parancsmag használata a Azure stack felügyeleti PowerShell-modulban.

## <a name="view-public-ip-address-consumption-in-azure-stack"></a>Nyilvános IP-címek felhasználásának megtekintése Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A felhő rendszergazdája a következőket tekintheti meg:
 - A bérlők számára lefoglalt nyilvános IP-címek száma.
 - A kiosztáshoz továbbra is elérhető nyilvános IP-címek száma.
 - Az adott helyen lefoglalt nyilvános IP-címek százalékos aránya.

A **nyilvános IP-készletek használati** csempe a nyilvános IP-címkészlet során felhasznált nyilvános IP-címek számát jeleníti meg. Az egyes IP-címek esetében a csempe a bérlői IaaS virtuálisgép-példányok, a háló infrastruktúra-szolgáltatások és a bérlők által explicit módon létrehozott nyilvános IP-címek használatát mutatja.

A csempe célja, hogy Azure Stack operátorok számára az ezen a helyen használt nyilvános IP-címek számát adja meg. A szám segít a rendszergazdáknak megállapítani, hogy alacsonyan futnak-e az erőforráson.

A **bérlői erőforrások** **nyilvános IP-címei** menüpontja csak azokat a nyilvános IP-címeket sorolja fel, amelyeket a *bérlők explicit módon hoztak létre*. A menüelem az **erőforrás-szolgáltatók**, a **hálózat** ablaktáblán található. A **nyilvános IP-készletek használati** csempe **használt** nyilvános IP-címeinek száma mindig eltér a **nyilvános IP-címek** csempén a **bérlői erőforrások**alatt lévő számtól (nagyobb, mint).

### <a name="view-the-public-ip-address-usage-information"></a>A nyilvános IP-cím használati adatainak megtekintése

A régióban felhasznált nyilvános IP-címek teljes számának megtekintése:

1. A Azure Stack felügyeleti portálon válassza a **minden szolgáltatás**lehetőséget. Ezután az **Adminisztráció** kategóriában válassza a **hálózat**lehetőséget.
1. A **hálózat** ablaktáblán a **nyilvános IP-készletek használati** csempe látható az **Áttekintés** szakaszban.

![Hálózati erőforrás-szolgáltató panel](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

A **használt** szám a nyilvános IP-címkészlet által hozzárendelt nyilvános IP-címek számát jelöli. Az **ingyenes** szám a nyilvános IP-címkészlet olyan nyilvános IP-címeinek a számát jelöli, amelyek még nincsenek hozzárendelve, és továbbra is elérhetők. A **(z)% használt** szám a használt vagy a hozzárendelt címek számát jelöli a nyilvános IP-címkészlet ezen a helyen lévő nyilvános IP-címeinek teljes száma százalékában.

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>A bérlői előfizetések által létrehozott nyilvános IP-címek megtekintése

Válassza ki a **nyilvános IP-címeket** a **bérlői erőforrások**területen. Tekintse át a bérlői előfizetések által explicit módon létrehozott nyilvános IP-címek listáját egy adott régióban.

![Bérlői nyilvános IP-címek](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Előfordulhat, hogy néhány dinamikusan lefoglalt nyilvános IP-cím megjelenik a listában. Azonban még nincs társítva egy adott címnek. A szolgáltatási erőforrás a hálózati erőforrás-szolgáltatóban lett létrehozva, de még nem a hálózati vezérlőben.

A hálózati vezérlő addig nem rendel hozzá címeket az erőforráshoz, amíg hozzá nem kötődik egy felülethez, egy hálózati adapterhez, egy terheléselosztóhoz vagy egy virtuális hálózati átjáróhoz. Ha a nyilvános IP-cím egy csatolóhoz kötődik, a hálózati vezérlő IP-címet foglal le. A címnek a **címe** mezőben kell megjelenni.

### <a name="view-the-public-ip-address-information-summary-table"></a>A nyilvános IP-cím információinak összefoglaló táblázatának megtekintése

Különböző esetekben a rendszer a nyilvános IP-címeket rendeli hozzá, amelyek meghatározzák, hogy a cím megjelenik-e egy listában vagy egy másikban.

| **Nyilvános IP-cím-hozzárendelési eset** | **Megjelenik a használat összegzése** | **Megjelenik a bérlő nyilvános IP-címei listán** |
| --- | --- | --- |
| Dinamikus nyilvános IP-cím még nincs hozzárendelve hálózati adapterhez vagy terheléselosztóhoz (ideiglenes) |Nem |Igen |
| Egy hálózati adapterhez vagy terheléselosztóhoz rendelt dinamikus nyilvános IP-cím. |Igen |Igen |
| Egy bérlői hálózati adapterhez vagy terheléselosztóhoz rendelt statikus nyilvános IP-cím. |Igen |Igen |
| A háló infrastruktúra-szolgáltatási végponthoz rendelt statikus nyilvános IP-cím. |Igen |Nem |
| A nyilvános IP-cím implicit módon lett létrehozva a IaaS virtuálisgép-példányokhoz, és a virtuális hálózaton kimenő NAT-hoz használatos. Ezek a színfalak mögött jönnek létre, amikor egy bérlő létrehoz egy virtuálisgép-példányt, hogy a virtuális gépek adatokat tudjanak küldeni az internetre. |Igen |Nem |

## <a name="next-steps"></a>További lépések

[Storage-fiókok kezelése Azure Stackban](azure-stack-manage-storage-accounts.md)
