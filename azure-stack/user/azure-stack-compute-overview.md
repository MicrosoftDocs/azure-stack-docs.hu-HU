---
title: Azure Stack hub virtuális gépek bemutatása
description: Ismerkedjen meg Azure Stack hub virtuális gépekkel.
author: sethmanheim
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2020
ms.openlocfilehash: ff462a8fbdb2de67e58f15248aaffa79f0abb695
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545762"
---
# <a name="introduction-to-azure-stack-hub-vms"></a>Azure Stack hub virtuális gépek bemutatása

Az Azure Stack hub a virtuális gépeket igény szerinti és méretezhető számítási erőforrásként kínálja. Kiválaszthat egy virtuális gépet, ha több vezérlésre van szüksége a számítástechnikai környezetben. Ez a cikk részletesen ismerteti az első virtuális gép létrehozását.

Egy Azure Stack hub virtuális gép rugalmasságot biztosít a virtualizálás számára, anélkül, hogy a fürtöket vagy az egyes gépeket kellene kezelnie. Azonban továbbra is fenn kell tartania a virtuális gépet olyan feladatok elvégzésével, mint például a konfigurálás, a javítás/frissítés és a rajta futó szoftver telepítése.

Azure Stack hub virtuális gépek többféleképpen is használhatók. Ilyenek többek között:

- **Fejlesztés és tesztelés** : Azure stack hub virtuális gépek lehetővé teszik, hogy olyan számítógépet hozzon létre, amely az alkalmazások kódolásához és teszteléséhez szükséges adott konfigurációval rendelkezik.

- **Felhőbeli alkalmazások** : mivel az alkalmazás iránti igény ingadozhat, a gazdasági szempontok alapján a Azure stack hub-beli virtuális gépen is futtathatja azt. Ha szüksége van rájuk, további virtuális gépekért kell fizetnie, és nem kell leállítania.

- **Kiterjesztett adatközpont** : az Azure stack hub virtuális hálózatban lévő virtuális gépek csatlakoztathatók a szervezet hálózatához vagy az Azure-hoz.

Az alkalmazás által használt virtuális gépek akár vertikális felskálázást vagy vertikális felskálázást is igénybe vehetnek, hogy megfeleljenek az igényeinek.

## <a name="before-creating-a-vm"></a>Virtuális gép létrehozása előtt

A Azure Stack hub alkalmazás-infrastruktúrájának kiépítéséhez mindig kialakítási szempontokat kell figyelembe venni. A virtuális gép ezen szempontjait érdemes meggondolni, mielőtt megkezdené az infrastruktúra létrehozását:

- Az alkalmazás erőforrásainak nevei.
- A virtuális gép mérete.
- A létrehozható virtuális gépek maximális száma.
- A virtuális gép által futtatott operációs rendszer.
- A virtuális gép konfigurálása az elindítása után.
- A virtuális gép által igényelt kapcsolódó erőforrások.

### <a name="names"></a>Nevek

A virtuális gép neve hozzá van rendelve, és az operációs rendszer részeként konfigurált számítógépnévvel rendelkezik. A virtuális gépek neve legfeljebb 15 karakter hosszú lehet.

Ha Azure Stack hub-t használ az operációsrendszer-lemez létrehozásához, akkor a számítógépnév és a virtuális gép neve azonos. Ha olyan saját rendszerképet tölt fel és használ, amely egy korábban konfigurált operációs rendszert tartalmaz, és egy virtuális gép létrehozásához használja, akkor a nevek eltérőek lehetnek. Ha feltölt egy saját lemezképfájlt, ajánlott eljárásként győződjön meg arról, hogy az operációs rendszer számítógépneve megegyezik a virtuális gép nevével.

### <a name="vm-size"></a>Virtuális gép mérete

A használt virtuális gép mérete a futtatni kívánt számítási feladatok mennyiségétől függ. A választott méret határoz meg olyan tényezőket, mint a feldolgozókapacitás, a memória és a tárolókapacitás. Azure Stack hub különböző típusú méreteket kínál számos felhasználási típus támogatásához.

### <a name="vm-limits"></a>VIRTUÁLIS gépek korlátai

Az előfizetése alapértelmezett kvóta-korlátozásokkal rendelkezik, amelyek hatással lehetnek a projekthez tartozó virtuális gépek üzembe helyezésére. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép.

### <a name="operating-system-disks-and-images"></a>Operációsrendszer-lemezek és -rendszerképek

Azure Stack hub-beli virtuális gépek az 1. generációs virtuális merevlemez (VHD/VHDX) formátumra korlátozódnak. A virtuális merevlemezek a számítógép operációs rendszerének (operációs rendszere) és az adattárolók tárolására használhatók. A virtuális merevlemezek az operációs rendszer telepítéséhez használt rendszerképekhez is használhatók. Az Azure Stack hub számos különböző verzióval és típussal használható piactért biztosít. A piactér lemezképeit a rendszerkép-közzétevő, az ajánlat, az SKU és a verzió azonosítja (általában a legújabb verzió a **legújabbként** van megadva).

A következő táblázat a rendszerkép információinak megkeresését mutatja be:

