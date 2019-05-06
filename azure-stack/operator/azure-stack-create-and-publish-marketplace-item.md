---
title: Hozzon létre, és a Piactéri elem közzététele az Azure Stackben |} A Microsoft Docs
description: Hozzon létre, és a Piactéri elem közzététele az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 8c77441f458e87a3b8da60541261338abb78057c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985422"
---
# <a name="create-and-publish-a-marketplace-item"></a>Piactéri termék létrehozása és közzététele

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="create-a-marketplace-item"></a>Piactéri elem létrehozása

1. Töltse le a [Azure Gallery Packager eszköz](https://www.aka.ms/azurestackmarketplaceitem) és a minta az Azure Stack piactéren elemet.
2. Nyissa meg a minta Piactéri elemet, és nevezze át a **SimpleVMTemplate** mappát. Használja ugyanazt a nevet, a Piactéri elem; Ha például **Contoso.TodoList**. Ez a mappa tartalmaz:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Azure Resource Manager-sablon létrehozása] ((/Azure/Azure-Resource-Manager/Resource-Group-Authoring-Templates), vagy válasszon ki egy sablont a Githubból. A Piactéri elem a sablont használja az erőforrás létrehozásához.

    > [!Note]  
    > Keményen soha nem kód a titkos kulcsok termékazonosító kulcsok, jelszót vagy az Azure Resource Manager-sablon bármely ügyfél azonosításra alkalmas adatok. A sablon JSON-fájlok érhetők el, hitelesítés nélkül a katalógusban közzététele után. Az összes titkos Store [kulcs Vault]((/azure/azure-resource-manager/resource-manager-keyvault-parameter), és hívja a sablonon belül azokat.

4. Győződjön meg arról, hogy az erőforrás sikeresen telepíthető-e, tesztelje a sablon a Microsoft Azure Stack API-kkal.
5. Ha a sablont a virtuálisgép-lemezkép támaszkodik, kövesse az utasításokat [virtuálisgép-lemezkép hozzáadása az Azure Stackhez](azure-stack-add-vm-image.md).
6. Az Azure Resource Manager-sablon mentése a **/Contoso.TodoList/DeploymentTemplates/** mappát.
7. Válassza ki az ikonok és a Marketplace-elem szövege. Az ikonok hozzáadása a **ikonok** mappában, és adja hozzá a szöveget a **erőforrások** fájlt a **karakterláncok** mappát. Használja a **kis**, **Közepes**, **nagy**, és **széles** ikonok elnevezési konvenciót. Tekintse meg a [segédanyag a Piactéri elem](#reference-marketplace-item-ui) ezeket a méreteket részletes leírását.

   > [!NOTE]
   > Négy ikon méretek (kicsi, közepes, nagy mennyiségű, széles) szükség, a Piactéri elem megfelelően létrehozásához.
   >
   >
8. Az a **Manifest.json** fájl, módosítsa **neve** a Piactéri elem nevét. Módosítson **közzétevő** a neve vagy a vállalat.
9. Alatt **összetevők**, módosítsa **neve** és **elérési út** a Azure Resource Manager-sablon, amely tartalmazza a megfelelő információkkal történő:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Cserélje le **My Marketplace-elemek** listáját, ahol a Piactéri elem meg kell jelennie a kategóriák közül:

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. Manifest.json további szerkesztéseket tekintse [hivatkozást: Piactéri elem manifest.json](#reference-marketplace-item-manifestjson).

12. A mappák csomag .azpkg fájlba, nyisson meg egy parancssort, és futtassa a következő parancsot:

    ```shell
    AzureGalleryPackager.exe package -m <path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > A teljes elérési útja a kimeneti csomaghoz léteznie kell. Például ha a kimeneti elérési út C:\MarketPlaceItem\yourpackage.azpkg, a mappa C:\MarketPlaceItem léteznie kell.
    >
    >

## <a name="publish-a-marketplace-item"></a>Piactéri elem közzététele

1. PowerShell vagy az Azure Storage Explorer használatával töltse fel a Piactéri elem (.azpkg) az Azure Blob storage. A helyi Azure Stack-tároló feltöltése, vagy töltse fel az Azure Storage; Ez a csomag egy ideiglenes helyre. Győződjön meg arról, hogy a blob nyilvánosan elérhető-e.
2. Az ügyfél virtuális gépen a Microsoft Azure Stack-környezetben győződjön meg arról, hogy a PowerShell-munkamenethez be van állítva a szolgáltatás rendszergazdai hitelesítő adataival. Talál útmutatást való hitelesítéséhez az Azure stack PowerShell [a PowerShell-sablon üzembe helyezése](../user/azure-stack-deploy-template-powershell.md).
3. Használata esetén [PowerShell 1.3.0](azure-stack-powershell-install.md) vagy újabb, használhatja a **Add-AzsGalleryItem** a Piactéri elem közzététele az Azure Stack PowerShell-parancsmagot. Előtt PowerShell 1.3.0 használatával, használhatja a parancsmagot **Add-AzureRMGalleryitem** helyén **Add-AzsGalleryItem**. Ha például a PowerShell 1.3.0 használ, vagy később:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg -Verbose
   ```

   | Paraméter | Leírás |
   | --- | --- |
   | SubscriptionID |Felügyeleti előfizetés-azonosítójára. Ez a PowerShell használatával kérheti le. Ha inkább a portálon beolvasásához, nyissa meg a szolgáltatói előfizetés, és másolja az előfizetés azonosítóját. |
   | GalleryItemUri |BLOB URI-t a Storage már feltöltött galéria csomag. |
   | API-verzió |Beállítás **2015-04-01**. |

4. Ugrás a portálra. Most láthatja a portálon, a Piactéri elem, az operátornak, vagy a felhasználó. A csomag listában való megjelenése több percet is igénybe vehet.

5. A Piactéri elem már megtörtént az Azure Stack piactéren. Kiválaszthatja, hogy törli-e a Blob storage-helyről.
    > [!Caution]  
    > Az összes alapértelmezett katalógus összetevők és az egyéni katalógus összetevőkhöz most már hozzáférhetők-hitelesítést a következő URL-címek nélkül:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Piactéri elem használatával is eltávolíthatja a **Remove-AzureRMGalleryItem** parancsmagot. Példa:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  -Verbose
   ```

   > [!NOTE]
   > A piactér felhasználói felület valószínűleg hiba elem eltávolítása után. A hiba javításához kattintson **beállítások** a portálon. Ezután válassza ki **módosítások elvetése** alatt **portál testreszabási**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Leírások: Piactéri elem manifest.json

### <a name="identity-information"></a>Azonosító adatok

| Name (Név) | Szükséges | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Name (Név) |X |String |[A-Za-z0-9]+ | |
| Közzétevő |X |String |[A-Za-z0-9]+ | |
| Version |X |String |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metaadatok

| Name (Név) | Szükséges | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Megjelenítendő név |X |String |Az ajánlás 80 karakter |A portál nem jelenítik meg a konfigurációelem nevét szabályosan Ha hosszabb 80 karakternél. |
| PublisherDisplayName |X |String |Az ajánlás 30 karakter |A portál nem jelenítik meg a közzétevő neve szabályosan ha 30 karakternél hosszabb. |
| PublisherLegalName |X |String |Legfeljebb 256 karakter hosszú lehet | |
| Összegzés |X |String |60 és 100 karakter | |
| LongSummary |X |String |140 és 256 karakter |Még alkalmazhatók az Azure Stackben. |
| Leírás |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500-as és 5000 karakternél | |

### <a name="images"></a>Képek

A piactéren az alábbi ikonok használja:

| Name (Név) | Szélesség | Magasság | Megjegyzések |
| --- | --- | --- | --- |
| Széles körű |255 px |115 px |Mindig szükséges. |
| Nagy |115 px |115 px |Mindig szükséges. |
| Közepes |90 px |90 px |Mindig szükséges. |
| Kicsi |40 px |40 px |Mindig szükséges. |
| Képernyőfelvétel |533 px |32 px |Optional |

### <a name="categories"></a>Categories

Minden Marketplace-elem egy kategóriát, amely azonosítja, ahol az elem megjelenik a portál felhasználói Felületét kell megcímkézni. Azure Stack a meglévő kategóriák közül választhat (számítási, adatok + tárolás, stb.), vagy válasszon egy újat.

### <a name="links"></a>Hivatkozások

Minden Marketplace-elem lehetnek különböző további tartalmakra is hivatkozik. A hivatkozások megadott neve és URI-k listáját:

| Name (Név) | Szükséges | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Megjelenítendő név |X |String |Legfeljebb 64 karakter hosszú lehet | |
| URI |X |URI | | |

### <a name="additional-properties"></a>További tulajdonságok

Mellett az előző metaadatok Marketplace szerzők megadhat egyéni kulcs/érték pár adatokat a következő formátumban:

| Name (Név) | Szükséges | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Megjelenítendő név |X |String |Legfeljebb 25 karakterből álló | |
| Érték |X |String |Legfeljebb 30 karakter hosszú lehet | |

### <a name="html-sanitization"></a>HTML-tisztító

Bármely mező, amely lehetővé teszi, hogy a HTML a következő elemek és attribútumok használata engedélyezett:

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Leírások: Piactéri elem felhasználói felület

Ikonok és a szöveg a Marketplace-elemek az Azure Stack portálon látható módon az alábbiak szerint.

### <a name="create-blade"></a>A Create (Létrehozás) panel

![A Create (Létrehozás) panel](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Piactéri elem részletei panel

![Piactéri elem részletei panel](media/azure-stack-create-and-publish-marketplace-item/image3.png)
