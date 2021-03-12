---
title: Marketplace-elemek létrehozása és közzététele Azure Stack központban
description: Megtudhatja, hogyan hozhat létre és tehet közzé Azure Stack hub Marketplace-elemeket.
author: sethmanheim
ms.topic: article
ms.date: 03/09/2021
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/16/2020
ms.openlocfilehash: deb0c21a6e352d2e2fa4fdab3cab718512746591
ms.sourcegitcommit: 4f1d22747c02ae280609174496933fca8c04a6cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102606451"
---
# <a name="create-and-publish-a-custom-azure-stack-hub-marketplace-item"></a>Egyéni Azure Stack hub Marketplace-elemek létrehozása és közzététele

Az Azure Stack hub piactéren közzétett minden elem az Azure Gallery-csomag (. azpkg) formátumát használja. Az *Azure Gallery Packager* eszköz lehetővé teszi, hogy egyéni Azure Gallery-csomagot hozzon létre, amelyet feltöltheti a Azure stack hub piactérre, amelyet aztán a felhasználók is letölthetnek. Az üzembe helyezési folyamat egy Azure Resource Manager sablont használ.

## <a name="marketplace-items"></a>Marketplace-elemek

A jelen cikkben szereplő példák bemutatják, hogyan hozhat létre Windows vagy Linux típusú, egyetlen virtuálisgép-Piactéri ajánlatot.

### <a name="prerequisites"></a>Előfeltételek

A virtuális gép Marketplace-elemének létrehozása előtt tegye a következőket:

1. Töltse fel az egyéni virtuálisgép-rendszerképet a Azure Stack hub-portálra, és kövesse a virtuálisgép- [rendszerkép hozzáadása Azure stack hub-hoz](azure-stack-add-vm-image.md)című témakör útmutatását.
2. A cikk utasításait követve csomagolja ki a rendszerképet (hozzon létre egy. azpkg), és töltse fel az Azure Stack hub piactérre.

## <a name="create-a-marketplace-item"></a>Piactér-elemek létrehozása

Egyéni Piactéri elem létrehozásához tegye a következőket:

1. Töltse le az [Azure Gallery Packager eszközt](https://aka.ms/azsmarketplaceitem):

   :::image type="content" source="media/azure-stack-create-and-publish-marketplace-item/samples-tool.png" alt-text="Katalógus-csomagoló":::

2. Az eszköz a. azpk formátumú csomagokat tartalmazza, amelyeket először ki kell kinyerni. A fájlkiterjesztés a ". azpk" fájlból a ". zip" névre nevezhető át, vagy az Ön által választott archiváló eszköz használatára van lehetősége:

   :::image type="content" source="media/azure-stack-create-and-publish-marketplace-item/sample-packages.png" alt-text="Minták csomagjai":::

3. A kibontás után a. zip fájl tartalmazza a rendelkezésre álló Linux-vagy Windows Azure Resource Manager-sablonokat. Újra felhasználhatja az előre elkészített Resource Manager-sablonokat, és módosíthatja a megfelelő paramétereket az Azure Stack hub-portálon megjelenő elem termék adataival. Vagy újra használhatja a. azpkg fájlt, és kihagyhatja a következő lépéseket a saját katalógus-csomag testreszabásához.

4. Hozzon létre egy Azure Resource Manager sablont, vagy használja a Windows/Linux rendszerhez készült minta sablonokat. Ezek a sablonok az 1. lépésben letöltött csomagoló Tool. zip fájlban találhatók. Használhatja a sablont, és módosíthatja a szövegmezőket, vagy letöltheti az előre konfigurált sablont a GitHubról. Azure Resource Manager-sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager sablonok](/azure/azure-resource-manager/resource-group-authoring-templates).

5. A katalógus csomagjának a következő struktúrát kell tartalmaznia:

   ![Képernyőfelvétel a katalógus-csomag struktúrájáról](media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png)

