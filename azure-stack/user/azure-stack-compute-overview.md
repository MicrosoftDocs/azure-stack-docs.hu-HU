---
title: Bevezetés az Azure Stack-alapú virtuális gépekbe
description: Azure Stack virtual machines bemutatása
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 33a7522994c23e20dddf587c2374c24e664e3171
ms.sourcegitcommit: d2012e765c3fa5bccb4756d190349e890f9f48bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65941152"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Bevezetés az Azure Stack-alapú virtuális gépekbe

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack egy igény szerinti, méretezhető számítási erőforrás egy típusú virtuális gépeken (VM) kínál. Amikor, a számítási környezet átfogóbb vezérlésére van szüksége egy virtuális Gépet választhat. Ez a cikk részletesen az első virtuális gép létrehozása előtt.

Azure Stack virtuális gép rugalmasságot biztosít a virtualization fürtök vagy az egyes gépek kezelése nélkül. Azonban akkor is szükséges műveleteket, mint a konfigurálását, javítását és a rajta futó szoftver telepítése a virtuális gép karbantartásához.

Azure Stack virtuális gépek többféleképpen is használhatja. Példa:

- **Fejlesztési és tesztelési**: Az Azure Stack-beli virtuális gépek lehetővé teszik a szükséges kódot adott konfigurációval rendelkező számítógép létrehozása és a egy alkalmazás teszteléséhez.

- **A felhőbeli alkalmazások**: Mivel az alkalmazások iránti igény ingadozhaz, célszerű gazdasági virtuális gépen az Azure Stack futtatásához. Amikor szükség van rájuk, és állítsa azokat le, ha ezt nem fizet további virtuális gépeket.

- **Bővített adatközpont**: Egy Azure Stack virtuális hálózatban lévő virtuális gépek is csatlakoztathatók, a vállalati hálózaton vagy az Azure-bA.

A virtuális gépek, a kérelem által használt vertikális és horizontális felskálázás, bármi szükség az igényeinek.

## <a name="before-creating-a-vm"></a>A virtuális gép létrehozása előtt

Nincsenek mindig kialakításával kapcsolatos szempontok alkalmazás-infrastruktúrák, az Azure Stackben készítése során. Virtuális gépek következő tulajdonságait fontos átgondolni, az infrastruktúra létrehozásának megkezdése előtt:

- Az alkalmazás erőforrásainak nevei.
- A virtuális gép méretét.
- A létrehozható virtuális gépek maximális számát.
- A virtuális Gépen futó operációs rendszert.
- A konfiguráció a virtuális gép elindítása után.
- A kapcsolódó erőforrásokat, a virtuális gépet.

### <a name="naming"></a>Elnevezése

Egy virtuális gép rendelkezik egy névvel, és a számítógép nevét, az operációs rendszer részeként konfigurálva van. A virtuális gépek neve legfeljebb 15 karakter hosszú lehet.

Azure Stack használatával az operációsrendszer-lemez létrehozása, ha a számítógép nevét és a virtuális gép neve azonosak. Ha feltöltése és a saját egy korábban konfigurált operációs rendszert tartalmazó lemezképek használata, és ezzel hozzon létre egy virtuális gépet, a neveket eltérő lehet. Amikor feltölti a saját képfájlt, ajánlott eljárásként, győződjön meg arról, a számítógép nevét az operációs rendszer és a virtuális gép neve azonos.

### <a name="vm-size"></a>Virtuális gép mérete

A használt virtuális gép méretét a számítási feladat, amelyet futtatni szeretne határozza meg. A választott méret határoz meg olyan tényezőket, mint a feldolgozókapacitás, a memória és a tárolókapacitás. Az Azure Stack számos különböző méretet különféle felhasználási módokhoz.

### <a name="vm-limits"></a>A virtuális gépekre korlátai

Az előfizetés rendelkezik alapértelmezett kvótakorlátozások vonatkoznak, amelyek hatással lehetnek a projektjéhez tartozó virtuális gépek telepítését. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép.

### <a name="operating-system-disks-and-images"></a>Operációsrendszer-lemezek és -rendszerképek

A virtuális gépek virtuális merevlemezeken (VHD-k) tárolják az operációs rendszereket és az adatokat. A VHD-ken találhatók az operációs rendszer telepítéséhez kiválasztható rendszerképek is. Az Azure Stack biztosít különböző verzióit és típusait az operációs rendszerek használata a piactéren. Marketplace-rendszerképek azonosítják a rendszerkép közzétevője, ajánlat, Termékváltozat és verzió (általában a verzió van megadva **legújabb**).

Az alábbi táblázat a rendszerképek információinak megkeresése:

