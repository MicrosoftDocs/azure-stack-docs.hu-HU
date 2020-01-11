---
title: Windows rendszerű virtuális gép létrehozása Azure Stack hub Portalon | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Windows Server 2016 virtuális gépet (VM) az Azure Stack hub Portal használatával.
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
ms.openlocfilehash: 53e33ed9da57209c1f937cbf72eb2c3882dfd1cd
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878577"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>Gyors útmutató: Windows Server rendszerű virtuális gép létrehozása az Azure Stack hub portálon

Ismerje meg, hogyan hozhat létre Windows Server 2016 virtuális gépet (VM) az Azure Stack hub portál használatával.

> [!NOTE]  
> A cikkben található képernyőképek úgy frissülnek, hogy megfeleljenek a Azure Stack hub 1808-es verziójában bevezetett felhasználói felületnek. 1808 támogatja a *felügyelt lemezek* használatát a nem felügyelt lemezeken kívül. Ha egy korábbi verziót használ, néhány rendszerkép (például a lemez kiválasztása) nem fog megjelenni a cikkben.  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Jelentkezzen be az Azure Stack hub portálra

Jelentkezzen be az Azure Stack hub portálra. Az Azure Stack hub-portál címe attól függ, hogy melyik Azure Stack hub-termékhez csatlakozik:

* A Azure Stack Development Kit (ASDK) esetében válassza a következőt: https://portal.local.azurestack.external.
* Azure Stack hub integrált rendszer esetén keresse meg azt az URL-címet, amelyet a Azure Stack hub-operátor biztosít.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Kattintson **az + erőforrás létrehozása** > **számítás** > **Windows Server 2016 Datacenter – utólagos használatú** > **Létrehozás**lehetőségre. <br> Ha nem látja a **Windows server 2016 Datacenter – utólagos használatú** bejegyzést, forduljon a Azure stack hub-kezelőhöz, és kérje meg, hogy vegye fel a piactérre a [Windows Server 2016 virtuálisgép-rendszerkép hozzáadása a Azure stack hub Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) -re című cikkben leírtak szerint.

    ![Windows rendszerű virtuális gép létrehozásának lépései a portálon](media/azure-stack-quick-windows-portal/image01.png)

2. Az **alapismeretek**területen adjon meg egy **nevet**, egy **felhasználónevet**és egy **jelszót**. Válasszon egy **előfizetést**. Hozzon létre egy **erőforráscsoportot**, vagy válasszon ki egy meglévőt, válasszon ki egy **helyet**, majd kattintson **az OK**gombra.

    ![Az alapvető beállítások konfigurálása](media/azure-stack-quick-windows-portal/image02.png)

3. A **méret**területen válassza a **D1 standard**lehetőséget, majd kattintson a **kiválasztás**elemre.  

    ![Virtuális gép méretének kiválasztása](media/azure-stack-quick-windows-portal/image03.png)

4. A **Beállítások** lapon végezze el a kívánt módosításokat az alapértelmezett értékeken.
   - A Azure Stack hub 1808-es verziójától kezdve konfigurálhatja a **tárolót** , ahol a *felügyelt lemezek*használatát is választhatja. A 1808 előtti verziókban csak a nem felügyelt lemezek használhatók.  

   ![Virtuális gép beállításainak konfigurálása](media/azure-stack-quick-windows-portal/image04.png)  

   Ha elkészült a konfigurációk, kattintson **az OK gombra** a folytatáshoz.

5. Az **Összefoglalás**területen kattintson az **OK** gombra a virtuális gép létrehozásához.
    ![az összefoglalás megtekintése és a virtuális gép létrehozása](media/azure-stack-quick-windows-portal/image05.png)

6. Az új virtuális gép megjelenítéséhez kattintson a **minden erőforrás**elemre, keresse meg a virtuális gép nevét, majd válassza ki a keresési eredmények között.

    ![Virtuális gép](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális gép használatával, törölje a virtuális gépet és annak erőforrásait. Ehhez válassza ki az erőforráscsoportot a virtuális gép lapon, és kattintson a **Törlés**gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy alapszintű Windows Server rendszerű virtuális gépet telepített. Ha többet szeretne megtudni Azure Stack hub virtuális gépekről, folytassa a [Azure stack hub-beli virtuális gépekkel kapcsolatos szempontokat](azure-stack-vm-considerations.md).