6. Cserélje le a következő Kiemelt értékeket (számokat) a sablon **Manifest.js** az [Egyéni rendszerkép feltöltésekor](azure-stack-add-vm-image.md#add-a-platform-image)megadott értékre.

   > [!NOTE]  
   > A Azure Resource Manager sablonban soha ne végezzen semmilyen titkos kulcsot, például a termékkulcsot, a jelszót vagy az ügyfél által azonosítható adatokat. A sablon JSON-fájljai a katalógusban közzétett egyszeri hitelesítés nélkül érhetők el. Tárolja [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) összes titkát, és hívja meg őket a sablonból.

   Javasoljuk, hogy a saját egyéni sablon közzététele előtt próbálja meg közzétenni a mintát, és ellenőrizze, hogy működik-e a környezetben. Miután ellenőrizte, hogy ez a lépés működik, törölje a mintát a katalógusból, és ismételje meg az ismétlődő módosításokat, amíg az eredmény nem teljesül.

   A következő sablon a fájl Manifest.jsmintája:

    ```json
    {
       "$schema": "https://gallery.azure.com/schemas/2015-10-01/manifest.json#",
       "name": "Test", (1)
       "publisher": "<Publisher name>", (2)
       "version": "<Version number>", (3)
       "displayName": "ms-resource:displayName", (4)
       "publisherDisplayName": "ms-resource:publisherDisplayName", (5)
       "publisherLegalName": "ms-resource:publisherDisplayName", (6)
       "summary": "ms-resource:summary",
       "longSummary": "ms-resource:longSummary",
       "description": "ms-resource:description",
       "longDescription": "ms-resource:description",
       "links": [
        { "displayName": "ms-resource:documentationLink", "uri": "http://go.microsoft.com/fwlink/?LinkId=532898" }
        ],
       "artifacts": [
          {
             "isDefault": true
          }
       ],
       "images": [{
          "context": "ibiza",
          "items": [{
             "id": "small",
             "path": "icons\\Small.png", (7)
             "type": "icon"
             },
             {
                "id": "medium",
                "path": "icons\\Medium.png",
                "type": "icon"
             },
             {
                "id": "large",
                "path": "icons\\Large.png",
                "type": "icon"
             },
             {
                "id": "wide",
                "path": "icons\\Wide.png",
                "type": "icon"
             }]
        }]
    }
    ```

    Az alábbi lista a példa sablon előző számozott értékeit ismerteti:

    - (1) – az ajánlat neve.
    - (2) – a közzétevő neve, szóköz nélkül.
    - (3) – a sablon verziója szóköz nélkül.
    - (4) – az ügyfelek által megjelenített név.
    - (5) – a közzétevő neve, amelyet az ügyfelek látnak.
    - (6) – a közzétevő jogi neve.
    - (7) – az egyes ikonok elérési útja és neve.

7. Az **MS-Resource** értékre hivatkozó összes mező esetében módosítania kell a megfelelő értékeket a **karakterlánc/resources.js** fájlon belül:

    ```json
    {
    "displayName": "<OfferName.PublisherName.Version>",
    "publisherDisplayName": "<Publisher name>",
    "summary": "Create a simple VM",
    "longSummary": "Create a simple VM and use it",
    "description": "<p>This is just a sample of the type of description you could create for your gallery item!</p><p>This is a second paragraph.</p>",
    "documentationLink": "Documentation"
    }
    ```

8. A központi telepítési sablonok fájljának szerkezete a következőképpen jelenik meg:

   :::image type="content" source="media/azure-stack-create-and-publish-marketplace-item/deployment-templates.png" alt-text="Üzembehelyezési sablonok":::

   Cserélje le a rendszerkép értékeit a **createuidefinition.js** fájlban az egyéni rendszerkép feltöltésekor megadott értékre.

9. Az erőforrás sikeres üzembe helyezésének biztosításához tesztelje a sablont az [Azure stack hub API](../user/azure-stack-profiles-azure-resource-manager-versions.md)-kkal.

10. Ha a sablon egy virtuálisgép-(VM-) rendszerképre támaszkodik, kövesse az utasításokat, és [adjon hozzá egy VM-rendszerképet Azure stack hubhoz](azure-stack-add-vm-image.md).

11. Mentse a Azure Resource Manager sablont a **/contoso.TodoList/DeploymentTemplates/** mappába.

12. Válassza ki a Marketplace-elem ikonjait és szövegét. Adjon hozzá ikonokat az **ikonok** mappához, és adjon hozzá szöveget az **erőforrások** fájlhoz a **karakterláncok** mappában. Az ikonokhoz használjon **kis**, **közepes**, **nagy** és **széles körű** elnevezési konvenciót. A méretek részletes leírását a [Marketplace-elemek felhasználói felületének dokumentációjában](#reference-marketplace-item-ui) tekintheti meg.

    > [!NOTE]
    > A Piactéri elem megfelelő létrehozásához mind a négy ikon mérete (kis, közepes, nagy, széles) szükséges.

13. A **Manifest.js** további szerkesztését a következő témakörben tekintheti meg [: a piactéri elem manifest.js](#reference-marketplace-item-manifestjson):.

14. Amikor befejezte a fájlok módosítását, alakítsa át egy. azpkg fájlba. A konverziót a **AzureGallery.exe** eszköz és a korábban letöltött minta-katalógus használatával hajtja végre. Futtassa az alábbi parancsot:

    ```shell
    .\AzureGalleryHubGallery.exe package -m c:\<path>\<gallery package name>\manifest.json -o c:\Temp
    ```

    > [!NOTE]
    > A kimeneti elérési út bármely választott útvonal lehet, és nem kell a C: meghajtó alatt lennie. A fájl manifest.jsának teljes elérési útját és a kimeneti csomagot azonban léteznie kell. Ha például a kimeneti elérési út `C:\<path>\galleryPackageName.azpkg` , akkor a mappának `C:\<path>` léteznie kell.
    >
    >

## <a name="publish-a-marketplace-item"></a>Piactér-elemek közzététele

### <a name="az-modules"></a>[Az modulok](#tab/az)

1. Az Azure Blob Storage-ba való feltöltéséhez használja a PowerShellt vagy a Azure Storage Explorer. Feltöltheti a helyi Azure Stack hub tárolóba, vagy feltöltheti az Azure Storage-ba, amely a csomag ideiglenes helye. Győződjön meg arról, hogy a blob nyilvánosan elérhető.

2. Ha a katalógus-csomagot Azure Stack hubhoz szeretné importálni, az első lépés az ügyfél virtuális géphez való távoli kapcsolódás (RDP), hogy az imént létrehozott fájlt a Azure Stack hubhoz másolja.

3. Környezet hozzáadása:

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Connect-AzAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Futtassa a következő szkriptet az erőforrás katalógusba történő importálásához:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg -Verbose
    ```

5. Ellenőrizze, hogy rendelkezik-e az elemek tárolására elérhető érvényes Storage-fiókkal. Az értéket lekérheti `GalleryItemURI` az Azure stack hub felügyeleti portálján. Válassza ki a **Storage-fiók-> blob tulajdonságai-> URL-címet** a. azpkg kiterjesztéssel. A Storage-fiók csak ideiglenes használatra szolgál, hogy közzé lehessen tenni a piactéren.

   A katalógus-csomag befejezése és az **Add-AzsGalleryItem** használatával való feltöltése után az egyéni virtuális gép ekkor megjelenik a piactéren, valamint az **erőforrás létrehozása** nézetben. Vegye figyelembe, hogy az egyéni katalógus-csomag nem látható a **piactér-kezelésben**.

   [![Egyéni Marketplace-elemek feltöltve](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Egyéni Marketplace-elemek feltöltve")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Miután sikeresen közzétette az elemet a piactéren, törölheti a tartalmat a Storage-fiókból.

   Az alapértelmezett katalógus-összetevők és az egyéni katalógus-összetevők mostantól hitelesítés nélkül elérhetők a következő URL-címeken keresztül:

   - `https://galleryartifacts.adminhosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`
   - `https://galleryartifacts.hosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`

7. A Piactéri elemeket a **Remove-AzGalleryItem** parancsmag használatával távolíthatja el. Például:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

> [!Note]  
> Előfordulhat, hogy a piactér felhasználói felülete hibát jelez az elemek eltávolítása után. A hiba elhárításához kattintson a **Beállítások** elemre a portálon. Ezután válassza a **módosítások elvetése** a **portál testreszabása** alatt lehetőséget.

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm)

1. Az Azure Blob Storage-ba való feltöltéséhez használja a PowerShellt vagy a Azure Storage Explorer. Feltöltheti a helyi Azure Stack hub tárolóba, vagy feltöltheti az Azure Storage-ba, amely a csomag ideiglenes helye. Győződjön meg arról, hogy a blob nyilvánosan elérhető.

2. Ha a katalógus-csomagot Azure Stack hubhoz szeretné importálni, az első lépés az ügyfél virtuális géphez való távoli kapcsolódás (RDP), hogy az imént létrehozott fájlt a Azure Stack hubhoz másolja.

3. Környezet hozzáadása:

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRMAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Futtassa a következő szkriptet az erőforrás katalógusba történő importálásához:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg -Verbose
    ```

5. Ellenőrizze, hogy rendelkezik-e az elemek tárolására elérhető érvényes Storage-fiókkal. Az értéket lekérheti `GalleryItemURI` az Azure stack hub felügyeleti portálján. Válassza ki a **Storage-fiók-> blob tulajdonságai-> URL-címet** a. azpkg kiterjesztéssel. A Storage-fiók csak ideiglenes használatra szolgál, hogy közzé lehessen tenni a piactéren.

   A katalógus-csomag befejezése és az **Add-AzsGalleryItem** használatával való feltöltése után az egyéni virtuális gép ekkor megjelenik a piactéren, valamint az **erőforrás létrehozása** nézetben. Vegye figyelembe, hogy az egyéni katalógus-csomag nem látható a **piactér-kezelésben**.

   [![Egyéni Marketplace-elemek feltöltve](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Egyéni Marketplace-elemek feltöltve")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Miután sikeresen közzétette az elemet a piactéren, törölheti a tartalmat a Storage-fiókból.

   Az alapértelmezett katalógus-összetevők és az egyéni katalógus-összetevők mostantól hitelesítés nélkül elérhetők a következő URL-címeken keresztül:

   - `https://galleryartifacts.adminhosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`
   - `https://galleryartifacts.hosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`

7. A Piactéri elemeket a **Remove-AzGalleryItem** parancsmag használatával távolíthatja el. Például:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

> [!Note]  
> Előfordulhat, hogy a piactér felhasználói felülete hibát jelez az elemek eltávolítása után. A hiba elhárításához kattintson a **Beállítások** elemre a portálon. Ezután válassza a **módosítások elvetése** a **portál testreszabása** alatt lehetőséget.

---

## <a name="reference-marketplace-item-manifestjson"></a>Hivatkozás: Marketplace-elem manifest.jsbekapcsolva

### <a name="identity-information"></a>Azonosító adatok

| Name | Kötelező | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Név |X |Sztring |[A-Za-z0-9] + | |
| Publisher |X |Sztring |[A-Za-z0-9] + | |
| Verzió |X |Sztring |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metaadatok

| Name | Kötelező | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |80 karakteres javaslat |Előfordulhat, hogy a portál nem jeleníti meg helyesen az elemnév nevét, ha az 80 karakternél hosszabb. |
| PublisherDisplayName |X |Sztring |30 karakterből álló javaslat |Előfordulhat, hogy a portál nem jeleníti meg megfelelően a közzétevő nevét, ha az hosszabb 30 karakternél. |
| PublisherLegalName |X |Sztring |Legfeljebb 256 karakter | |
| Összefoglalás |X |Sztring |60 – 100 karakter | |
| LongSummary |X |Sztring |140 – 256 karakter |Azure Stack központban még nem alkalmazható. |
| Leírás |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 – 5 000 karakter | |

### <a name="images"></a>Képek

A piactér a következő ikonokat használja:

| Name | Szélesség | Magasság | Jegyzetek |
| --- | --- | --- | --- |
| Széles |255 px |115 px |Mindig szükséges |
| Nagy |115 px |115 px |Mindig szükséges |
| Közepes |90 px |90 px |Mindig szükséges |
| Kicsi |40 px |40 px |Mindig szükséges |
| Képernyőfelvétel |533 px |324 px |Választható |

### <a name="categories"></a>Kategóriák

Minden Piactéri elemnek címkével kell rendelkeznie, amely meghatározza, hogy az elem hol jelenik meg a portál felhasználói felületén. Kiválaszthatja Azure Stack hub egyik meglévő kategóriáját (**számítás**, **adatok + tárolás** stb.), vagy választhat egy újat is.

### <a name="links"></a>Hivatkozások

Minden Piactéri tétel tartalmazhat további tartalmakra mutató hivatkozásokat. A hivatkozások nevek és URI-k listájaként vannak megadva:

| Name | Kötelező | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |Legfeljebb 64 karakter hosszú lehet. | |
| URI |X |URI | | |

### <a name="additional-properties"></a>További tulajdonságok

Az előző metaadatok mellett a piactér-szerzők az alábbi formában is biztosíthatnak egyéni kulcs/érték párokat:

| Name | Kötelező | Típus | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |Legfeljebb 25 karakter hosszú lehet. | |
| Érték |X |Sztring |Legfeljebb 30 karakter. | |

### <a name="html-sanitization"></a>HTML-tisztítás

A HTML-t engedélyező mezők esetében a következő [elemek és attribútumok engedélyezettek](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Hivatkozás: Marketplace-elemek felhasználói felülete

A Marketplace-elemek ikonjai és szövege a Azure Stack hub-portálon látható módon történik.

### <a name="create-blade"></a>A Create (Létrehozás) panel

![Panel létrehozása – Azure Stack hub Marketplace-elemek](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Marketplace-elemek részletei panel

![Azure Stack hub Marketplace-elemek részletei panel](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Stack hub Marketplace – áttekintés](azure-stack-marketplace.md)
- [Marketplace-elemek letöltése](azure-stack-download-azure-marketplace-item.md)
- [Azure Resource Manager sablonok formázása és szerkezete](/azure/azure-resource-manager/resource-group-authoring-templates)
