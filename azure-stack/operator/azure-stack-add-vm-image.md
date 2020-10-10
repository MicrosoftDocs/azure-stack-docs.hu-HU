---
title: Egyéni virtuálisgép-rendszerkép hozzáadása Azure Stack hubhoz
description: Ismerje meg, hogyan adhat hozzá vagy távolíthat el egy egyéni virtuálisgép-rendszerképet Azure Stack hubhoz.
author: sethmanheim
ms.topic: how-to
ms.date: 10/09/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 5266766786cd359446e8313ec19e07746b227fb2
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899482"
---
# <a name="add-and-remove-a-custom-vm-image-to-azure-stack-hub"></a>Egyéni virtuálisgép-rendszerkép hozzáadása és eltávolítása Azure Stack hubhoz

Azure Stack hub-ban operátorként hozzáadhat egyéni virtuálisgép-rendszerképet a piactérhez, és elérhetővé teheti azt a felhasználók számára. A virtuálisgép-rendszerképeket a rendszergazdai portálon vagy a Windows PowerShellen keresztül adhatja hozzá az Azure Stack Hub Marketplace-hez. Használja a globális Microsoft Azure Marketplace rendszerképét az egyéni rendszerkép alapjaként, vagy hozzon létre egy sajátot a Hyper-V használatával.

## <a name="add-an-image"></a>Kép hozzáadása

Az általánosított és speciális rendszerképek hozzáadására vonatkozó utasításokat a felhasználói útmutató **számítási** szakaszában találja. Létre kell hoznia egy általánosított rendszerképet, mielőtt a rendszerképet felkínálja a felhasználóknak. Útmutatásért lásd: [virtuális gép áthelyezése Azure stack hub-ra – áttekintés](../user/vm-move-overview.md). A bérlők számára elérhető rendszerképek létrehozásakor a felhasználói portál vagy a bérlői címtár végpontjai helyett a Azure Stack hub felügyeleti portált vagy a rendszergazdai végpontokat használja.

A képek elérhetővé tételéhez két lehetőség közül választhat:

- **A rendszerkép csak Azure Resource Manager használatával érhető el**  
  Ha a lemezképet a Azure stack hub felügyeleti portálon keresztül adja hozzá a **számítási**  >  **lemezképekben**, az összes bérlő hozzáférhet a lemezképhez. A felhasználóknak azonban Azure Resource Manager sablont kell használniuk az eléréséhez. A Azure Stack hub piactéren nem jelenik meg.

- **Rendszerkép nyújtása az Azure Stack hub piactéren**  
    Miután hozzáadta a rendszerképet a Azure Stack hub felügyeleti portálján, létrehozhat egy Piactéri ajánlatot. Útmutatásért lásd: [egyéni Azure stack hub Marketplace-elem létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md).

## <a name="add-a-platform-image"></a>Platform rendszerképének hozzáadása

Ha Azure Stack hubhoz szeretné hozzáadni a platformot, használja a Azure Stack hub felügyeleti portált vagy a végpontot a PowerShell használatával. Először létre kell hoznia egy általánosított virtuális merevlemezt. További információ: [virtuális gép áthelyezése Azure stack hub-ra – áttekintés](../user/vm-move-overview.md).

### <a name="portal"></a>[Portál](#tab/image-add-portal)

Adja hozzá a virtuálisgép-rendszerképet Azure Stack hub-operátorként a portál használatával.

1. Jelentkezzen be Azure Stack hubhoz operátorként. A menüben válassza a **minden szolgáltatás**  >  **számítási**  >  **lemezképek**  >  **Hozzáadás**lehetőséget.

   ![Virtuálisgép-rendszerkép hozzáadása](./media/azure-stack-add-vm-image/tca4.png)

2. A **rendszerkép létrehozása**területen adja meg a **közzétevőt**, az **ajánlatot**, az **SKU**-t, a **verziót**és az operációsrendszer-lemez blob URI-ját. Ezután válassza a **Létrehozás** lehetőséget a virtuálisgép-rendszerkép létrehozásának megkezdéséhez.

   ![Egyéni rendszerkép közvetlen telepítési felhasználói felülete](./media/azure-stack-add-vm-image/tca5.png)

   A rendszerkép sikeres létrehozása után a virtuális gép rendszerképének állapota **sikeresre**változik.

