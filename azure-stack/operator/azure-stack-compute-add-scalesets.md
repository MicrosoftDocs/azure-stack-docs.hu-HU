---
title: Virtual Machine Scale Sets elérhetővé az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan a felhő üzemeltetője adhat hozzá virtuálisgép-méretezési csoportok az Azure Stack piactéren
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 2cca789487803124e1a7b24efa199d7f68760fb6
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453408"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>A Virtual machine scale sets elérhetővé az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*
  
A Virtual machine scale sets az Azure Stack számítási erőforrás. Üzembe helyezéséhez és az azonos virtuális gépek kezelésére használhatja őket. Mivel az összes virtuális gép ugyanúgy van konfigurálva, a méretezési csoportok nem igénylik a virtuális gépek előzetes kiépítését. Sokkal jobban is egyszerűsödik a nagy számítási igényű, big data és tárolóalapú számítási feladatokra nagyméretű szolgáltatások kiépítése.

Ez a cikk végigvezeti a méretezési csoportok az Azure Stack piactéren elérhetővé. Az eljárás befejezése után a felhasználók adhat hozzá, azok az előfizetések virtuálisgép-méretezési csoport állítja be.

Virtuális gép méretezési csoportok az Azure Stack Azure-beli virtuális gépek méretezési hasonlóak. További információkért tekintse meg a következő videót:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) (A virtuálisgép-méretezési csoportokról Guy Bowerman mesél)

Az Azure Stacken a virtuálisgép-méretezési csoportok nem támogatják az automatikus méretezés. További példányokat adhat hozzá egy méretezési csoportot a Resource Manager-sablonok, a parancssori felület vagy a PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

* **A Marketplace-en:** Regisztrálja az Azure Stack a globális Azure-ral ahhoz, hogy a piactéren elemek rendelkezésre állását. Kövesse a [regisztrálása az Azure Stack az Azure-ral](azure-stack-registration.md).
* **Operációs rendszer lemezképét:** Egy virtuálisgép-méretezési készlet létrehozása előtt le kell töltenie a Virtuálisgép-rendszerképek a VMSS a használatra a [Azure Stack piactéren](azure-stack-download-azure-marketplace-item.md). A lemezképek már jelen lehetett, mielőtt egy felhasználó hozhat létre egy új VMSS kell lennie.

## <a name="use-the-azure-stack-portal"></a>Az Azure Stack portal használata

