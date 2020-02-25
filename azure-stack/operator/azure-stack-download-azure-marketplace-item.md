---
title: Marketplace-elemek letöltése az Azure-ból és közzététel Azure Stack hubhoz
description: Ismerje meg, hogyan töltheti le a Marketplace-elemeket az Azure-ból, és hogyan tehet közzé Azure Stack hub-ban.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: d909786e3cf3bacbf981fc2150f20084d283e81b
ms.sourcegitcommit: c653efe7cd5177bf61c1a321ba7f38c0cdf90346
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77575150"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Marketplace-elemek letöltése Azure Stack hubhoz 

Felhőbeli operátorként letöltheti az elemeket Azure Stack hubhoz a piactéren, és elérhetővé teheti azokat minden felhasználó számára az Azure Stack hub-környezettel. A kiválasztható elemek az Azure Marketplace azon elemeinek válogatott listája, amelyek előzetesen teszteltek, és az Azure Stack hub használatával támogatottak. A lista további elemeket is felvesz a listára, ezért folytassa az új tartalmak újbóli beadásával.

A Marketplace-termékek letöltése két forgatókönyvet érint:

- **Csatlakoztatott forgatókönyv**: az Azure stack hub-környezetnek csatlakoznia kell az internethez. Az Azure Stack hub felügyeleti portál segítségével megkeresheti és letöltheti az elemeket.
- **Leválasztott vagy részlegesen összekapcsolt forgatókönyv**: a Piactéri elemek letöltéséhez a Piactéri hírszolgáltatás eszköz használatával kell elérnie az internetet. Ezután továbbítja a letöltéseket a leválasztott Azure Stack hub-telepítésre. Ez a forgatókönyv a PowerShellt használja.

A letölthető Piactéri elemek teljes listájáért tekintse meg a [Azure stack hub Azure Marketplace-elemeit](azure-stack-marketplace-azure-items.md) . A Azure Stack hub Marketplace legújabb kiegészítéseinek, törlésének és frissítéseinek listáját a [Azure stack hub Marketplace változásairól](azure-stack-marketplace-changes.md) szóló cikkben találja.

> [!NOTE]
> A katalógus a felhő alapján eltérő lesz, és a Azure Stack hub rendszer csatlakozik a szolgáltatáshoz. A felhőalapú környezetet az Azure Stack hub regisztrálásához használt Azure-előfizetés határozza meg.

## <a name="connected-scenario"></a>Csatlakoztatott esetre vonatkozó forgatókönyv

Ha Azure Stack hub csatlakozik az internethez, akkor a felügyeleti portálon töltheti le a piactér-elemeket.

### <a name="prerequisites"></a>Előfeltételek

Az Azure Stack hub üzembe helyezéséhez internetkapcsolatra van szükség, és regisztrálni kell az Azure-ban.

### <a name="use-the-portal-to-download-marketplace-items"></a>Marketplace-elemek letöltése a portál használatával

1. Jelentkezzen be az Azure Stack hub felügyeleti portálján.

