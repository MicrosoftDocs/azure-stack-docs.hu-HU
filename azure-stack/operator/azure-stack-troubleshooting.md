---
title: A Microsoft Azure Stack hibaelhárítása |} A Microsoft Docs
description: Az Azure Stack hibaelhárítása.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: 9b78a7ee9af9dde3cbb40b52268cb4cbfc0a6dcc
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268242"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>A Microsoft Azure Stack hibaelhárítása

Ez a dokumentum az Azure Stackhez készült gyakori hibaelhárítási információt nyújt. A javaslatok és Kódminták elsajátítania –, és előfordulhat, hogy nem minden esetben oldja meg a problémát. 

## <a name="deployment"></a>Környezet
### <a name="general-deployment-failure"></a>Általános üzembehelyezési hiba
Ha hibát tapasztal a telepítés során, a központi telepítést a sikertelen lépés újraindíthatja használatával - Újrafuttatás lehetősége az üzembe helyezési parancsfájlt.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>ASDK üzembe helyezés végén a PowerShell-munkamenet még meg nyitva, és nem jelenik meg a kimenetet.
Ez a viselkedés esetén valószínűleg csak egy PowerShell-parancsablakban alapértelmezett viselkedését eredményét lett kiválasztva. A development kit központi telepítés sikeres volt, de a parancsfájl az ablak kiválasztásakor szünetel. Ellenőrizheti a telepítés véget ért által keresett szót "Válassza" a parancsablakban címsorában. Kijelölésének, az ESC billentyű lenyomásával, és azt követően a befejezési üzenetet jelenjenek meg.

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Üzembe helyezés külső hozzáférés hiánya miatt meghiúsul.
Telepítése sikertelen szakaszában, ahol a külső hozzáférésre szükség, ha az alábbi példához hasonlóan kivételt fogja visszaadni:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Ha ez a hiba akkor fordul elő, ellenőrizze, hogy az összes minimális hálózati követelményeket áttekintésével a [üzembe helyezési hálózati forgalom dokumentáció](deployment-networking.md). Egy hálózat-ellenőrző eszköz érhető el a partnerek számára a Partner eszközkészlet részeként.

A fenti kivétel miatt sikertelen jellemzően az interneten megtalálható erőforrásokhoz való kapcsolódás problémái miatt.

Ez az a probléma ellenőrzéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Powershell
2. Enter-PSSession a WAS01 vagy a ERCs virtuális
3. Futtassa a parancsmagot: Test-NetConnection login.windows.net -port 443

Ha ez a parancs nem sikerül, ellenőrizze a TOR-kapcsoló, és bármely más hálózati eszközök vannak konfigurálva [hálózati forgalom](azure-stack-network.md).

## <a name="virtual-machines"></a>Virtuális gépek
### <a name="default-image-and-gallery-item"></a>Alapértelmezett kép és a katalógus elem
Virtuális gépek az Azure Stack üzembe helyezése előtt hozzá kell adni egy Windows Server rendszerképet és a katalógus elemet.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Az Azure Stack gazdagépen az újraindítás után néhány virtuális gép esetleg nem indul el automatikusan.
A gazdagép az újraindítást követően, Észreveheti, az Azure Stack-szolgáltatások nem érhetők el közvetlenül.  Ennek az az oka az Azure Stack [infrastruktúra virtuális gépein](../asdk/asdk-architecture.md#virtual-machine-roles ) és erőforrás-szolgáltatók konzisztenciájának ellenőrzése hosszabb időt is igénybe, de végül automatikusan elindul.

Előfordulhat, hogy a bérlői virtuális gépek nem indul el automatikusan az Azure Stack development kit gazdagép újraindítása után is. Ez egy ismert probléma, és csak az online állapotba helyezés néhány manuális lépéseket igényel:

1.  Indítsa el az Azure Stack development kit állomás **Feladatátvevőfürt-kezelőben** a Start menüből.
2.  Válassza ki a fürt **S-Cluster.azurestack.local**.
3.  Válassza ki **szerepkörök**.
4.  Bérlő virtuális gépek megjelennek egy *mentett* állapota. Ha minden infrastruktúra virtuális gépek futnak, kattintson a jobb gombbal a bérlői virtuális gépeket, és válassza ki **Start** folytatni a virtuális gép.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Tudom a törölt néhány virtuális gép, de továbbra is tekintse meg a VHD-fájlok a lemezen. Ez a viselkedés várható?
Igen, ez az elvárt működés. Így mivel készült:

* Ha töröl egy virtuális Gépet, a VHD-k nem törlődnek. A lemezei külön erőforrások az erőforráscsoportban.
* Lekérdezi egy storage-fiók törlésekor a törlés látható azonnal az Azure Resource Manageren keresztül, de a lemezek tartalmazhat továbbra is tartanak Storage szemétgyűjtés futtatásáig.

Ha a VHD-k "árva" című fontos tudni, hogy találhatók-e egy storage-fiók, amely törölve lett a mappában részei. Ha a tárfiók nem lett törölve, normál, azok továbbra is létezik.

Tudjon meg többet az adatmegőrzési küszöbérték és igény szerinti visszaigénylését konfigurálásával kapcsolatos [tárfiókok kezelését](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Tárolási visszaigénylését
Akár 14 óra regenerált kapacitás megjelennek a portálon vehet igénybe. Terület-visszaigénylést számos tényező befolyásolja, többek között a használat százalékos aránya a tároló belső fájlok block blob tárolóban függ. Ezért attól függően, hogy mennyi adat törlődik, nincs garancia arra, hogy a szemétgyűjtő futtatásakor kell visszaigényelt terület mennyisége a.

