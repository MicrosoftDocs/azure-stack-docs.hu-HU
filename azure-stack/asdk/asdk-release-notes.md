---
title: ASDK kibocsátási megjegyzések | Microsoft Docs
description: A Azure Stack Development Kit (ASDK) javításai, javításai és ismert problémái.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 2323f13029d4d356ce54b825bab69da5ca21d6ab
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993580"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a Azure Stack Development Kit (ASDK) változásairól, javításáról és ismert problémáiról tartalmaz információkat. Ha nem biztos abban, hogy melyik verziót futtatja, [a portálon ellenőrizze a](../operator/azure-stack-updates.md)következőt:.

A [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-hírcsatornára](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)való feliratkozással naprakészen tarthatja a ASDK újdonságait.

::: moniker range="azs-1910"
## <a name="build-11910058"></a>1\.1910.0.58 létrehozása

### <a name="new-features"></a>ÚJ funkciók

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

### <a name="new-features"></a>ÚJ funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1908)találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1\.1907.0.20 létrehozása

### <a name="new-features"></a>ÚJ funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1907#fixes-2) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1907)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>1\.1906.0.30 létrehozása

### <a name="new-features"></a>ÚJ funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1) tartalmazza.

### <a name="changes"></a>Változások

- Hozzáadott egy **AzS-SRNG01** támogatási gyűrűs virtuális gépet, amely a log Collection szolgáltatást üzemelteti a Azure stack számára. További információ: virtuálisgép- [szerepkörök](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1906#fixes-3) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1906)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-3) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end
