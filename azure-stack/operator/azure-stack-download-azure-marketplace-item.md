---
title: Marketplace-elemek letöltése az Azure-ból | Microsoft Docs
description: A Felhőbeli operátor az Azure-ból a Azure Stack üzembe helyezéshez is letöltheti a Piactéri elemeket.
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
ms.date: 04/24/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: c5a53fcd2aa2ec516e5677e85a1516ac10208c3f
ms.sourcegitcommit: c2690b2dd36918ff3e47e359cac926128bb83101
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68544124"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Marketplace-elemek letöltése az Azure-ból Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Felhőbeli operátorként letöltheti az elemeket az Azure Marketplace-ről, és elérhetővé teheti őket a Azure Stackban. A kiválasztható elemek az Azure Marketplace azon elemeinek válogatott listája, amelyek előzetesen tesztelve vannak, és amelyek a Azure Stack használatával használhatók. A lista további elemeket is felvesz a listára, ezért folytassa az új tartalmak újbóli beadásával. 

Az Azure Marketplace-hez való csatlakozás kétféleképpen történhet: 

- **Csatlakoztatott forgatókönyv** – a Azure stack-környezet csatlakozását igényli az internethez. Az elemek megkereséséhez és letöltéséhez használja a Azure Stack portált. 
- **Leválasztott vagy részben csatlakoztatott forgatókönyv** – a piactér-elemek letöltéséhez az Internet eléréséhez a Marketplace Syndication eszköz használatával kell elérnie az internetet. Ezután továbbítja a letöltéseket a leválasztott Azure Stack-telepítésre. Ez a forgatókönyv a PowerShellt használja.

A letölthető Piactéri elemek listáját a [Azure stack Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md) című részben tekintheti meg.

## <a name="connected-scenario"></a>Csatlakoztatott esetre vonatkozó forgatókönyv

Ha Azure Stack csatlakozik az internethez, a felügyeleti portálon töltheti le a Marketplace-elemeket.

### <a name="prerequisites"></a>Előfeltételek

Az Azure Stack üzemelő példánynak internetkapcsolattal kell rendelkeznie, és regisztrálnia kell [Az Azure](azure-stack-registration.md )-ban.

### <a name="use-the-portal-to-download-marketplace-items"></a>Marketplace-elemek letöltése a portál használatával
  
1. Jelentkezzen be a Azure Stack felügyeleti portálra.

