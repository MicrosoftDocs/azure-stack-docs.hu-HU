---
title: Csomag létrehozása az Azure Stackben |} A Microsoft Docs
description: Felhő-rendszergazdaként hozzon létre egy csomagot, amely lehetővé teszi, hogy a virtuális gépek előfizetők kiépítése.
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
ms.openlocfilehash: b120346d489f676919cb05863f81db9bfb102634
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66836996"
---
# <a name="create-a-plan-in-azure-stack"></a>Csomag létrehozása az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

[Az Azure Stack-csomagok](azure-stack-overview.md) egy vagy több szolgáltatás és a kvóták csoportosításán alapulnak. Szolgáltatóként a felhasználóknak kínált terveket is létrehozhat. Viszont a felhasználók előfizethetnek azokra a használatára a tervek, szolgáltatások és kvóták tartoznak. Ez a példa bemutatja, hogyan hozzon létre egy csomagot, amely tartalmazza a számítási, hálózati és tárolási erőforrás-szolgáltatókat. Ez a csomag virtuális gépek lehetővé teszi az előfizetők számára.

## <a name="create-a-plan-1902-and-later"></a>Hozzon létre egy csomagot (1902 és újabb verziók)

1. Jelentkezzen be a [Azure Stack rendszergazdai portál](https://adminportal.local.azurestack.external).

2. Csomag és ajánlat, amely a felhasználók előfizethetnek létrehozásához válassza **+ erőforrás létrehozása**, majd **ajánlatok és csomagok**, majd **terv**.
  
   ![Csomag kiválasztása](media/azure-stack-create-plan/select-plan.png)

3. Lapokra osztott felhasználói felület, amely lehetővé teszi, hogy adja meg a csomag nevét, szolgáltatások és kvóták megadása a kiválasztott minden jelenik meg. Ennél is fontosabb áttekintheti a hoz létre, mielőtt hozna létre, az ajánlat részleteit.

   Alatt a **alapjai** lapján a **új terv** ablakban adja meg egy **megjelenítendő név** és a egy **erőforrásnév**. Megjelenített név a csomag rövid neve operátorok számára látható. Vegye figyelembe, hogy a felügyeleti portálon csomag részletei láthatók csak az operátorok számára.

   ![Adja meg a részleteket](media/azure-stack-create-plan/plan-name.png)

4. Hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévőt, a csomag tárolójaként.

   ![Adja meg az erőforráscsoportot](media/azure-stack-create-plan/resource-group.png)

5. Válassza ki a **szolgáltatások** lapon, vagy kattintson a **tovább: Szolgáltatások >** gombra, és válassza ki a jelölőnégyzetet a **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**.
  
   ![Szolgáltatások kiválasztása](media/azure-stack-create-plan/services.png)

6. Válassza ki a **kvóták** lapon, vagy kattintson a **tovább: Kvóták >** gombra. A **Microsoft.Storage**, vagy az alapértelmezett kvóta választhat a legördülő listából, vagy válasszon **hozzon létre új** egyéni kvóta.
  
   ![Kvóták](media/azure-stack-create-plan/quotas.png)

7. Ha egy új kvóta próbál létrehozni, adja meg egy **neve** kvóta, majd adja meg a kvóta értékeket. Válassza ki **OK** létrehozni a kvótát.

   ![Új kvóta](media/azure-stack-create-plan/new-quota.png)

8. Ismételje meg a 6 és 7 létrehozása és hozzárendelése a kvóták **Microsoft.Network** és **Microsoft.Compute**. Ha mindhárom szolgáltatást a hozzárendelt kvóták, a következő példához hasonlóan fog megtekintése.

   ![Teljes kvóta hozzárendelések](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Válassza ki **tekintse át + létrehozása** , tekintse át a tervet. Tekintse át az értékek és a kvóták, győződjön meg arról, hogy helyesek. Vegye figyelembe a helybővítés nyilak szolgáltatáskvóták/párjaihoz balra. Egy új funkció lehetővé teszi a kiválasztott tervek, egyenként, a csomagban lévő minden egyes kvóta részleteinek megtekintéséhez, és térjen vissza a szükséges módosításokat, bontsa ki a kvóták.

   ![A terv létrehozása](media/azure-stack-create-plan/create.png)

10. Amikor elkészült, válassza ki a **létrehozás** a terv létrehozásához.

11. Tekintse meg az új csomag, a bal oldalon kattintson a **minden szolgáltatás**válassza **csomagok**, és keressen rá a tervet, és válassza ki a nevét. Ha az erőforrások listája hosszú, akkor **keresési** keresse meg a csomag neve.

## <a name="create-a-plan-1901-and-earlier"></a>Hozzon létre egy csomagot (1901 és korábbi verziók)

1. Jelentkezzen be a [Azure Stack rendszergazdai portál](https://adminportal.local.azurestack.external).

2. Csomag és ajánlat, amely a felhasználók előfizethetnek létrehozásához válassza **+ új**, majd **ajánlatok és csomagok**, majd **terv**.
  
   ![Csomag kiválasztása](media/azure-stack-create-plan/select-plan1901.png)

3. Alatt **új terv**, adjon meg egy **megjelenített név** és a egy **erőforrásnév**. A megjelenített név a csomag rövid neve, a felhasználók számára is. Csak a rendszergazda láthatja az erőforrás nevét, mely segítségével a rendszergazdák a csomaggal mint Azure Resource Manager-erőforrással rendelkező működéséhez.

   ![Adja meg a részleteket](media/azure-stack-create-plan/plan-name1901.png)

4. Hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévőt, a csomag tárolójaként.

   ![Adja meg az erőforráscsoportot](media/azure-stack-create-plan/resource-group1901.png)

5. Válassza ki **szolgáltatások** , és ezután jelölje be a **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**. Következő lépésként válassza ki **kiválasztása** a konfiguráció mentéséhez. Jelölőnégyzetek jelennek meg, ha az egérrel rámutat az egyes lehetőségek.
  
   ![Szolgáltatások kiválasztása](media/azure-stack-create-plan/services1901.png)

6. Válassza ki **kvóták**, **Microsoft.Storage (helyi)** , majd válassza a alapértelmezett kvóta, vagy válasszon **új kvóta létrehozása** testre szabott kvóta.
  
   ![Kvóták](media/azure-stack-create-plan/quotas1901.png)

7. Ha egy új kvóta próbál létrehozni, adja meg egy **neve** kvóta > adja meg a kvóta értékeket > válassza **OK**. A **kvóta létrehozása** párbeszédpanelje bezárul.

   ![Új kvóta](media/azure-stack-create-plan/new-quota1901.png)

   Ezután válassza ki a létrehozott új kvóta. Válassza a kvóta rendel hozzá, és bezárja a kiválasztása párbeszédpanelen.
  
   ![A kvóta hozzárendelése](media/azure-stack-create-plan/assign-quota1901.png)

8. Ismételje meg a 6 és 7 létrehozása és hozzárendelése a kvóták **Microsoft.Network (helyi)** és **Microsoft.Compute (helyi)** . Ha mindhárom szolgáltatást a hozzárendelt kvóták, a következő példához hasonlóan fog megtekintése.

   ![Teljes kvóta hozzárendelések](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. Alatt **kvóták**, válassza a **OK**, majd a **új csomag**, válassza a **létrehozás** a terv létrehozásához.

    ![A terv létrehozása](media/azure-stack-create-plan/create1901.png)

10. Az új terv megtekintéséhez válasszon **összes erőforrás**, keressen rá a tervet, és válassza ki a nevét. Ha az erőforrások listája hosszú, akkor **keresési** keresse meg a csomag neve.

    ![Tekintse át a terv](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>További lépések

* [Ajánlat létrehozása](azure-stack-create-offer.md)
