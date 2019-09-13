---
title: Ebből a cikkből megtudhatja, hogyan frissítheti Azure Stack ajánlatait és csomagjait | Microsoft Docs
description: Ez a cikk a meglévő Azure Stack ajánlatok és csomagok megtekintését és módosítását ismerteti.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 01e5bc6e91311a0c27acffd4079931c3ee000d29
ms.sourcegitcommit: 8ddd70ba5ce05c591d3fa62597981859af107c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70936135"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack kiegészítő csomagok

Azure Stack operátorként kiegészítő csomagokat hozhat létre az [alapcsomag módosításához, ha további](azure-stack-create-plan.md) szolgáltatásokat szeretne biztosítani, vagy a *számítógép*-, a *tárterület*-vagy a *hálózati* kvótákat az alapcsomag kezdeti ajánlatán kívül szeretné kiterjeszteni. A kiegészítő csomagok módosítják az alapcsomagot, és választható bővítmények, amelyeket a felhasználók választhatnak.

Minden alkalommal, amikor egyetlen csomagban egyesíti az összeset, optimálisnak kell lennie. Más esetekben érdemes lehet alapcsomagot használni, majd kiegészítő csomagokkal felkínálni a további szolgáltatásokat. Dönthet például úgy, hogy a IaaS-szolgáltatásokat egy alapcsomag részeként kínálja fel, és minden, a kiegészítő csomagként kezelt Pásti-szolgáltatással rendelkezik.

A kiegészítő csomagok használatának másik oka az erőforrás-használat figyelése. Ehhez egy olyan alapcsomaggal kell kezdenie, amely viszonylag kis mennyiségű kvótát tartalmaz (a szükséges szolgáltatástól függően). Ezután, ahogy a felhasználók elérik a kapacitást, a rendszer figyelmezteti, hogy a hozzárendelt terv alapján felhasználták az erőforrások kiosztását. Innen a felhasználók kiválaszthatnak egy kiegészítő csomagot, amely biztosítja a további erőforrásokat.

> [!NOTE]
> Ha nem szeretne kiegészítő csomagot használni a kvóta meghosszabbításához, akkor a [kvóta eredeti konfigurációját is szerkesztheti](azure-stack-quota-types.md#edit-a-quota).

Ha kiegészítő csomagot ad hozzá egy meglévő ajánlat-előfizetéshez, a további erőforrások akár egy órát is igénybe vehetnek.

A kiegészítő csomagok egy meglévő ajánlat módosításával jönnek létre.

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>Kiegészítő csomag létrehozása (1902 és újabb)

1. Jelentkezzen be a Azure Stack felügyeleti portálra felhőalapú rendszergazdaként.
2. Kövesse ugyanezeket a lépéseket [egy új alapcsomag](azure-stack-create-plan.md) létrehozásához, amely a korábban nem kínált szolgáltatásokat kínáló új csomag létrehozására szolgál.
3. A felügyeleti portálon kattintson az **ajánlatok** lehetőségre, majd válassza ki a kiegészítő csomaggal frissítendő ajánlatot.

   ![Kiegészítő csomag létrehozása](media/create-add-on-plan/add-on1.png)

4. Az ajánlat tulajdonságainak alján válassza a **kiegészítő csomagok**lehetőséget. Kattintson a **Hozzáadás**lehetőségre.

    ![Kiegészítő csomag létrehozása](media/create-add-on-plan/add-on2.png)

5. Válassza ki a hozzáadni kívánt csomagot. Ebben a példában a csomag neve **20-storageaccounts**. A terv kiválasztása után **a Kiválasztás gombra kattintva adja** hozzá a csomagot az ajánlathoz. Értesítést kap arról, hogy a csomag sikeresen hozzá lett adva az ajánlathoz.

    ![Kiegészítő csomag létrehozása](media/create-add-on-plan/add-on3.png)

6. Tekintse át az ajánlatban található kiegészítő csomagok listáját annak ellenőrzéséhez, hogy az új kiegészítő csomag szerepel-e a listában.

    [![Kiegészítő csomag létrehozása](media/create-add-on-plan/add-on4.png "Kiegészítő csomag létrehozása")](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-add-on-plan-1901-and-earlier"></a>Kiegészítő csomag létrehozása (1901-es és korábbi verziók)

1. Jelentkezzen be a Azure Stack felügyeleti portálra felhőalapú rendszergazdaként.
2. Kövesse ugyanezeket a lépéseket [egy új alapcsomag](azure-stack-create-plan.md) létrehozásához, amely a korábban nem kínált szolgáltatásokat kínáló új csomag létrehozására szolgál. Ebben a példában a Key Vault (**Microsoft.** kulcstartó) szolgáltatások szerepelni fognak az új tervben.
3. A felügyeleti portálon kattintson az **ajánlatok** lehetőségre, majd válassza ki a kiegészítő csomaggal frissítendő ajánlatot.

   ![Kiegészítő csomag létrehozása](media/create-add-on-plan/1.PNG)

4. Görgessen az ajánlat tulajdonságai oldal aljára, és válassza a **kiegészítő csomagok**lehetőséget. Kattintson a **Hozzáadás**lehetőségre.

    ![Kiegészítő csomag létrehozása](media/create-add-on-plan/2.PNG)

5. Válassza ki a hozzáadni kívánt csomagot. Ebben a példában a tervet **Key Vault-tervnek**nevezzük. A terv kiválasztása után **a Kiválasztás gombra kattintva adja** hozzá a csomagot az ajánlathoz. Értesítést kap arról, hogy a csomag sikeresen hozzá lett adva az ajánlathoz.

    ![Kiegészítő csomag létrehozása](media/create-add-on-plan/3.PNG)

6. Tekintse át az ajánlatban található kiegészítő csomagok listáját annak ellenőrzéséhez, hogy az új kiegészítő csomag szerepel-e a listában.

    ![Kiegészítő csomag létrehozása](media/create-add-on-plan/4.PNG)
::: moniker-end

## <a name="next-steps"></a>További lépések

* [Ajánlat létrehozása](azure-stack-create-offer.md)
