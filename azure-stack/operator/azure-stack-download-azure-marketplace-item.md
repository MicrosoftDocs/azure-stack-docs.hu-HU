---
title: Az Azure marketplace-elemek letöltése |} A Microsoft Docs
description: A felhő üzemeltetője letöltheti marketplace-elemek az Azure-ból az Azure Stack üzemelő példányához.
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
ms.reviewer: unknown
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 5b90bc5014d0f8ebfaed8532f62b7c4f3aee8667
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691864"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Az Azure marketplace-elemek letöltése az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Felhő-felelősként elemek letöltése az Azure Marketplace-ről, és elérhetővé teheti őket az Azure Stackben. Az elemek is vannak az Azure Marketplace-elemek, amelyek előzetesen teszteltük, és támogatja az Azure Stack használatának rendszerezett listáját. További elemek gyakran kerülnek a listán, tehát továbbra is látogasson vissza új tartalmat. 

Az Azure piactéren való kapcsolódáshoz két forgatókönyv közül választhat: 

- **Ez a forgatókönyv csatlakoztatott** – az Azure Stack-környezet az internetre is csatlakoztatva van szükség. Az Azure Stack portálon használatával keresse meg és töltse le az elemeket. 
- **A leválasztott vagy részlegesen csatlakoztatott forgatókönyv** -eszközzel a Marketplace-en tartalomtípus-gyűjtési a Marketplace-elemek letöltése az Internet eléréséhez szükséges. Ezt követően a letöltött fájlok átvitele az Azure Stack kapcsolat nélküli telepítése. Ebben a forgatókönyvben a Powershellt használja.

Lásd: [Azure Marketplace-elemek az Azure Stack](azure-stack-marketplace-azure-items.md) letöltheti a marketplace-elemek listáját.

## <a name="connected-scenario"></a>Csatlakoztatott forgatókönyv

Ha az Azure Stack csatlakozik az internethez, a felügyeleti portál segítségével Piactéri termékek letöltése.

### <a name="prerequisites"></a>Előfeltételek

Az Azure Stack üzemelő példányához kell internetkapcsolattal rendelkezik, és lehet [regisztrálva van az Azure](azure-stack-registration.md ).

### <a name="use-the-portal-to-download-marketplace-items"></a>A portál használatával töltse le a marketplace-elemek
  
1. Jelentkezzen be az Azure Stack rendszergazdai portálon.

