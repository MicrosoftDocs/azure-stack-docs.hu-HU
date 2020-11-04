---
title: Marketplace-elemek letöltése az Azure-ból és közzététel a Azure Stackba | Microsoft Docs
description: Ismerje meg, hogyan töltheti le a Marketplace-elemeket az Azure-ból, és hogyan tehet közzé Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 9aa49c7913817168ed05f29b40d6ec8cf26e85b8
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329170"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Marketplace-elemek letöltése Azure Stack hubhoz 

Felhőbeli operátorként letöltheti az elemeket Azure Stack hubhoz a piactéren, és elérhetővé teheti azokat minden felhasználó számára az Azure Stack hub-környezettel. A kiválasztható elemek az Azure Marketplace azon elemeinek válogatott listája, amelyek előzetesen tesztelve vannak, és amelyek a Azure Stack használatával használhatók. A lista további elemeket is felvesz a listára, ezért folytassa az új tartalmak újbóli beadásával.

A Marketplace-termékek letöltése két forgatókönyvet érint:

- **Csatlakoztatott forgatókönyv** : az Azure stack hub-környezetnek csatlakoznia kell az internethez. Az Azure Stack hub felügyeleti portál segítségével megkeresheti és letöltheti az elemeket.
- **Leválasztott vagy részlegesen összekapcsolt forgatókönyv** : a Piactéri elemek letöltéséhez a Piactéri hírszolgáltatás eszköz használatával kell elérnie az internetet. Ezután továbbítja a letöltéseket a leválasztott Azure Stack-telepítésre. Ez a forgatókönyv a PowerShellt használja.

A letölthető Piactéri elemek teljes listáját a [Azure stack Azure Marketplace-elemek](../../operator/azure-stack-marketplace-azure-items.md) című részben tekintheti meg. Tekintse meg a [Azure stack Marketplace Changes](../../operator/azure-stack-marketplace-changes.md) cikket, amely felsorolja a Azure stack Marketplace legújabb kiegészítéseit, törléseit és frissítéseit.

> [!NOTE]
> A katalógus a felhő alapján eltérő lesz, és a Azure Stack hub rendszer csatlakozik a szolgáltatáshoz. A felhőalapú környezetet az Azure Stack hub regisztrálásához használt Azure-előfizetés határozza meg.

## <a name="connected-scenario"></a>Csatlakoztatott esetre vonatkozó forgatókönyv

Ha Azure Stack hub csatlakozik az internethez, akkor a felügyeleti portálon töltheti le a piactér-elemeket.

### <a name="prerequisites"></a>Előfeltételek

Az Azure Stack hub üzembe helyezéséhez internetkapcsolatra van szükség, és [regisztrálni kell az Azure](registration-tzl.md)-ban.

### <a name="use-the-portal-to-download-marketplace-items"></a>Marketplace-elemek letöltése a portál használatával

1. Jelentkezzen be az Azure Stack hub felügyeleti portálján.

