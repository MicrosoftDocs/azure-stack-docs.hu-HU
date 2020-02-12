---
title: Azure Stack hub-ellenőrzés – ajánlott eljárások
description: Ez a cikk az érvényesítés szolgáltatásként történő használatának ajánlott eljárásait ismerteti.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 647e724b179d994819032859c325bf711cb9d2ee
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143832"
---
# <a name="create-an-oem-package"></a>OEM-csomag létrehozása

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az Azure Stack hub OEM-bővítmény csomagja az a mechanizmus, amellyel az OEM-specifikus tartalmak bekerülnek a Azure Stack hub-infrastruktúrába, a központi telepítésben való használathoz, valamint olyan operatív folyamatokhoz, mint például a frissítés, a bővítés és a mezők cseréje.

## <a name="creating-the-package"></a>A csomag létrehozása

A létrehozás és az ellenőrzés után az OEM-bővítmény használható az alapszolgáltatásban.  A folytatás előtt győződjön meg arról, hogy elvégezte az [OEM-csomag létrehozásához](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true)szükséges lépéseket. A csomagot ezután a rendszer elküldi a Microsoftnak a csomag-ellenőrzési munkafolyamatba való bejelentkezéshez az Varga teszt eredményeivel együtt. A következő lépések részletesen ismertetik, hogyan lehet a létrehozott fájlokat egyetlen zip-fájlba csomagolni, amelyet az alaplemez képes használni.

1. Azonosítsa a csomag következő tartalmát:
    - A csomag tartalmát tartalmazó Zip-fájl
    - Egy `oemMetadata.xml`nevű jegyzékfájl, amelynek meg kell egyeznie a tartalomban található metadata. xml fájl tartalmával.

2. Válassza ki a tartalmi fájlokat, és hozzon létre egy zip-fájlt a tartalomból:

    ![zip-fájl tartalma](media/vaas-create-oem-package-1.png) ![az elemek tartalmának tömörítése](media/vaas-create-oem-package-2.png)

3. Nevezze át az eredményül kapott fájlt, hogy a rendszer elég legyen annak azonosításához.

## <a name="verifying-the-contents"></a>A tartalom ellenőrzése

A zip-fájl struktúrájának ellenőrzéséhez vizsgálja meg, és ellenőrizze, hogy nincsenek-e almappák. A TLD a tömörített tartalommal rendelkezik. Alább látható egy érvényes csomag szerkezete.
> [!IMPORTANT]
> A szülő mappa a tartalom helyett cipzárat okoz a csomagok aláírásához.

![Csomag tartalmának megfelelő tömörített tartalma](media/vaas-create-oem-package-3.png)

A zip-fájl már feltölthető az Varga-ra, és a Microsoft aláírja a csomag-ellenőrzési munkafolyamatban.

## <a name="next-steps"></a>Következő lépések

- [OEM-csomag ellenőrzése](azure-stack-vaas-validate-oem-package.md)