>[!IMPORTANT]  
> Ebben a szakaszban található információk használata az Azure Stack 1808 vagy újabb verziójára vonatkozik. Ha a verzió 1807 vagy korábbi, lásd: [hozzáadása a Virtual Machine Scale Set (előtt 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808).

1. Jelentkezzen be az Azure Stack portálon. Folytassa a **minden szolgáltatás**, majd **a Virtual machine scale sets**, majd a **számítási**válassza **a Virtual machine scale sets**.
   ![Válassza ki a virtuálisgép-méretezési csoportok](media/azure-stack-compute-add-scalesets/all-services.png)

2. Válassza ki a létrehozás ***a Virtual machine scale sets***.
   ![Virtuálisgép-méretezési csoport létrehozása](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Az üres mezőket kitölteni, választhat a legördülő menük a **operációs rendszer lemezképét**, **előfizetés**, és **példány mérete**. Válassza ki **Igen** a **felügyelt lemezek használata**. Kattintson a **létrehozás**.
    ![Konfigurálása és létrehozása](media/azure-stack-compute-add-scalesets/create.png)

4. Tekintse meg az új virtuális gép méretezési csoportot, nyissa meg a **összes erőforrás**, keresse meg a virtuális gép méretezési csoport nevét, és válassza ki a nevét a keresés.
   ![Megtekintheti a méretezési csoportban](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Adja hozzá a virtuális gép méretezési csoportot (előtt 1808 verzió)

>[!IMPORTANT]  
> Ebben a szakaszban található információk vonatkozik, ha az Azure Stack 1808 előtti verziót használja. Ha 1808 vagy újabb verzióját használja, lásd: [használata az Azure Stack portálon](#use-the-azure-stack-portal).

1. Nyissa meg az Azure Stack piactéren, és csatlakozzon az Azure-bA. Válassza ki **Marketplace felügyeleti**, majd kattintson a **+ Hozzáadás az Azure-ból**.

    ![Marketplace-en kezelése](media/azure-stack-compute-add-scalesets/image01.png)

2. Adja hozzá, és töltse le a virtuális gép méretezési készlet Piactéri elem.

    ![Virtuálisgép-méretezési csoportot](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>A virtuális gép méretezési lemezképek frissítéséhez

Miután létrehozott egy virtuálisgép-méretezési csoportot, a felhasználók frissíthetik rendszerképeket a méretezési csoport anélkül, hogy a méretezési csoportot, hogy újra létre kell hozni. A lemezképek frissítésének folyamata attól függ, hogy a következő esetekben:

1. Adja meg a virtuális gép méretezési csoport üzembe helyezési sablonjának **legújabb** a **verzió**:  

   Ha a `version` értékre van állítva **legújabb** a a `imageReference` méretezési csoport sablonjának szakaszában állítsa be, a méretezési csoport példányaihoz használata a méretezési műveletek vertikális felskálázás a rendszerkép elérhető legújabb verzióját. Vertikális felskálázás befejezése után törölheti a régebbi virtuális gép méretezési csoportok példányai. A tartozó értékeket `publisher`, `offer`, és `sku` változatlanok maradnak.

   Az alábbi JSON-példa `latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Vertikális felskálázás használni egy új rendszerképet, le kell töltenie, hogy új lemezképet:  

   * Ha a lemezképet a marketplace-en, mint a rendszerképet a méretezési csoportban lévő egy újabb verzióra, töltse le az új rendszerképet, amely felváltja a régi lemezképet. Miután váltja fel a képet, a felhasználó folytassa vertikális felskálázás.

   * Ha a rendszerkép verziószámát a marketplace-en ugyanaz, mint a rendszerképet a méretezési csoportban, törölnie kell a rendszerképet a méretezési csoportban lévő használatban lévő, és töltse le az új lemezképet. Az eredeti rendszerkép eltávolítása és az új kép letöltése közötti időszakban nem skálázhatja.

   Ez a folyamat megadása kötelező újra szindikálása, amellyel használja a ritka fájlformátum, 1803-verzióval jelent meg.

2. Virtuálisgép-méretezési csoport üzembe helyezési sablon beállítása **legújabb nem határoz meg** a **verzió** , és adja meg helyette egy verziószámot:  

    Ha egy újabb verzióval (amely megváltoztatja a rendelkezésre álló verzió) egy rendszerképet tölt le, a méretezési csoportban nem vertikális felskálázás. Ez szándékosan van, mert a rendszerkép verziószámát, a méretezési csoport sablonjában megadott elérhetőnek kell lennie.  

További információkért lásd: [operációsrendszer-lemezek és lemezképek](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési készlet méretezése

Egy virtuálisgép-méretezési csoport, győződjön meg arról, hogy kisebb vagy nagyobb értékre mérete skálázhatja.

1. A portálon, válassza ki a méretezési majd **méretezés**.

2. A virtuális gép méretezési méretezés új szintjének beállítása a csúszka segítségével, és kattintson a **mentése**.

     ![A készlet méretezése](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Távolítsa el a virtuálisgép-méretezési csoportot

Egy virtuális gép eltávolítása a gyűjteményelem készlet méretezése, futtassa a következő PowerShell-parancsot:

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> Előfordulhat, hogy a gyűjteményelem nem azonnal eltávolítani. Előfordulhat, hogy frissítse a több alkalommal a portált az elem jeleníti meg a Marketplace-ről eltávolítása előtt kell.

## <a name="next-steps"></a>További lépések

* [Azure Marketplace-elemek letöltése az Azure-ból az Azure Stackbe](azure-stack-download-azure-marketplace-item.md)
