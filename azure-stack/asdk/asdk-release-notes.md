---
title: ASDK kibocsátási megjegyzései
description: A Azure Stack Development Kit (ASDK) javításai, javításai és ismert problémái.
author: sethmanheim
ms.topic: article
ms.date: 04/06/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: bbe37512d943a45b5981f4e862f55440ade3b08f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80806702"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a Azure Stack Development Kit (ASDK) változásairól, javításáról és ismert problémáiról tartalmaz információkat. Ha nem biztos abban, hogy melyik verziót futtatja, [a portálon ellenőrizze a](../operator/azure-stack-updates.md)következőt:.

Az [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-hírcsatornára](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)való feliratkozással naprakész maradhat a ASDK újdonságai.

::: moniker range="azs-2002"
## <a name="build-12002035"></a>1.2002.0.35 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban rögzített problémák, változások és új funkciók listáját a [Azure stack kibocsátási megjegyzései](../operator/release-notes.md)című szakaszban találja.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- A visszafejtési tanúsítvány jelszava új lehetőség a biztonsági másolatok visszafejtéséhez szükséges titkos kulcsot tartalmazó önaláírt tanúsítvány (. pfx) jelszavának megadására. Ez a jelszó csak akkor szükséges, ha a biztonsági másolat tanúsítvány használatával van titkosítva.
- A jelen kiadás Azure Stack ismert problémáinak listáját az [ismert problémák](../operator/known-issues.md) című cikkben találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.

#### <a name="sql-vm-provision-fails-in-asdk"></a>Az SQL virtuális gép kiépítése meghiúsul a ASDK

- Alkalmazható: Ez a probléma a 2002-es ASDK vonatkozik.
- Ok: új SQL-alapú virtuális gép létrehozásakor a ASDK 2002-es verziójában hibaüzenetet kaphat a " **Microsoft. SQLServer. Management" közzétevővel, a "SqlIaaSAgent" típussal és a Type Handler "2,0" verziójával, amely nem található a bővítmény-tárházban.** Azure Stack központban nincs **SqlIaaSAgent** 2,0.
::: moniker-end

::: moniker range="azs-1910"
## <a name="build-11910058"></a>1.1910.0.58 létrehozása

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
  
## <a name="build-11908020"></a>1.1908.0.20 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1908#whats-new-2) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1908)találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1.1907.0.20 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1907#fixes-3) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1907)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-3) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end
