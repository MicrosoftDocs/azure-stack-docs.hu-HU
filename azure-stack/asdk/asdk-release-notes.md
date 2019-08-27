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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 3f11a7b5066d0b50d85a40be1df47dfe1a5ade38
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025845"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a Azure Stack Development Kit (ASDK) változásairól, javításáról és ismert problémáiról tartalmaz információkat. Ha nem biztos abban, hogy melyik verziót futtatja, [a portálon ellenőrizze a](../operator/azure-stack-updates.md)következőt:.

Az [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)-hírcsatornára való feliratkozással naprakész maradhat a ASDK újdonságai.

## <a name="build-11907020"></a>1\.1907.0.20 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) tartalmazza.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Ha egyes Piactéri rendszerképeket használó virtuálisgép-erőforrásokat hoz létre, előfordulhat, hogy nem tudja befejezni az üzemelő példányt. Áthidaló megoldásként kattintson a **sablon és paraméterek letöltése** hivatkozásra az **Összefoglalás** lapon, majd a **sablon** panelen kattintson a **telepítés** gombra.
- Az ebben a kiadásban kijavított Azure Stack problémák listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1907.md#fixes) tartalmazza.
- Az ismert problémák listáját [ebben a cikkben](../operator/azure-stack-release-notes-known-issues-1907.md)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](../operator/azure-stack-release-notes-1907.md#hotfixes) nem alkalmazhatók a Azure stack ASDK.

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

## <a name="build-11905040"></a>1\.1905.0.40 létrehozása

<!-- ### Changes -->

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) tartalmazza.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- Kijavítva egy olyan problémát, amelyben a **RegisterWithAzure. psm1** PowerShell-szkriptet kellett SZERKESZTENI a [ASDK](asdk-register.md) sikeres regisztrálásához.
- A jelen kiadásban rögzített egyéb Azure Stack problémák listájáért tekintse meg a Azure Stack kibocsátási megjegyzések [jelen szakaszát](../operator/azure-stack-release-notes-1905.md#fixes) .
- Az ismert problémák listáját [ebben a cikkben](../operator/azure-stack-release-notes-known-issues-1905.md)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](../operator/azure-stack-release-notes-1905.md#hotfixes) nem alkalmazhatók a Azure stack ASDK.

## <a name="build-11904036"></a>1\.1904.0.36 létrehozása

<!-- ### Changes -->

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban szereplő új funkciók listáját a Azure Stack kibocsátási megjegyzések [jelen szakasza](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) tartalmazza.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- A regisztrációs parancsfájl futtatásakor a szolgáltatás egyszerű időtúllépése miatt a [ASDK](asdk-register.md) sikeres regisztrálásához szerkesztenie kell a **RegisterWithAzure. psm1** PowerShell-szkriptet. Tegye a következőket:

  1. A ASDK-gazdagépen nyissa meg a **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** fájlt egy emelt szintű engedélyekkel rendelkező szerkesztőben.
  2. A 1249. sorban adjon hozzá `-TimeoutInSeconds 1800` egy paramétert a végén. Ezt a kiegészítést a regisztrációs parancsfájl futtatásához szükséges egyszerű szolgáltatásnév okozza. A 1249. sor ekkor a következőképpen jelenik meg:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Az 1902-es kiadásban azonosított VPN-kapcsolati probléma kijavítva.

- A jelen kiadásban rögzített egyéb Azure Stack problémák listájáért tekintse meg a Azure Stack kibocsátási megjegyzések [jelen szakaszát](../operator/azure-stack-release-notes-1904.md#fixes) .
- Az ismert problémák listáját [ebben a cikkben](../operator/azure-stack-release-notes-known-issues-1904.md)találja.
- Vegye figyelembe, hogy az [elérhető Azure stack gyorsjavítások](../operator/azure-stack-release-notes-1904.md#hotfixes) nem alkalmazhatók a Azure stack ASDK.

