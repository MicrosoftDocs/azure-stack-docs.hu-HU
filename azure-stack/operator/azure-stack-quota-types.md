---
title: Az Azure Stackben kvótatípusok |} A Microsoft Docs
description: Megtekintheti és szerkesztheti a különböző kvótatípusok elérhető szolgáltatásokat és erőforrásokat az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 03/27/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 848b2a0c912a00a2185d7e4b7b8d8446bc1f6aca
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64295224"
---
# <a name="quota-types-in-azure-stack"></a>Az Azure Stackben kvótatípusok

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

[Kvóták](azure-stack-plan-offer-quota-overview.md#plans) erőforrásokat, amelyeket a felhasználói előfizetés is üzembe helyezése és felhasználása a vonatkozó korlátok beállításához. Egy kvóta megszabhatja például, hogy egy felhasználó legfeljebb öt virtuális gépet hozhat létre. Mindegyik erőforrásra különböző típusú kvóták vonatkoznak.

> [!IMPORTANT]
> Új kvóták csak akkor érhető el, a felhasználói portálon, vagy előtt módosított kvóták érvényesítése akár két órát is igénybe vehet.

## <a name="compute-quota-types"></a>COMPUTE kvótatípusok

| **Típus** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Virtuális gépek maximális száma | 50 | Egy előfizetést hozhat létre ezen a helyen lévő virtuális gépek maximális számát. |
| Virtuálisgép-magok maximális száma | 100 | Egy előfizetést hozhat létre ezen a helyen lévő magok maximális számának (például egy a3-as virtuális gép rendelkezik négy magot). |
| A rendelkezésre állási csoportok maximális száma | 10 | Ezen a helyen létrehozott rendelkezésre állási csoportok maximális száma. |
| A virtuálisgép-méretezési csoport maximális száma | 100 | Ezen a helyen létrehozott virtuális gép méretezési csoportok maximális száma. |
| Maximális kapacitás (GB) standard szintű felügyelt lemez | 2048 | A standard szintű managed disks ezen a helyen létrehozott maximális kapacitását. |
| Maximális kapacitás (GB) prémium szintű felügyelt lemez | 2048 | A prémium szintű maximális kapacitását a felügyelt lemezek, ezen a helyen létrehozott. |

> [!NOTE]  
> Nem felügyelt lemez (lapblobok) maximális kapacitása elkülönül a felügyelt lemez kvótát. Ez az érték állítható be **tárolási kvótákat**.

## <a name="storage-quota-types"></a>Tárolási kvóta típusa

| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális kapacitás (GB) |2048 |Tárterület teljes kapacitás (beleértve a minden társított pillanatképet a blobok, táblák, üzenetsorok), amely képes használni a ezen a helyen az előfizetéshez. |
| Tárfiókok teljes száma |20 |Egy előfizetést hozhat létre ezen a helyen storage-fiókok maximális számát. |

> [!NOTE]  
> A maximális kapacitás, a felügyelt lemezek elkülönül a teljes tárolási kvótát. Ez az érték állítható be **kvóták számítási**.

## <a name="network-quota-types"></a>Hálózati kvótatípusok

| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális nyilvános IP-címek |50 |Egy előfizetést hozhat létre ezen a helyen nyilvános IP-címek maximális számát. |
| Maximális virtuális hálózatok |50 |Ezen a helyen előfizetést hozhat létre virtuális hálózatok maximális száma. |
| Maximális virtuális hálózati átjárók |1 |Virtuális hálózati átjárók (VPN-átjárók), amely egy előfizetést hozhat létre ezen a helyen maximális számát. |
| Maximális hálózati kapcsolatok |2 |A maximális hálózati kapcsolatok száma (point-to-point vagy site-to-site), amely egy előfizetést hozhat létre ezen a helyen az összes virtuális hálózati átjáró között. |
| Maximális terheléselosztók |50 |Egy előfizetést hozhat létre ezen a helyen terheléselosztók maximális számát. |
| Maximális hálózati adapterek |100 |A hálózati adapterek számára olyan előfizetést hozhat létre ezen a helyen maximális számát. |
| Maximális hálózati biztonsági csoportok |50 |Egy előfizetést hozhat létre ezen a helyen hálózati biztonsági csoportok maximális száma. |

## <a name="view-an-existing-quota"></a>Egy meglévő kvóta megtekintése

Egy meglévő kvóta megtekintése két különböző módja van:

### <a name="plans"></a>Tervek

1. Az adminisztrátori portál bal oldali navigációs panelén válassza **csomagok**.
2. Válassza ki a csomagot, szeretné, részleteinek megtekintéséhez kattintson annak nevére.
3. A megnyíló panelen válassza ki a **szolgáltatások és kvóták**.
4. Válassza ki a kvótát, lásd: ehhez kattintson rá a szeretné a **neve** oszlop.

    [![Kvóták](media/azure-stack-quota-types/quotas1sm.png "kvóták megtekintése")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Erőforrás-szolgáltatók

1. Keresse meg az alapértelmezett irányítópult, a felügyeleti portálon, a **erőforrás-szolgáltatók** csempére.
2. Válassza ki a szolgáltatást, hogy meg szeretné tekinteni, például a kvótához **számítási**, **hálózati**, vagy **tárolási**.
3. Válassza ki **kvóták**, majd válassza ki a megtekinteni kívánt kvótát.

## <a name="edit-a-quota"></a>Kvóta szerkesztése

Kvóta szerkesztése két különböző módja van:

### <a name="edit-a-plan"></a>A terv szerkesztése

1. Az adminisztrátori portál bal oldali navigációs panelén válassza **csomagok**.
2. Válassza ki a csomagot, amelynek szeretné a kvótát, szerkesztéséhez kattintson annak nevére.
3. A megnyíló panelen válassza ki a **szolgáltatások és kvóták**.
4. Válassza ki a szerkesztéséhez kattintson a kívánt kvótát az **neve** oszlop.
    [![Kvóták](media/azure-stack-quota-types/quotas1sm.png "kvóták megtekintése")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. A megnyíló panelen válassza ki a **Szerkesztés a számítási**, **Szerkesztés a hálózati**, vagy **Storage szerkesztése**.
    ![Kvóták](media/azure-stack-quota-types/quotas3.png "kvóták megtekintése")

Azt is megteheti akkor is az alábbi eljárás egy kvóta szerkesztése:

1. Az alapértelmezett irányítópult, a felügyeleti portál, keresse meg a **erőforrás-szolgáltatók** csempére.
2. Válassza ki a szolgáltatást, amely a módosítandó, például a kvótához **számítási**, **hálózati**, vagy **tárolási**.
3. Majd **kvóták**, majd válassza ki a módosítani kívánt kvótát.
4. Az a **beállítása tárolási kvótákat**, **kvóták beállítása számítási**, vagy **beállítása hálózati kvóták** ablaktáblán (attól függően, a típus szerkesztését választotta kvóta), az értékeket szerkesztheti, és válassza ki **Mentése**.

### <a name="edit-original-configuration"></a>Eredeti konfiguráció szerkesztése
  
Dönthet úgy, hogy az eredeti konfiguráció helyett a kvóta szerkesztése [egy kiegészítő csomag használatával](create-add-on-plan.md). Ha szerkeszti a kvótát, az új konfigurációt automatikusan globálisan érvényes a kvóta használó összes csomag és az összes meglévő előfizetéseket, amelyek ezeket a csomagokat. A kvóta szerkesztése eltér attól, hogy ha használ egy kiegészítő csomagot adjon meg egy módosított kvóta, amely egy felhasználó úgy dönt, hogy az előfizetés.

Az új értékekkel kvóta globálisan vonatkoznak, a módosított kvóta használó kívül minden csomagra, és minden meglévő előfizetésekhez, amelyek ezeket a csomagokat.

## <a name="next-steps"></a>További lépések

- [Ismerje meg, további információk csomagok, ajánlatok és kvóták.](azure-stack-plan-offer-quota-overview.md)
- [Kvóták létrehozása a csomag létrehozásakor.](azure-stack-create-plan.md)

<!-- Update_Description: add edit a quota -->