2. A Piactéri elemek letöltése előtt tekintse át a rendelkezésre álló tárolóhelyet. Később, amikor kiválasztja az elemeket a letöltéshez, összehasonlíthatja a letöltési méretet a rendelkezésre álló tárterület kapacitásával. Ha a kapacitás korlátozott, vegye figyelembe a [rendelkezésre álló terület kezelésének](azure-stack-manage-storage-shares.md#manage-available-space)lehetőségeit.

   A rendelkezésre álló terület áttekintéséhez: a **régió felügyelete**területen válassza ki a vizsgálni kívánt régiót, majd lépjen az **erőforrás-szolgáltatók** > **tárolóhoz**:

   ![Tárterület áttekintése Azure Stack hub felügyeleti portálján](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Nyissa meg Azure Stack hub Marketplace-t, és kapcsolódjon az Azure-hoz. Ehhez válassza ki a piactér- **kezelő** szolgáltatást, válassza a **piactér elemek elemet**, majd válassza a **Hozzáadás az Azure-ból**lehetőséget:

   ![Marketplace-elemek hozzáadása az Azure-ból](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Az egyes sorok a jelenleg elérhető verziót is megjelenítik. Ha egy piactér-elem több verziója is elérhető, a Version ( **verzió** ) oszlopban **több**látható. Az egyes elemekre kattintva megtekintheti a leírását és a további információkat, beleértve a letöltési méretet is:

   ![Hozzáadás az Azure-ból](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Ha egy elem verziója **több**néven jelenik meg, kiválaszthatja az elemet, majd kiválaszthat egy adott verziót az eredményül kapott verzió legördülő listából:

   ![Hozzáadás az Azure-ból](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Válassza ki a kívánt elemet, majd válassza a **Letöltés**lehetőséget. A letöltési idő változó, és a hálózati kapcsolattól függ. A letöltés befejezése után az új Piactéri elem üzembe helyezhető Azure Stack hub-operátorként vagy felhasználóként.

7. A letöltött elem üzembe helyezéséhez válassza az **+ erőforrás létrehozása**lehetőséget, majd keressen az új piactér elem kategóriái között. Ezután válassza ki az elemet a telepítési folyamat megkezdéséhez. A folyamat eltérő lehet a piactér különböző elemeinél.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Leválasztott vagy részben csatlakoztatott forgatókönyv

Ha Azure Stack hub korlátozott vagy nem rendelkezik internetkapcsolattal, akkor a PowerShell és a *Marketplace Syndication eszköz* használatával töltheti le a piactér elemeit egy internetkapcsolattal rendelkező gépre. Ezután vigye át az elemeket az Azure Stack hub-környezetbe. Leválasztott környezetben nem töltheti le a Marketplace-elemeket az Azure Stack hub portál használatával.

A Marketplace Syndication eszköz egy csatlakoztatott forgatókönyvben is használható.

A forgatókönyv két részből áll:

- **1. rész**: Letöltés a piactér elemeiből. Az internet-hozzáféréssel rendelkező számítógépen konfigurálja a PowerShellt, töltse le a szindikált eszközt, majd töltse le az elemeket az Azure Marketplace-ről.
- **2. rész**: feltöltés és közzététel Azure stack hub piactéren. Áthelyezi a letöltött fájlokat az Azure Stack hub-környezetbe, majd közzéteheti őket a Azure Stack hub piactéren.

### <a name="prerequisites"></a>Előfeltételek

- Csatlakoztatott környezet (nem kell Azure Stack hub). Az Azure-ból származó termékek listájának lekéréséhez és a részletek helyi letöltéséhez kapcsolat szükséges. Ha ez megtörtént, a többi eljáráshoz nincs szükség internetkapcsolatra. Létrehoz egy katalógust azokról az elemekről, amelyeket korábban letöltött a leválasztott környezetben való használathoz.

- Cserélhető adathordozó a leválasztott környezethez való kapcsolódáshoz és az összes szükséges összetevő átviteléhez.

- Leválasztott Azure Stack hub-környezet a következő előfeltételekkel:

  - Az Azure Stack hub üzembe helyezését regisztrálni kell az Azure-ban.

  - Az internetkapcsolattal rendelkező számítógépnek **Azure stack hub PowerShell-moduljának 1.2.11 vagy újabb verziójával** kell rendelkeznie. Ha még nem létezik, [telepítse Azure stack Hub-specifikus PowerShell-modulokat](azure-stack-powershell-install.md).

  - A letöltött Piactéri elem importálásának engedélyezéséhez konfigurálnia kell az [Azure stack hub operátor PowerShell-környezetét](azure-stack-powershell-configure-admin.md) .

- Töltse le a AZS. Syndication. admin modult a PowerShell-galéria az alábbi parancs használatával
  ```
  Install-Module -Name Azs.Syndication.Admin
  ```

A Azure Stack regisztrálása után figyelmen kívül hagyhatja a Marketplace-kezelő panelen megjelenő alábbi üzenetet, mivel ez nem vonatkozik a leválasztott használati esetre:

![Piactér-kezelés](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Marketplace-elemek letöltése a Marketplace Syndication eszköz használatával

> [!IMPORTANT]
> Ügyeljen arra, hogy minden alkalommal letöltse a Marketplace Syndication eszközt, amikor a Piactéri elemeket leválasztott forgatókönyvben tölti le. Ezen az eszközön gyakori változások történnek, és a legújabb verziót kell használni az egyes letöltésekhez.

1. Egy internetkapcsolattal rendelkező számítógépen nyisson meg egy PowerShell-konzolt rendszergazdaként.

2. Jelentkezzen be a megfelelő Azure Cloud-és AzureAD-címtár-Bérlőbe az Azure Stack hub regisztrálásához használt Azure-fiók használatával. A fiók hozzáadásához a PowerShellben futtassa a következőt: **Add-AzureRmAccount**. 

   ```powershell  
   Login-AzureRmAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```
   A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiók konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   > [!NOTE]
   > Ha a munkamenet lejár, a jelszó módosult, vagy egyszerűen csak szeretné váltani a fiókokat, az **Add-AzureRmAccount**: **Remove-AzureRmAccount-scope folyamat**használatával történő bejelentkezés előtt futtassa a következő parancsmagot.

3. Ha több előfizetéssel rendelkezik, a következő parancs futtatásával válassza ki a regisztrációhoz használtt:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   ```

4. Ha még nem tette meg az előfeltételek lépésben, töltse le a Marketplace Syndication eszköz legújabb verzióját:

   ```powershell
   Install-Module -Name Azs.Syndication.Admin
   ```

5. Az alábbi parancs futtatásával válassza ki a Piactéri elemeket (például a virtuálisgép-lemezképeket, a bővítményeket vagy a megoldási sablonokat). 

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   Ez egy olyan táblázatot jelenít meg, amely felsorolja a kijelölt előfizetésben elérhető összes Azure Stack regisztrációt. Válassza ki azt a regisztrációt, amely megfelel annak a Azure Stack-környezetnek, amelyet a piactérhez tartozó elemek letöltéséhez, majd kattintson **az OK gombra**.

     ![Azure Stack regisztrációk kiválasztása](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Ekkor egy második táblázat jelenik meg, amely felsorolja az összes letölthető Piactéri elemet. Válassza ki a letölteni kívánt elemet, és jegyezze fel a **verziót**. A **CTRL** billentyűt lenyomva több lemezképet is kijelölhet.
     ![válassza a Azure Stack regisztrációk](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   A képek listáját a **feltételek hozzáadása** lehetőség használatával is szűrheti.
   ![válassza a Azure Stack regisztrációk](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Miután elvégezte a beállításokat, kattintson az OK gombra.

6. A piactérről kiválasztott piactér-elemek azonosítói a `$products` változóban lesznek mentve. A kijelölt elemek letöltésének megkezdéséhez használja az alábbi parancsot. Cserélje le a célmappa elérési útját egy olyan helyre, amely az Azure Marketplace-ről letöltött fájlokat tárolja:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. A letöltés időpontja az elem méretétől függ. A letöltés befejezése után az elem elérhető a parancsfájlban megadott mappában. A letöltés tartalmaz egy VHD-fájlt (virtuális gépek esetében) vagy egy. zip-fájlt (a virtuálisgép-bővítmények és az erőforrás-szolgáltatók esetében). Tartalmazhat egy. *azpkg* formátumú katalógus-csomagot is, amely egy. zip-fájl.

8. Ha a letöltés meghiúsul, próbálkozzon újra a következő PowerShell-parancsmag újbóli futtatásával:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. A **AZS. Syndication. admin** modult helyileg is exportálnia kell, hogy átmásolja azt a gépre, amelyről a Marketplace-elemeket Azure stack hubhoz importálja.

   > [!NOTE]
   > A modul exportálásához használt célmappa nem lehet a piactér elemeinek exportálási helyétől eltérő helyen.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>A letöltés és közzététel a Azure Stack hub piactéren a PowerShell használatával

1. Át kell helyeznie azokat a fájlokat, amelyeket [korábban helyileg töltött le](#use-the-marketplace-syndication-tool-to-download-marketplace-items) egy olyan gépre, amely az Azure stack hub-környezethez kapcsolódik. A Marketplace Syndication eszköznek emellett elérhetőnek kell lennie az Azure Stack hub-környezet számára, mivel az importálási művelet végrehajtásához az eszközt kell használnia.

   Az alábbi ábrán egy példa látható a mappák struktúrájában. A **D:\downloadfolder** tartalmazza a piactér összes letöltött elemét. Minden almappa egy Piactéri elem (például **Microsoft. Custom-script-Linux-ARM-2.0.3**), amelyet a termékazonosító nevez el. Az egyes almappákon belül a Piactéri elem letöltött tartalma.

   ![Marketplace letöltési könyvtár szerkezete](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. A [cikk](azure-stack-powershell-configure-admin.md) utasításait követve konfigurálja az Azure stack hub-kezelő PowerShell-munkamenetét.

3. Jelentkezzen be az Azure Stack hubhoz egy olyan identitással, amely tulajdonosi hozzáféréssel rendelkezik az "alapértelmezett szolgáltatói előfizetéshez".

4. Importálja a szindikált modult, majd indítsa el a Marketplace Syndication eszközt a következő parancsfájl futtatásával:

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. A parancsfájl sikeres befejeződése után a piactér elemeinek elérhetőnek kell lenniük Azure Stack hub piactéren.
