---
title: A ASDK hibáinak megoldása | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani a Azure Stack Development Kitt (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 7946b8339c9ff1127c0a9d9572c49527208b38f2
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118661"
---
# <a name="troubleshoot-the-asdk"></a>A ASDK hibáinak megoldása
Ez a cikk a Azure Stack Development Kit (ASDK) gyakori hibaelhárítási információit tartalmazza. Ha olyan problémát tapasztal, amely nem dokumentált, mindenképpen tekintse meg az [MSDN-fórum Azure stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) segítséget.  

> [!IMPORTANT]
> Mivel a ASDK egy kiértékelési környezet, a Microsoft ügyfélszolgálati szolgálatai (CSS) nem kínálunk hivatalos támogatást.

Az ebben a szakaszban leírt hibaelhárítási problémákra vonatkozó javaslatok több forrásból származnak, és előfordulhat, hogy nem oldják meg az adott problémát. A kód példái a "ahogy van", a várt eredmények pedig nem garantáltak. Ez a szakasz a termékre vonatkozó újdonságokkal kapcsolatos gyakori szerkesztési és frissítési funkciókra vonatkozik.

## <a name="deployment"></a>Környezet
### <a name="deployment-failure"></a>Üzembe helyezési hiba
Ha a telepítés során hiba lép fel, a központi telepítési parancsfájl újbóli beállításával újraindíthatja a telepítést a sikertelen lépéssel. Példa:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>A telepítés végén a PowerShell-munkamenet továbbra is nyitva van, és nem jelenít meg kimenetet
Ez a viselkedés valószínűleg csak egy PowerShell-parancssorablak alapértelmezett viselkedésének eredménye, ha ki van választva. A ASDK központi telepítése sikeres volt, de a parancsfájlt az ablak kiválasztásakor szüneteltették. Ellenőrizze, hogy a telepítés befejeződött-e, ha a "kiválasztás" szót keresi a parancssori ablakban. Az ESC billentyű lenyomásával törölje a kijelölését, és a befejezési üzenetet is meg kell jeleníteni.

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
### <a name="default-image-and-gallery-item"></a>Alapértelmezett rendszerkép és gyűjtemény elem
A virtuális gépek Azure Stack-ben való üzembe helyezése előtt hozzá kell adni egy Windows Server-lemezképet és-gyűjteményi elemeket.

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>A Azure Stack gazdagép újraindítása után néhány virtuális gép nem indul el automatikusan.
A gazdagép újraindítása után észreveheti, Azure Stack szolgáltatások nem azonnal érhetők el. Ennek az az oka, hogy Azure Stack infrastruktúra-alapú [virtuális gépek](asdk-architecture.md#virtual-machine-roles) és RPs a konzisztencia ellenőrzéséhez hosszabb időt vesz igénybe, de végül automatikusan elindul.

Azt is megfigyelheti, hogy a bérlői virtuális gépek nem indulnak el automatikusan a ASDK-gazdagép újraindítása után. Ez egy ismert probléma, és csak néhány manuális lépést kell megtennie, hogy online állapotba kerüljön:

1.  A ASDK-gazdagépen indítsa el **Feladatátvevőfürt-kezelő** a Start menüből.
2.  Válassza ki az **S-cluster. azurestack. local**fürtöt.
3.  Válassza a **szerepkörök**lehetőséget.
4.  A bérlői virtuális gépek *mentett* állapotban jelennek meg. Ha az összes infrastruktúra-virtuális gép fut, kattintson a jobb gombbal a bérlői virtuális gépekre, és válassza a **Start** lehetőséget a virtuális gép folytatásához.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Töröltem néhány virtuális gépet, de továbbra is láthatók a lemezen lévő VHD-fájlok. Ez a viselkedés várható?
Igen, ez a várt viselkedés. Ez a következőképpen lett kialakítva:

* Ha töröl egy virtuális gépet, a VHD-k nem törlődnek. A lemezek különálló erőforrások az erőforráscsoporthoz.
* Ha a Storage-fiók törölve lesz, a törlés azonnal látható Azure Resource Manageron keresztül, de az esetlegesen tartalmazott lemezek továbbra is tárolás alatt maradnak a tárolóban, amíg a rendszer a szemetet nem fut.

Ha "árva" virtuális merevlemezeket lát, fontos tisztában lennie azzal, hogy egy törölt Storage-fiók mappájában vannak-e. Ha a Storage-fiók nem lett törölve, a virtuális merevlemezek továbbra is a megszokott módon maradnak.

További információk az adatmegőrzési küszöbérték és az igény szerinti, a [Storage-fiókok kezelése](../operator/azure-stack-manage-storage-accounts.md)című témakörben olvashatók.

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Tárhely-visszanyerés
Akár 14 órát is igénybe vehet, ha visszaigényelt kapacitást szeretne megjeleníteni a portálon. A lemezterület-visszanyerés a különböző tényezőktől függ, például a belső tároló fájljainak használati százaléka a blob-tárolóban. Ezért attól függően, hogy mennyi adattal törli a rendszer, nem garantálható, hogy a rendszer mennyi helyet szabadít fel a Garbage Collector futtatásakor.

## <a name="next-steps"></a>További lépések
[Látogasson el a Azure Stack támogatási fórumára](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
