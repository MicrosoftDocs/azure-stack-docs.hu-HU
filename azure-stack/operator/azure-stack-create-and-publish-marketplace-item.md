---
title: Marketplace-elemek létrehozása és közzététele Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és tehet közzé Azure Stack Marketplace-elemeket.
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
ms.date: 10/25/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 5481cdc7e4ad8dc2b3b3cbbb56a403335ff97524
ms.sourcegitcommit: 35de1a6e0fb6fe9f938d613f161dc378b3452541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72915204"
---
# <a name="create-and-publish-a-custom-azure-stack-marketplace-item"></a>Egyéni Azure Stack Marketplace-elemek létrehozása és közzététele

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack piactéren közzétett minden elem az Azure Gallery-csomag (. azpkg) formátumát használja. A *Azure Gallery Packager* eszköz lehetővé teszi, hogy egyéni Azure Gallery-csomagot hozzon létre, amelyet feltöltheti a Azure stack Marketplace-re, amelyet aztán a felhasználók is letölthetnek. Az üzembe helyezési folyamat egy Azure Resource Manager sablont használ.

## <a name="marketplace-items"></a>Marketplace-elemek

A jelen cikkben szereplő példák bemutatják, hogyan hozhat létre Windows vagy Linux típusú, egyetlen virtuálisgép-Piactéri ajánlatot.

## <a name="create-a-marketplace-item"></a>Piactéri elem létrehozása

