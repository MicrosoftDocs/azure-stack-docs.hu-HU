---
title: Hálózati erőforrások kezelése
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan kezelheti a hálózati erőforrásokat, beleértve a MAC-címkészletet és a nyilvános IP-címek egy adott régióban való felhasználását.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 8f52158e53bbb48b25b9b3a898c0ece33ab3c1be
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77508021"
---
# <a name="manage-network-resources-in-azure-stack-hub"></a>Hálózati erőforrások kezelése Azure Stack központban

## <a name="mac-address-pool"></a>MAC-címkészlet

Azure Stack hub egy statikus MAC-címkészlet használatával automatikusan létrehozza és hozzárendeli a MAC-címeket a virtuális gépekhez. Az üzembe helyezés során a rendszer automatikusan létrehozza ezt a MAC-címkészletet, és a következő tartományt használja:

- StartMacAddress: 00-1D-D8-B7-00-00
- EndMacAddress: 00-1D-D8-F4-FF-FF

> [!Note]  
> Ez a MAC-címkészlet minden Azure Stack hub-rendszeren azonos, és nem konfigurálható.

Attól függően, hogy a virtuális hálózatok hogyan kapcsolódnak a meglévő vállalati hálózatokhoz, előfordulhat, hogy a virtuális gépek duplikált MAC-címei várhatók.

További információ a MAC-címkészlet kihasználtságáról: a [Get-AzsMacAddressPool](https://docs.microsoft.com/powershell/module/azs.fabric.admin/get-azsmacaddresspool) parancsmag használata a Azure stack hub Administrator PowerShell-modulban.

## <a name="view-public-ip-address-consumption-in-azure-stack-hub"></a>Nyilvános IP-címek felhasználásának megtekintése Azure Stack központban

A felhő rendszergazdája a következőket tekintheti meg:
 - A bérlők számára lefoglalt nyilvános IP-címek száma.
 - A kiosztáshoz továbbra is elérhető nyilvános IP-címek száma.
 - Az adott helyen lefoglalt nyilvános IP-címek százalékos aránya.

A **nyilvános IP-készletek használati** csempe a nyilvános IP-címkészlet során felhasznált nyilvános IP-címek számát jeleníti meg. Az egyes IP-címek esetében a csempe a bérlői IaaS virtuálisgép-példányok, a háló infrastruktúra-szolgáltatások és a bérlők által explicit módon létrehozott nyilvános IP-címek használatát mutatja.

A csempe célja, hogy Azure Stack hub-operátorok számára az ezen a helyen használt nyilvános IP-címek számát adja meg. A szám segítségével a rendszergazdák meghatározhatják, hogy az adott erőforráson alacsonyan futnak-e.

A **bérlői erőforrások** **nyilvános IP-címei** menüpontja csak azokat a nyilvános IP-címeket sorolja fel, amelyeket a *bérlők explicit módon hoztak létre*. A menüelem az **erőforrás-szolgáltatók** -> **hálózati** ablaktáblán található. A **nyilvános IP-készletek használati** csempe **használt** nyilvános IP-címeinek száma mindig eltér a **nyilvános IP-címek** csempén a **bérlői erőforrások**alatt lévő számtól (nagyobb, mint).

### <a name="view-the-public-ip-address-usage-information"></a>A nyilvános IP-cím használati adatainak megtekintése

A régióban felhasznált nyilvános IP-címek teljes számának megtekintése:

1. Az Azure Stack hub felügyeleti portálján válassza a **minden szolgáltatás**lehetőséget. Ezután az **Adminisztráció** kategóriában válassza a **hálózat**lehetőséget.
1. A **hálózat** ablaktáblán a **nyilvános IP-készletek használati** csempe látható az **Áttekintés** szakaszban.

    ![Hálózati erőforrás-szolgáltató ablaktábla Azure Stack hub felügyeleti portálján](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-01.png)

A **használt** szám a nyilvános IP-címkészlet által hozzárendelt nyilvános IP-címek számát jelöli. A **szabad** szám a nyilvános IP-címkészlet azon nyilvános IP-címeinek számát jelöli, amelyeket még nem rendeltek hozzá, és amelyek továbbra is elérhetők. A **(z)% használt** szám a használt vagy a hozzárendelt címek számát jelöli a nyilvános IP-címkészlet ezen a helyen lévő nyilvános IP-címeinek teljes száma százalékában.

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>A bérlői előfizetések által létrehozott nyilvános IP-címek megtekintése

Válassza ki a **nyilvános IP-címeket** a **bérlői erőforrások**területen. Tekintse át a bérlői előfizetések által explicit módon létrehozott nyilvános IP-címek listáját egy adott régióban.

![Bérlői nyilvános IP-címek a Azure Stack hub felügyeleti portálján](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-02.png)

Előfordulhat, hogy néhány dinamikusan lefoglalt nyilvános IP-cím megjelenik a listában. Azonban még nincs társítva egy adott címnek. A szolgáltatási erőforrás a hálózati erőforrás-szolgáltatóban lett létrehozva, de még nem a hálózati vezérlőben.

A hálózati vezérlő addig nem rendel hozzá címeket az erőforráshoz, amíg hozzá nem kötődik egy felülethez, egy hálózati adapterhez, egy terheléselosztóhoz vagy egy virtuális hálózati átjáróhoz. Ha a nyilvános IP-cím egy csatolóhoz kötődik, a hálózati vezérlő IP-címet foglal le. A címnek a **címe** mezőben kell megjelenni.

### <a name="view-the-public-ip-address-information-summary-table"></a>A nyilvános IP-cím információinak összefoglaló táblázatának megtekintése

Különböző esetekben a rendszer a nyilvános IP-címeket rendeli hozzá, amelyek meghatározzák, hogy a cím megjelenik-e egy listában vagy egy másikban.

| **Nyilvános IP-cím-hozzárendelési eset** | **Megjelenik a használat összegzése** | **Megjelenik a bérlő nyilvános IP-címei listán** |
| --- | --- | --- |
| A dinamikus nyilvános IP-cím még nincs hozzárendelve hálózati adapterhez vagy terheléselosztóhoz (ideiglenes). |Nem |Igen |
| Egy hálózati adapterhez vagy terheléselosztóhoz rendelt dinamikus nyilvános IP-cím. |Igen |Igen |
| Egy bérlői hálózati adapterhez vagy terheléselosztóhoz rendelt statikus nyilvános IP-cím. |Igen |Igen |
| A háló infrastruktúra-szolgáltatási végponthoz rendelt statikus nyilvános IP-cím. |Igen |Nem |
| A nyilvános IP-cím implicit módon lett létrehozva a IaaS virtuálisgép-példányokhoz, és a virtuális hálózaton kimenő NAT-hoz használatos. Ezek a színfalak mögött jönnek létre, amikor egy bérlő létrehoz egy virtuálisgép-példányt, hogy a virtuális gépek adatokat tudjanak küldeni az internetre. |Igen |Nem |

## <a name="next-steps"></a>Következő lépések

[Storage-fiókok kezelése Azure Stack központban](azure-stack-manage-storage-accounts.md)