2. A Piactéri elemek letöltése előtt tekintse át a rendelkezésre álló tárolóhelyet. Később, amikor kiválasztja az elemeket a letöltéshez, összehasonlíthatja a letöltési méretet a rendelkezésre álló tárterület kapacitásával. Ha a kapacitás korlátozott, vegye figyelembe a [rendelkezésre álló terület kezelésének](../../operator/azure-stack-manage-storage-shares.md#manage-available-space)lehetőségeit.

   A rendelkezésre álló terület áttekintéséhez: a **régió felügyelete** területen válassza ki a vizsgálni kívánt régiót, majd nyissa meg az **erőforrás-szolgáltatók**  >  **tárolót** :

   ![Tárterület áttekintése Azure Stack felügyeleti portálon](media/azure-stack-download-azure-marketplace-item-tzl/storage.png)

3. Nyissa meg Azure Stack hub Marketplace-t, és kapcsolódjon az Azure-hoz. Ehhez válassza ki a piactér- **kezelő** szolgáltatást, válassza a **piactér elemek elemet** , majd válassza a **Hozzáadás az Azure-ból** lehetőséget:

   ![Marketplace-elemek hozzáadása az Azure-ból](media/azure-stack-download-azure-marketplace-item-tzl/marketplace.png)

4. Az egyes sorok a jelenleg elérhető verziót is megjelenítik. Ha egy piactér-elem több verziója is elérhető, a Version ( **verzió** ) oszlopban **több** látható. Az egyes elemekre kattintva megtekintheti a leírását és a további információkat, beleértve a letöltési méretet is:

   ![Hozzáadás az Azure-ból](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure-1.png)

5. Ha egy elem verziója **több** néven jelenik meg, kiválaszthatja az elemet, majd kiválaszthat egy adott verziót az eredményül kapott verzió legördülő listából:

   ![Hozzáadás az Azure-ból](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure-3.png)

6. Válassza ki a kívánt elemet, majd válassza a **Letöltés** lehetőséget. A letöltési idő változó, és a hálózati kapcsolattól függ. A letöltés befejezése után telepítheti az új Piactéri elemeket Azure Stack operátorként vagy felhasználóként.

7. A letöltött elem üzembe helyezéséhez válassza az **+ erőforrás létrehozása** lehetőséget, majd keressen az új piactér elem kategóriái között. Ezután válassza ki az elemet a telepítési folyamat megkezdéséhez. A folyamat eltérő lehet a piactér különböző elemeinél.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Leválasztott vagy részben csatlakoztatott forgatókönyv

A leválasztott környezetekben nem tölthetők le Marketplace-elemek az Azure-ból. A Piactéri elemek helyi meghajtóra való letöltéséhez az offline szindikált eszközt kell használni, majd onnan fel kell tölteni ezeket az elemeket az Azure Stack hubhoz.

[Az offline hírszolgáltatási eszközöket innen töltheti le](https://aka.ms/azsSyndicationtool).

### <a name="download-marketplace-items-from-azure"></a>Marketplace-elemek letöltése az Azure-ból

#### <a name="prerequisites"></a>Előfeltételek

- Azure PowerShell telepítve van a gépen.

- Az Azure-előfizetés azonosítója, a regisztrációs erőforráscsoport és a regisztrációs név, amelyet az Azure Stack hub regisztrálásához használt. Ezeket az információkat a **régió kezelése** **panel tulajdonságok** lapján találja a Azure stack hub-kezelő portálon belül.

- Annak a fióknak a felhasználóneve és jelszava, amelynek tulajdonosa/közreműködői jogosultsága van az Azure-előfizetési AZONOSÍTÓhoz.

- Helyi meghajtó vagy hálózati elérési út, amelybe az offline fájlok írhatók.

#### <a name="download-items"></a>Elemek letöltése

1. Nyissa meg a PowerShellt, és lépjen a kibontott mappára.

2. Futtassa a **Invoke-AzSMarketplaceDownload.ps1** PowerShell-parancsfájlt:

    ```powershell
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationSubscriptionId '<subscription ID>' ` 
       -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' `
       -TenantName mytenant.onmicrosoft.com -DownloadFolder 'F:\offlineSyndication'
    ```

    Ha már bejelentkezett Azure PowerShellon keresztül, az Azure-környezetben is átadható:

    ```powershell
    Add-AzureRmAccount -Environment AzureCloud -Tenant mytenant.onmicrosoft.com 
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' -DownloadFolder 'F:\offlineSyndication' -AzureContext $(Get-AzureRMContext)
    ```

    Ha nem adja át az Azure-környezetet, a rendszer kérni fogja, hogy jelentkezzen be.

3. Megjelenik egy ablak, ahol kiválaszthatja a letölteni kívánt terméket. A CTRL billentyűt lenyomva tartva több elemet is kijelölhet.

4. Válassza az **OK** lehetőséget. Ez letölti a piactér elemét és annak függőségeit, ha vannak ilyenek.

### <a name="upload-marketplace-items-to-azure-stack-hub"></a>Marketplace-elemek feltöltése Azure Stack hubhoz

#### <a name="prerequisites"></a>Előfeltételek

- Azure Stack a felügyeleti erőforrás-kezelő végpontját és a címtár-bérlőt.

- Hozzáférés az offline Piactéri elemekhez.

#### <a name="upload-items"></a>Elemek feltöltése

1. Nyissa meg a PowerShellt, és lépjen a kibontott mappára.

2. Futtassa a **Invoke-AzSMarketplaceUpload.ps1** PowerShell-parancsfájlt:

    ```powershell
    .\Invoke-AzsMarketplaceUpload.ps1 -AzureStackCloudName "AzureStack-Admin" -AzureStackAdminARMEndpoint https://adminmanagement.<region>.<fqdn> -TenantName mytenant.onmicrosoft.com -DownloadFolder F:\offlineSyndication
    ```

    Azt is megteheti, hogy saját maga is beállíthatja a Azure Stack környezetet Azure PowerShell, hitelesítenie kell magát a felügyeleti erőforrás-kezelő végponton, és át kell adni a környezetet a parancsfájlnak:

    ```powershell
    Add-AzureRmEnvironment -Name Redmond-Admin -ARMEndpoint https://adminmanagement.redmond.azurestack.corp.microsoft.com

    Add-AzureRmAccount -Environment Redmond-Admin

    .\Invoke-AzsMarketplaceUpload.ps1 -DownloadFolder F:\Downloads\offlining -AzureContext $(Get-AzureRmContext)
    ```

    Ez az eljárás feltölti a piactér elemeit a megadott Azure Stack hubhoz.
