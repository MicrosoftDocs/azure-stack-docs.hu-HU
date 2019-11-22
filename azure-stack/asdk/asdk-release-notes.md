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
ms.openlocfilehash: f5955751df89262441b7afc2381d34b6cc74691c
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298727"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a Azure Stack Development Kit (ASDK) változásairól, javításáról és ismert problémáiról tartalmaz információkat. Ha nem biztos abban, hogy melyik verziót futtatja, [a portálon ellenőrizze a](../operator/azure-stack-updates.md)következőt:.

A [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-hírcsatornára](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)való feliratkozással naprakészen tarthatja a ASDK újdonságait.

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

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1908) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1908) of the Azure Stack release notes. -->
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1908)találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1\.1907.0.20 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update-1907) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1907#fixes-1907) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1907)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-1907) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>1\.1906.0.30 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1906) tartalmazza.

### <a name="changes"></a>Módosítások

- Hozzáadott egy **AzS-SRNG01** támogatási gyűrűs virtuális gépet, amely a log Collection szolgáltatást üzemelteti a Azure stack számára. További információ: virtuálisgép- [szerepkörök](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1906#fixes-1906) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1906)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-1906) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end
