---
title: Marketplace-elemek letöltése az Azure-ból és közzététel a Azure Stackba | Microsoft Docs
description: Ismerje meg, hogyan töltheti le a Marketplace-elemeket az Azure-ból, és hogyan tehet közzé Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: bc696d4b14aecd5890893f00b64cf2c4a3804173
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74299174"
---
# <a name="download-existing-marketplace-items-from-azure-and-publish-to-azure-stack"></a>Meglévő Piactéri elemek letöltése az Azure-ból és közzététel a Azure Stack

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Felhőbeli operátorként letöltheti az elemeket az Azure Marketplace-ről, és elérhetővé teheti őket a Azure Stackban. A kiválasztható elemek az Azure Marketplace azon elemeinek válogatott listája, amelyek előzetesen tesztelve vannak, és amelyek a Azure Stack használatával használhatók. A lista további elemeket is felvesz a listára, ezért folytassa az új tartalmak újbóli beadásával.

Az Azure Marketplace-hez való csatlakozás két forgatókönyvből áll:

- **Csatlakoztatott forgatókönyv** – a Azure stack-környezet csatlakozását igényli az internethez. Az elemek megkereséséhez és letöltéséhez használja a Azure Stack portált.
- **Leválasztott vagy részben csatlakoztatott forgatókönyv** – a piactér-elemek letöltéséhez az Internet eléréséhez a Marketplace Syndication eszköz használatával kell elérnie az internetet. Ezután továbbítja a letöltéseket a leválasztott Azure Stack-telepítésre. Ez a forgatókönyv a PowerShellt használja.

A letölthető Piactéri elemek teljes listáját a [Azure stack Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md) című részben tekintheti meg. Tekintse meg a [Azure stack Marketplace Changes](azure-stack-marketplace-changes.md) cikket, amely felsorolja a Azure stack Marketplace legújabb kiegészítéseit, törléseit és frissítéseit.

## <a name="connected-scenario"></a>Csatlakoztatott esetre vonatkozó forgatókönyv

Ha Azure Stack csatlakozik az internethez, a felügyeleti portálon töltheti le a Marketplace-elemeket.

### <a name="prerequisites"></a>Előfeltételek

Az Azure Stack üzemelő példánynak internetkapcsolattal kell rendelkeznie, és [regisztrálnia kell az Azure](azure-stack-registration.md)-ban.

### <a name="use-the-portal-to-download-marketplace-items"></a>Marketplace-elemek letöltése a portál használatával
  
1. Jelentkezzen be a Azure Stack felügyeleti portálra.

