---
title: Windows rendszerű virtuális gép létrehozása Azure Stack hub-portálon
description: Megtudhatja, hogyan hozhat létre Windows Server 2016 virtuális gépet (VM) az Azure Stack hub Portal használatával.
author: mattbriggs
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/06/2020
ms.openlocfilehash: 77c7e3484186a0bea1f6220330338128aad72fad
ms.sourcegitcommit: 2d2ae0b6db2e4f43f8496b184f30cddbb08b2cbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91815418"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>Gyors útmutató: Windows Server rendszerű virtuális gép létrehozása az Azure Stack hub portálon

Ismerje meg, hogyan hozhat létre Windows Server 2016 virtuális gépet (VM) az Azure Stack hub portál használatával.

> [!NOTE]  
> A cikkben található képernyőképek úgy frissülnek, hogy megfeleljenek a Azure Stack hub 1808-es verziójában bevezetett felhasználói felületnek. 1808 támogatja a *felügyelt lemezek* használatát a nem felügyelt lemezeken kívül. Ha egy korábbi verziót használ, néhány rendszerkép (például a lemez kiválasztása) nem fog megjelenni a cikkben.  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Jelentkezzen be az Azure Stack hub portálra

Jelentkezzen be az Azure Stack hub portálra. Az Azure Stack hub-portál címe attól függ, hogy melyik Azure Stack hub-termékhez csatlakozik:

* A Azure Stack Development Kit (ASDK) esetében válassza a következőt: `https://portal.local.azurestack.external` .
* Azure Stack hub integrált rendszer esetén keresse meg azt az URL-címet, amelyet a Azure Stack hub-operátor biztosít.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Válassza **az erőforrás létrehozása**  >  **számítás**lehetőséget. Keresse meg a következőt: ` Windows Server 2016 Datacenter - Pay as you use` .
    Ha nem látja a **Windows Server 2016 Datacenter – utólagos használatú** bejegyzést, forduljon a Azure stack hub Cloud operatorhoz, és kérje meg, hogy adja hozzá a rendszerképet a Azure stack hub piactérhez. Útmutatásért a felhő operátora hivatkozhat az [egyéni Azure stack hub Marketplace-elem létrehozására és közzétételére](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Windows Server 2016 Datacenter – használaton kívüli fizetés](./media/azure-stack-quick-windows-portal/image1.png)

1. Kattintson a **Létrehozás** gombra.

    ![Erőforrás létrehozása](./media/azure-stack-quick-windows-portal/image2.png)

1. Adja meg a **név**, a **lemez típusa**, a **Felhasználónév**és a **jelszó** értéket az **alapok**területen. Válasszon egy **előfizetést**. Hozzon létre egy **erőforráscsoportot**, vagy válasszon ki egy meglévőt, válasszon ki egy **helyet**, majd kattintson **az OK gombra**.

    ![Virtuális gép létrehozása – alapismeretek](./media/azure-stack-quick-windows-portal/image3.png)

1. A **méret**területen válassza a **D1_v2** lehetőséget, majd válassza a **kiválasztás**lehetőséget.

    ![VM-méret létrehozása](./media/azure-stack-quick-windows-portal/image4.png)

1. A **Beállítások** lapon végezze el a kívánt módosításokat az alapértelmezett értékeken. A kívánt nyilvános bejövő portokat a kapcsolódó legördülő listából kell konfigurálnia. Ha elkészült, kattintson **az OK gombra**.

    ![Virtuális gép létrehozása – beállítások](./media/azure-stack-quick-windows-portal/image5.png)

1. A virtuális gép létrehozásához kattintson **az OK gombra** az **Összefoglalás** területen.

    ![Virtuális gép létrehozása – összefoglalás](./media/azure-stack-quick-windows-portal/image6.png)

1. Az új virtuális gép áttekintéséhez válassza a **Virtual Machines** lehetőséget. Keresse meg a virtuális gép nevét, majd válassza ki a virtuális gépet a keresési eredmények között.

![VIRTUÁLIS gép létrehozása – virtuális gép keresése](./media/azure-stack-quick-windows-portal/image7.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális gép használatával, törölje a virtuális gépet és annak erőforrásait. Ehhez válassza ki az erőforráscsoportot a virtuális gép lapon, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy alapszintű Windows Server rendszerű virtuális gépet telepített. Ha többet szeretne megtudni Azure Stack hub virtuális gépekről, folytassa a [Azure stack hub-beli virtuális gépekkel kapcsolatos szempontokat](azure-stack-vm-considerations.md).
