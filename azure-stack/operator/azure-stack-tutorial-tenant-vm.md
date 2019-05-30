---
title: Virtuális gépek az Azure Stack-felhasználók számára elérhetővé |} A Microsoft Docs
description: 'Útmutató: virtuális gépek elérhetővé tétele az Azure Stackben'
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 09/11/2018
ms.custom: mvc
ms.openlocfilehash: b39eaf4365f74e59c6dd60db67fdadf1162cb91d
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268139"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Oktatóanyag: virtuális gépek számára elérhetővé az Azure Stack-felhasználók

Azure Stack a felhőalapú rendszergazdák (más néven bérlői) a felhasználók előfizethetnek ajánlatok is létrehozhat. Előfizetés egy ajánlatra, a felhasználók akkor használhatnak az Azure Stack-szolgáltatásokat, amely egy ajánlatot biztosít.

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy virtuális gép egy ajánlatot, és ezután lépjen be felhasználóként az ajánlat teszteléséhez.

Amiről tanulni fog:

> [!div class="checklist"]
> * Ajánlat létrehozása
> * Kép hozzáadása
> * Az ajánlat tesztelése

Az Azure Stackben szolgáltatások előfizetések, ajánlatok és csomagok használó felhasználók érkeznek. Több ajánlattal is fizessen elő a felhasználók. Ajánlat lehet egy vagy több tervek, és a csomag egy vagy több szolgáltatást is rendelkezhet.

![Az előfizetések, ajánlatok és csomagok](media/azure-stack-key-features/image4.png)

További tudnivalókért lásd: [funkcióiról és koncepciójáról az Azure Stack-kulcsának](azure-stack-overview.md).

## <a name="create-an-offer"></a>Ajánlat létrehozása

Ajánlatok egy vagy több olyan csomag, amelyet szolgáltatók vásárolhatja meg vagy fizessen elő a felhasználók csoportjai. Ajánlat létrehozásának folyamata több lépésből áll. Először kéri, hogy az ajánlatra, majd a csomagot, végül pedig, kvóták létrehozása.

1. [Jelentkezzen be a](../asdk/asdk-connect.md) egy felhőalapú rendszergazdaként, és válassza ki a Portal **+ erőforrás létrehozása** > **ajánlatok és csomagok** > **ajánlat**.

   ![Új ajánlat](media/azure-stack-tutorial-tenant-vm/image01.png)

