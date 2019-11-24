---
title: A virtuálisgép-méretezési csoportok elérhetővé tétele a Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá a felhőalapú operátorok virtuálisgép-méretezési csoportokat Azure Stack Marketplace-hez.
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: ed73441f1a8d3c1c722ce35d5deda9ab7387283b
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974097"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>A virtuálisgép-méretezési csoportok elérhetővé tétele Azure Stack

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*
  
A virtuálisgép-méretezési csoportok egy Azure Stack számítási erőforrás. A velük azonos virtuális gépek (VM-EK) készletének üzembe helyezésére és kezelésére használható. A méretezési csoportok az összes azonos módon konfigurált virtuális géppel nem igénylik a virtuális gépek előzetes üzembe helyezését. A nagyméretű számítási, big data és tároló munkaterheléseket célzó nagy méretű szolgáltatások kiépítése egyszerűbb.

Ez a cikk végigvezeti a Azure Stack piactéren elérhető méretezési csoportok végrehajtásának lépésein. Az eljárás elvégzése után a felhasználók virtuálisgép-méretezési csoportokat adhatnak hozzá az előfizetésekhez.

A Azure Stack a virtuálisgép-méretezési csoportok hasonlóak az Azure-beli virtuálisgép-méretezési csoportokhoz. További információkért tekintse meg a következő videókat:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)
* [Virtuálisgép-méretezési csoportok a Guy lugassal](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack a virtuálisgép-méretezési csoportok nem támogatják az autoskálázást. A méretezési csoportokhoz további példányokat adhat hozzá a Resource Manager-sablonok, a CLI vagy a PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

* **Azure stack Marketplace:** Azure Stack regisztrálása a globális Azure-ban, amely lehetővé teszi a Azure Stack piactéren található elemek rendelkezésre állását. Kövesse a [Azure stack regisztrálása az Azure](azure-stack-registration.md)-ban című témakör utasításait.
* **Operációs rendszer képe:** A virtuálisgép-méretezési csoport létrehozása előtt le kell töltenie a virtuálisgép-lemezképeket a méretezési csoportba a [Azure stack piactéren](azure-stack-download-azure-marketplace-item.md). A lemezképeknek már léteznie kell, mielőtt egy felhasználó létre tud hozni egy új méretezési készletet.

## <a name="use-the-azure-stack-portal"></a>A Azure Stack portál használata

>[!IMPORTANT]  
> Az ebben a szakaszban szereplő információk a Azure Stack 1808-es vagy újabb verziójának használatakor érvényesek. Ha a verzió 1807-es vagy korábbi, tekintse meg [a virtuálisgép-méretezési csoport hozzáadása (a 1808 előtt)](#add-the-virtual-machine-scale-set-prior-to-version-1808)című témakört.

1. Jelentkezzen be a Azure Stack portálra. Ezután nyissa meg a **minden szolgáltatás**, majd a **virtuálisgép-méretezési**csoportok elemet, majd a **számítás**területen válassza a **virtuálisgép-méretezési**csoportok lehetőséget.
   ![válassza ki a virtuálisgép-méretezési csoportokat](media/azure-stack-compute-add-scalesets/all-services.png)

2. Válassza a ***virtuálisgép-méretezési csoportok létrehozása***lehetőséget.
   ![Virtuálisgép-méretezési csoport létrehozása](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Töltse ki az üres mezőket, válasszon a legördülő listából az **operációsrendszer-lemez lemezképének**, **előfizetésének**és **példányának méretétől**függően. A **felügyelt lemezek használatához**válassza az **Igen** lehetőséget. Ezt követően kattintson a **Create** (Létrehozás) gombra.
    ![konfigurálja és hozza létre a virtuálisgép-méretezési csoportokat](media/azure-stack-compute-add-scalesets/create.png)

4. Az új virtuálisgép-méretezési csoport megjelenítéséhez nyissa meg az **összes erőforrást**, keresse meg a virtuálisgép-méretezési csoport nevét, majd válassza ki a kívánt nevet a keresésben.
   ![tekintse meg a virtuálisgép-méretezési csoport](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>A virtuálisgép-méretezési csoport hozzáadása (a 1808-es verzió előtt)

>[!IMPORTANT]  
> Az ebben a szakaszban szereplő információk akkor érvényesek, ha a Azure Stack a 1808-es verziónál korábbi verzióját használja. Ha a 1808-es vagy újabb verzióját használja, tekintse meg [a Azure stack portál használatát](#use-the-azure-stack-portal)ismertető témakört.

1. Nyissa meg a Azure Stack Marketplace-t, és kapcsolódjon az Azure-hoz. Válassza a **piactér-kezelés**elemet, majd kattintson **a + Hozzáadás az Azure-ból**lehetőségre.

    ![Azure Stack Marketplace-kezelés](media/azure-stack-compute-add-scalesets/image01.png)

2. Adja hozzá és töltse le a virtuálisgép-méretezési csoport Marketplace-elemét.

    ![Virtuálisgép-méretezési csoport Marketplace-eleme](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Lemezképek frissítése virtuálisgép-méretezési csoportokban

A virtuálisgép-méretezési csoport létrehozása után a felhasználók frissíthetik a méretezési csoport lemezképeit anélkül, hogy a méretezési csoport újból létre kellene hozni. A rendszerkép frissítésének folyamata a következő esetekben függ:

1. A virtuálisgép-méretezési csoport telepítési sablonja a **legújabb** **verziót**adja meg:  

   Ha a méretezési csoport sablonjának `imageReference` szakaszában a `version` a **legújabb** értékre van állítva, a méretezési csoportban a méretezési csoportban lévő skálázási műveletek a lemezkép legújabb elérhető verzióját használják a méretezési csoport példányaihoz. A méretezés befejezése után törölheti a régebbi virtuálisgép-méretezési csoportok példányait. A `publisher`, `offer`és `sku` értékei változatlanok maradnak.

   A következő JSON-példa a `latest`t adja meg:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   A méretezés előtt új rendszerképet kell letöltenie, majd le kell töltenie az új rendszerképet:  

   * Ha Azure Stack piactéren lévő rendszerkép a méretezési csoport rendszerképének újabb verziója, töltse le az új rendszerképet, amely felváltja a régebbi rendszerképet. A rendszerkép cseréje után a felhasználók továbbra is felméretezhetők.

   * Ha Azure Stack piactéren a rendszerkép verziója megegyezik a méretezési csoport rendszerképével, törölje a méretezési csoportokban használt képet, majd töltse le az új rendszerképet. Az eredeti rendszerkép eltávolításának és az új rendszerkép letöltésének ideje alatt a vertikális felskálázás nem lehetséges.

   Ez a folyamat a 1803-es verzióval bevezetett ritka fájlformátumot használó lemezképek újraszindikátusához szükséges.

2. A virtuálisgép-méretezési csoport központi telepítési sablonja **nem határozza meg a legújabb** **verziót** , és a verziószámot adja meg helyette:  

    Ha újabb verziót tölt le (amely megváltoztatja az elérhető verziót), a méretezési csoport nem méretezhető fel. Ez a kialakítás, mivel a méretezési csoport sablonjában megadott rendszerkép-verziónak elérhetőnek kell lennie.  

További információ: [operációsrendszer-lemezek és-lemezképek](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport méretezése

A virtuálisgép-méretezési csoport méretét méretezheti úgy, hogy nagyobb vagy kisebb legyen.

1. A portálon válassza ki a méretezési csoport elemet, majd válassza a **skálázás**lehetőséget.

2. A görgetősáv használatával állítsa be a virtuálisgép-méretezési csoport új skálázási szintjét, majd kattintson a **Mentés**gombra.

     ![A virtuálisgép-készlet méretezése](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport eltávolítása

A virtuálisgép-méretezési csoport katalógusbeli elemének eltávolításához futtassa a következő PowerShell-parancsot:

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> Előfordulhat, hogy a gyűjtemény elemét nem távolítja el azonnal. Előfordulhat, hogy többször is frissítenie kell a portált, mielőtt az elem megjelenik Azure Stack piactéren.

## <a name="next-steps"></a>További lépések

* [Azure Marketplace-elemek letöltése az Azure-ból az Azure Stackbe](azure-stack-download-azure-marketplace-item.md)
