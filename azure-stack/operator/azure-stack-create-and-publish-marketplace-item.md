---
title: Marketplace-elemek létrehozása és közzététele Azure Stackban | Microsoft Docs
description: Marketplace-elemek létrehozása és közzététele Azure Stackban.
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
ms.date: 08/20/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: b9e1e9a1cdd0afe18a5395c99fb2eef932791667
ms.sourcegitcommit: 1a8ebd8103608b5ee9e804d7015eefe05ef55185
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69643852"
---
# <a name="create-and-publish-a-marketplace-item"></a>Piactéri termék létrehozása és közzététele

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Piactéri elem létrehozása

1. Töltse le az [Azure Gallery Packager eszközt](https://www.aka.ms/azurestackmarketplaceitem) és a minta Azure stack Marketplace-elemeket.
2. Nyissa meg a minta Marketplace-elemeket, és nevezze át a **SimpleVMTemplate** mappát. Ugyanazt a nevet használja, mint a Marketplace-eleme; például: **contoso. TodoList**. Ez a mappa a következőket tartalmazza:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Hozzon létre egy Azure Resource Manager sablont](/azure/azure-resource-manager/resource-group-authoring-templates) , vagy válasszon egy sablont a githubból. A Marketplace-tétel ezt a sablont használja egy erőforrás létrehozásához.

    > [!NOTE]  
    > A Azure Resource Manager sablonban soha ne végezzen semmilyen titkos kulcsot, például a termékkulcsot, a jelszót vagy az ügyfél által azonosítható adatokat. A sablon JSON-fájljai a katalógusban közzétett egyszeri hitelesítés nélkül érhetők el. Tárolja [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) összes titkát, és hívja meg őket a sablonból.

