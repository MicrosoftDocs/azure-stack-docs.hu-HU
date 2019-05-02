---
title: A Microsoft Azure Stack hibaelhárítása |} A Microsoft Docs
description: Az Azure Stack Development Kit (ASDK) hibaelhárítási információkat.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: a890e02ae1f2447db6fd25329881a4daa4e489b2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986059"
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>A Microsoft Azure Stack Development Kit (ASDK) hibáinak elhárítása
Ez a cikk a ASDK általános hibaelhárítási információkat nyújt. Ha nem ismertetett problémát tapasztal, ellenőrizze, hogy ellenőrizze a [Azure Stack MSDN-fórumában](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) további segítséget és információt.  

> [!IMPORTANT]
> Mivel a ASDK kiértékelési környezet, rendszer nem érhető el – a Microsoft támogatja a szolgáltatások (CSS) hivatalos támogatja.

A problémák megoldásához, amelyek ebben a szakaszban ismertetett javaslatok több forrásból származnak, és előfordulhat, hogy, vagy sem oldja meg az adott problémát. Hitelesítésikód-példák "adott állapotában", ha nem lehet garantálni a várt eredményt. Ebben a szakaszban megegyezik a gyakori módosítások és a frissítések fejlesztéseket a termékhez vannak megvalósítva.

## <a name="deployment"></a>Környezet
### <a name="deployment-failure"></a>Központi telepítési problémái
Ha hibát tapasztal a telepítés során, a központi telepítést a sikertelen lépés újraindíthatja használatával – Újrafuttatás lehetősége a telepítési parancsfájl a következő példához hasonlóan:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Az üzembe helyezés végén a PowerShell-munkamenet még meg nyitva, és semmilyen kimenet nem jelenik meg.
Ez a viselkedés esetén valószínűleg csak egy PowerShell-parancsablakban alapértelmezett viselkedését eredményét lett kiválasztva. A development kit központi telepítés sikeres volt, de a parancsfájl az ablak kiválasztásakor szünetel. Ellenőrizheti a telepítés véget ért által keresett szót "Válassza" a parancsablakban címsorában. Kijelölésének, az ESC billentyű lenyomásával, és azt követően a befejezési üzenetet jelenjenek meg.

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
### <a name="default-image-and-gallery-item"></a>Alapértelmezett kép és a katalógus elem
Virtuális gépek az Azure Stack üzembe helyezése előtt hozzá kell adni egy Windows Server rendszerképet és a katalógus elemet.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Az Azure Stack gazdagépen az újraindítás után néhány virtuális gép esetleg nem indul el automatikusan.
A gazdagép az újraindítást követően, Észreveheti, az Azure Stack-szolgáltatások nem érhetők el közvetlenül. Ennek az az oka az Azure Stack [infrastruktúra virtuális gépein](asdk-architecture.md#virtual-machine-roles) RPs hajtsa végre a megfelelő néhány konzisztenciájának ellenőrzése időt, de végül automatikusan elindul.

Előfordulhat, hogy a bérlői virtuális gépek nem indul el automatikusan az Azure Stack development kit gazdagép újraindítása után is. Ez egy ismert probléma, és csak az online állapotba helyezés néhány manuális lépéseket igényel:

1.  Indítsa el az Azure Stack development kit állomás **Feladatátvevőfürt-kezelőben** a Start menüből.
2.  Válassza ki a fürt **S-Cluster.azurestack.local**.
3.  Válassza ki **szerepkörök**.
4.  Bérlő virtuális gépek megjelennek egy *mentett* állapota. Ha minden infrastruktúra virtuális gépek futnak, kattintson a jobb gombbal a bérlői virtuális gépeket, és válassza ki **Start** folytatni a virtuális gép.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Tudom a törölt néhány virtuális gép, de továbbra is tekintse meg a VHD-fájlok a lemezen. Ez a viselkedés várható?
Igen, ez a viselkedés várható. Így mivel készült:

* Ha töröl egy virtuális Gépet, a VHD-k nem törlődnek. A lemezei külön erőforrások az erőforráscsoportban.
* Lekérdezi egy storage-fiók törlésekor a törlés látható azonnal az Azure Resource Manageren keresztül, de a lemezek tartalmazhat továbbra is tartanak Storage szemétgyűjtés futtatásáig.

Ha a VHD-k "árva" című fontos tudni, hogy találhatók-e egy storage-fiók, amely törölve lett a mappában részei. Ha a tárfiók nem lett törölve, normál, azok továbbra is létezik.

Tudjon meg többet az adatmegőrzési küszöbérték és igény szerinti visszaigénylését konfigurálásával kapcsolatos [tárfiókok kezelését](../operator/azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Tárolási visszaigénylését
Akár 14 óra regenerált kapacitás megjelennek a portálon vehet igénybe. Terület-visszaigénylést számos tényező befolyásolja, többek között a használat százalékos aránya a tároló belső fájlok block blob tárolóban függ. Ezért attól függően, hogy mennyi adat törlődik, nincs garancia arra, hogy a szemétgyűjtő futtatásakor kell visszaigényelt terület mennyisége a.

## <a name="next-steps"></a>További lépések
[Látogasson el az Azure Stack támogatási fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
