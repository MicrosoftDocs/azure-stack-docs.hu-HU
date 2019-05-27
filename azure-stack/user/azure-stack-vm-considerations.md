---
title: Az Azure Stack-beli Virtuálisgép-funkciókat |} A Microsoft Docs
description: Különböző lehetőségek és szempontok ismerje meg az Azure Stack a virtuális gépek használatakor.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/19/2018
ms.openlocfilehash: 2a9a9a2c402538eee428bad08c9772b288fa1740
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197337"
---
# <a name="azure-stack-vm-features"></a>Az Azure Stack-beli Virtuálisgép-funkciókat

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack virtuális gépeken (VM) adja meg az igény szerinti, méretezhető számítási erőforrás. Mielőtt telepít virtuális gépeket, tisztában kell lennie a virtuális gép az Azure Stackben elérhető funkciókat és a Microsoft Azure közötti különbségeket. Ez a cikk ismerteti a különbségeket, és azonosítja a legfontosabb szempont a virtuális gépek üzembe helyezése tervezéséhez. Azure Stack és az Azure magas szintű különbségeit kapcsolatos további információkért tekintse meg a [szempontok kulcs](azure-stack-considerations.md) cikk.

## <a name="vm-differences"></a>Virtuális gép különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
| Virtuálisgép-lemezképek | Az Azure Marketplace-en lemezképeket, amelyek segítségével hozzon létre egy virtuális Gépet tartalmaz. Tekintse meg a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) oldal az Azure piactéren elérhető rendszerképek listájának megtekintéséhez. | Alapértelmezés szerint nincsenek képeket elérhető az Azure Stack piactéren. Az Azure Stack-felhő rendszergazda kell közzétenni vagy töltse le a rendszerképeket az Azure Stack piactéren felhasználók használatba vétel előtt. |
| Virtuálisgép-méretek | Az Azure-beli virtuális gépek számos különböző méretet támogatja. A rendelkezésre álló méretekben és lehetőségekkel kapcsolatos további információkért tekintse meg a [Windows virtuálisgép-méretek](/azure/virtual-machines/virtual-machines-windows-sizes) és [Linux Virtuálisgép-méretek](/azure/virtual-machines/linux/sizes) témaköröket. | Az Azure Stack az Azure-ban elérhető Virtuálisgép-méretek egy részét támogatja. Ha szeretné megtekinteni a támogatott méretek listáját, tekintse meg a [Virtuálisgép-méretek](#vm-sizes) című szakaszát. |
| Virtuális gép kvóták | [Magkvóta korlátozásának](/azure/azure-subscription-service-limits#service-specific-limits) Microsoft-beállításokat. | Az Azure Stack-felhő rendszergazda hozzá kell rendelnie kvóták, mielőtt azok a felhasználók a virtuális gép kínálnak. |
| Virtuálisgép-bővítmények |Az Azure számos különböző Virtuálisgép-bővítmények támogatja. Az elérhető bővítmények kapcsolatos további információkért tekintse meg a [Virtuálisgép-bővítmények és szolgáltatások](/azure/virtual-machines/windows/extensions-features) cikk.| Az Azure Stack az Azure-ban elérhető bővítmények egy részét támogatja, és minden kiterjesztést adott verziójával rendelkezik. Az Azure Stack-felhő rendszergazda választhat a kiterjesztések lehetővé kell tenni a felhasználók számára. A támogatott bővítmények listájának megtekintéséhez, tekintse meg a [Virtuálisgép-bővítmények](#vm-extensions) című szakaszát. |
| Virtuálisgép-hálózat | A bérlői virtuális Géphez rendelt nyilvános IP-címeket az interneten keresztül érhetők el.<br><br><br>Az Azure virtuális gépek rendelkeznek egy rögzített DNS-nevet. | Az Azure Stack Development Kit környezetben csak egy bérlői virtuális Géphez rendelt nyilvános IP-címek érhetők el. A felhasználó az Azure Stack Development Kit használatával hozzáféréssel kell rendelkeznie [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) vagy [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) egy az Azure Stackben létrehozott virtuális Géphez való csatlakozáshoz.<br><br>Egy adott Azure Stack-példánnyal belül létrehozott virtuális gépek rendelkezik egy DNS-nevet, a felhő a rendszergazda által konfigurált érték alapján |
| Virtuálisgép-tároló | Támogatja a [felügyelt lemezeket.](/azure/virtual-machines/windows/managed-disks-overview) | A felügyelt lemezek az Azure Stackben 1808 és újabb verziói támogatottak. |
| Virtuálisgép-lemezek teljesítménye | Függ a lemez típusát és méretét. | A virtuális gép, a lemezek vannak csatolva a virtuális gép méretétől függ. További információkért tekintse meg a [Virtuálisgép-méretek támogatott az Azure Stack](azure-stack-vm-sizes.md) cikk.
| API-verziók | Az Azure mindig a legújabb API-verzióit a Virtuálisgép-funkció rendelkezik. | Az Azure Stack konkrét Azure-szolgáltatás és az adott API-verziók támogatja ezeket a szolgáltatásokat. Ha szeretné megtekinteni a támogatott API-verziók listáját, tekintse meg a [API-verziók](#api-versions) című szakaszát. |
| Az Azure Instance Metadata szolgáltatás | Az Azure Instance Metadata szolgáltatás Virtuálisgép-példányok, amelyek segítségével kezelheti és konfigurálhatja a virtuális gép futtatásával kapcsolatos adatokat biztosít.  | Az Azure Instance Metadata szolgáltatás nem támogatott az Azure Stacken. |
| Virtuális gép rendelkezésre állási csoportok|Több tartalék tartomány (2 vagy 3 régiónként).<br>Több frissítési tartományt.|Több tartalék tartomány (2 vagy 3 régiónként).<br>Több frissítési tartományt (legfeljebb 20).|
| Virtuálisgép-méretezési csoportok|Az automatikus méretezés használata támogatott.|Az automatikus méretezés nem támogatott.<br><br>További példányok hozzáadása egy méretezési csoportot a portálon, a Resource Manager-sablonok vagy a PowerShell használatával. |
| Virtuálisgép-diagnosztika | Linux rendszerű virtuális gép diagnosztikai támogatottak. | Linux rendszerű virtuális gép diagnosztikai nem támogatottak az Azure Stackben. A Virtuálisgép-diagnosztika engedélyezve van egy Linux rendszerű virtuális Gépet helyez üzembe, a központi telepítés sikertelen lesz. Az üzembe helyezés akkor is sikertelen, ha engedélyezi a Linux rendszerű virtuális gép alapvető metrikák diagnosztikai beállításokon keresztül.

## <a name="vm-sizes"></a>A virtuális gépek mérete

Az Azure Stack közzétételével erőforráskorlátok elkerülése érdekében keresztül használatalapú erőforrások (helyi és a szolgáltatásiszint-kiszolgáló.) Ezek a korlátok a bérlői erőforrás-használat más bérlők által hatásának csökkentése révén javíthatja.

- A hálózati kimenő forgalom a virtuális gépről nincsenek sávszélesség caps helyen. Az Azure Stackben Caps ugyanazok, mint a caps az Azure-ban.
- A tárolási erőforrások az Azure Stack (bemeneti/kimeneti műveletek másodpercenként) IOPS-korlátok storage tárolóhoz a hozzáférés a bérlők által erőforrások alapszintű overconsumption elkerülése érdekében valósít meg.
- Virtuálisgép-lemezekhez a lemez iops-t az Azure Stacken, helyett a lemez típusát a virtuális gép méretének a függvénye. Ez azt jelenti, hogy Standard_Fs sorozat virtuális gép, függetlenül attól, hogy SSD vagy HDD a lemez típusát a további adatokat lemez IOPS-korlátját 2300 IOPS.

Az alábbi táblázat a virtuális gépek által támogatott az Azure Stacken és annak konfigurációját sorolja fel:

| Típus           | Méret          | Támogatott méretek tartományán |
| ---------------| ------------- | ------------------------ |
|Általános célú |Alapszintű A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Általános célú |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Általános célú |D-sorozat       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Általános célú |Dv2-sorozat     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Általános célú |DS-sorozat      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Általános célú |DSv2-sorozat    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Memóriahasználatra optimalizált|D-sorozat       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Memóriahasználatra optimalizált|DS-sorozat      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Memóriahasználatra optimalizált|Dv2-sorozat     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Memóriahasználatra optimalizált|DSv2 sorozat –  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Virtuálisgép-méretek és a társított erőforrás mennyiségek konzisztensek az Azure Stack és az Azure között. A konzisztencia magában foglalja a memória, a magok számát és a létrehozott adatlemezek száma és mérete. Az azonos méretű virtuális gépek teljesítményét azonban egy adott Azure Stack-környezet alapul szolgáló jellemzőit függ.

## <a name="vm-extensions"></a>Virtuálisgép-bővítmények

Az Azure Stack bővítmények egy kis készletét tartalmazza. Frissítések és a további kiterjesztések tartalomtípus-gyűjtési Marketplace-en keresztül érhető el.

A következő PowerShell-parancsfájl használatával érhetők el az Azure Stack környezettel Virtuálisgép-bővítmények listájának beolvasása:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Egy virtuális gép üzembe helyezése a bővítmény kiépítési túl hosszú időt vesz igénybe, ha lehetővé teszik az üzembe helyezési időtúllépés helyett fel, vagy törölje a virtuális Gépet, a folyamat leállítása érdekében.

## <a name="api-versions"></a>API-verziók

Virtuálisgép-funkció az Azure Stack a következő API-verziók támogatják:

![Virtuális gép erőforrástípusok](media/azure-stack-vm-considerations/vm-resoource-types.png)

A következő PowerShell-parancsfájl segítségével az API-verziók lekérése az Azure Stack-környezet által biztosított Virtuálisgép-funkciók:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

A támogatott erőforrástípusok és API-verziók listáját eltérőek lehetnek, ha a felhő üzemeltetője frissíti az Azure Stack-környezet újabb verzióra.

## <a name="windows-activation"></a>Windows-aktiválás

Windows termékek termékhasználati jogosultságoknak és a Microsoft licencfeltételeit összhangban kell felhasználni. Használja az Azure Stack [automatikus Virtuálisgép-aktiválás](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) Windows Serveres virtuális gépek aktiválása (AVMA).

- Az Azure Stack gazdagépen az AVMA kulcsokat Windows aktiválja a Windows Server 2016-hoz. Az összes virtuális gépek, amelyek a Windows Server 2012 R2 vagy újabb a rendszer automatikusan aktiválja.
- Futtassa a Windows Server 2012 vagy korábban nem automatikusan aktiválódik, és aktiválni kell a virtuális gépek [MAK-aktiválást](https://technet.microsoft.com/library/ff793438.aspx). A MAK-aktiválást használjon, meg kell adnia a saját termékkulcsot.

A Microsoft Azure Windows virtuális gépek aktiválása KMS-aktiválást használ. Ha egy virtuális Gépet az Azure Stack az Azure és az esetlegesen fellépő aktiválási problémák helyezi át, tekintse meg a [aktiválási problémák elhárítása Azure Windows virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). További információ található a [hibaelhárítása Windows-aktiválási hibák az Azure virtuális gépekhez](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) Azure támogatási csapatának blogbejegyzést.

## <a name="next-steps"></a>További lépések

[Egy Windows virtuális gép létrehozása a PowerShell-lel az Azure Stackben](azure-stack-quick-create-vm-windows-powershell.md)