2. A Piactéri elemek letöltése előtt tekintse át a rendelkezésre álló tárolóhelyet. Később, amikor kiválasztja az elemeket a letöltéshez, összehasonlíthatja a letöltési méretet a rendelkezésre álló tárterület kapacitásával. Ha a kapacitás korlátozott, vegye figyelembe a [rendelkezésre álló terület kezelésének](azure-stack-manage-storage-shares.md#manage-available-space)lehetőségeit. 

    A rendelkezésre álló terület áttekintéséhez a **régió kezelése** területen válassza ki a vizsgálni kívánt régiót, majd lépjen az **erőforrás-szolgáltatók** > **tárolóhoz**:

    ![Tárhely áttekintése](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Nyissa meg a Azure Stack Marketplace-t, és kapcsolódjon az Azure-hoz. Ehhez válassza ki a piactér- **kezelő** szolgáltatást, válassza a **piactér elemek elemet**, majd válassza a **Hozzáadás az Azure-ból**lehetőséget:

    ![Hozzáadás az Azure-ból](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    A portál megjeleníti az Azure piactéren letölthető elemek listáját. A termékeket név, közzétevő és/vagy terméktípus alapján szűrheti. Az egyes elemekre kattintva megtekintheti a leírását és a további információkat, beleértve a letöltési méretet is:

    ![Marketplace-lista](media/azure-stack-download-azure-marketplace-item/image03.PNG)

4. Válassza ki a kívánt elemet, majd válassza a **Letöltés**lehetőséget. A letöltési idő változhat.

    ![Üzenet letöltése](media/azure-stack-download-azure-marketplace-item/image04.png)

    A letöltés befejezése után telepítheti az új Piactéri elemeket Azure Stack operátorként vagy felhasználóként.

5. A letöltött elem üzembe helyezéséhez válassza az **+ erőforrás létrehozása**lehetőséget, majd keressen az új piactér elem kategóriái között. Ezután válassza ki az elemet a telepítési folyamat megkezdéséhez. A folyamat eltérő lehet a piactér különböző elemeinél. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Leválasztott vagy részben csatlakoztatott forgatókönyv

Ha Azure Stack leválasztott módban van, és nincs internetkapcsolata, a PowerShell és a *Marketplace Syndication eszköz* használatával töltheti le a Piactéri elemeket egy internetkapcsolattal rendelkező gépre. Ezután vigye át az elemeket a Azure Stack környezetbe. Leválasztott környezetben nem töltheti le a Marketplace-elemeket a Azure Stack portál használatával. 

A Marketplace Syndication eszköz egy csatlakoztatott forgatókönyvben is használható. 

A forgatókönyv két részből áll:
- **1. rész:** Letöltés az Azure Marketplace-ről. Az internet-hozzáféréssel rendelkező számítógépen konfigurálja a PowerShellt, töltse le a szindikált eszközt, majd töltse le az elemeket az Azure Marketplace-en.  
- **2. rész:** Töltse fel és tegye közzé a Azure Stack piactéren. Áthelyezi a letöltött fájlokat a Azure Stack-környezetbe, importálja őket a Azure Stackba, majd közzéteheti őket a Azure Stack piactéren.  


### <a name="prerequisites"></a>Előfeltételek
- Az Azure Stack üzembe helyezését [regisztrálni kell az Azure](azure-stack-registration.md )-ban.  

- Az internetkapcsolattal rendelkező számítógépen **Azure stack PowerShell-modul 1.2.11** vagy újabb verziójúnak kell lennie. Ha még nem létezik, [telepítse Azure stack PowerShell](azure-stack-powershell-install.md)-modulokat.  

- A letöltött Piactéri elemek importálásának engedélyezéséhez konfigurálni kell a [Azure stack kezelőhöz tartozó PowerShell-környezetet](azure-stack-powershell-configure-admin.md) .  

- Olyan Azure Stack Storage- [fiókkal](azure-stack-manage-storage-accounts.md) kell rendelkeznie, amely nyilvánosan elérhető tárolóval rendelkezik (ez egy tárolási blob). A tárolót ideiglenes tárolóként használhatja a Piactéri elemek katalógusának fájljaihoz. Ha nem ismeri a Storage-fiókokat és-tárolókat, tekintse meg a [Blobok használata – Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) az Azure dokumentációjában.

- A Marketplace Syndication eszköz az első eljárás során töltődik le. 

- A [AzCopy](/azure/storage/common/storage-use-azcopy) az optimális letöltési teljesítmény érdekében telepítheti, de erre nincs szükség.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Marketplace-elemek letöltése a Marketplace Syndication eszköz használatával

1. Egy internetkapcsolattal rendelkező számítógépen nyisson meg egy PowerShell-konzolt rendszergazdaként.

2. Adja hozzá a Azure Stack regisztrálásához használt Azure-fiókot. A fiók hozzáadásához a PowerShellben paraméterek `Add-AzureRmAccount` nélkül futtassa a parancsot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

3. Ha több előfizetéssel rendelkezik, a következő parancs futtatásával válassza ki a regisztrációhoz használtt:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
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
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importálja a szindikált modult, majd indítsa el az eszközt az alábbi parancsok futtatásával. Cserélje `Destination folder path` le az elemet az Azure Marketplace-ről letöltött fájlok tárolására szolgáló helyre.   

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

   Vegye figyelembe `Export-AzSOfflineMarketplaceItem` , hogy a `-cloud` Felhőbeli környezetet megadó további jelzőt tartalmaz. Alapértelmezés szerint ez a **azurecloud**.

6. Az eszköz futtatásakor az alábbi képhez hasonló képernyő jelenik meg az elérhető Piactéri elemek listájával:

   [![Azure Marketplace-elemek előugró ablak](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace-elemek")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Válassza ki a letölteni kívánt elemet, és jegyezze fel a *verziót*. A *CTRL* billentyűt lenyomva több lemezképet is kijelölhet. A verzióra  hivatkozik, amikor a következő eljárásban importálja az elemet. 
   
   A képek listáját a **feltételek hozzáadása** lehetőség használatával is szűrheti.

8. Kattintson az **OK gombra**, majd tekintse át és fogadja el a jogi feltételeket. 

9. A letöltés időpontja az elem méretétől függ. A letöltés befejezése után az elem elérhető a parancsfájlban megadott mappában. A letöltés egy VHD-fájlt (virtuális gépek esetén) vagy egy. zip fájlt tartalmaz (a virtuálisgép-bővítmények esetében). Tartalmazhat egy *. azpkg* formátumú katalógus-csomagot is, amely egyszerűen egy. zip-fájl.

10. Ha a letöltés meghiúsul, próbálkozzon újra a következő PowerShell-parancsmag újbóli futtatásával:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Az újrapróbálkozás előtt távolítsa el azt a terméket tartalmazó mappát, amelyben a letöltés sikertelen volt. Ha például a letöltési parancsfájl meghibásodik a letöltésekor `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, távolítsa el a `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` mappát, majd futtassa újra a parancsmagot.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>A letöltés és közzététel Azure Stack piactéren (1811-es és újabb verzió) importálása

1. Helyezze át a helyileg [letöltött](#use-the-marketplace-syndication-tool-to-download-marketplace-items) fájlokat, hogy azok elérhetők legyenek a Azure stack-környezet számára. A Marketplace Syndication eszköznek elérhetőnek kell lennie a Azure Stack-környezet számára is, mivel az importálási művelet végrehajtásához az eszközt kell használnia.

   Az alábbi ábrán egy példa látható a mappák struktúrájában. `D:\downloadfolder`a piactér összes letöltött elemét tartalmazza. Minden almappa egy Marketplace-elem (például `microsoft.custom-script-linux-arm-2.0.3`:), amelyet a termék azonosítója nevez el. Az egyes almappákon belül a Piactéri elem letöltött tartalma.

   [![Marketplace letöltési könyvtár szerkezete](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Marketplace letöltési könyvtár szerkezete")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. A Azure Stack kezelő PowerShell-munkamenet konfigurálásához kövesse az [ebben a cikkben](azure-stack-powershell-configure-admin.md) szereplő utasításokat. 

3. Importálja a szindikált modult, majd indítsa el a Marketplace Syndication eszközt a következő parancsfájl futtatásával:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   A `-origin` paraméter határozza meg az összes letöltött terméket tartalmazó legfelső szintű mappát, `"D:\downloadfolder"`például:.

   A `-AzsCredential` paraméter megadása nem kötelező. A hozzáférési jogkivonat megújítására szolgál, ha lejárt. Ha a `-AzsCredential` paraméter nincs megadva, és a jogkivonat lejár, a rendszer felszólítja az operátor hitelesítő adatainak megadására.

    > [!Note]  
    > AD FS csak a felhasználói identitásokkal való interaktív hitelesítést támogatja. Ha egy hitelesítőadat-objektumra van szükség, egyszerű szolgáltatásnevet (SPN) kell használnia. Az Azure Stack és a AD FS identitás-kezelési szolgáltatásként való létrehozásával kapcsolatos további információkért lásd: [AD FS egyszerű szolgáltatás kezelése](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. A parancsfájl sikeres befejeződése után az elemnek elérhetőnek kell lennie a Azure Stack piactéren.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>A letöltés és közzététel Azure Stack piactéren (1809 és alacsonyabb) importálása

1. A [korábban letöltött](#use-the-marketplace-syndication-tool-to-download-marketplace-items) virtuálisgép-lemezképekhez vagy-megoldási sablonokhoz helyileg elérhetővé kell tenni a fájlokat a Azure stack-környezet számára.  

2. A felügyeleti portálon feltöltheti a Marketplace-alkalmazáscsomag (. azpkg fájl) és a virtuális merevlemez lemezképét (a. vhd fájlt) Azure Stack blob Storage-ba. A csomag és a lemez fájljainak feltöltése a Azure Stack számára elérhetővé válik, így később közzéteheti az adott tételt a Azure Stack piactéren.

   A feltöltéshez nyilvánosan elérhető tárolóval rendelkező Storage-fiók szükséges (lásd a forgatókönyv előfeltételeit).  
   1. A Azure Stack felügyeleti portálon lépjen a **minden szolgáltatás** elemre, majd az **adat + tárolás** kategóriában válassza a **Storage-fiókok**lehetőséget.  
   
   2. Válasszon ki egy Storage-fiókot az előfizetésből, majd a **blob Service**alatt válassza a **tárolók**lehetőséget.  
      [![Blob Service](media/azure-stack-download-azure-marketplace-item/blob-service.png "Blob Service")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. Válassza ki a használni kívánt tárolót, majd válassza a **feltöltés** lehetőséget a **blob feltöltése** ablaktábla megnyitásához.  
      [![Tároló](media/azure-stack-download-azure-marketplace-item/container.png "Tároló")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. A blob feltöltése panelen keresse meg a csomagot és a lemezeket, amelyeket a Storage szolgáltatásba szeretne betölteni, majd válassza a **feltöltés**lehetőséget: [![Feltöltés](media/azure-stack-download-azure-marketplace-item/uploadsm.png "Feltöltés")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. A feltöltött fájlok a tároló ablaktáblán jelennek meg. Válasszon ki egy fájlt, majd másolja az URL-címet a **blob tulajdonságai** ablaktáblából. Ezt az URL-címet a következő lépésben fogja használni, amikor a Marketplace-elemet Azure Stackba importálja.  A következő képen a tároló *blob-test-Storage* , a fájl pedig a *Microsoft. WindowsServer2016DatacenterServerCore-ARM. 1.0.801. azpkg*.  A fájl URL- *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg* címe:.  
      [![Blob tulajdonságai](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "Blob tulajdonságai")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Importálja a VHD-rendszerképet Azure Stackre az **Add-AzsPlatformimage** parancsmag használatával. Ha ezt a parancsmagot használja, cserélje le a közzétevőt, az *ajánlatot*és az egyéb paramétereket az importálandó rendszerkép értékeire. 

   A rendszerkép közzétevőjét , *ajánlatát*és *SKU* -értékét a AZPKG fájlból letöltött szövegfájlból kérheti le. A szövegfájl a célhelyen tárolódik. A *verzió* értéke az előző eljárás során az Azure-ban az elem letöltésekor feljegyzett verzió. 
 
   A következő példa parancsfájlban a Windows Server 2016 Datacenter-Server Core virtuális gép értékeit használja a rendszer. A *-Osuri* érték az adott blob Storage-helyének elérési útja.

   A parancsfájl alternatívájaként a [cikkben ismertetett eljárást](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) követve importálhatja a következőt:. VHD-rendszerkép a Azure Portal használatával.

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **Tudnivalók a megoldási sablonokról:** Néhány sablon egy kis 3 MB-ot is tartalmazhat. VHD-fájl **fixed3. vhd**néven. Ezt a fájlt nem kell Azure Stackba importálnia. Fixed3. vhd.  Ezt a fájlt néhány megoldási sablon tartalmazza, hogy megfeleljen az Azure Marketplace közzétételi követelményeinek.

   Tekintse át a sablonok leírását, majd töltse le, majd importálja a megoldás sablonnal való együttműködéshez szükséges további követelményeket (például VHD-ket).  
   
   **A bővítmények ismertetése:** Ha a virtuális gép rendszerkép-bővítményeivel dolgozik, használja a következő paramétereket:
   - *Publisher*
   - *Típus*
   - *Verzió*  

   A bővítmények esetében nem használja az *ajánlatot* .   


4.  A PowerShell használatával tegye közzé a Marketplace-elemeket Azure Stack az **Add-AzsGalleryItem** parancsmag használatával. Példa:  
    ```powershell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     -Verbose
    ```
5. A katalógusbeli elemek közzététele után már elérhető a használata. A gyűjtemény közzétételének megerősítéséhez lépjen a **minden szolgáltatás**elemre, majd az **általános** kategóriában válassza a **piactér**lehetőséget.  Ha a letöltés egy megoldási sablon, ügyeljen arra, hogy az adott megoldási sablonhoz tartozó függő VHD-rendszerképet adjon hozzá.  
  [![Piactér megtekintése](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "Piactér megtekintése")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

A Azure Stack PowerShell-1.3.0 megjelenése után hozzáadhatók a virtuálisgép-bővítmények is. Példa:

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>További lépések

[Marketplace-termék létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
