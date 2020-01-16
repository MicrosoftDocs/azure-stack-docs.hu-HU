---
title: Marketplace-elemek letöltése az Azure-ból és közzététel az Azure Stack hub-ban | Microsoft Docs
description: Ismerje meg, hogyan töltheti le a Marketplace-elemeket az Azure-ból, és hogyan tehet közzé Azure Stack hub-ban.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: 80cf9d192be07f951ee959c7a83419bb16bd2bbb
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022951"
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

2. A Piactéri elemek letöltése előtt tekintse át a rendelkezésre álló tárolóhelyet. Később, amikor kiválasztja az elemeket a letöltéshez, összehasonlíthatja a letöltési méretet a rendelkezésre álló tárterület kapacitásával. Ha a kapacitás korlátozott, vegye figyelembe a [rendelkezésre álló terület kezelésének](azure-stack-manage-storage-shares.md#manage-available-space)lehetőségeit.

   A rendelkezésre álló terület áttekintéséhez válassza ki a vizsgálni kívánt régiót, majd lépjen az **erőforrás-szolgáltatók** > **Storage** **elemre**:

   ![Tárterület áttekintése Azure Stack hub felügyeleti portálján](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Nyissa meg Azure Stack hub Marketplace-t, és kapcsolódjon az Azure-hoz. Ehhez válassza ki a piactér- **kezelés** szolgáltatást, válassza a **piactér elemek elemet**, majd válassza a **Hozzáadás az Azure-ból**lehetőséget:

   ![Marketplace-elemek hozzáadása az Azure-ból](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Az egyes sorok a jelenleg elérhető verziót is megjelenítik. Ha egy piactér-elem több verziója is elérhető, a Version ( **verzió** ) oszlopban **több**látható. Az egyes elemekre kattintva megtekintheti a leírását és a további információkat, beleértve a letöltési méretet is:

   ![Hozzáadás az Azure-ból](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Ha egy elem verziója **több**néven jelenik meg, kiválaszthatja az elemet, majd kiválaszthat egy adott verziót az eredményül kapott verzió legördülő listából:

   ![Hozzáadás az Azure-ból](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Válassza ki a kívánt elemet, majd válassza a **Letöltés**lehetőséget. A letöltési idő változó, és a hálózati kapcsolattól függ. A letöltés befejezése után az új Piactéri elem üzembe helyezhető Azure Stack hub-operátorként vagy felhasználóként.

7. A letöltött elem üzembe helyezéséhez válassza az **+ erőforrás létrehozása**lehetőséget, majd keressen az új piactér elem kategóriái között. Ezután válassza ki az elemet a telepítési folyamat megkezdéséhez. A folyamat eltérő lehet a piactér különböző elemeinél.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Leválasztott vagy részben csatlakoztatott forgatókönyv

Ha Azure Stack hub korlátozott vagy nem rendelkezik internetkapcsolattal, használhatja a PowerShellt és a *Marketplace szindikált eszközt* a Piactéri elemek letöltéséhez az internetkapcsolattal rendelkező gépre. Ezután vigye át az elemeket az Azure Stack hub-környezetbe. Leválasztott környezetben nem töltheti le a Marketplace-elemeket az Azure Stack hub portál használatával.

A Marketplace Syndication eszköz egy csatlakoztatott forgatókönyvben is használható.

A forgatókönyv két részből áll:

- **1. rész**: Letöltés a piactér elemeiből. Az internet-hozzáféréssel rendelkező számítógépen konfigurálja a PowerShellt, töltse le a szindikált eszközt, majd töltse le az elemeket az Azure Marketplace-ről.
- **2. rész**: feltöltés és közzététel Azure stack hub piactéren. Áthelyezi a letöltött fájlokat az Azure Stack hub-környezetbe, importálhatja őket Azure Stack hubhoz, majd közzéteheti őket a Azure Stack hub piactéren.

### <a name="prerequisites"></a>Előfeltételek

- Csatlakoztatott környezet (nem kell Azure Stack hub). Az Azure-ból származó termékek listájának lekéréséhez és a részletek helyi letöltéséhez kapcsolat szükséges. Ha ez megtörtént, a többi eljáráshoz nincs szükség internetkapcsolatra. Létrehoz egy katalógust azokról az elemekről, amelyeket korábban letöltött a leválasztott környezetben való használathoz.

- Cserélhető adathordozó a leválasztott környezethez való kapcsolódáshoz és az összes szükséges összetevő átviteléhez.

- Leválasztott Azure Stack hub-környezet a következő előfeltételekkel:

  - Az Azure Stack hub üzembe helyezését regisztrálni kell az Azure-ban.

  - Az internetkapcsolattal rendelkező számítógépnek **Azure stack hub PowerShell-moduljának 1.2.11** vagy újabb verziójának kell lennie. Ha még nem létezik, [telepítse Azure stack Hub-specifikus PowerShell-modulokat](azure-stack-powershell-install.md).

  - A letöltött Piactéri elemek importálásának engedélyezéséhez konfigurálni kell az [Azure stack hub-kezelő PowerShell-környezetét](azure-stack-powershell-configure-admin.md) .

  - Az [Azure stack hub-eszközök](https://github.com/Azure/AzureStack-Tools) klónozása GitHub-tárházban.

- Rendelkeznie kell egy olyan [Storage-fiókkal](azure-stack-manage-storage-accounts.md) Azure stack központban, amely nyilvánosan elérhető tárolóval rendelkezik (ez egy tárolási blob). A tárolót ideiglenes tárolóként használhatja a Piactéri elemek katalógusának fájljaihoz. Ha nem ismeri a Storage-fiókokat és-tárolókat, tekintse meg a [Blobok használata – Azure Portal](/azure/storage/blobs/storage-quickstart-blobs-portal) az Azure dokumentációjában.

- A Marketplace Syndication eszköz az első eljárás során töltődik le.

- A [AzCopy](/azure/storage/common/storage-use-azcopy) az optimális letöltési teljesítmény érdekében telepítheti, de nem szükséges.

A regisztráció után figyelmen kívül hagyhatja a Marketplace-kezelő panelen megjelenő alábbi üzenetet, mivel ez nem vonatkozik a leválasztott használati esetekre:

![Piactér-kezelés](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Marketplace-elemek letöltése a Marketplace Syndication eszköz használatával

> [!IMPORTANT]
> Ügyeljen arra, hogy minden alkalommal letöltse a Marketplace Syndication eszközt, amikor a Piactéri elemeket leválasztott forgatókönyvben tölti le. A szkript gyakori módosításokat hajt végre, és a legújabb verziót kell használni az egyes letöltésekhez.

1. Egy internetkapcsolattal rendelkező számítógépen nyisson meg egy PowerShell-konzolt rendszergazdaként.

2. Adja hozzá a Azure Stack hub regisztrálásához használt Azure-fiókot. A fiók hozzáadásához a PowerShellben futtassa az **Add-AzureRmAccount** paraméter nélkül. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiók konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   > [!NOTE]
   > Ha a munkamenet lejár, a jelszó módosult, vagy egyszerűen csak szeretné váltani a fiókokat, az **Add-AzureRmAccount**: **Remove-AzureRmAccount-scope folyamat**használatával történő bejelentkezés előtt futtassa a következő parancsmagot.

3. Ha több előfizetéssel rendelkezik, a következő parancs futtatásával válassza ki a regisztrációhoz használtt:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Töltse le a Marketplace Syndication eszköz legújabb verzióját a következő parancsfájl használatával:

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master
   ```

5. Importálja a szindikált modult, majd indítsa el az eszközt a következő parancs futtatásával:

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1
   ```

6. A Piactéri elemek, például a virtuálisgép-lemezképek, bővítmények vagy a megoldási sablonok exportálásához futtassa a következő parancsot. Cserélje le a célmappa elérési útját egy olyan helyre, amely az Azure Marketplace-ről letöltött fájlokat tárolja:

   ```powershell
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" -azCopyDownloadThreads "[optional - AzCopy threads number]" -azureContext $AzureContext
   ```

   Az erőforrás-szolgáltatók vagy a Pásti-szolgáltatások exportálásához futtassa a következő parancsot:

   ```powershell
   Export-AzSOfflineResourceProvider -destination "Destination folder path" -azCopyDownloadThreads "AzCopy threads number" -azureContext $AzureContext
   ```

   A `-azCopyDownloadThreads` paraméter nem kötelező. Csak akkor használható, ha alacsony sávszélességű hálózattal rendelkezik, és prémium szintű letöltést használ. Ez a beállítás határozza meg az egyidejű műveletek számát a AzCopy-ben. Ha alacsony sávszélességű hálózatot futtat, megadhat egy alacsonyabb számot, amellyel elkerülhető az erőforrás-verseny okozta hiba. További részleteket ebben az Azure- [cikkben](/previous-versions/azure/storage/storage-use-azcopy#specify-the-number-of-concurrent-operations-to-start)találhat.

   A `-azureContext` paraméter szintén nem kötelező. Ha nem adja meg az Azure-környezetet, a parancsmag az alapértelmezett Azure-környezetet fogja használni.

7. Az eszköz futtatásakor az alábbi képhez hasonló képernyő jelenik meg az elérhető Azure Marketplace-elemek listájával:

   ![Marketplace-elemek](media/azure-stack-download-azure-marketplace-item/tool1.png)

8. Ha a Marketplace-elem több verziója is elérhető, a **verzió** oszlopban **több verzió**is látható. Ha egy elem verziója **több verzióként**jelenik meg, akkor kiválaszthatja az elemet, majd kiválaszthat egy adott verziót az eredményül kapott verzió kiválasztása ablakból.

9. Válassza ki a letölteni kívánt elemet, és jegyezze fel a **verziót**. Több rendszerkép kiválasztásához a **Ctrl** billentyűt is megtarthatja. Ha a következő eljárásban importálja az elemet, a  *verzióra* kell hivatkoznia.

    A képek listáját a **feltételek hozzáadása** lehetőség használatával is szűrheti.

10. Ha nem telepítette az Azure Storage-eszközöket, a következő üzenet jelenik meg. Az eszközök telepítéséhez győződjön meg róla, hogy letöltötte a [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy):

    ![Storage-eszközök](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

11. Kattintson az **OK gombra**, majd tekintse át és fogadja el a jogi feltételeket.

12. A letöltés időpontja az elem méretétől függ. A letöltés befejezése után az elem elérhető a parancsfájlban megadott mappában. A letöltés tartalmaz egy VHD-fájlt (virtuális gépek esetében) vagy egy. zip-fájlt (a virtuálisgép-bővítmények és az erőforrás-szolgáltatók esetében). Tartalmazhat egy *. azpkg* formátumú katalógus-csomagot is, amely egyszerűen egy. zip-fájl.

13. Ha a letöltés meghiúsul, próbálkozzon újra a következő PowerShell-parancsmag újbóli futtatásával:

   ```powershell
   # for Marketplace items
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"

   # for Resource providers
   Export-AzSOfflineResourceProvider -Destination "Destination folder path in quotes"
   ```

   Az újrapróbálkozás előtt távolítsa el azt a terméket tartalmazó mappát, amelyben a letöltés sikertelen volt. Ha például a letöltési parancsfájl meghibásodik a **D:\downloadFolder\microsoft.customscriptextension-ARM-1.9.1**való letöltéskor, távolítsa el a **D:\downloadFolder\microsoft.customscriptextension-ARM-1.9.1** mappát, majd futtassa újra a parancsmagot.

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>A letöltés és közzététel a Azure Stack hub piactéren a PowerShell használatával

1. A [korábban letöltött](#use-the-marketplace-syndication-tool-to-download-marketplace-items) fájlokat helyileg kell áthelyeznie, hogy elérhetők legyenek az Azure stack hub-környezet számára. A Marketplace Syndication eszköznek emellett elérhetőnek kell lennie az Azure Stack hub-környezet számára, mivel az importálási művelet végrehajtásához az eszközt kell használnia.

   Az alábbi ábrán egy példa látható a mappák struktúrájában. A  **D:\downloadfolder** a piactér összes letöltött elemét tartalmazza. Minden almappa egy Piactéri elem (például **Microsoft. Custom-script-Linux-ARM-2.0.3**), amelyet a termékazonosító nevez el. Az egyes almappákon belül a Piactéri elem letöltött tartalma.

   ![Marketplace letöltési könyvtár szerkezete](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. A Azure Stack hub-kezelő PowerShell-munkamenet konfigurálásához kövesse az [ebben a cikkben](azure-stack-powershell-configure-admin.md) szereplő utasításokat .

3. Importálja a szindikált modult, majd indítsa el a Marketplace Syndication eszközt a következő parancsfájl futtatásával:

   ```powershell
   $credential = Get-Credential -Message "Enter the Azure Stack Hub operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   A `-origin` paraméter azt a legfelső szintű mappát határozza meg, amely az összes letöltött terméket tartalmazza; például `"D:\downloadfolder"`.

   A `-AzsCredential` paraméter nem kötelező. A hozzáférési jogkivonat megújítására szolgál, ha lejárt. Ha a `-AzsCredential` paraméter nincs megadva, és a jogkivonat lejár, a rendszer felszólítja az operátor hitelesítő adatainak megadására.

   > [!NOTE]
   > AD FS csak a felhasználói identitásokkal való interaktív hitelesítést támogatja. Ha egy hitelesítőadat-objektumra van szükség, akkor egy egyszerű szolgáltatásnevet (SPN) kell használnia. További információ az Azure Stack hub és a AD FS identitás-kezelési szolgáltatásként való beállításáról: [AD FS egyszerű szolgáltatásnév kezelése](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. A parancsfájl sikeres befejeződése után az elemnek elérhetőnek kell lennie Azure Stack hub piactéren.
