---
title: Azure Stack gyors üzembe helyezés – Windows virtuális gép létrehozása
description: Azure Stack gyors üzembe helyezés – Windows virtuális gép létrehozása a portálon
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 47a2e2090a4bc6f4d72f5288f31a6e1cb53bdac7
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985972"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Gyors útmutató: egy Windows server virtuális gép létrehozása az Azure Stack portálon

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Létrehozhat egy Windows Server 2016 virtuális gép az Azure Stack-portál használatával. Kövesse a cikkben hozhat létre és használhat egy virtuális gépet.

> [!NOTE]  
> Ebben a cikkben a képernyőképek a felhasználói felület az Azure Stack verzió 1808 bevezetett megfelelően frissülnek. 1808 használatának támogatásával bővült *felügyelt lemezek* nem felügyelt lemezek mellett. Egy korábbi verzióját használja, ha bizonyos képek lemezkiválasztás, például mi jelenjen meg ez a cikk eltérő lesz.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be az Azure Stack portálon

Jelentkezzen be az Azure Stack portálon. Az Azure Stack portálon címe attól függ, mely az Azure Stack-termék, amelyhez csatlakozik:

* Az Azure Stack Development Kit (ASDK) az Ugrás: https://portal.local.azurestack.external.
* Az Azure Stackkel integrált rendszer nyissa meg az Azure Stack-operátorokról megadott URL-CÍMÉT.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson a **+ erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter - használat alapján kell fizetnie,-Ön-használható**  >   **Hozzon létre**. Ha nem lát **Windows Server 2016 Datacenter - használat alapján kell fizetnie,-Ön-használható** bejegyzést, lépjen kapcsolatba az Azure Stack-operátorokról. Kérje meg, hogy azok adja hozzá a Marketplace-en leírtak a [a Windows Server 2016 virtuális gép lemezképének hozzáadása az Azure Stack piactéren](../operator/azure-stack-create-and-publish-marketplace-item.md) cikk.

    ![A portál egy Windows virtuális gép létrehozásának lépései](media/azure-stack-quick-windows-portal/image01.png)
2. A **alapjai**, adjon meg egy **neve**, **felhasználónév**, és **jelszó**. Válasszon egy **előfizetés**. Hozzon létre egy **erőforráscsoport**, vagy válasszon ki egy meglévő egy, válassza ki a **hely**, és kattintson a **OK**.

    ![Az alapvető beállítások konfigurálása](media/azure-stack-quick-windows-portal/image02.png)
3. A **mérete** válassza **D1 Standard**, majd kattintson a **válassza**.  
    ![Virtuális gép méretének kiválasztása](media/azure-stack-quick-windows-portal/image03.png)

4. Az a **beállítások** lapon, végezze el a kívánt módosításokat, az alapértelmezett értékeket.
   - Verziójától kezdve az Azure Stack 1808, konfigurálhat **tárolási** , amelyek kiválaszthatja használandó *felügyelt lemezek*. Korábbi 1808 csak a nem felügyelt lemezek is használható.  
   ![A virtuális gép beállításainak konfigurálása](media/azure-stack-quick-windows-portal/image04.png)  
   Ha a beállítások elkészültek, válassza ki a **OK** folytatásához.

5. A **összefoglalás**, kattintson a **OK** a virtuális gép létrehozásához.
    ![Összefoglalás megtekintése és a virtuális gép létrehozása](media/azure-stack-quick-windows-portal/image05.png)

6. Tekintse meg az új virtuális gépet, kattintson a **összes erőforrás**, és keresse meg a virtuális gép nevét, majd kattintson a nevére, a keresési eredmények között.
    ![Tekintse meg a virtuális gép](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, a virtuális gép használatával, törölje a virtuális gép és az erőforrások. Ehhez válassza ki az erőforráscsoportot a virtuális gép oldalon, és kattintson a **törlése**.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett egy alapszintű Windows Server virtuális gépet. További információ a virtuális gépekről az Azure Stack, továbbra is [szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md).