|Metódus|Leírás|
|---------|---------|
|Azure Stack hub-portál|Az értékek a rendszerképek kiválasztásakor automatikusan megjelennek.|
|Azure Stack hub PowerShell|`Get-AzVMImagePublisher -Location "location"`<br>`Get-AzVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API-k     |[Rendszerkép-közzétevők listázása](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Rendszerkép-ajánlatok listázása](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Képsku-képek listázása](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Megadhatja a saját rendszerképének feltöltését és használatát. Ha így tesz, a közzétevő neve, az ajánlat és az SKU nem használatos.

### <a name="extensions"></a>Bővítmények

A virtuálisgép-bővítmények a telepítés utáni konfiguráció és az automatizált feladatok révén további képességeket biztosítanak a virtuális gépek számára. Ezeket a gyakori feladatokat a bővítmények segítségével hajthatja végre:

- **Egyéni parancsfájlok futtatása** : az egyéni szkriptek bővítménye lehetővé teszi a számítási feladatok konfigurálását a virtuális gépen úgy, hogy a virtuális gép üzembe helyezésekor futtatja a parancsfájlt.

- **Konfigurációk üzembe helyezése és kezelése** : a PowerShell desired State CONFIGURATION (DSC) bővítménye segítségével beállíthatja a DSC-t egy virtuális gépen a konfigurációk és környezetek kezeléséhez.

- **Diagnosztikai adatok gyűjtése** : a Azure Diagnostics bővítmény segít a virtuális gép konfigurálásában az alkalmazás állapotának figyelésére használható diagnosztikai adatok gyűjtéséhez.

### <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

A virtuális gép a következő táblázatban található erőforrásokat használja, és a virtuális gép létrehozásakor szükséges vagy létre kell hozni:

|Erőforrás|Kötelező|Leírás|
|---------|---------|---------|
|Erőforráscsoport|Yes|A virtuális gépnek egy erőforráscsoport tagjának kell lennie.|
|Tárfiók|No|A virtuális gépnek felügyelt lemezek használata esetén nincs szükség a Storage-fiókra a virtuális merevlemezek tárolásához. |
|Virtuális hálózat|Yes|A virtuális gépnek egy virtuális hálózat tagjának kell lennie.|
|Nyilvános IP-cím|No|A virtuális gép rendelkezhet hozzárendelt nyilvános IP-címmel, hogy távolról is el lehessen érni.|
|Hálózati adapter|Yes|A virtuális gépnek szüksége van a hálózati illesztőre a hálózattal való kommunikációhoz.|
|Adatlemezek|No|A virtuális gépek a tárolókapacitást bővítő adatlemezeket is tartalmazhatnak.|

## <a name="create-your-first-vm"></a>Az első virtuális gép létrehozása

A virtuális gépek több módon is létrehozhatók. Az Ön által választott környezettől függ. A következő táblázat a virtuális gép létrehozásának megkezdéséhez nyújt segítséget:

|Metódus|Cikk|
|---------|---------|
|Azure Stack hub-portál|[Hozzon létre egy Windows rendszerű virtuális gépet az Azure stack hub portálon](azure-stack-quick-windows-portal.md).<br>[Hozzon létre egy linuxos virtuális gépet az Azure stack hub portál használatával](azure-stack-quick-linux-portal.md).|
|Sablonok|Azure Stack hub gyors üzembe helyezési sablonjai a következő helyen találhatók:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://aka.ms/aa6z60s)|
|PowerShell|[Windows rendszerű virtuális gép létrehozása a PowerShell használatával Azure Stack hub-ban](azure-stack-quick-create-vm-windows-powershell.md)<br>[Linuxos virtuális gép létrehozása a PowerShell használatával Azure Stack hub-ban](azure-stack-quick-create-vm-linux-powershell.md)|
|parancssori felület|[Windows rendszerű virtuális gép létrehozása a parancssori felület használatával Azure Stack hub-ban](azure-stack-quick-create-vm-windows-cli.md)<br>[Linuxos virtuális gép létrehozása a parancssori felület használatával Azure Stack hub-ban](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>A virtuális gép felügyelete

A virtuális gépeket böngészőalapú portál, parancsfájlok futtatását támogató parancssori eszközök, vagy közvetlenül az API-k segítségével kezelheti. Néhány tipikus felügyeleti feladat:

- Virtuális gép adatainak beolvasása.
- Csatlakozás virtuális géphez.
- A rendelkezésre állás kezelése.
- Biztonsági mentések készítése.

### <a name="get-information-about-your-vm"></a>A virtuális gép adatainak lekérése

A következő táblázat néhány módszert mutat be a virtuális gépekkel kapcsolatos információk lekéréséhez:

|Metódus|Leírás|
|---------|---------|
|Azure Stack hub-portál|A központi menüben kattintson a **Virtual Machines** elemre, majd válassza ki a virtuális gépet a listából. A virtuális gép lapján elérhető az áttekintési információk, az értékek beállítása és a figyelési mérőszámok.|
|Azure PowerShell|A virtuális gépek kezelése az Azure-ban és Azure Stack hub-ban is hasonló. A PowerShell használatával kapcsolatos további információkért tekintse meg a [Windows rendszerű virtuális gépek létrehozása és kezelése a Azure PowerShell modullal](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)című témakört.|
|Ügyfél-SDK-k|A C# használatával a virtuális gépek kezelése hasonló az Azure-ban és Azure Stack hub-ban. További információkért lásd: [Windows rendszerű virtuális gépek létrehozása és kezelése az Azure-ban a C# használatával](/azure/virtual-machines/windows/csharp).|

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

A virtuális géphez való kapcsolódáshoz használhatja a Azure Stack hub portál **kapcsolódási** lehetőségét.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Stack hub-beli virtuális gépekkel kapcsolatos megfontolások](azure-stack-vm-considerations.md)