3. Rendszerkép hozzáadásakor csak Azure Resource Manager-alapú sablonok és PowerShell-telepítések esetén érhető el. Ha egy képet Piactéri elemként kíván elérhetővé tenni a felhasználók számára, tegye közzé a piactér-elemeket a piactér-elem [létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című cikkben ismertetett lépések alapján. Ügyeljen rá, hogy a **közzétevő**, az **ajánlat**, az **SKU**és a **Version** értékeket jegyezze fel. A Resource Manager-sablon szerkesztéséhez és az egyéni. azpkg való Manifest.jséhez szüksége lesz rájuk.

### <a name="powershell"></a>[PowerShell](#tab/image-add-ps)

 Virtuálisgép-rendszerkép hozzáadása Azure Stack hub-kezelőként a PowerShell használatával.

1. [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).  

2. Jelentkezzen be Azure Stack hubhoz operátorként. Útmutatásért lásd: [bejelentkezés Azure stack hubhoz operátorként](azure-stack-powershell-configure-admin.md).

3. Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa a következőket:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<Offer>" `
      -sku "<SKU>" `
      -version "<#.#.#>" `
      -OSType "<OS type>" `
      -OSUri "<OS URI>"
   ```

   Az **Add-AzsPlatformimage** parancsmag a Azure Resource Manager sablonok által a virtuálisgép-rendszerképre való hivatkozáshoz használt értékeket határozza meg. Az értékek a következők:
   - **Publisher**  
     Például: `Canonical`  
     A felhasználó által a lemezkép telepítésekor használt virtuálisgép-rendszerkép **közzétevő** neve szegmense. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **ajánlat**  
     Például: `UbuntuServer`  
     A virtuálisgép-lemezkép telepítésekor a felhasználók által használt virtuálisgép-rendszerkép **ajánlatának** neve szegmense. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **SKU**  
     Például: `14.04.3-LTS`  
     Azon virtuálisgép-rendszerkép **SKU** -neve szegmense, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **verziója**  
     Például: `1.0.0`  
     Azon virtuálisgép-rendszerkép verziója, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ennek a verziónak a formátuma ** \# . \# . \# **. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **osType**  
     Például: `Linux`  
     A rendszerkép **OsType** Windows vagy **Linux** **rendszernek** kell lennie.  
   - **OSUri**  
     Például: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Megadhatja a blob Storage URI-JÁT `osDisk` .  

     További információkért lásd az [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) parancsmag PowerShell-referenciáját.

4. Rendszerkép hozzáadásakor csak Azure Resource Manager-alapú sablonok és PowerShell-telepítések esetén érhető el. Ha egy képet Piactéri elemként kíván elérhetővé tenni a felhasználók számára, tegye közzé a piactér-elemeket a piactér-elem [létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című cikkben ismertetett lépések alapján. Ügyeljen rá, hogy a **közzétevő**, az **ajánlat**, az **SKU**és a **Version** értékeket jegyezze fel. A Resource Manager-sablon szerkesztéséhez és az egyéni. azpkg való Manifest.jséhez szüksége lesz rájuk.

---

## <a name="remove-a-platform-image"></a>Platform rendszerképének eltávolítása

A platform-rendszerképet a portál vagy a PowerShell használatával távolíthatja el.

### <a name="portal"></a>[Portál](#tab/image-rem-portal)

A virtuálisgép-rendszerkép Azure Stack hub-kezelőként való eltávolításához a Azure Stack hub-portálon hajtsa végre az alábbi lépéseket:

1. Nyissa meg az Azure Stack hub [felügyeleti portált](https://portal.azure.com/signin/index).

2. Ha a virtuálisgép-rendszerképhez társított Piactéri elem tartozik, válassza a **piactér-kezelés**elemet, majd válassza ki a törölni kívánt virtuálisgép-Piactéri elemet.

3. Ha a virtuálisgép-rendszerkép nem rendelkezik társított Piactéri elemmel, navigáljon az **összes szolgáltatás > a számítási >** virtuálisgép-lemezképek elemre, majd válassza a virtuális gép lemezképe melletti három pontot (**..**.).

4. Válassza a **Törlés** elemet.

### <a name="powershell"></a>[PowerShell](#tab/image-rem-ps)

Ha el szeretné távolítani a virtuálisgép-rendszerképet Azure Stack hub-kezelőként a PowerShell használatával, kövesse az alábbi lépéseket:

1. [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).

2. Jelentkezzen be Azure Stack hubhoz operátorként.

3. Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa a következőket:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<Publisher>" `
    -offer "<Offer>" `
    -sku "<SKU>" `
    -version "<Version>" `
   ```

   A **Remove-AzsPlatformImage** parancsmag a Azure Resource Manager sablonok által a virtuálisgép-rendszerképre való hivatkozáshoz használt értékeket határozza meg. Az értékek a következők:
   - **Publisher**  
     Például: `Canonical`  
     A felhasználó által a lemezkép telepítésekor használt virtuálisgép-rendszerkép **közzétevő** neve szegmense. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **ajánlat**  
     Például: `UbuntuServer`  
     A virtuálisgép-lemezkép telepítésekor a felhasználók által használt virtuálisgép-rendszerkép **ajánlatának** neve szegmense. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **SKU**  
     Például: `14.04.3-LTS`  
     Azon virtuálisgép-rendszerkép **SKU** -neve szegmense, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **verziója**  
     Például: `1.0.0`  
     Azon virtuálisgép-rendszerkép verziója, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ennek a verziónak a formátuma ** \# . \# . \# **. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  

     A **Remove-AzsPlatformImage** parancsmaggal kapcsolatos további információkért tekintse meg a Microsoft PowerShell [Azure stack hub kezelő moduljának dokumentációját](/powershell/azure/azure-stack/overview).

---

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure Stack hub Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
- [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)