|Módszer|Leírás|
|---------|---------|
|Az Azure Stack portálon|Az értékek a rendszerképek kiválasztásakor automatikusan megjelennek.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API-k     |[Rendszerkép-közzétevők listázása](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Rendszerkép-ajánlatok listázása](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Kép termékváltozatok listázása](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Kiválaszthatja, hogy fel és saját rendszerképét használja. Ha így tesz, a közzétevő neve, az ajánlat és a Termékváltozat nem használ.

### <a name="extensions"></a>Kiterjesztések

A Virtuálisgép-bővítmények lehetővé teszik a virtuális gép további képességeket üzembe helyezést követő konfigurálás és automatizált feladatok útján.
A bővítményekkel a következő gyakori feladatok végezhetők el:

- **Egyéni szkriptek futtatása**: Az egyéni szkriptbővítmény segítségével a virtuális gép számítási feladatainak konfigurálásában a parancsfájl futtatásával, ha a virtuális gép ki van építve.

- **Üzembe helyezés és kezelés konfigurációk**: A PowerShell Desired State Configuration (DSC) bővítmény segítségével állítsa be a virtuális gép DSC konfigurációk és környezetek kezeléséhez.

- **Diagnosztikai adatok gyűjtésére**: Az Azure Diagnostics bővítmény segítségével konfigurálhatja az alkalmazás állapotának figyelésére használható diagnosztikai adatok gyűjtésére a virtuális Gépet.

### <a name="related-resources"></a>Kapcsolódó erőforrások

Az erőforrások az alábbi táblázat a virtuális gép által használt, és létezik, vagy hozható létre, ha a virtuális gép létrehozása:

|Resource|Szükséges|Leírás|
|---------|---------|---------|
|Erőforráscsoport|Igen|A virtuális gépnek egy erőforráscsoport tagjának kell lennie.|
|Tárfiók|Nem|A virtuális Gépet nem szükséges a tárfiók tárolja a virtuális merevlemezek, ha a felügyelt lemezeket használ. <br>A virtuális Gépet kell a tárfiók a virtuális merevlemezek tárolásához, ha nem felügyelt lemezek használatával.|
|Virtuális hálózat|Igen|A virtuális gépnek egy virtuális hálózat tagjának kell lennie.|
|Nyilvános IP-cím|Nem|A virtuális gép rendelkezhet hozzárendelt nyilvános IP-címmel, hogy távolról is el lehessen érni.|
|Hálózati adapter|Igen|A virtuális gépnek szüksége van a hálózati illesztőre a hálózattal való kommunikációhoz.|
|Adatlemezek|Nem|A virtuális gépek a tárolókapacitást bővítő adatlemezeket is tartalmazhatnak.|

## <a name="create-your-first-vm"></a>Az első virtuális gép létrehozása

Lehetősége van több virtuális gép létrehozásához. A választott környezettől függ. A következő táblázat információkat nyújtanak segítséget a virtuális gép létrehozásának lépései:

|Módszer|Cikk|
|---------|---------|
|Az Azure Stack portálon|Egy Windows virtuális gép létrehozása az Azure Stack portálon<br>[Az Azure Stack portal használata Linux rendszerű virtuális gép létrehozása](azure-stack-quick-linux-portal.md)|
|Sablonok|Az Azure Stack-gyorsindítási sablonok a következő helyen találhatók:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Windows virtuális gép létrehozása az Azure Stack PowerShell használatával](azure-stack-quick-create-vm-windows-powershell.md)<br>[Linux rendszerű virtuális gép létrehozása az Azure Stack PowerShell használatával](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Windows virtuális gép létrehozása az Azure Stack parancssori felület használatával](azure-stack-quick-create-vm-windows-cli.md)<br>[Linux rendszerű virtuális gép létrehozása az Azure Stack parancssori felület használatával](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>A virtuális gép kezelése

Virtuális gépek egy böngészőalapú portállal, parancsfájlkezelést, vagy közvetlenül az API-kon keresztül támogató parancssori eszközökkel használata kezelheti. Néhány tipikus felügyeleti feladatokhoz, előfordulhat, hogy végre a következők:

- Virtuális gép adatainak beolvasása
- Csatlakozás virtuális géphez
- Rendelkezésre állás kezelése
- Biztonsági mentések készítése

### <a name="get-information-about-your-vm"></a>A virtuális gép adatainak lekérése

Az alábbi táblázat bemutatja, egy virtuális gép információkat szerezhet a több szempontból.

|Módszer|Leírás|
|---------|---------|
|Az Azure Stack portálon|A központi menüben kattintson a **Virtual Machines** elemre, majd válassza ki a virtuális gépet a listából. A lapon a virtuális gép elérhető lesz az információk áttekintését, értékek beállításait és a mérőszámok figyelését.|
|Azure PowerShell|Virtuális gépek kezelése a hasonló az Azure és az Azure Stackben. PowerShell-lel kapcsolatos további információkért lásd a következő Azure témakör:<br>[Hozzon létre és kezelheti a Windows virtuális gépek az Azure PowerShell modullal](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Ügyfél-SDK-k|A virtuális gépek kezelése a C# használatával hasonlít az Azure és az Azure Stackben. További információkért tekintse meg a következő Azure témakör:<br>[Létrehozása és kezelése Windows virtuális gépek az Azure-ban C# használatával](/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

Használhatja a **Connect** gomb az Azure Stack portálon a virtuális Géphez való csatlakozáshoz.

## <a name="next-steps"></a>További lépések

- [Szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md)
