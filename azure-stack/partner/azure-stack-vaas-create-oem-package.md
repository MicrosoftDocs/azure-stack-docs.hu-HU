---
title: OEM-bővítményi csomag létrehozása
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan hozhat létre OEM-bővítményi csomagot Azure Stack hub-ban.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 61e71e891f68118a89f0295528f6aab21507da1c
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625475"
---
# <a name="create-an-oem-package"></a>OEM-csomag létrehozása

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az Azure Stack hub OEM-bővítmény csomagja az a mechanizmus, amellyel az OEM-specifikus tartalom hozzá lett adva Azure Stack hub-infrastruktúrához. A tartalom a központi telepítés és az üzemeltetési folyamatok, például a frissítés, a bővítés és a mezők cseréje során használatos.

## <a name="creating-the-package"></a>A csomag létrehozása

A létrehozás és az ellenőrzés után az OEM-bővítmény használható az alapszolgáltatásban. A folytatás előtt győződjön meg arról, hogy végrehajtotta az [OEM-csomagok létrehozásának](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true)lépéseit. A csomagot ezután a rendszer elküldi a Microsoftnak a csomag-ellenőrzési munkafolyamatba való bejelentkezéshez az Varga teszt eredményeivel együtt. A következő lépések részletesen ismertetik, hogyan lehet a létrehozott fájlokat egyetlen zip-fájlba csomagolni, amelyet az alaplemez képes használni.

1. Azonosítsa a csomag következő tartalmát:
    - A csomag tartalmát tartalmazó Zip-fájl.
    - Egy `oemMetadata.xml`nevű jegyzékfájl, amelynek a tartalmának meg kell egyeznie a csomag tartalmának gyökerében lévő `metadata.xml` fájlban szereplő tartalommal.

2. Válassza ki a tartalmi fájlokat, és hozzon létre egy zip-fájlt:

    a zip-fájl tartalmának ![OEM-bővítmény létrehozásakor](media/vaas-create-oem-package-1.png) ![az elemek tartalmának tömörítése egy OEM-bővítmény csomagjának létrehozásakor](media/vaas-create-oem-package-2.png)

3. Nevezze át az eredményül kapott fájlt, hogy az azonosítható legyen.

## <a name="verifying-the-contents"></a>A tartalom ellenőrzése

A zip-fájl struktúrájának ellenőrzéséhez vizsgálja meg, és ellenőrizze, hogy nincsenek-e almappák. A TLD a tömörített tartalommal rendelkezik. Az alábbi érvényes csomag-struktúra látható:

> [!IMPORTANT]
> A szülő mappa a tartalom helyett cipzárat okoz a csomagok aláírásához.

![Csomag tartalmának megfelelő kicsomagolása OEM-bővítmény létrehozásakor](media/vaas-create-oem-package-3.png)

A zip-fájl már feltölthető az Varga-ra, és a Microsoft aláírja a csomag-ellenőrzési munkafolyamatban.

## <a name="next-steps"></a>Következő lépések

- [OEM-csomag ellenőrzése](azure-stack-vaas-validate-oem-package.md)
