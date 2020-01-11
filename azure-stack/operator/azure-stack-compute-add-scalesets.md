---
title: A virtuálisgép-méretezési csoportok elérhetővé tétele Azure Stack központban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá a felhőalapú operátorok virtuálisgép-méretezési csoportokat Azure Stack hub Marketplace-hez.
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
ms.openlocfilehash: 4794704678d5b344ea9d0bae9bf89f134e6bff52
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883014"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>A virtuálisgép-méretezési csoportok elérhetővé tétele Azure Stack központban

 
A virtuálisgép-méretezési csoportok egy Azure Stack hub számítási erőforrás. A velük azonos virtuális gépek (VM-EK) készletének üzembe helyezésére és kezelésére használható. A méretezési csoportok az összes azonos módon konfigurált virtuális géppel nem igénylik a virtuális gépek előzetes üzembe helyezését. A nagyméretű számítási, big data és tároló munkaterheléseket célzó nagy méretű szolgáltatások kiépítése egyszerűbb.

Ez a cikk végigvezeti a Azure Stack hub piactéren elérhető méretezési csoportok végrehajtásának lépésein. Az eljárás elvégzése után a felhasználók virtuálisgép-méretezési csoportokat adhatnak hozzá az előfizetésekhez.

Az Azure Stack hub virtuálisgép-méretezési csoportjai hasonlóak az Azure-beli virtuálisgép-méretezési csoportokhoz. További információkért tekintse meg a következő videókat:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)
* [Virtuálisgép-méretezési csoportok a Guy lugassal](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Azure Stack központban a virtuálisgép-méretezési csoportok nem támogatják az autoskálázást. A méretezési csoportokhoz további példányokat adhat hozzá a Resource Manager-sablonok, a CLI vagy a PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

* **Azure stack hub Marketplace:** Regisztrálja Azure Stack központot a globális Azure-ban, hogy lehetővé tegye a Azure Stack hub piactéren található elemek rendelkezésre állását. Kövesse az [Azure stack hub regisztrálása az Azure](azure-stack-registration.md)-ban című témakör utasításait.
* **Operációs rendszer képe:** A virtuálisgép-méretezési csoport létrehozása előtt le kell töltenie a virtuálisgép-lemezképeket a méretezési csoportba a [Azure stack hub piactéren](azure-stack-download-azure-marketplace-item.md). A lemezképeknek már léteznie kell, mielőtt egy felhasználó létre tud hozni egy új méretezési készletet.

## <a name="use-the-azure-stack-hub-portal"></a>Az Azure Stack hub portál használata

>[!IMPORTANT]  
> Az ebben a szakaszban szereplő információk a Azure Stack hub 1808-es vagy újabb verziójának használatakor érvényesek. 

1. Jelentkezzen be az Azure Stack hub portálra. Ezután nyissa meg a **minden szolgáltatás**, majd a **virtuálisgép-méretezési**csoportok elemet, majd a **számítás**területen válassza a **virtuálisgép-méretezési**csoportok lehetőséget.
   ![válassza ki a virtuálisgép-méretezési csoportokat](media/azure-stack-compute-add-scalesets/all-services.png)

2. Válassza a ***virtuálisgép-méretezési csoportok létrehozása***lehetőséget.
   ![Virtuálisgép-méretezési csoport létrehozása](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Töltse ki az üres mezőket, válasszon a legördülő listából az **operációsrendszer-lemez lemezképének**, **előfizetésének**és **példányának méretétől**függően. A **felügyelt lemezek használatához**válassza az **Igen** lehetőséget. Ezt követően kattintson a **Create** (Létrehozás) gombra.
    ![konfigurálja és hozza létre a virtuálisgép-méretezési csoportokat](media/azure-stack-compute-add-scalesets/create.png)

4. Az új virtuálisgép-méretezési csoport megjelenítéséhez nyissa meg az **összes erőforrást**, keresse meg a virtuálisgép-méretezési csoport nevét, majd válassza ki a kívánt nevet a keresésben.
   ![tekintse meg a virtuálisgép-méretezési csoport](media/azure-stack-compute-add-scalesets/search.png)

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

2. A virtuálisgép-méretezési csoport központi telepítési sablonja **nem határozza meg a legújabb** **verziót** , és a verziószámot adja meg helyette:  

    Ha a Azure Stack-kezelő egy újabb verziójú rendszerképet tölt le (és törli a régebbi verziót), a méretezési csoport nem méretezhető fel. Ez a kialakítás, mivel a méretezési csoport sablonjában megadott rendszerkép-verziónak elérhetőnek kell lennie.  

További információ: [operációsrendszer-lemezek és-lemezképek](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport méretezése

A virtuálisgép-méretezési csoport méretét méretezheti úgy, hogy nagyobb vagy kisebb legyen.

1. A portálon válassza ki a méretezési csoport elemet, majd válassza a **skálázás**lehetőséget.

2. A görgetősáv használatával állítsa be a virtuálisgép-méretezési csoport új skálázási szintjét, majd kattintson a **Mentés**gombra.

     ![A virtuálisgép-készlet méretezése](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="next-steps"></a>Következő lépések

* [Marketplace-elemek letöltése az Azure-ból Azure Stack hubhoz](azure-stack-download-azure-marketplace-item.md)
