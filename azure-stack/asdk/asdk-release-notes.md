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
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 9d0820634a469f775c1e3b6637c604ae98681be2
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70188193"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a Azure Stack Development Kit (ASDK) változásairól, javításáról és ismert problémáiról tartalmaz információkat. Ha nem biztos abban, hogy melyik verziót futtatja, [a portálon ellenőrizze a](../operator/azure-stack-updates.md)következőt:.

Az [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-hírcsatornára](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)való feliratkozással naprakész maradhat a ASDK újdonságai.

::: moniker range="azs-1908"
## <a name="build-11908020"></a>1\.1908.0.20 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1908.md#whats-new) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](../operator/azure-stack-release-notes-1908.md#fixes) of the Azure Stack release notes. -->
- Az ismert problémák listáját [ebben a cikkben](../operator/azure-stack-release-notes-known-issues-1908.md)találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1\.1907.0.20 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1907.md#fixes) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](../operator/azure-stack-release-notes-known-issues-1907.md)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](../operator/azure-stack-release-notes-1907.md#hotfixes) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>1\.1906.0.30 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) tartalmazza.

### <a name="changes"></a>Módosítások

- Hozzáadott egy **AzS-SRNG01** támogatási gyűrűs virtuális gépet, amely a log Collection szolgáltatást üzemelteti a Azure stack számára. További információ: virtuálisgép- [szerepkörök](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1906.md#fixes) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](../operator/azure-stack-release-notes-known-issues-1906.md)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](../operator/azure-stack-release-notes-1906.md#hotfixes) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end

::: moniker range="azs-1905"
## <a name="build-11905040"></a>1\.1905.0.40 létrehozása

<!-- ### Changes -->

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) tartalmazza.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Kijavítva egy olyan problémát, amelyben a **RegisterWithAzure. psm1** PowerShell-szkriptet kellett SZERKESZTENI a [ASDK sikeres regisztrálásához](asdk-register.md) .
- A jelen kiadásban rögzített egyéb Azure Stack problémák listájáért tekintse meg a Azure Stack kibocsátási megjegyzések [jelen szakaszát](../operator/azure-stack-release-notes-1905.md#fixes) .
- Az ismert problémák listáját [ebben a cikkben](../operator/azure-stack-release-notes-known-issues-1905.md)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](../operator/azure-stack-release-notes-1905.md#hotfixes) nem alkalmazhatók a Azure stack ASDK.
::: moniker-end
