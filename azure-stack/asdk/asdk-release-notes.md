---
title: ASDK kibocsátási megjegyzései
description: A Azure Stack Development Kit (ASDK) javításai, javításai és ismert problémái.
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/10/2020
ms.openlocfilehash: ea7c991a1d9b16580cb4fbd7f812ec776da6e084
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674302"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a Azure Stack Development Kit (ASDK) változásairól, javításáról és ismert problémáiról tartalmaz információkat. Ha nem biztos abban, hogy melyik verziót futtatja, [a portálon ellenőrizze a](../operator/azure-stack-updates.md)következőt:.

Az ![ RSS ](./media/asdk-release-notes/feed-icon-14x14.png) [RSS-hírcsatornára](https://aka.ms/asdkrss)való feliratkozással naprakész maradhat a ASDK újdonságai.

::: moniker range="azs-2008"
## <a name="build-120081388"></a>1.2008.13.88 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban rögzített problémák, változások és új funkciók listáját a [Azure stack kibocsátási megjegyzései](../operator/release-notes.md)című szakaszban találja.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

::: moniker-end

::: moniker range="azs-2005"
## <a name="build-12005040"></a>1.2005.0.40 létrehozása

### <a name="new-features"></a>Új funkciók

- Az ebben a kiadásban rögzített problémák, változások és új funkciók listáját a [Azure stack kibocsátási megjegyzései](../operator/release-notes.md)című szakaszban találja.

### <a name="fixed-and-known-issues"></a>Rögzített és ismert problémák

- A visszafejtési tanúsítvány jelszava új lehetőség a biztonsági másolatok visszafejtéséhez szükséges titkos kulcsot tartalmazó önaláírt tanúsítvány (. pfx) jelszavának megadására. Ez a jelszó csak akkor szükséges, ha a biztonsági másolat tanúsítvány használatával van titkosítva.
- Kijavított egy problémát, amely miatt a Felhőbeli helyreállítás sikertelen volt, ha az eredeti külső tanúsítvány jelszava megváltozott a több csomópontos forrásrendszer esetében. 
- A jelen kiadás Azure Stack ismert problémáinak listáját az [ismert problémák](../operator/known-issues.md) című cikkben találja.
- Vegye figyelembe, hogy az elérhető Azure Stack gyorsjavítások nem alkalmazhatók a ASDK.

#### <a name="initial-configuration-fails-in-asdk"></a>A kezdeti konfiguráció meghiúsul a ASDK

- A ASDK telepítésekor előfordulhat, hogy a " **Deployment-Phase0-DeployBareMetal"** hibaüzenet jelenik meg, a " **Deployment-InitialSteps" állapot pedig "Error"**.

- Áthidaló megoldás:

1. Nyissa meg a fájlt a C:\CloudDeployment\Roles\PhysicalMachines\Tests\BareMetal.Tests.ps1 bármely olyan szerkesztőben, amely egy vonal számlálóval, például a PowerShell ISE-vel rendelkezik.

2. Cserélje le a 822 sort a alábbiakra:

   ```powershell

   PartNumber = if($_.PartNumber) {$_.PartNumber.Trim()} else {""};

   ```  
::: moniker-end

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
