---
title: Terv létrehozása Azure Stackban | Microsoft Docs
description: Felhőbeli rendszergazdaként hozzon létre egy csomagot, amely lehetővé teszi az előfizetők számára a virtuális gépek üzembe helyezését.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 6e127983afe023448d6caad23cf79e8a3a289c17
ms.sourcegitcommit: 8ddd70ba5ce05c591d3fa62597981859af107c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70936162"
---
# <a name="create-a-plan-in-azure-stack"></a>Csomag létrehozása az Azure Stackben

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

[Azure stack csomagok](azure-stack-overview.md) egy vagy több szolgáltatás és a kvótáik csoportjai. Szolgáltatóként létrehozhat terveket a felhasználók számára. A felhasználók viszont előfizethetnek az ajánlatokra, hogy használhassák a csomagokat, szolgáltatásokat és kvótákat. Ebből a példából megtudhatja, hogyan hozhat létre olyan csomagot, amely tartalmazza a számítási, hálózati és tárolási erőforrás-szolgáltatókat. Ez a csomag lehetővé teszi, hogy az előfizetők virtuális gépeket építsenek ki.

::: moniker range=">=azs-1902"
## <a name="create-a-plan-1902-and-later"></a>Terv létrehozása (1902 és újabb)

1. Jelentkezzen be a [Azure stack felügyeleti portálra](https://adminportal.local.azurestack.external).

2. Egy olyan csomag és ajánlat létrehozásához, amelyhez a felhasználók előfizethetnek, válassza az **+ erőforrás létrehozása**, majd a **+ csomagok**, majd a **terv**lehetőséget.
  
   ![Válasszon csomagot](media/azure-stack-create-plan/select-plan.png)

3. Megjelenik egy Többlapos felhasználói felület, amely lehetővé teszi a csomag nevének megadását, a szolgáltatások hozzáadását és a kvóták definiálását a kiválasztott szolgáltatásokhoz. A legfontosabb, hogy a létrehozás előtt áttekintheti a létrehozott ajánlat részleteit.

   Az **új csomag** ablak alapismeretek lapján adja meg a **megjelenítendő nevet** és az **erőforrás nevét**. A megjelenítendő név a terv felhasználóbarát neve, amelyet a kezelők láthatnak. Vegye figyelembe, hogy a felügyeleti portálon a részletek csak az operátorok számára láthatók.

   ![Részletek megadása](media/azure-stack-create-plan/plan-name.png)

4. Hozzon létre egy új **erőforráscsoportot**, vagy válasszon ki egy meglévőt, mint tárolót a csomag számára.

   ![Az erőforráscsoport meghatározása](media/azure-stack-create-plan/resource-group.png)

5. Válassza a **szolgáltatások** fület, vagy kattintson **a Tovább gombra: Szolgáltatások >** gombra, majd jelölje be a **Microsoft. számítás**, a **Microsoft. Network**és a **Microsoft. Storage**jelölőnégyzetét.
  
   ![Szolgáltatások kiválasztása](media/azure-stack-create-plan/services.png)

6. Válassza a **kvóták** fület, vagy **kattintson a Tovább gombra: Kvóták** > gomb. A **Microsoft. Storage**elem mellett válassza az alapértelmezett kvótát a legördülő listából, vagy válassza az **új létrehozása** lehetőséget a testreszabott kvóta létrehozásához.
  
   ![Kvóták](media/azure-stack-create-plan/quotas.png)

7. Ha új kvótát hoz létre, adja meg a kvóta **nevét** , majd adja meg a kvóta értékeit. A kvóta létrehozásához kattintson **az OK gombra** .

   ![Új kvóta](media/azure-stack-create-plan/new-quota.png)

8. Ismételje meg a 6. és 7. lépést a **Microsoft. Network** és a **Microsoft. számítási**szolgáltatás kvótáinak létrehozásához és hozzárendeléséhez. Ha mindhárom szolgáltatáshoz kvóták vannak rendelve, a következő példához hasonlóan néznek ki.

   ![Kvóta-hozzárendelések befejezése](media/azure-stack-create-plan/all-quotas-assigned.png)

9. A terv áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet. Ellenőrizze, hogy helyesek-e az összes érték és kvóta. Figyelje meg az egyes szolgáltatások/kvóták pár bal oldalán található bővítési nyilakat. Egy új szolgáltatás lehetővé teszi, hogy a kiválasztott csomagokban lévő kvótákat egyenként kibontva megtekintse a csomagok egyes kvótáinak részleteit, és térjen vissza a szükséges módosítások elvégzéséhez.

   ![A csomag létrehozása](media/azure-stack-create-plan/create.png)

10. Ha elkészült, válassza a **Létrehozás** lehetőséget a terv létrehozásához.

11. Az új csomag megjelenítéséhez kattintson a bal oldalon a **minden szolgáltatás**elemre, válassza a **csomagok**lehetőséget, majd keresse meg a csomagot, és válassza ki a nevét. Ha az erőforrások listája hosszú, használja a **Search (keresés** ) lehetőséget a terv név szerinti megkereséséhez.
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-a-plan-1901-and-earlier"></a>Terv létrehozása (1901 és korábbi)

1. Jelentkezzen be a [Azure stack felügyeleti portálra](https://adminportal.local.azurestack.external).

2. Ha olyan csomagot és ajánlatot szeretne létrehozni, amelyhez a felhasználók előfizethetnek, válassza az **+ új**, majd a **+ csomagok**, majd a **terv**lehetőséget.
  
   ![Válasszon csomagot](media/azure-stack-create-plan/select-plan1901.png)

3. Az **új csomag**területen adja meg a **megjelenítendő nevet** és az **erőforrás nevét**. A megjelenítendő név a terv felhasználóbarát neve, amelyet a felhasználók láthatnak. Csak a rendszergazda láthatja az erőforrás nevét, amelyet a rendszergazdák Azure Resource Manager erőforrásként használhatnak a csomaggal való együttműködéshez.

   ![Részletek megadása](media/azure-stack-create-plan/plan-name1901.png)

4. Hozzon létre egy új **erőforráscsoportot**, vagy válasszon ki egy meglévőt, mint tárolót a csomag számára.

   ![Az erőforráscsoport meghatározása](media/azure-stack-create-plan/resource-group1901.png)

5. Válassza a **szolgáltatások** lehetőséget, majd jelölje be a **Microsoft. számítás**, a **Microsoft. Network**és a **Microsoft. Storage**jelölőnégyzetet. Ezután válassza a **kijelölés lehetőséget** a konfiguráció mentéséhez. A jelölőnégyzetek akkor jelennek meg, amikor az egérmutató az egyes lehetőségek fölé mutat.
  
   ![Szolgáltatások kiválasztása](media/azure-stack-create-plan/services1901.png)

6. Válasszaa kvóták, a **Microsoft. Storage (helyi)** lehetőséget, majd válassza ki az alapértelmezett kvótát, vagy válassza az **új kvóta létrehozása** lehetőséget a testreszabott kvóta létrehozásához.
  
   ![Kvóták](media/azure-stack-create-plan/quotas1901.png)

7. Ha új kvótát hoz létre, adja meg a kvóta **nevét** > adja meg a kvóta értékét > válassza az **OK gombot**. A **kvóta létrehozása** párbeszédpanel bezárul.

   ![Új kvóta](media/azure-stack-create-plan/new-quota1901.png)

   Ezután válassza ki a létrehozott új kvótát. Válassza ki a kvótát, és zárja be a kiválasztási párbeszédpanelt.
  
   ![Kvóta kiosztása](media/azure-stack-create-plan/assign-quota1901.png)

8. Ismételje meg a 6. és 7. lépést a **Microsoft. Network (helyi)** és a **Microsoft. számítás (helyi)** kvótáinak létrehozásához és hozzárendeléséhez. Ha mindhárom szolgáltatáshoz kvóták vannak rendelve, a következő példához hasonlóan néznek ki.

   ![Kvóta-hozzárendelések befejezése](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. A **kvóták**területen válassza az **OK**, majd az **új terv**alatt a **Létrehozás** lehetőséget a terv létrehozásához.

    ![A csomag létrehozása](media/azure-stack-create-plan/create1901.png)

10. Az új terv megtekintéséhez válassza a **minden erőforrás**elemet, majd keresse meg a csomagot, és válassza ki a nevét. Ha az erőforrások listája hosszú, használja a **Search (keresés** ) lehetőséget a terv név szerinti megkereséséhez.

    ![A csomag áttekintése](media/azure-stack-create-plan/plan-overview1901.png)
::: moniker-end

## <a name="next-steps"></a>További lépések

* [Ajánlat létrehozása](azure-stack-create-offer.md)
