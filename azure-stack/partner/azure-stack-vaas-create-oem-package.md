---
title: OEM-bővítményi csomag létrehozása
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan hozhat létre OEM-bővítményi csomagot Azure Stack hub-ban.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c38a2738fcdfcb61c032982ce4ae0d2ea88d6e3f
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661473"
---
# <a name="create-an-oem-package"></a>Hardvergyártói csomag létrehozása

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az Azure Stack hub OEM-bővítmény csomagja az a mechanizmus, amellyel az OEM-specifikus tartalom hozzá lett adva Azure Stack hub-infrastruktúrához. A tartalom a központi telepítés és az üzemeltetési folyamatok, például a frissítés, a bővítés és a mezők cseréje során használatos.

## <a name="creating-the-package"></a>A csomag létrehozása

A létrehozás és az ellenőrzés után az OEM-bővítmény használható az alapszolgáltatásban. A folytatás előtt győződjön meg arról, hogy végrehajtotta az [OEM-csomagok létrehozásának](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true)lépéseit. A csomagot ezután a rendszer elküldi a Microsoftnak a csomag-ellenőrzési munkafolyamatba való bejelentkezéshez az Varga teszt eredményeivel együtt. A következő lépések részletesen ismertetik, hogyan lehet a létrehozott fájlokat egyetlen zip-fájlba csomagolni, amelyet az alaplemez képes használni.

1. Azonosítsa a csomag következő tartalmát:
    - A csomag tartalmát tartalmazó Zip-fájl.
    - Egy nevű `oemMetadata.xml`jegyzékfájl, amelynek meg kell egyeznie a csomag tartalmának `metadata.xml` gyökerében lévő fájl tartalmával.

2. Válassza ki a tartalmi fájlokat, és hozzon létre egy zip-fájlt:

    ![A zip-fájl tartalma, ha OEM-](media/vaas-create-oem-package-1.png) ![bővítményt hoz létre egy OEM-bővítmény létrehozásakor az elemek tartalmának tömörítéséhez](media/vaas-create-oem-package-2.png)

3. Nevezze át az eredményül kapott fájlt, hogy az azonosítható legyen.

## <a name="verifying-the-contents"></a>A tartalom ellenőrzése

A zip-fájl struktúrájának ellenőrzéséhez vizsgálja meg, és ellenőrizze, hogy nincsenek-e almappák. A TLD a tömörített tartalommal rendelkezik. Az alábbi érvényes csomag-struktúra látható:

> [!IMPORTANT]
> A szülő mappa a tartalom helyett cipzárat okoz a csomagok aláírásához.

![Csomag tartalmának megfelelő kicsomagolása OEM-bővítmény létrehozásakor](media/vaas-create-oem-package-3.png)

A zip-fájl már feltölthető az Varga-ra, és a Microsoft aláírja a csomag-ellenőrzési munkafolyamatban.

## <a name="next-steps"></a>További lépések

- [OEM-csomag ellenőrzése](azure-stack-vaas-validate-oem-package.md)
