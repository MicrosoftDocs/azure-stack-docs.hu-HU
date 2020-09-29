---
title: A virtuálisgép-méretezési csoportok elérhetővé tétele Azure Stack központban
description: Megtudhatja, hogyan adhat hozzá a felhőalapú operátorok virtuálisgép-méretezési csoportokat Azure Stack hub Marketplace-hez.
author: sethmanheim
ms.topic: article
ms.date: 08/28/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2019
ms.openlocfilehash: 3714b69e2a65baeb0a5b0ba9a029a5b3144bee1a
ms.sourcegitcommit: 28894abb31423046f6775aadef490c467f1b1820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2020
ms.locfileid: "89089694"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>A virtuálisgép-méretezési csoportok elérhetővé tétele Azure Stack központban

A virtuálisgép-méretezési csoportok egy Azure Stack hub számítási erőforrás. A méretezési csoportok használatával azonos virtuális gépek (VM-EK) készletét helyezheti üzembe és kezelheti. A méretezési csoportok az összes azonos módon konfigurált virtuális géppel nem igénylik a virtuális gépek előzetes üzembe helyezését. A nagyméretű számítási, big data-és tároló-munkaterheléseket célzó nagy méretű szolgáltatások kiépítése egyszerűbb.

Ez a cikk végigvezeti az Azure Stack hub piactéren elérhető méretezési csoportok létrehozásának folyamatán. Az eljárás elvégzése után a felhasználók virtuálisgép-méretezési csoportokat adhatnak hozzá az előfizetésekhez.

Az Azure Stack hub virtuálisgép-méretezési csoportjai hasonlóak az Azure-beli virtuálisgép-méretezési csoportokhoz. További információkért tekintse meg a következő videókat:

* [Mark Russinovich megbeszéli az Azure-méretezési csoportokat](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtuálisgép-méretezési csoportok a Guy lugassal](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack központban a virtuálisgép-méretezési csoportok nem támogatják az autoskálázást. A méretezési csoportokhoz további példányokat adhat hozzá a Resource Manager-sablonok, a CLI vagy a PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

* **Azure stack hub Marketplace:** Regisztrálja Azure Stack központot a globális Azure-ban, hogy lehetővé tegye az elemek rendelkezésre állását az Azure Stack hub piactéren. Kövesse az [Azure stack hub regisztrálása az Azure](azure-stack-registration.md)-ban című témakör utasításait.
* **Operációs rendszer képe:** A virtuálisgép-méretezési csoport létrehozása előtt le kell töltenie a virtuálisgép-lemezképeket a méretezési csoportba a [Azure stack hub piactéren](azure-stack-download-azure-marketplace-item.md). A lemezképeknek már léteznie kell, mielőtt egy felhasználó létre tud hozni egy új méretezési készletet.

## <a name="use-the-azure-stack-hub-portal"></a>Az Azure Stack hub portál használata

1. Jelentkezzen be az Azure Stack hub portálra. Ezután nyissa meg a **minden szolgáltatás**, majd a **virtuálisgép-méretezési**csoportok elemet, majd a **számítás**területen válassza a **virtuálisgép-méretezési**csoportok lehetőséget.
   ![Virtuálisgép-méretezési csoportok kiválasztása](media/azure-stack-compute-add-scalesets/all-services.png)

2. Válassza a ***virtuálisgép-méretezési csoportok létrehozása***lehetőséget.
   ![Virtuálisgép-méretezési csoport létrehozása](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Töltse ki az üres mezőket, válasszon a legördülő listából az **operációsrendszer-lemez lemezképének**, **előfizetésének**és **példányának méretétől**függően. A **felügyelt lemezek használatához**válassza az **Igen** lehetőséget. Ezután kattintson a **Létrehozás**gombra.
    ![Virtuálisgép-méretezési csoportok konfigurálása és létrehozása](media/azure-stack-compute-add-scalesets/create.png)

4. Az új virtuálisgép-méretezési csoport megjelenítéséhez nyissa meg az **összes erőforrást**, keresse meg a virtuálisgép-méretezési csoport nevét, majd válassza ki a kívánt nevet a keresésben.
   ![A virtuálisgép-méretezési csoport megtekintése](media/azure-stack-compute-add-scalesets/search.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Lemezképek frissítése virtuálisgép-méretezési csoportokban

A virtuálisgép-méretezési csoport létrehozása után a felhasználók frissíthetik a méretezési csoport lemezképeit anélkül, hogy a méretezési csoport újból létre kellene hozni. A rendszerkép frissítésének folyamata a következő esetekben függ:

1. A virtuálisgép-méretezési csoport telepítési sablonja a **legújabb** **verziót**adja meg:  

   Ha a a `version` méretezési csoport sablonjának szakasza a **legújabb** értékre van állítva, a méretezési csoportban a méretezési csoport `imageReference` skálázási műveletei a lemezkép legújabb elérhető verzióját használják a méretezési csoport példányaihoz. A méretezés befejezése után törölheti a régebbi virtuálisgép-méretezési csoportok példányait. A,, `publisher` `offer` és `sku` változatlan marad.

   A következő JSON-példa a következőket határozza meg `latest` :  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

2. A virtuálisgép-méretezési csoport központi telepítési sablonja **nem határozza meg a legújabb** **verziót** , és a verziószámot adja meg helyette:  

    Ha a Azure Stack-kezelő egy újabb verziójú rendszerképet tölt le (és törli a régebbi verziót), a méretezési csoport nem méretezhető fel. Ez a kialakítás, mivel a méretezési csoport sablonjában megadott rendszerkép-verziónak elérhetőnek kell lennie.  

További információ: [operációsrendszer-lemezek és-lemezképek](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport méretezése

A virtuálisgép-méretezési csoport méretét megváltoztathatja nagyobb vagy kisebb értékre.

1. A portálon válassza ki a méretezési csoport elemet, majd válassza a **skálázás**lehetőséget.

2. A görgetősáv használatával állítsa be a virtuálisgép-méretezési csoport új skálázási szintjét, majd kattintson a **Mentés**gombra.

     ![A virtuálisgép-készlet méretezése](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="next-steps"></a>Következő lépések

* [Marketplace-elemek letöltése az Azure-ból Azure Stack hubhoz](azure-stack-download-azure-marketplace-item.md)
