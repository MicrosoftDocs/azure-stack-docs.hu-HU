---
title: Kvóta-típusok Azure Stack hub-ban | Microsoft Docs
description: Megtekintheti és szerkesztheti az Azure Stack hub szolgáltatásaihoz és erőforrásaihoz elérhető különböző típusú kvótákat.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 2ce9d539d68c64a2a57976ee8b4b6af2b99f6af7
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809831"
---
# <a name="quota-types-in-azure-stack-hub"></a>Kvóta típusai Azure Stack központban

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

A [kvóták](service-plan-offer-subscription-overview.md#plans) határozzák meg a felhasználói előfizetés által kiépíthető vagy felhasználható erőforrások korlátait. Egy kvóta például lehetővé teheti, hogy egy felhasználó legfeljebb öt virtuális gépet hozzon létre. Mindegyik erőforrásra különböző típusú kvóták vonatkoznak.

> [!IMPORTANT]
> Akár két óráig is eltarthat, amíg az új kvóták elérhetővé válnak a felhasználói portálon, vagy a módosított kvóta érvénybe léptetése előtt.

## <a name="compute-quota-types"></a>Számítási kvóták típusai

| **Típus** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Virtuális gépek maximális száma | 50 | Azon virtuális gépek maximális száma, amelyekhez előfizetések hozhatók létre ezen a helyen. |
| VM-magok maximális száma | 100 | Az előfizetések által az adott helyen létrehozható magok maximális száma (például egy A3-as virtuális gép négy maggal rendelkezik). |
| Rendelkezésre állási készletek maximális száma | 10 | Az ezen a helyen létrehozható rendelkezésre állási készletek maximális száma. |
| A virtuálisgép-méretezési csoportok maximális száma | 100 | Az ezen a helyen létrehozható méretezési csoportok maximális száma. |
| Standard szintű felügyelt lemez maximális kapacitása (GB-ban) | 2048 | Az ezen a helyen létrehozható standard szintű felügyelt lemezek maximális kapacitása. Ez az érték a standard szintű felügyelt lemezek foglalási mérete és a szabványos Pillanatképek felhasznált mérete összesen. |
| Prémium szintű felügyelt lemez maximális kapacitása (GB) | 2048 | Az ezen a helyen létrehozható prémium szintű felügyelt lemezek maximális kapacitása. Ez az érték a prémium szintű felügyelt lemezek foglalási méretének és a prémium Pillanatképek felhasznált méretének teljes összege. |

> [!NOTE]
> A nem felügyelt lemezek maximális kapacitása (Blobok) elkülönül a felügyelt lemez kvótájában. Ezt az értéket **maximális kapacitás (GB)** értékre állíthatja a **tárolási kvótákban**.

## <a name="storage-quota-types"></a>Tárolási kvóták típusai

| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális kapacitás (GB) |2048 |Az ezen a helyen lévő előfizetések által felhasználható tárterület teljes kapacitása. Ez az érték az összes blob (beleértve a nem felügyelt lemezeket is) és az összes hozzá tartozó pillanatkép, tábla és várólista felhasznált méretének teljes mérete. |
| A Storage-fiókok teljes száma |20 |Az előfizetés által az adott helyen létrehozható tárolási fiókok maximális száma. |

> [!NOTE]
> Ha egy előfizetésben túllépi a **maximális kapacitást (GB)** , nem hozhat létre új tárolási erőforrást ebben az előfizetésben. A virtuális gépeken ebben az előfizetésben létrehozott nem felügyelt lemezeket azonban folyamatosan használhatja, ami a kvótán kívüli teljes kapacitást is kihasználhatja.<br>A felügyelt lemezek maximális kapacitása el van különítve a teljes tárolási kvótától. Ezt az értéket **számítási kvótákban**állíthatja be.

## <a name="network-quota-types"></a>Hálózati kvóták típusai

| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Virtuális hálózatok maximális száma |50 |Azon virtuális hálózatok maximális száma, amelyekhez az előfizetés létre tud hozni ezen a helyen. |
| Virtuális hálózati átjárók maximális száma |1 |Azon virtuális hálózati átjárók (VPN-átjárók) maximális száma, amelyekhez az előfizetés létre tud hozni ezen a helyen. |
| Maximális hálózati kapcsolatok |2 |A hálózati kapcsolatok (pont – pont vagy helyek közötti) maximális száma, amelyet az előfizetés az adott helyen található összes virtuális hálózati átjárón létre tud hozni. |
| Nyilvános IP-címek maximális száma |50 |Azon nyilvános IP-címek maximális száma, amelyekhez az előfizetés létre tud hozni ezen a helyen. |
| Hálózati adapterek maximális száma |100 |Azon hálózati adapterek maximális száma, amelyekhez az előfizetés létre tud hozni ezen a helyen. |
| Maximális terheléselosztó |50 |Az előfizetések által az adott helyen létrehozott terheléselosztó maximális száma. |
| Hálózati biztonsági csoportok maximális száma |50 |Azon hálózati biztonsági csoportok maximális száma, amelyekre az előfizetés létre tud hozni ezen a helyen. |

## <a name="view-an-existing-quota"></a>Meglévő kvóta megtekintése

Két különböző módon lehet megtekinteni egy meglévő kvótát:

### <a name="plans"></a>Csomagok

1. A felügyeleti portál bal oldali navigációs paneljén válassza a **csomagok**lehetőséget.
2. Válassza ki azt a csomagot, amelyre vonatkozóan meg szeretné tekinteni a részleteket, ha a nevére kattint.
3. A megnyíló panelen válassza a **szolgáltatások és kvóták**lehetőséget.
4. Válassza ki a megtekinteni kívánt kvótát a Name ( **név** ) oszlopban.

    [![Kvóták a Azure Stack hub felügyeleti portálján](media/azure-stack-quota-types/quotas1sm.png "Kvóták megtekintése a felügyeleti portálon")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Erőforrás-szolgáltatók

1. A felügyeleti portál alapértelmezett irányítópultján keresse meg az erőforrás- **szolgáltatók** csempét.
2. Válassza ki a megtekinteni kívánt kvótával rendelkező szolgáltatást, például a **számítás**, a **hálózat**vagy a **tárterület**elemet.
3. Válassza a **kvóták**lehetőséget, majd válassza ki a megtekinteni kívánt kvótát.

## <a name="edit-a-quota"></a>Kvóta szerkesztése

A kvóták szerkesztésének két különböző módja van:

### <a name="edit-a-plan"></a>Csomag szerkesztése

1. A felügyeleti portál bal oldali navigációs paneljén válassza a **csomagok**lehetőséget.
2. Válassza ki azt a csomagot, amelynek a nevét módosítani szeretné, majd kattintson a kívánt kvótára.
3. A megnyíló panelen válassza a **szolgáltatások és kvóták**lehetőséget.
4. Válassza ki a szerkeszteni kívánt kvótát a **Name (név** ) oszlopban, majd kattintson rá.

    [![Kvóták a Azure Stack hub felügyeleti portálján](media/azure-stack-quota-types/quotas1sm.png "Kvóták megtekintése a felügyeleti portálon")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. A megnyíló panelen válassza a **Szerkesztés a számítás**, **a hálózat szerkesztése**vagy **a tárterület szerkesztése**lehetőséget.

    ![Csomag szerkesztése Azure Stack hub felügyeleti portálján](media/azure-stack-quota-types/quotas3.png "Csomag szerkesztése Azure Stack hub felügyeleti portálján")

Azt is megteheti, hogy az alábbi eljárást követve módosítja a kvótát:

1. A felügyeleti portál alapértelmezett irányítópultján keresse meg az erőforrás- **szolgáltatók** csempét.
2. Válassza ki a módosítani kívánt kvótával rendelkező szolgáltatást, például a **számítást**, a **hálózatot**vagy a **tárterületet**.
3. Ezután válassza a **kvóták**lehetőséget, majd válassza ki a módosítani kívánt kvótát.
4. A **tárolási kvóták beállítása**lapon adja meg a **számítási kvótákat**, vagy **állítsa be a hálózati kvóták** panelt (a szerkesztésre kiválasztott kvóta típusától függően), szerkessze az értékeket, majd kattintson a **Mentés**gombra.

### <a name="edit-original-configuration"></a>Eredeti konfiguráció szerkesztése
  
Megadhatja, hogy egy adott kvóta eredeti konfigurációját [egy kiegészítő csomag használata](create-add-on-plan.md)helyett szerkessze. Ha szerkeszt egy kvótát, az új konfiguráció automatikusan globálisan vonatkozik minden olyan csomagra, amely a kvótát és a csomagokat használó összes meglévő előfizetést használja. A kvóta szerkesztése eltér, mint amikor egy bővítményt használ egy módosított kvóta biztosításához, amelyet a felhasználó az előfizetéshez választ.

A kvóta új értékei globálisan vonatkoznak minden olyan csomagra, amely a módosított kvótát és a csomagokat használó összes meglévő előfizetést használja.

## <a name="next-steps"></a>Következő lépések

- [További információ a szolgáltatások, csomagok, ajánlatok és kvóták használatáról.](service-plan-offer-subscription-overview.md)
- [Hozzon létre kvótákat a csomag létrehozásakor.](azure-stack-create-plan.md)