1. A **új ajánlat**, adja meg egy **megjelenített név** és **erőforrásnév**, és ezután válasszon ki egy új vagy meglévő **erőforráscsoport**. A megjelenítendő név az ajánlat rövid neve. Csak a felhő üzemeltetője láthatja az erőforrás nevét, amelynek a neve, amely segítségével a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással működik.

   ![Display name](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Válassza ki **Alapcsomagok**, majd a a **terv** szakaszban jelölje be **Hozzáadás** hozzáadása egy új csomagot az ajánlat.

   ![Csomag hozzáadása](media/azure-stack-tutorial-tenant-vm/image03.png)

1. Az a **új terv** területén adja meg **megjelenített név** és **erőforrásnév**. Megjelenített név a csomag rövid neve, a felhasználók számára. Csak a felhő üzemeltetője láthatja az erőforrás nevét, amely a felhő üzemeltetői használják a csomaggal mint Azure Resource Manager-erőforrással a neve.

   ![Csomag megjelenített neve](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Válassza ki **szolgáltatások**. A szolgáltatások listájában válasszon **Microsoft.Compute**, **Microsoft.Network**, és **Microsoft.Storage**. Válasszon **kiválasztása** ezek a szolgáltatások hozzáadása a tervet.

   ![Szolgáltatások megtervezése](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Válassza ki **kvóták**, majd válassza ki az első szolgáltatás, amely a létrehozandó tartozó kvótát. Egy IaaS-kvóta használja a következő példát segítségképp kvóták a számítási, hálózati és tárolási szolgáltatások konfigurálásához.

   - Először hozza létre a számítási szolgáltatás tartozó kvótát. Jelölje ki a névteret listában **Microsoft.Compute** majd **új kvóta létrehozása**.

     ![Új kvóta létrehozása](media/azure-stack-tutorial-tenant-vm/image06.png)

   - A **kvóta létrehozása**, adjon meg egy nevet a kvótát. Módosíthatja, vagy fogadja el az kvóta értékeket hoz létre kvóta esetében látható. Ebben a példában, hogy fogadja el az alapértelmezett beállításokat, és válassza **OK**.

     ![Kvóta neve](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Válasszon **Microsoft.Compute** a névterek listája, és válassza ki a létrehozott kvótát. Ez a kvóta a számítási szolgáltatás hivatkozik.

     ![Válassza a kvóta](media/azure-stack-tutorial-tenant-vm/image08.png)

      Ismételje meg ezeket a lépéseket a hálózati és tárolási szolgáltatások. Ha elkészült, válassza ki a **OK** a **kvóták** kvóták mentéséhez.

1. A **új terv**válassza **OK**.

1. A **terv**, válassza ki az új csomagot, majd **válassza**.

1. A **új ajánlat**válassza **létrehozás**. Az ajánlat létrehozásakor, megjelenik egy értesítés.

1. Válassza ki az irányítópult menü **kínál** , és ezután válassza ki a létrehozott ajánlat.

1. Válassza ki **állapot módosítása**, majd **nyilvános**.

    ![Nyilvános állapota](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Kép hozzáadása

Előtt is építhet ki virtuális gépeket, hozzá kell adnia egy rendszerképet az Azure Stack piactéren. A kép tetszőleges, többek között a Linux-rendszerképeket az Azure Marketplace-ről is hozzáadhat.

Ha csatlakoztatott forgatókönyvekben működik, és ha regisztrált az Azure Stack-példány az Azure-ral, majd letöltheti a Windows Server 2016 VM-lemezképet az Azure Marketplace-ről ismertetett lépések segítségével a [letöltési Marketplace-en az Azure Stackhez az Azure-ból elemek](azure-stack-download-azure-marketplace-item.md) témakör.

Eltérő konfigurációs elemeket a Marketplace-en történő hozzáadásával kapcsolatos további információkért lásd: [az Azure Stack piactéren](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Most, hogy létrehozott egy ajánlatot, tesztelheti azt. Egy felhasználó, regisztrálni előfizetnek az ajánlatra, és majd a virtuális gép hozzáadása.

1. **Előfizetés egy ajánlatra**

   a. Jelentkezzen be a felhasználói portál egy olyan felhasználói fiókkal, és válassza ki a **előfizetés beszerzése** csempére.
   - Egy integrált rendszer, az URL-címet az operátor régió és külső tartománynév függően változik, és a következő formátumban lesz https://portal.&lt; *régió*&gt;.&lt; *FQDN*&gt;.
   - Ha az Azure Stack fejlesztői készletet használ, a portál címe https://portal.local.azurestack.external.

   ![Előfizetés beszerzése](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. A **előfizetés beszerzése**, adjon meg egy nevet az Ön előfizetéséhez az **megjelenítendő név** mező. Válassza ki **ajánlat**, majd válassza a lehetővé teszi a **válasszon ajánlatot** lista. Kattintson a **Létrehozás** gombra.

   ![Ajánlat létrehozása](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. Az előfizetés megtekintéséhez jelölje ki **minden szolgáltatás**, majd a **általános** kategória kiválasztása **előfizetések**. Válassza ki az új előfizetés megtekintéséhez, hogy mely szolgáltatások tartoznak az előfizetés.

   >[!NOTE]
   >Miután előfizetett egy ajánlatra, akkor előfordulhat, hogy frissítse a portált, hogy mely szolgáltatások tartoznak az új előfizetés megtekintéséhez.

1. **Virtuális gép kiépítése**

   Az a felhasználói portál üzembe helyezése egy virtuális gépet az új előfizetéssel.

   a. Jelentkezzen be a felhasználói portál egy olyan felhasználói fiókkal.
   - Egy integrált rendszer, az URL-címet az operátor régió és külső tartománynév függően változik, és a következő formátumban lesz https://portal.&lt; *régió*&gt;.&lt; *FQDN*&gt;.
     - Ha az Azure Stack fejlesztői készletet használ, a portál címe https://portal.local.azurestack.external.

   b.  Az irányítópulton, válassza ki a **+ erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter próbaverzió**, majd válassza ki a **Létrehozása**.

   c. A **alapjai**, adja meg a következő információkat:
      - Adjon meg egy **neve**
      - Adjon meg egy **felhasználónév**
      - Adjon meg egy **jelszó**
      - Válasszon egy **előfizetés**
      - Hozzon létre egy **erőforráscsoport** (vagy válasszon ki egy meglévőt.) 
      - Válassza ki **OK** menteni ezeket az információkat.

   d. A **méret kiválasztása**válassza **A1 Standard**, majd **kiválasztása**.  

   e. A **beállítások**válassza **virtuális hálózati**.

   f. A **válasszon egy virtuális hálózatot**válassza **új létrehozása**.

   g. A **virtuális hálózat létrehozása**, fogadja el az alapértelmezett beállításokat, és válassza ki **OK**.

   h. Válassza ki **OK** a **beállítások** a hálózati konfiguráció mentéséhez.

      i. A **összefoglalás**válassza **OK** a virtuális gép létrehozásához.  

   j. Az új virtuális gép, jelölje ki a **összes erőforrás**. Keresse meg a virtuális gépet, és válassza ki a nevét a keresési eredmények közül.

   
## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Ajánlat létrehozása
> * Kép hozzáadása
> * Az ajánlat tesztelése

A következő oktatóanyaggal, amelyben tudatjuk a felhasználókkal hogyan:
> [!div class="nextstepaction"]
> [SQL-adatbázisok az Azure Stack-felhasználók számára elérhetővé](azure-stack-tutorial-sql-server.md)