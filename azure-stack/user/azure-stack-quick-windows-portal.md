---
title: Windows virtuális gép létrehozása az Azure Stack portálon |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Windows Server 2016 virtuális gép (VM) az Azure Stack portálon.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cc9a6baa3c71e58c2671b1f1b221e18a0c4f38c1
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816170"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>Gyors útmutató: Windows server rendszerű virtuális gép létrehozása az Azure Stack portal

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ismerje meg, hogyan hozhat létre egy Windows Server 2016 virtuális gép (VM) az Azure Stack-portál használatával.

> [!NOTE]  
> Ebben a cikkben a képernyőképek a felhasználói felület az Azure Stack verzió 1808 bevezetett megfelelően frissülnek. 1808 használatának támogatásával bővült *felügyelt lemezek* nem felügyelt lemezek mellett. Egy korábbi verzióját használja, ha bizonyos képek lemezkiválasztás, például mi jelenjen meg ez a cikk eltérő lesz.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be az Azure Stack portálon

Jelentkezzen be az Azure Stack portálon. Az Azure Stack portálon címe attól függ, mely az Azure Stack-termék, amelyhez csatlakozik:

* Az az Azure Stack Development Kit (ASDK), nyissa meg: https://portal.local.azurestack.external.
* Az Azure Stackkel integrált rendszer nyissa meg az Azure Stack-operátorokról megadott URL-CÍMÉT.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Kattintson a **+ erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter - használat alapján kell fizetnie,-Ön-használható**  >   **Hozzon létre**. <br> Ha nem látja a **Windows Server 2016 Datacenter - használat alapján kell fizetnie,-Ön-használható** bejegyzést, lépjen kapcsolatba az Azure Stack-operátorokról, és kérje meg, hogy azok adja hozzá a Marketplace-en leírtak a [, a Windows Server 2016 virtuális gép lemezképének hozzáadása a Az Azure Stack piactéren](../operator/azure-stack-create-and-publish-marketplace-item.md) cikk.

    ![A lépések Windows virtuális gép létrehozása a portálon](media/azure-stack-quick-windows-portal/image01.png)

2. A **alapjai**, adjon meg egy **neve**, **felhasználónév**, és **jelszó**. Válasszon egy **előfizetés**. Hozzon létre egy **erőforráscsoport**, vagy válasszon ki egy meglévő egy, válassza ki a **hely**, és kattintson a **OK**.

    ![Az alapvető beállítások konfigurálása](media/azure-stack-quick-windows-portal/image02.png)

3. Alatt **mérete**válassza **D1 Standard**, majd kattintson a **kiválasztása**.  

    ![Válassza ki a virtuális gép mérete](media/azure-stack-quick-windows-portal/image03.png)

4. Az a **beállítások** lapon, végezze el a kívánt módosításokat, az alapértelmezett értékeket.
   - Verziójától kezdve az Azure Stack 1808, konfigurálhat **tárolási** , amelyek kiválaszthatja használandó *felügyelt lemezek*. A 1808 előtti verziókban csak a nem felügyelt lemezek is használható.  

   ![Virtuális gép beállításainak konfigurálása](media/azure-stack-quick-windows-portal/image04.png)  

   Ha a beállítások elkészültek, válassza ki a **OK** folytatásához.

5. A **összefoglalás**, kattintson a **OK** a virtuális gép létrehozásához.
    ![Összefoglalás megtekintése és a virtuális gép létrehozása](media/azure-stack-quick-windows-portal/image05.png)

6. Az új virtuális gép megtekintéséhez kattintson **összes erőforrás**, és keresse meg a virtuális gép nevét, majd válassza ki a keresési eredmények között.

    ![Tekintse meg a virtuális gép](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, a virtuális gép, törölje a virtuális gép és az erőforrásait. Ehhez válassza ki az erőforráscsoportot a virtuális gép oldalon, és kattintson a **törlése**.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett egy alapszintű Windows Server virtuális Gépet. Azure Stack virtuális gépekkel kapcsolatos további információkért folytassa [szempontok az Azure Stack-beli virtuális gépek](azure-stack-vm-considerations.md).
