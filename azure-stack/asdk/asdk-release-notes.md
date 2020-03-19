---
title: ASDK kibocsátási megjegyzései
description: A Azure Stack Development Kit (ASDK) javításai, javításai és ismert problémái.
author: sethmanheim
ms.topic: article
ms.date: 03/18/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 6f7bec082564c9f7cb3a0c70cec7e8e7e48fbd35
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511810"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a Azure Stack Development Kit (ASDK) változásairól, javításáról és ismert problémáiról tartalmaz információkat. Ha nem biztos abban, hogy melyik verziót futtatja, [a portálon ellenőrizze a](../operator/azure-stack-updates.md)következőt:.

A [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-hírcsatornára](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)való feliratkozással naprakészen tarthatja a ASDK újdonságait.

::: moniker range="azs-2002"
## <a name="build-12002035"></a>1\.2002.0.35 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban rögzített problémák, változások és új funkciók listáját a [Azure stack kibocsátási megjegyzései](../operator/release-notes.md)című szakaszban találja.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- A visszafejtési tanúsítvány jelszava új lehetőség a biztonsági másolatok visszafejtéséhez szükséges titkos kulcsot tartalmazó önaláírt tanúsítvány (. pfx) jelszavának megadására. Ez a jelszó csak akkor szükséges, ha a biztonsági másolat tanúsítvány használatával van titkosítva.
- A jelen kiadás Azure Stack ismert problémáinak listáját az [ismert problémák](../operator/known-issues.md) című cikkben találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.
::: moniker-end

::: moniker range="azs-1910"
## <a name="build-11910058"></a>1\.1910.0.58 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban rögzített problémák, változások és új funkciók listáját a [Azure stack kibocsátási megjegyzései](../operator/release-notes.md)című szakaszban találja.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Kijavított egy problémát a naplók gyűjtésével és az Azure Storage blob-tárolóban való tárolásával kapcsolatban. A művelet szintaxisa a következő:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- Kijavítottuk a központi telepítési problémát, ha egy lassú betöltésű nyomtatásisor-kezelő szolgáltatás megakadályozza bizonyos Windows-szolgáltatások eltávolítását, és újraindítást igényel.
- A jelen kiadás Azure Stack ismert problémáinak listáját az [ismert problémák](../operator/known-issues.md) című cikkben találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.
::: moniker-end

::: moniker range="azs-1908"
  
## <a name="build-11908020"></a>1\.1908.0.20 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1908)találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1\.1907.0.20 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1907#fixes-2) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1907)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end
