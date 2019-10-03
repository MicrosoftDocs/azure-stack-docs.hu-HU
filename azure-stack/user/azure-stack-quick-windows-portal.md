---
title: Windows rendszerű virtuális gép létrehozása Azure Stack portálon | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Windows Server 2016 rendszerű virtuális gépet (VM) a Azure Stack-portálon.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 95fef782ca7efe09f7c93fbf0e28e81ed34d8166
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71823925"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>Gyors útmutató: Windows Server rendszerű virtuális gép létrehozása a Azure Stack portálon

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Megtudhatja, hogyan hozhat létre Windows Server 2016 virtuális gépet (VM) a Azure Stack portál használatával.

> [!NOTE]  
> A cikkben szereplő Képernyőképek a Azure Stack 1808-es verziójában bevezetett felhasználói felülettel egyeznek meg. 1808 támogatja a *felügyelt lemezek* használatát a nem felügyelt lemezeken kívül. Ha egy korábbi verziót használ, néhány rendszerkép (például a lemez kiválasztása) nem fog megjelenni a cikkben.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Bejelentkezés a Azure Stack portálra

Jelentkezzen be a Azure Stack portálra. A Azure Stack-portál címe attól függ, hogy Azure Stack melyik termékhez csatlakozik:

* A Azure Stack Development Kit (ASDK) esetében válassza a következőt: https://portal.local.azurestack.external.
* Azure Stack integrált rendszer esetén keresse meg a Azure Stack operátor által megadott URL-címet.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Kattintson **az + erőforrás létrehozása** > **számítási**@no__t – 3**Windows Server 2016 Datacenter – utólagos használatú** > **Létrehozás**lehetőségre. <br> Ha nem látja a **Windows server 2016 Datacenter – utólagos használatú** bejegyzést, forduljon a Azure stack operátorhoz, és kérje meg, hogy vegye fel a piactérre a [Windows Server 2016 virtuálisgép-rendszerkép hozzáadása a Azure stack Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) -re című cikkben leírtak szerint. .

    ![Windows rendszerű virtuális gép létrehozásának lépései a portálon](media/azure-stack-quick-windows-portal/image01.png)

2. Az **alapismeretek**területen adjon meg egy **nevet**, egy **felhasználónevet**és egy **jelszót**. Válasszon egyelőfizetést. Hozzon létre egy **erőforráscsoportot**, vagy válasszon ki egy meglévőt, válasszon ki egy **helyet**, majd kattintson **az OK**gombra.

    ![Alapvető beállítások konfigurálása](media/azure-stack-quick-windows-portal/image02.png)

3. A **méret**területen válassza a **D1 standard**lehetőséget, majd kattintson a **kiválasztás**elemre.  

    ![Virtuális gép méretének kiválasztása](media/azure-stack-quick-windows-portal/image03.png)

4. A **Beállítások** lapon végezze el a kívánt módosításokat az alapértelmezett értékeken.
   - A Azure Stack 1808-es verziótól kezdődően konfigurálhatja a tárolót, ahol a *felügyelt lemezek*használatát is kiválaszthatja. A 1808 előtti verziókban csak a nem felügyelt lemezek használhatók.  

   ![Virtuális gép beállításainak konfigurálása](media/azure-stack-quick-windows-portal/image04.png)  

   Ha elkészült a konfigurációk, kattintson **az OK gombra** a folytatáshoz.

5. Az **Összefoglalás**területen kattintson az **OK** gombra a virtuális gép létrehozásához.
    @no__t – 0View összefoglalása és a virtuális gép létrehozása @ no__t-1

6. Az új virtuális gép megjelenítéséhez kattintson a **minden erőforrás**elemre, keresse meg a virtuális gép nevét, majd válassza ki a keresési eredmények között.

    ![Virtuális gép](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális gép használatával, törölje a virtuális gépet és annak erőforrásait. Ehhez válassza ki az erőforráscsoportot a virtuális gép lapon, és kattintson a **Törlés**gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy alapszintű Windows Server rendszerű virtuális gépet telepített. Ha többet szeretne megtudni a Azure Stack virtuális gépekről, folytassa a [Azure stack virtuális gépekkel kapcsolatos szempontokat](azure-stack-vm-considerations.md).