> [!IMPORTANT]
> A virtuálisgép-Piactéri elem létrehozása előtt töltse fel az egyéni virtuálisgép-rendszerképet a Azure Stack-portálra, és kövesse a virtuálisgép- [rendszerkép hozzáadása a Azure Stackhoz](azure-stack-add-vm-image.md#add-a-vm-image-as-an-azure-stack-operator-using-the-portal)című részben leírtakat. Ezután kövesse a cikk utasításait a rendszerkép (. azpkg létrehozása) előkészítéséhez és a Azure Stack Marketplace-re való feltöltéséhez.

Egyéni Piactéri elem létrehozásához tegye a következőket:

1. Töltse le az [Azure Gallery Packager eszközt](https://aka.ms/azsmarketplaceitem) és a minta Azure stack Gallery csomagot. Ez a letöltés egyéni virtuálisgép-sablonokat tartalmaz. Bontsa ki a. zip fájlt, és nevezze át a **SimpleVMTemplate** mappát annak az elemnek a nevére, amelyet meg fog jeleníteni a Azure stack-portálon.

2. Hozzon létre egy Azure Resource Manager sablont, vagy használja a Windows/Linux rendszerhez készült minta sablonokat. Ezek a sablonok az 1. lépésben letöltött csomagoló Tool. zip fájlban találhatók. Használhatja a sablont, és módosíthatja a szövegmezőket, vagy letöltheti az előre konfigurált sablont a GitHubról. Azure Resource Manager-sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager sablonok](/azure/azure-resource-manager/resource-group-authoring-templates).

3. A katalógus csomagjának a következő struktúrát kell tartalmaznia:

   :::image type="content" source="media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png" alt-text="GYŰJTEMÉNYI csomag":::

   A központi telepítési sablonok fájljának szerkezete a következőképpen jelenik meg:

   :::image type="content" source="media/azure-stack-create-and-publish-marketplace-item/gallerypkg2.png" alt-text="GYŰJTEMÉNYI csomag":::

4. A manifest. JSON sablonban cserélje le a következő Kiemelt értékeket (a számokat), az [Egyéni rendszerkép feltöltésekor](azure-stack-add-vm-image.md#add-a-vm-image-as-an-azure-stack-operator-using-the-portal)megadott értékkel.

   > [!NOTE]  
   > A Azure Resource Manager sablonban soha ne végezzen semmilyen titkos kulcsot, például a termékkulcsot, a jelszót vagy az ügyfél által azonosítható adatokat. A sablon JSON-fájljai a katalógusban közzétett egyszeri hitelesítés nélkül érhetők el. Tárolja [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) összes titkát, és hívja meg őket a sablonból.

   A következő sablon a manifest. JSON fájl mintája:

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
       "uiDefinition": {
          "path": "UIDefinition.json" (7)
          },
       "links": [
        { "displayName": "ms-resource:documentationLink", "uri": "http://go.microsoft.com/fwlink/?LinkId=532898" }
        ],
       "artifacts": [
          {
             "name": "<Template name>",
             "type": "Template",
             "path": "DeploymentTemplates\\<Template name>.json", (8)
             "isDefault": true
          }
       ],
       "categories":[ (9)
          "Custom",
          "<Template name>"
          ],
       "images": [{
          "context": "ibiza",
          "items": [{
             "id": "small",
             "path": "icons\\Small.png", (10)
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
    - (4) – a név, amelyet az ügyfelek látnak.
    - (5) – a közzétevő neve, amelyet az ügyfelek látnak.
    - (6) – a közzétevő jogi neve.
    - (7) – a **UIDefinition. JSON** fájl tárolási helyének elérési útja.  
    - (8) – a JSON fő sablonfájl elérési útja és neve.
    - (9) – azoknak a kategóriáknak a neve, amelyekben ez a sablon megjelenik.
    - (10) – az egyes ikonok elérési útja és neve.

5. Az **MS-Resource**értékre hivatkozó összes mező esetében módosítania kell a megfelelő értékeket a **karakterláncok/erőforrások. JSON** fájlban:

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

    ![csomag megjeleníti](media/azure-stack-create-and-publish-marketplace-item/pkg1.png) ![csomag megjelenítését](media/azure-stack-create-and-publish-marketplace-item/pkg2.png)

6. Az erőforrás sikeres üzembe helyezésének biztosításához tesztelje a sablont a [Azure stack API](../user/azure-stack-profiles-azure-resource-manager-versions.md)-kkal.

7. Ha a sablon egy virtuálisgép-(VM-) rendszerképre támaszkodik, kövesse az utasításokat, és [adjon hozzá egy VM-rendszerképet a Azure Stackhoz](azure-stack-add-vm-image.md).

8. Mentse a Azure Resource Manager sablont a **/contoso.TodoList/DeploymentTemplates/** mappába.

9. Válassza ki a Marketplace-elem ikonjait és szövegét. Adjon hozzá ikonokat az **ikonok** mappához, és adjon hozzá szöveget az **erőforrások** fájlhoz a **karakterláncok** mappában. Az ikonokhoz használjon **kis**, **közepes**, **nagy**és **széles körű** elnevezési konvenciót. A méretek részletes leírását a [Marketplace-elemek felhasználói felületének dokumentációjában](#reference-marketplace-item-ui) tekintheti meg.

    > [!NOTE]
    > A Piactéri elem megfelelő létrehozásához mind a négy ikon mérete (kis, közepes, nagy, széles) szükséges.

10. A manifest. JSON további szerkesztését a következő témakörben tekintheti meg [: hivatkozás: Marketplace Item manifest. JSON](#reference-marketplace-item-manifestjson).

11. Amikor befejezte a fájlok módosítását, alakítsa át egy. azpkg fájlba. A konverziót a **AzureGallery. exe** eszköz és a korábban letöltött minta-katalógus használatával hajtja végre. Futtassa az alábbi parancsot:

    ```shell
    .\AzureGallery.exe package –m c:\<path>\<gallery package name>\manifest.json –o c:\Temp
    ```

    > [!NOTE]
    > A kimeneti elérési út bármely választott útvonal lehet, és nem kell a C: meghajtó alatt lennie. A manifest. JSON fájl és a kimeneti csomag teljes elérési útja azonban léteznie kell. Ha például a kimeneti elérési út `C:\<path>\galleryPackageName.azpkg`, akkor a `C:\<path>` mappának léteznie kell.
    >
    >

## <a name="publish-a-marketplace-item"></a>Piactéri elem közzététele

1. Az Azure Blob Storage-ba való feltöltéséhez használja a PowerShellt vagy a Azure Storage Explorer. Feltöltheti a helyi Azure Stack tárolóba, vagy feltöltheti az Azure Storage-ba, amely a csomag ideiglenes helye. Győződjön meg arról, hogy a blob nyilvánosan elérhető.

2. Ha a katalógus-csomagot Azure Stackba szeretné importálni, az első lépés az ügyfél virtuális géphez való távoli kapcsolódás (RDP), hogy az imént létrehozott fájlt átmásolja a Azure Stackba.

3. Környezet hozzáadása:

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Futtassa a következő szkriptet az erőforrás katalógusba történő importálásához:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg –Verbose
    ```

5. Ellenőrizze, hogy rendelkezik-e az elemek tárolására elérhető érvényes Storage-fiókkal. A `GalleryItemURI` értéket a Azure Stack felügyeleti portálról kérheti le. Válassza ki a **Storage-fiók-> blob tulajdonságai-> URL-címet**a. azpkg kiterjesztéssel. A Storage-fiók csak ideiglenes használatra szolgál, hogy közzé lehessen tenni a piactéren.

   A katalógus-csomag befejezése és az **Add-AzsGalleryItem**használatával való feltöltése után az egyéni virtuális gép ekkor megjelenik a piactéren, valamint az **erőforrás létrehozása** nézetben. Vegye figyelembe, hogy az egyéni katalógus-csomag nem látható a **piactér-kezelésben**.

   [![Egyéni Marketplace-elemek feltöltve](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Egyéni Marketplace-elemek feltöltve")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Miután sikeresen közzétette az elemet a piactéren, törölheti a tartalmat a Storage-fiókból.

   > [!CAUTION]  
   > Az alapértelmezett katalógus-összetevők és az egyéni katalógus-összetevők mostantól hitelesítés nélkül elérhetők a következő URL-címeken keresztül:  
   `https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
   `https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`

6. A Piactéri elemeket a **Remove-AzureRMGalleryItem** parancsmag használatával távolíthatja el. Példa:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

   > [!NOTE]
   > Előfordulhat, hogy a piactér felhasználói felülete hibát jelez az elemek eltávolítása után. A hiba elhárításához kattintson a **Beállítások** elemre a portálon. Ezután válassza a **módosítások elvetése** a **portál testreszabása**alatt lehetőséget.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Hivatkozás: Marketplace Item manifest. JSON

### <a name="identity-information"></a>Azonosító adatok

| Név | Szükséges | Type (Típus) | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| Név |X |Sztring |[A-Za-z0-9] + | |
| Gyártó/kiadó |X |Sztring |[A-Za-z0-9] + | |
| Verzió |X |Sztring |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metaadatok

| Név | Szükséges | Type (Típus) | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |80 karakteres javaslat |Előfordulhat, hogy a portál nem jeleníti meg helyesen az elemnév nevét, ha az 80 karakternél hosszabb. |
| PublisherDisplayName |X |Sztring |30 karakterből álló javaslat |Előfordulhat, hogy a portál nem jeleníti meg megfelelően a közzétevő nevét, ha az hosszabb 30 karakternél. |
| PublisherLegalName |X |Sztring |Legfeljebb 256 karakter | |
| Összefoglalás |X |Sztring |60 – 100 karakter | |
| LongSummary |X |Sztring |140 – 256 karakter |Azure Stackban még nem alkalmazható. |
| Leírás |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 – 5 000 karakter | |

### <a name="images"></a>Lemezképek

A piactér a következő ikonokat használja:

| Név | Szélessége | Magasság | Megjegyzések |
| --- | --- | --- | --- |
| Széles |255 px |115 px |Mindig szükséges |
| Nagy |115 px |115 px |Mindig szükséges |
| Közepes |90 px |90 px |Mindig szükséges |
| Kicsi |40 px |40 px |Mindig szükséges |
| Képernyőkép |533 px |324 px |Mindig szükséges |

### <a name="categories"></a>Kategóriák

Minden Piactéri elemnek címkével kell rendelkeznie, amely meghatározza, hogy az elem hol jelenik meg a portál felhasználói felületén. Kiválaszthatja a meglévő kategóriák egyikét Azure Stack (**számítás**, **adatok + tárolás**stb.), vagy választhat egy újat.

### <a name="links"></a>Hivatkozások

Minden Piactéri tétel tartalmazhat további tartalmakra mutató hivatkozásokat. A hivatkozások nevek és URI-k listájaként vannak megadva:

| Név | Szükséges | Type (Típus) | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |Legfeljebb 64 karakter hosszú lehet. | |
| URI |X |URI | | |

### <a name="additional-properties"></a>További tulajdonságok

Az előző metaadatok mellett a piactér-szerzők az alábbi formában is biztosíthatnak egyéni kulcs/érték párokat:

| Név | Szükséges | Type (Típus) | Korlátozások | Leírás |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sztring |Legfeljebb 25 karakter hosszú lehet. | |
| Value (Díj) |X |Sztring |Legfeljebb 30 karakter. | |

### <a name="html-sanitization"></a>HTML-tisztítás

A HTML-t engedélyező mezők esetében a következő [elemek és attribútumok engedélyezettek](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Hivatkozás: Marketplace-elemek felhasználói felülete

A Marketplace-elemek ikonjai és szövege a Azure Stack-portálon látható módon történik.

### <a name="create-blade"></a>A Create (Létrehozás) panel

![Panel létrehozása – Azure Stack Marketplace-elemek](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Marketplace-elemek részletei panel

![Azure Stack Marketplace-elemek részletei panel](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Stack Marketplace – áttekintés](azure-stack-marketplace.md)
- [Marketplace-elemek letöltése](azure-stack-download-azure-marketplace-item.md)
- [Azure Resource Manager sablonok formázása és szerkezete](/azure/azure-resource-manager/resource-group-authoring-templates)