2. A Piactéri elemek letöltése előtt tekintse át a rendelkezésre álló tárolóhelyet. Később, amikor kiválasztja az elemeket a letöltéshez, összehasonlíthatja a letöltési méretet a rendelkezésre álló tárterület kapacitásával. Ha a kapacitás korlátozott, vegye figyelembe a [rendelkezésre álló terület kezelésének](azure-stack-manage-storage-shares.md#manage-available-space)lehetőségeit.

    A rendelkezésre álló terület áttekintéséhez: a **régió felügyelete**területen válassza ki a vizsgálni kívánt régiót, majd lépjen az **erőforrás-szolgáltatók** > **tárolóhoz**:

    ![Tárterület áttekintése Azure Stack felügyeleti portálon](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Nyissa meg Azure Stack Piactért, és kapcsolódjon az Azure-hoz. Ehhez válassza ki a piactér- **kezelő** szolgáltatást, válassza a **piactér elemek elemet**, majd válassza a **Hozzáadás az Azure-ból**lehetőséget:

    ![Marketplace-elemek hozzáadása az Azure-ból](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. A portál megjeleníti az Azure piactéren letölthető elemek listáját. A termékeket név, közzétevő és/vagy terméktípus alapján szűrheti. Az egyes sorok a jelenleg elérhető verziót is megjelenítik. Ha egy piactér-elem több verziója is elérhető, a Version ( **verzió** ) oszlopban **több**látható. Az egyes elemekre kattintva megtekintheti a leírását és a további információkat, beleértve a letöltési méretet is:

    [![Marketplace-elemek listája](media/azure-stack-download-azure-marketplace-item/add-from-azure1sm.png "Marketplace-elemek listája")](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png#lightbox)

5. Ha egy elem verziója **több**néven jelenik meg, kiválaszthatja az elemet, majd kiválaszthat egy adott verziót az eredményül kapott verzió legördülő listából:

    [![Verzió kiválasztása](media/azure-stack-download-azure-marketplace-item/add-from-azure3sm.png "Verzió kiválasztása")](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png#lightbox)

6. Válassza ki a kívánt elemet, majd válassza a **Letöltés**lehetőséget. A letöltési idő változhat.

    ![Az Azure Marketplace-elemek letöltése](media/azure-stack-download-azure-marketplace-item/image04.png)

    A letöltés befejezése után telepítheti az új Piactéri elemeket Azure Stack operátorként vagy felhasználóként.

7. A letöltött elem üzembe helyezéséhez válassza az **+ erőforrás létrehozása**lehetőséget, majd keressen az új piactér elem kategóriái között. Ezután válassza ki az elemet a telepítési folyamat megkezdéséhez. A folyamat eltérő lehet a piactér különböző elemeinél.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Leválasztott vagy részben csatlakoztatott forgatókönyv

Ha Azure Stack leválasztott módban van, a PowerShell és a *Marketplace Syndication eszköz* használatával töltheti le a Piactéri elemeket egy internetkapcsolattal rendelkező gépre. Ezután vigye át az elemeket a Azure Stack környezetbe. Leválasztott környezetben nem töltheti le a Marketplace-elemeket a Azure Stack portál használatával.

A Marketplace Syndication eszköz egy csatlakoztatott forgatókönyvben is használható.

A forgatókönyv két részből áll:

- **1. rész:** Letöltés az Azure Marketplace-ről. Az internet-hozzáféréssel rendelkező számítógépen konfigurálja a PowerShellt, töltse le a szindikált eszközt, majd töltse le az elemeket az Azure Marketplace-ről.  
- **2. rész:** Feltöltés és közzététel Azure Stack piactéren. Áthelyezi a letöltött fájlokat a Azure Stack-környezetbe, importálja őket a Azure Stackba, majd közzéteheti őket Azure Stack piactéren.  

### <a name="prerequisites"></a>Előfeltételek

- Csatlakoztatott környezet (nem kell Azure Stack). Az Azure-ból származó termékek listájának lekéréséhez és a részletek helyi letöltéséhez kapcsolat szükséges. Ha ez megtörtént, a többi eljárás nem igényel internetkapcsolatot. Létrehoz egy katalógust azokról az elemekről, amelyeket korábban letöltött a leválasztott környezetben való használathoz.

- USB-kulcs vagy külső meghajtó a leválasztott környezethez való csatlakozáshoz és az összes szükséges összetevő átviteléhez.

- Leválasztott Azure Stack-környezet a következő előfeltételekkel:
  - Az Azure Stack üzembe helyezését [regisztrálni kell az Azure](azure-stack-registration.md)-ban.
  - Az internetkapcsolattal rendelkező számítógépen **Azure stack PowerShell-modul 1.2.11 vagy újabb verziójúnak** kell lennie. Ha még nem létezik, [telepítse az Azure stack-specifikus PowerShell-modulokat](azure-stack-powershell-install.md).
  - A letöltött Piactéri elemek importálásának engedélyezéséhez konfigurálni kell a [Azure stack kezelőhöz tartozó PowerShell-környezetet](azure-stack-powershell-configure-admin.md) .
  - A [Azure stack-eszközök](https://github.com/Azure/AzureStack-Tools) GitHub-tárházának klónozása.

- Olyan Azure Stack Storage- [fiókkal](azure-stack-manage-storage-accounts.md) kell rendelkeznie, amely nyilvánosan elérhető tárolóval rendelkezik (ez egy tárolási blob). A tárolót ideiglenes tárolóként használhatja a Piactéri elemek katalógusának fájljaihoz. Ha nem ismeri a Storage-fiókokat és-tárolókat, tekintse meg a következő témakört: [Blobok használata – Azure Portal](/azure/storage/blobs/storage-quickstart-blobs-portal) az Azure dokumentációjában.

- A Marketplace Syndication eszköz az első eljárás során töltődik le.

- A [AzCopy](/azure/storage/common/storage-use-azcopy) az optimális letöltési teljesítmény érdekében telepítheti, de ez nem kötelező.

A regisztráció után figyelmen kívül hagyhatja a Marketplace-kezelő panelen megjelenő alábbi üzenetet, mivel ez nem vonatkozik a leválasztott használati esetekre:

[![Nem regisztrált üzenet](media/azure-stack-download-azure-marketplace-item/toolsmsgsm.png "Nem regisztrált üzenet")](media/azure-stack-download-azure-marketplace-item/toolsmsg.png#lightbox)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Marketplace-elemek letöltése a Marketplace Syndication eszköz használatával

> [!IMPORTANT]
> Ügyeljen arra, hogy minden alkalommal letöltse a Marketplace Syndication eszközt, amikor a Piactéri elemeket leválasztott forgatókönyvben tölti le. A szkript gyakori módosításokat hajt végre, és a legújabb verziót kell használni az egyes letöltésekhez.

1. Egy internetkapcsolattal rendelkező számítógépen nyisson meg egy PowerShell-konzolt rendszergazdaként.

2. Adja hozzá a Azure Stack regisztrálásához használt Azure-fiókot. A fiók hozzáadásához a PowerShellben a `Add-AzureRmAccount` paraméterek nélkül futtassa a parancsot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   [!include[Remove Account](../../includes/remove-account.md)]

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

5. Importálja a szindikált modult, majd indítsa el az eszközt az alábbi parancsok futtatásával. Cserélje le a `Destination folder path`t az Azure Marketplace-ről letöltött fájlok tárolására szolgáló helyre.

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
   ```

   Vegye figyelembe, hogy `Export-AzSOfflineMarketplaceItem` tartalmaz egy további `-cloud` jelzőt, amely megadja a felhőalapú környezetet. Alapértelmezés szerint ez a **azurecloud**.

6. Az eszköz futtatásakor az alábbi képhez hasonló képernyő jelenik meg az elérhető Azure Marketplace-elemek listájával:

   [![Azure Marketplace-elemek előugró ablak](media/azure-stack-download-azure-marketplace-item/tool1sm.png "Azure Marketplace-elemek")](media/azure-stack-download-azure-marketplace-item/tool1.png#lightbox)

7. Ha egy piactér-elem több verziója is elérhető, a Version ( **verzió** ) oszlopban **több verzió**is látható. Ha egy elem verziója **több verzióként**jelenik meg, akkor kiválaszthatja az elemet, majd kiválaszthat egy adott verziót az eredményül kapott verzió kiválasztása ablakból:

   [![Verzió-választó](media/azure-stack-download-azure-marketplace-item/tool2sm.png "Verzió kiválasztása")](media/azure-stack-download-azure-marketplace-item/tool2.png#lightbox)

7. Ha nem telepítette az Azure Storage-eszközöket, a következő üzenet jelenik meg. Az eszközök telepítéséhez győződjön meg róla, hogy letöltötte a [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy):

   ![Storage-eszközök](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

8. Válassza ki a letölteni kívánt elemet, és jegyezze fel a **verziót**. A **CTRL** billentyűt lenyomva több lemezképet is kijelölhet. A *verzióra* hivatkozik, amikor a következő eljárásban importálja az elemet.

   A képek listáját a **feltételek hozzáadása** lehetőség használatával is szűrheti.

9. Kattintson az **OK gombra**, majd tekintse át és fogadja el a jogi feltételeket.

10. A letöltés időpontja az elem méretétől függ. A letöltés befejezése után az elem elérhető a parancsfájlban megadott mappában. A letöltés egy VHD-fájlt (virtuális gépek esetén) vagy egy. zip fájlt tartalmaz (a virtuálisgép-bővítmények esetében). Tartalmazhat egy *. azpkg* formátumú katalógus-csomagot is, amely egyszerűen egy. zip-fájl.

11. Ha a letöltés meghiúsul, próbálkozzon újra a következő PowerShell-parancsmag újbóli futtatásával:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Az újrapróbálkozás előtt távolítsa el azt a terméket tartalmazó mappát, amelyben a letöltés sikertelen volt. Ha például a letöltési parancsfájl nem sikerül `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`ra való letöltéskor, távolítsa el a `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` mappát, majd futtassa újra a parancsmagot.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-using-powershell"></a>A letöltés és a közzététel a Azure Stack piactéren a PowerShell használatával

1. Helyezze át a helyileg [letöltött](#use-the-marketplace-syndication-tool-to-download-marketplace-items) fájlokat, hogy azok elérhetők legyenek a Azure stack-környezet számára. A Marketplace Syndication eszköznek emellett elérhetőnek kell lennie a Azure Stack-környezet számára, mivel az importálási művelet végrehajtásához az eszközt kell használnia.

   Az alábbi ábrán egy példa látható a mappák struktúrájában. `D:\downloadfolder` tartalmazza a piactér összes letöltött elemét. Minden almappa egy Marketplace-elem (például `microsoft.custom-script-linux-arm-2.0.3`), amelyet a termékazonosító nevez el. Az egyes almappákon belül a Piactéri elem letöltött tartalma.

   [![Marketplace letöltési könyvtár szerkezete](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Marketplace letöltési könyvtár szerkezete")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. A Azure Stack kezelő PowerShell-munkamenet konfigurálásához kövesse az [ebben a cikkben](azure-stack-powershell-configure-admin.md) szereplő utasításokat.

3. Importálja a szindikált modult, majd indítsa el a Marketplace Syndication eszközt a következő parancsfájl futtatásával:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   A `-origin` paraméter a letöltött termékeket tartalmazó legfelső szintű mappát határozza meg. például `"D:\downloadfolder"`.

   A `-AzsCredential` paraméter nem kötelező. A hozzáférési jogkivonat megújítására szolgál, ha lejárt. Ha a `-AzsCredential` paraméter nincs megadva, és a jogkivonat lejár, a rendszer felszólítja az operátor hitelesítő adatainak megadására.

    > [!NOTE]  
    > AD FS csak a felhasználói identitásokkal való interaktív hitelesítést támogatja. Ha egy hitelesítőadat-objektumra van szükség, akkor egy egyszerű szolgáltatásnevet (SPN) kell használnia. További információ az Azure Stack és a AD FS identitás-kezelési szolgáltatásként való konfigurálásáról: [AD FS egyszerű szolgáltatásnév kezelése](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. A parancsfájl sikeres befejeződése után az elemnek elérhetőnek kell lennie Azure Stack piactéren.

## <a name="next-steps"></a>További lépések

- [Egyéni virtuálisgép-rendszerkép hozzáadása](azure-stack-add-vm-image.md)
- [Egyéni Piactéri elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