2.  Marketplace-elemek letöltése előtt tekintse át a rendelkezésre álló lemezterület. Később Ha letölthető elemet választja, összehasonlíthatja a letöltési méret a rendelkezésre álló tárolókapacitását. Ha kapacitása korlátozott, érdemes lehet lehetőségei [kezeléséhez rendelkezésre álló területet](azure-stack-manage-storage-shares.md#manage-available-space). 

    Rendelkezésre álló területet, áttekintheti a **régiók kezelése** válassza ki a régiót, ismerje meg, és folytassa a kívánt **erőforrás-szolgáltatók** > **tárolási**:

    ![Tekintse át a tárolóhely](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Nyissa meg az Azure Stack piactéren, és csatlakozzon az Azure-bA. Ehhez válassza ki a **Piactéri elemek**, majd válassza ki **hozzáadása az Azure-ból**:

    ![Adja hozzá az Azure-ból](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    A portálon az Azure Marketplace-ről letölthető elemek listáját jeleníti meg. Termékek név, kiadó és/vagy termék típusa szerint szűrheti. Kiválaszthatja az egyes elemekre kattintva megtekintheti azok leírását és a további tudnivalókért, köztük a letöltési méret:

    ![Marketplace-en listája](media/azure-stack-download-azure-marketplace-item/image03.PNG)

4. Válassza ki az elemet, és válassza **letöltése**. Letöltési ideje eltérőek lehetnek.

    ![Töltse le az üzenet](media/azure-stack-download-azure-marketplace-item/image04.png)

    A letöltés befejezése után telepítheti az új Piactéri elem az Azure Stack-operátorokról vagy a felhasználó.

5. A letöltött elem üzembe helyezéséhez válassza **+ erőforrás létrehozása**, majd keresse meg az új Piactéri elem a kategóriák között. Ezután válassza ki az elemet, a telepítés megkezdéséhez. A folyamat különböző marketplace-elemek esetében eltérő. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Leválasztott vagy részlegesen csatlakoztatott forgatókönyv

Ha az Azure Stack kapcsolat nélküli üzemmódban, és internetkapcsolat nélkül, használhatja a PowerShell és a *marketplace tartalomtípus-gyűjtési eszköz* egy gépen a marketplace-elemek letöltése az internetkapcsolattal rendelkező. Ezután a cikkek átvitele az Azure Stack környezettel. Leválasztott környezet esetén a marketplace-elemek nem tudja letölteni az Azure Stack-portál használatával. 

A Marketplace-en az együttműködési eszköz egy csatlakoztatott forgatókönyvben is használható. 

Ebben a forgatókönyvben két részből áll:
- **1. rész:** Töltse le az Azure Marketplace-ről. Internet-hozzáféréssel rendelkező számítógépen a PowerShell konfigurálása, töltse le a tartalomtípus-gyűjtési eszközt, és töltse le az Azure piactéren elemek űrlap.  
- **2. rész:** Feltöltése és közzététele az Azure Stack piactéren. Importálja őket az Azure Stack helyezze át az Azure Stack környezettel letöltött fájlokat, és közzéteheti az Azure Stack piactéren.  


### <a name="prerequisites"></a>Előfeltételek
- Az Azure Stack üzemelő példány lehet [regisztrálva van az Azure](azure-stack-registration.md ).  

- Rendelkeznie kell internetkapcsolattal rendelkező számítógép **Azure Stack PowerShell-modul verzióját 1.2.11** vagy újabb verziója. Ha még nincs, [meghatározott Azure Stack PowerShell-modulok telepítéséhez](azure-stack-powershell-install.md).  

- A letöltött Piactéri elem, importálás engedélyezése a [PowerShell környezetet az Azure Stack-operátorokról](azure-stack-powershell-configure-admin.md) kell konfigurálni.  

- Rendelkeznie kell egy [tárfiók](azure-stack-manage-storage-accounts.md) az Azure Stackben, amely rendelkezik egy nyilvánosan elérhető tárolót (amely egy storage-blobba). A tárolót használja ideiglenes tárolóként a piactéren elemek gyűjteménye fájlokat. Ha nem ismeri a storage-fiókok és a tárolók, lásd: [blobok használata – Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) az Azure dokumentációjában olvashatók.

- A piactér tartalomtípus-gyűjtési eszközt letölti az első eljárás során. 

- Telepíthet [AzCopy](/azure/storage/common/storage-use-azcopy) letölthető optimális teljesítményt, de ez nem kötelező.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>A piactér tartalomtípus-gyűjtési eszköz használatával töltse le a marketplace-elemek

1. Internetkapcsolattal rendelkező számítógépen nyissa meg rendszergazdaként egy PowerShell-konzolt.

2. Adja hozzá az Azure-fiók, amely regisztrálja az Azure Stack segítségével. A fiók hozzáadása a PowerShell futási `Add-AzureRmAccount` paraméterek nélkül. Az Azure-fiók hitelesítő adatainak megadását kéri, és lehetséges, hogy a fiók konfigurációja alapján 2 többtényezős hitelesítés használatára.

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot, válassza ki a regisztrációhoz használja:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Töltse le a Marketplace-en az együttműködési eszköz legújabb verzióját a következő parancsfájlt:  

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

5. A tartalomtípus-gyűjtési modul importálása, és ezután indítsa el az eszközt a következő parancsok futtatásával. Cserélje le `Destination folder path` együtt az Azure Marketplace-ről letöltött fájlok tárolási helyét.   

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

   Vegye figyelembe, hogy `Export-AzSOfflineMarketplaceItem` rendelkezik egy további `-cloud` jelzőt, amely meghatározza a felhőalapú környezetben. Alapértelmezés szerint a **azurecloud**.

6. Amikor az eszköz fut, a rendelkezésre álló marketplace-elemek listáját a következő képen hasonló képernyő kell megjelennie:

   [![Az Azure Marketplace-elemek előugró](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace-elemek")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Válassza ki, hogy töltse le, és jegyezze fel a kívánt elemet a *verzió*. Tartsa a *Ctrl* billentyűvel pedig kijelölheti több lemezképet. Hivatkozik az *verzió* a következő eljárással elem importálásakor. 
   
   Rendszerképek listájának használatával emellett szűrheti a **adja meg a feltételeket** lehetőséget.

8. Válassza ki **OK**, és tekintse át és fogadja el a jogi feltételeket. 

9. Az, hogy a letöltési idő attól függ, hogy az elem méretét. A letöltés befejezése után a cikk érhető el, hogy a parancsfájl megadott mappában. A letöltés tartalmaz egy VHD-fájl (virtuális gépek esetén) vagy egy .zip-fájlt (a virtuális gépi bővítmények). Emellett tartalmazhat egy gyűjteménycsomag, az a *.azpkg* formátum, amely egyszerűen egy .zip fájl.

10. Ha a letöltés sikertelen, próbálkozzon újra a következő PowerShell-parancsmag újbóli futtatásával:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Mielőtt újra próbálkozna, távolítsa el a termék mappát, amelyben a letöltés nem sikerült. Például, ha a letöltés parancsfájl futtatása sikertelen, a letöltésekor `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, távolítsa el a `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` mappát, majd futtassa újra a parancsmagot.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>A letöltés importálása és közzététele az Azure Stack piactéren (1811 és újabb)

1. Helyezze át a fájlokat, amely rendelkezik [korábban letöltött](#use-the-marketplace-syndication-tool-to-download-marketplace-items) helyben, hogy elérhetők legyenek az Azure Stack-környezet. A Marketplace-en az együttműködési eszköz is elérhetőnek kell lennie az Azure Stack-környezethez, mert kell használnia az eszközt az importálási művelet végrehajtásához.

   Az alábbi képen egy mappa szerkezete példa látható. `D:\downloadfolder` a letöltött marketplace-elemek tartalmazza. Minden almappa Piactéri elem (például `microsoft.custom-script-linux-arm-2.0.3`) nevű által a termék azonosítóját. Minden almappa belül van a marketplace-elem letöltött tartalmat.

   [![Marketplace-en letöltési könyvtárstruktúrát](media/azure-stack-download-azure-marketplace-item/mp1sm.png "letöltési könyvtárstruktúrát Marketplace-en")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Kövesse a [Ez a cikk](azure-stack-powershell-configure-admin.md) konfigurálása az Azure Stack operátori PowerShell-munkamenetet. 

3. Importálja a modult a tartalomtípus-gyűjtési és majd indítsa el a piactéren az együttműködési eszköz a következő szkript futtatásával:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   A `-origin` paraméter adja meg a legfelső szintű mappát, amely tartalmazza az összes letöltött termékek; például `"D:\downloadfolder"`.

   A `-AzsCredential` paramétert nem kötelező megadni. Ha már lejárt szolgál a hozzáférési jogkivonat megújításához. Ha a `-AzsCredential` paraméter nincs megadva, és a jogkivonat lejár, megjelenik egy kérdés a operátor hitelesítő adatok beírását.

    > [!Note]  
    > Az AD FS csak támogatja az interaktív hitelesítéshez a felhasználói identitásokat. Ha egy hitelesítőadat-objektumnak szükség, egy szolgáltatásnév (SPN) kell használnia. Egy egyszerű szolgáltatást az Azure Stack és az AD FS, az identity management-szolgáltatás beállításának további információkért lásd: [kezelés egyszerű szolgáltatást az AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

4. Miután a parancsprogram sikeresen lefutott, az elem elérhetőnek kell lennie az Azure Stack piactéren.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>A letöltés importálása és közzététele az Azure Stack piactéren (1809 és alacsonyabb)

1. A virtuálisgép-lemezképek vagy megoldássablonokkal, amely rendelkezik a fájlok [korábban letöltött](#use-the-marketplace-syndication-tool-to-download-marketplace-items) az Azure Stack környezettel való helyben elérhetővé kell tenni.  

2. A felügyeleti portál használatával a Piactéri elem csomag (.azpkg fájlt) és a virtuális merevlemez (.vhd fájl) Rendszerkép feltöltése az Azure Stack Blob storage. A csomag feltöltése és tartalmazó fájlokat elérhetővé válnak az Azure Stackhez az elem később az Azure Stack piactéren való közzétételéhez.

   Feltöltés szükséges hozzá egy nyilvánosan elérhető tárolót egy tárfiókot, (tekintse meg a forgatókönyv előfeltételei).  
   1. Az Azure Stack felügyeleti portálon, lépjen a **minden szolgáltatás** , majd a a **adatok + tárolás** kategória, jelölje be **tárfiókok**.  
   
   2. Válassza ki a tárfiókot az előfizetéséből, majd a **BLOB SERVICE**válassza **tárolók**.  
      [![BLOB szolgáltatás](media/azure-stack-download-azure-marketplace-item/blob-service.png "Blob szolgáltatás")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. Válassza ki a tárolót használja, és válassza ki a kívánt **feltöltése** megnyitásához a **blob feltöltése** ablaktáblán.  
      [![Tároló](media/azure-stack-download-azure-marketplace-item/container.png "tároló")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. A blob feltöltése panelen keresse meg a csomag és a lemez fájlokat betölteni a storage-ba, és jelölje ki **feltöltése**: [![Töltse fel](media/azure-stack-download-azure-marketplace-item/uploadsm.png "feltöltése")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. Feltöltött fájlok a tároló panelen jelennek meg. Válasszon ki egy fájlt, és másolja az URL-címet a **Blob tulajdonságai** ablaktáblán. Amikor importálja a Piactéri elem az Azure Stack a következő lépésben fogja használni az URL-címet.  Az alábbi ábrán a tároló-e *test-blobtároló* , és a fájl *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  A fájl URL-cím *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg* .  
      [![BLOB tulajdonságai](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "Blob tulajdonságai")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Importálja a VHD-lemezképet az Azure Stack használatával a **Add-AzsPlatformimage** parancsmagot. Ha ezt a parancsmagot használja, cserélje le a *közzétevő*, *ajánlat*, és más paraméterértékek importált kép értékekkel. 

   Beszerezheti a *közzétevő*, *ajánlat*, és *termékváltozat* a szövegfájl, amely letölti a AZPKG fájl a lemezkép értékét. A szöveges fájlt tárolja a célhelyen. A *verzió* értéke a verziót, ha az elem letöltése az Azure-ból az előző eljárásban feljegyzett. 
 
   A következő példa parancsfájlt a Windows Server 2016 Datacenter - Server Core virtuális gép értékeit kell használni. Az érték *- Osuri* van egy példa az elem a blob storage helyének elérési útját.

   Ez a szkript alternatívájaként használhatja a [ebben a cikkben leírt eljárás](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) importálása a. VHD-lemezképet az Azure portal használatával.

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **Megoldás-sablonok:** Egyes sablonok lehetnek egy kis 3 MB. VHD-fájl nevű **fixed3.vhd**. Nem kell ezt a fájlt importálja az Azure Stackhez. Fixed3.vhd.  Ez a fájl megtalálható néhány megoldássablonokkal, az Azure Marketplace közzétételi igényeinek megfelelően.

   Tekintse át a sablon leírása, és töltse le és importálja a további követelményeket, például VHD-k, amelyek szükségesek a megoldás sablonnal dolgozni.  
   
   **A bővítményekről:** Amikor a lemezképet virtuálisgép-bővítmények használata során használja a következő paramétereket:
   - *Publisher*
   - *Típus*
   - *Verzió*  

   Ne használjon *ajánlat* bővítmények.   


4.  A Piactéri elem közzététele az Azure Stack használatával a PowerShell használatával a **Add-AzsGalleryItem** parancsmagot. Példa:  
    ```powershell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     -Verbose
    ```
5. A katalógus egy elemét a közzététel után már használhatja. Ellenőrizze, hogy a gyűjteményelem közzé van téve, lépjen a **minden szolgáltatás**, majd a a **általános** kategória, jelölje be **Marketplace**.  Ha a letöltés megoldássablon, mindenképpen adja hozzá bármelyik függő VHD-lemezképet a megoldássablon.  
  [![Nézet marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "nézet Marketplace-en")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

Az Azure Stack PowerShell 1.3.0 kiadása most már hozzáadhat virtuális gépi bővítmények. Példa:

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>További lépések

[Hozzon létre, és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md)