4. Az erőforrás sikeres üzembe helyezésének biztosításához tesztelje a sablont a Microsoft Azure Stack API-kkal.
5. Ha a sablon egy virtuálisgép-rendszerképre támaszkodik, kövesse az utasításokat a [virtuálisgép-rendszerképek Azure Stackhoz való hozzáadásához](azure-stack-add-vm-image.md).
6. Mentse a Azure Resource Manager sablont a **/contoso.TodoList/DeploymentTemplates/** mappába.
7. Válassza ki a Marketplace-elem ikonjait és szövegét. Adjon hozzá ikonokat az **ikonok** mappához, és adjon hozzá szöveget az **erőforrások** fájlhoz a **karakterláncok** mappában. Az ikonokhoz használjon **kis**, **közepes**, **nagy**és **széles körű** elnevezési konvenciót. A méretek részletes leírását a [Marketplace-elemek felhasználói felületének dokumentációjában](#reference-marketplace-item-ui) tekintheti meg.

   > [!NOTE]
   > A Piactéri elem megfelelő létrehozásához mind a négy ikon mérete (kis, közepes, nagy, széles) szükséges.
   >
   >

8. A **manifest. JSON** fájlban módosítsa a **név nevet** a Marketplace-elem nevére. Módosítsa a **közzétevőt** is a nevére vagy a vállalatára. A rendszerkép közzététele előtt frissítse a manifest. JSON fájlt, és frissítse a következő sort: "Name": "xxx". Ügyeljen arra, hogy minden egyes rendszerkép-verzióhoz más nevet használjon.
9. Azösszetevők területen módosítsa a megfelelő információk **nevét** és **elérési útját** a Azure Resource Manager sablonhoz:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Cserélje le a **saját piactér elemeit** azon kategóriák listájára, amelyekben a Marketplace-elemnek meg kell jelennie:

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. A manifest. JSON további szerkesztéséhez tekintse meg a [következő hivatkozást: Marketplace-elemek jegyzékfájlja](#reference-marketplace-item-manifestjson). JSON.

12. A mappák. azpkg fájlba való csomagolásához nyisson meg egy parancssort, és futtassa a következő parancsot:

    ```shell
    AzureGalleryPackager.exe package -m <absolute path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > A manifest. JSON fájl teljes elérési útja, valamint a kimeneti csomagnak léteznie kell. Ha például a kimeneti elérési út C:\MarketPlaceItem\yourpackage.azpkg, akkor a **C:\MarketPlaceItem** mappának léteznie kell.
    >
    >

## <a name="publish-a-marketplace-item"></a>Piactéri elem közzététele

1. Az Azure Blob Storage-ba való feltöltéséhez használja a PowerShellt vagy a Azure Storage Explorer. Feltöltheti a helyi Azure Stack tárolóba, vagy feltöltheti az Azure Storage-ba. Ez a csomag ideiglenes helye. Győződjön meg arról, hogy a blob nyilvánosan elérhető.
2. Győződjön meg arról, hogy a Microsoft Azure Stack-környezetben lévő ügyfél virtuális gépen a PowerShell-munkamenet be van állítva a szolgáltatás-rendszergazdai hitelesítő adataival. A PowerShell hitelesítésével kapcsolatban Azure Stack a [sablon üzembe helyezése a PowerShell használatával](../user/azure-stack-deploy-template-powershell.md)című témakörben talál útmutatást.
3. Ha PowerShell- [1.3.0](azure-stack-powershell-install.md) vagy újabb verziót használ, az **Add-AzsGalleryItem** PowerShell-parancsmag használatával közzéteheti a Piactéri elemeket Azure stack. A PowerShell-1.3.0 használata előtt használja az Add- **AzureRMGalleryitem** parancsmagot az **Add-AzsGalleryItem**helyett. Ha például PowerShell-1.3.0 vagy újabb verziót használ:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg -Verbose
   ```

   | Paraméter | Leírás |
   | --- | --- |
   | SubscriptionID |Rendszergazdai előfizetés azonosítója. A PowerShell használatával kérheti le. Ha szeretné lekérni a portálon, nyissa meg a szolgáltatói előfizetést, és másolja az előfizetés-azonosítót. |
   | GalleryItemUri |A gyűjteményhez már feltöltött katalógushoz tartozó blob URI. |
   | Apiversion |Beállítás **2015-04-01**. |

4. Nyissa meg a portált. Most már megtekintheti a piactér elemét a portálon, operátorként vagy felhasználóként. A csomag több percet is igénybe vehet.

5. A Piactéri eleme már mentve lett a Azure Stack piactéren. Dönthet úgy, hogy törli a blob Storage-helyről.

    > [!CAUTION]  
    > Az alapértelmezett katalógus-összetevők és az egyéni katalógus-összetevők mostantól hitelesítés nélkül elérhetők a következő URL-címeken keresztül:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. A Piactéri elemeket a **Remove-AzureRMGalleryItem** parancsmag használatával távolíthatja el. Példa:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  -Verbose
   ```

   > [!NOTE]
   > Előfordulhat, hogy a piactér felhasználói felülete hibát jelez az elemek eltávolítása után. A hiba elhárításához kattintson a **Beállítások** elemre a portálon. Ezután válassza a **módosítások** elvetése a **portál testreszabása**alatt lehetőséget.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Leírások: Marketplace-elemek jegyzékfájlja. JSON

### <a name="identity-information"></a>Azonosító adatok

| Name (Név) | Kötelező | Type | Megkötések | Leírás |
| --- | --- | --- | --- | --- |
| Name (Név) |X |Sztring |[A-Za-z0-9]+ | |
| Kiadó |X |Sztring |[A-Za-z0-9]+ | |
| Version |X |Sztring |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metaadatok

| Name (Név) | Kötelező | Type | Megkötések | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |80 karakteres javaslat |Előfordulhat, hogy a portál nem jeleníti meg helyesen az elemnév nevét, ha az 80 karakternél hosszabb. |
| PublisherDisplayName |X |Sztring |30 karakterből álló javaslat |Előfordulhat, hogy a portál nem jeleníti meg megfelelően a közzétevő nevét, ha az hosszabb 30 karakternél. |
| PublisherLegalName |X |Sztring |Legfeljebb 256 karakter | |
| Összegzés |X |Sztring |60 – 100 karakter | |
| LongSummary |X |Sztring |140 – 256 karakter |Azure Stackban még nem alkalmazható. |
| Leírás |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 – 5 000 karakter | |

### <a name="images"></a>Képek

A piactér a következő ikonokat használja:

| Name (Név) | Szélesség | Magasság | Megjegyzések |
| --- | --- | --- | --- |
| Széles |255 px |115 px |Mindig szükséges |
| Nagy |115 px |115 px |Mindig szükséges |
| Közepes |90 px |90 px |Mindig szükséges |
| Kicsi |40 px |40 px |Mindig szükséges |
| Képernyőfelvétel |533 px |324 px |Mindig szükséges |

### <a name="categories"></a>Categories

Minden Piactéri elemnek címkével kell rendelkeznie, amely meghatározza, hogy az elem hol jelenik meg a portál felhasználói felületén. Kiválaszthatja a meglévő kategóriák egyikét Azure Stackban (**számítás**, **adatok + tárolás**stb.), vagy választhat egy újat.

### <a name="links"></a>Hivatkozások

Minden Piactéri tétel tartalmazhat további tartalmakra mutató hivatkozásokat. A hivatkozások nevek és URI-k listájaként vannak megadva:

| Name (Név) | Kötelező | Type | Megkötések | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |Legfeljebb 64 karakter hosszú lehet. | |
| URI |X |URI | | |

### <a name="additional-properties"></a>További tulajdonságok

Az előző metaadatok mellett a piactér-szerzők az alábbi formában is biztosíthatnak egyéni kulcs/érték párokat:

| Name (Név) | Kötelező | Type | Megkötések | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |Legfeljebb 25 karakter hosszú lehet. | |
| Value |X |Sztring |Legfeljebb 30 karakter. | |

### <a name="html-sanitization"></a>HTML-tisztítás

A HTML-t engedélyező mezők esetében a következő [elemek és attribútumok engedélyezettek](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Leírások: Marketplace-elemek felhasználói felülete

A Marketplace-elemek ikonjai és szövege a Azure Stack-portálon látható módon történik.

### <a name="create-blade"></a>A Create (Létrehozás) panel

![A Create (Létrehozás) panel](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Marketplace-elemek részletei panel

![Marketplace-elemek részletei panel](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>További lépések

* [Azure Stack Marketplace – áttekintés](azure-stack-marketplace.md)
* [Marketplace-elemek letöltése](azure-stack-download-azure-marketplace-item.md)
