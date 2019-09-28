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
ms.date: 09/27/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: e61c3dad2383f1b6471b784b303b9f22a7dfde51
ms.sourcegitcommit: b28190b4e91cd0c045d019083e7e9203410d7c29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71354552"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a Azure Stack Development Kit (ASDK) változásairól, javításáról és ismert problémáiról tartalmaz információkat. Ha nem biztos abban, hogy melyik verziót futtatja, [a portálon ellenőrizze a](../operator/azure-stack-updates.md)következőt:.

Az [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-hírcsatornára](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)való feliratkozással naprakész maradhat a ASDK újdonságai.

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

::: moniker range="azs-1905"
## <a name="build-11905040"></a>1\.1905.0.40 létrehozása

<!-- ### Changes -->

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](/azure-stack/operator/release-notes?view=azs-1905#whats-in-this-update-1905) tartalmazza.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Kijavítva egy olyan problémát, amelyben a **RegisterWithAzure. psm1** PowerShell-szkriptet kellett SZERKESZTENI a [ASDK sikeres regisztrálásához](asdk-register.md) .
- A jelen kiadásban rögzített egyéb Azure Stack problémák listájáért tekintse meg a Azure Stack kibocsátási megjegyzések [jelen szakaszát](/azure-stack/operator/release-notes?view=azs-1905#fixes-1905) .
- Az ismert problémák listáját [ebben a cikkben](/azure-stack/operator/known-issues?view=azs-1905)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](/azure-stack/operator/release-notes?view=azs-1905#hotfixes-1905) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end
