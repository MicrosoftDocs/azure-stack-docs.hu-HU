---
title: Kiegészítő csomagok létrehozása
titleSuffix: Azure Stack Hub
description: További információ a kiegészítő csomagok létrehozásáról Azure Stack központban.
author: bryanla
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 11/3/2020
ms.openlocfilehash: a3cc9436ec0e3ec0c939760454b194bfec4a8c58
ms.sourcegitcommit: ecd98662194d2cdb15c22f8b1f99812fc5f4c15a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93344881"
---
# <a name="create-add-on-plans-in-azure-stack-hub"></a>Kiegészítő csomagok létrehozása a Azure Stack hub-ban

Azure Stack hub-kezelőként kiegészítő csomagokat hozhat létre az [alapcsomag](azure-stack-create-plan.md) módosításához, ha további szolgáltatásokat szeretne biztosítani, vagy a *számítógép* -, a *tárterület* -vagy a *hálózati* kvótákat az alapcsomag kezdeti ajánlatán kívül szeretné kiterjeszteni. A kiegészítő csomagok módosítják az alapcsomagot, és választható bővítmények, amelyeket a felhasználók az előfizetésük alapján választhatnak.

Minden alkalommal, amikor egyetlen csomagban egyesíti az összeset, optimálisnak kell lennie. Más esetekben érdemes lehet alapcsomagot használni, majd kiegészítő csomagokkal felkínálni a további szolgáltatásokat. Dönthet például úgy, hogy a IaaS-szolgáltatásokat egy alapcsomag részeként kínálja, és minden olyan, a kiegészítő csomagként kezelt Pásti-szolgáltatással rendelkezik.

A kiegészítő csomagok használatának másik oka az erőforrás-használat figyelése. Ehhez egy olyan alapcsomaggal kell kezdenie, amely viszonylag kis mennyiségű kvótát tartalmaz (a szükséges szolgáltatástól függően). Ezután, ahogy a felhasználók elérik a kapacitást, a rendszer figyelmezteti, hogy a hozzárendelt terv alapján felhasználták az erőforrások kiosztását. Innen a felhasználók kiválaszthatnak egy kiegészítő csomagot, amely biztosítja a további erőforrásokat.

> [!NOTE]
> Ha nem szeretne kiegészítő csomagot használni a kvóta meghosszabbításához, akkor a [kvóta eredeti konfigurációját is szerkesztheti](azure-stack-quota-types.md#edit-a-quota).

A kiegészítő csomagok [létrehozása ugyanúgy](azure-stack-create-plan.md) történik, mint az alapcsomag. A kettő közötti különbség akkor van meghatározva, amikor a csomagot hozzáadják egy ajánlathoz. Alapszintű csomagként vagy kiegészítő csomagként van kijelölve. Ha bővítményt ad hozzá egy meglévő ajánlathoz, a további erőforrások akár egy órát is igénybe vehetnek, hogy megjelenjenek az előfizetésben.

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>Kiegészítő csomag létrehozása (1902 és újabb)

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra felhőalapú rendszergazdaként.
2. Kövesse ugyanezeket a lépéseket [egy új alapcsomag](azure-stack-create-plan.md) létrehozásához, amely a korábban nem kínált szolgáltatásokat kínáló új terv létrehozásához szükséges.
3. A felügyeleti portálon válassza az **ajánlatok** lehetőséget, majd válassza ki a kiegészítő csomaggal frissítendő ajánlatot.

   ![Képernyőkép, amely bemutatja, hogyan választható ki a Azure Stack felügyeleti portálon a kiegészítő csomaggal frissíthető ajánlat.](media/create-add-on-plan/add-on1.png)

4. Az ajánlat tulajdonságainak alján válassza a **kiegészítő csomagok** lehetőséget. Válassza a **Hozzáadás** elemet.

    ![A Azure Stack felügyeleti portálon a kiegészítő csomagok kiválasztását bemutató képernyőkép.](media/create-add-on-plan/add-on2.png)

5. Válassza ki a hozzáadni kívánt csomagot. Ebben a példában a csomag neve **20-storageaccounts**. A terv kiválasztása után **a Kiválasztás gombra kattintva adja** hozzá a csomagot az ajánlathoz. Értesítést kap arról, hogy a csomag sikeresen hozzá lett adva az ajánlathoz.

    ![Képernyőkép, amely bemutatja, hogyan választható ki a Azure Stack felügyeleti portálon felvenni kívánt kiegészítő csomag.](media/create-add-on-plan/add-on3.png)

6. Tekintse át az ajánlatban található kiegészítő csomagok listáját annak ellenőrzéséhez, hogy az új kiegészítő csomag szerepel-e a listában.

    [![Képernyőkép, amely a Azure Stack felügyeleti portálon áttekinthető bővítményi csomagok listáját jeleníti meg.](media/create-add-on-plan/add-on4.png)](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Kiegészítő csomag létrehozása (1901-es és korábbi verziók)

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra felhőalapú rendszergazdaként.
2. Kövesse ugyanezeket a lépéseket [egy új alapcsomag](azure-stack-create-plan.md) létrehozásához, amely a korábban nem kínált szolgáltatásokat kínáló új terv létrehozásához szükséges. Ebben a példában a Key Vault ( **Microsoft.** kulcstartó) szolgáltatások szerepelni fognak az új tervben.
3. A felügyeleti portálon válassza az **ajánlatok** lehetőséget, majd válassza ki a kiegészítő csomaggal frissítendő ajánlatot.

   ![Válassza az ajánlat lehetőséget, ha a Azure Stack felügyeleti portálon a kiegészítő csomaggal szeretne frissíteni](media/create-add-on-plan/1.PNG)

4. Görgessen az ajánlat tulajdonságai oldal aljára, és válassza a **kiegészítő csomagok** lehetőséget. Válassza a **Hozzáadás** elemet.

    ![Kiegészítő csomagok kiválasztása Azure Stack felügyeleti portálon](media/create-add-on-plan/2.PNG)

5. Válassza ki a hozzáadni kívánt csomagot. Ebben a példában a tervet **Key Vault-tervnek** nevezzük. A terv kiválasztása után **a Kiválasztás gombra kattintva adja** hozzá a csomagot az ajánlathoz. Értesítést kap arról, hogy a csomag sikeresen hozzá lett adva az ajánlathoz.

    ![Válassza ki a hozzáadni kívánt bővítményi csomagot Azure Stack felügyeleti portálon](media/create-add-on-plan/3.PNG)

6. Tekintse át az ajánlatban található kiegészítő csomagok listáját annak ellenőrzéséhez, hogy az új kiegészítő csomag szerepel-e a listában.

    ![Azure Stack felügyeleti portálon található kiegészítő csomagok listájának áttekintése](media/create-add-on-plan/4.PNG)

::: moniker-end

## <a name="next-steps"></a>Következő lépések

* [Ajánlat létrehozása](azure-stack-create-offer.md)
