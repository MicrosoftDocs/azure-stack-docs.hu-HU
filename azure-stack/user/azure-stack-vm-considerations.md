---
title: Azure Stack virtuális gépek funkciói | Microsoft Docs
description: Ismerkedjen meg a különböző funkciókkal és szempontokkal a virtuális gépek Azure Stack-ben való használatakor.
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
ms.date: 10/09/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: ea117df9b763d33fee59b6a7f8f951b8d81cc6e7
ms.sourcegitcommit: 03f5932da0147f78e986dcc3da610db54f195bd7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72252491"
---
# <a name="azure-stack-vm-features"></a>Virtuálisgép-funkciók Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack virtuális gépek (VM-EK) igény szerinti, méretezhető számítási erőforrásokat biztosítanak. A virtuális gépek üzembe helyezése előtt megismerheti a Azure Stack és Microsoft Azure elérhető virtuálisgép-funkciók közötti különbségeket. Ez a cikk ismerteti ezeket a különbségeket, és azonosítja a virtuális gépek központi telepítésének megtervezésével kapcsolatos legfontosabb szempontokat. A Azure Stack és az Azure közötti magas szintű különbségek megismeréséhez tekintse meg a [legfontosabb szempontokat](azure-stack-considerations.md) ismertető cikket.

## <a name="vm-differences"></a>VIRTUÁLIS gépek közötti különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
| Virtuálisgép-lemezképek | Az Azure Marketplace-en lemezképek hozhatók létre virtuális gépek létrehozásához. Az Azure Marketplace-en elérhető rendszerképek listájának megtekintéséhez tekintse meg az [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) oldalát. | Alapértelmezés szerint nincs elérhető lemezkép a Azure Stack piactéren. A Azure Stack a felhő rendszergazdájának közzé kell tennie vagy le kell töltenie a lemezképeket a Azure Stack piactéren, mielőtt a felhasználók használni tudják őket. |
| Virtuálisgép-méretek | Az Azure számos méretben támogatja a virtuális gépeket. A rendelkezésre álló méretek és beállítások megismeréséhez tekintse meg a [Windows rendszerű virtuális gépek méreteit](/azure/virtual-machines/virtual-machines-windows-sizes) és a linuxos virtuálisgép- [méretek](/azure/virtual-machines/linux/sizes) témakört. | A Azure Stack az Azure-ban elérhető virtuálisgép-méretek egy részhalmazát támogatja. A támogatott méretek listájának megtekintéséhez tekintse meg a jelen cikk [VM-méretek](#vm-sizes) című szakaszát. |
| Virtuális gépek kvótái | A [kvóták korlátait](/azure/azure-subscription-service-limits#service-specific-limits) a Microsoft állítja be. | A Azure Stack-felhő rendszergazdájának kvótákat kell hozzárendelnie, mielőtt a virtuális gépet felkínálják a felhasználóknak. |
| Virtuálisgép-bővítmények |Az Azure a virtuálisgép-bővítmények széles választékát támogatja. Az elérhető bővítmények megismeréséhez tekintse meg a virtuálisgép [-bővítmények és-szolgáltatások](/azure/virtual-machines/windows/extensions-features) című cikket.| Azure Stack támogatja az Azure-ban elérhető bővítmények egy részhalmazát, és mindegyik bővítmény rendelkezik adott verzióval. A Azure Stack Cloud admin kiválaszthatja, hogy mely bővítményeket szeretné elérhetővé tenni a felhasználók számára. A támogatott bővítmények listájának megtekintéséhez tekintse meg a jelen cikk virtuálisgép- [bővítmények](#vm-extensions) című szakaszát. |
| Virtuálisgép-hálózat | A bérlői virtuális géphez hozzárendelt nyilvános IP-címek elérhetők az interneten keresztül.<br><br><br>Az Azure-beli virtuális gépek rögzített DNS-névvel rendelkeznek. | A bérlői virtuális géphez hozzárendelt nyilvános IP-címek csak a Azure Stack Development Kit környezeten belül érhetők el. A felhasználónak [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) -vagy [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) -kapcsolattal kell rendelkeznie a Azure stack Development Kithoz a Azure Stackban létrehozott virtuális géphez való csatlakozáshoz.<br><br>Az adott Azure Stack-példányon belül létrehozott virtuális gépek DNS-neve a felhőalapú rendszergazda által konfigurált érték alapján történik. |
| Virtuális gép tárterülete | Támogatja a [felügyelt lemezeket.](/azure/virtual-machines/windows/managed-disks-overview) | A felügyelt lemezek a 1808-es és újabb verziójú Azure Stack támogatottak. |
| A virtuális gép lemezének teljesítménye | A lemez típusától és méretétől függ. | A virtuális gép azon virtuálisgép-méretétől függ, amelyhez a lemezek csatolva vannak. További információkért tekintse meg Azure Stack cikkben [támogatott virtuálisgép-méreteket](azure-stack-vm-sizes.md) .
| API-verziók | Az Azure mindig a virtuálisgép-funkciók legújabb API-verzióit tartalmazza. | Azure Stack támogatja a szolgáltatások adott Azure-szolgáltatásait és adott API-verzióit. A támogatott API-verziók listájának megtekintéséhez tekintse meg a jelen cikk [API-verziók](#api-versions) című szakaszát. |
| Azure-Instance Metadata Service | Az Azure Instance Metadata Service információt nyújt a virtuális gép felügyeletére és beállítására használható virtuálisgép-példányok futtatásáról.  | Az Azure Instance Metadata Service Azure Stack nem támogatott. |
| Virtuális gépek rendelkezésre állási csoportjai|Több tartalék tartomány (2 vagy 3 régiónként).<br>Több frissítési tartomány.|Több tartalék tartomány (2 vagy 3 régiónként).<br>Egyetlen frissítési tartomány, élő áttelepítéssel a munkaterhelések megóvása érdekében a frissítés során. 20 frissítési tartomány támogatott a sablonok kompatibilitásához.<br>A virtuális gép és a rendelkezésre állási csoportnak azonos helyen és erőforráscsoporthoz kell lennie.|
| Virtual Machine Scale Sets|Az autoskálázás támogatott.|Az autoskálázás nem támogatott.<br><br>További példányok hozzáadása egy méretezési csoporthoz a portál, a Resource Manager-sablonok vagy a PowerShell használatával. |
| Felhőbeli tanúsító | Válassza ki a Azure Stackban elérhető Storage-fiók tulajdonságaiból származó végpontokat. | A [Felhőbeli tanúsító](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) a feladatátvevő fürt Kvórumának olyan típusa, amely Microsoft Azure használatával szavaz a fürt kvórumáról.<br>A globális Azure-beli végpontok a Azure Stackhoz képest a következőkhöz hasonlóak:<br>Globális Azure esetén:<br>`https://mywitness.blob.core.windows.net/`<br>Azure Stack esetén:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Virtuálisgép-diagnosztika | A Linux rendszerű virtuális gépek diagnosztikája támogatott. | A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stackban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi. |

## <a name="vm-sizes"></a>Virtuálisgép-méretek

A Azure Stack erőforrás-korlátozásokat határoz meg, hogy elkerülje az erőforrások felhasználását (a kiszolgáló helyi és szolgáltatási szintjén). Ezek a korlátok javítják a bérlői élményt azáltal, hogy csökkentik a többi bérlő erőforrás-felhasználását.

- A virtuális gépről kifelé irányuló hálózati forgalomhoz rendelkezésre állnak a sávszélesség-sapkák. A Azure Stackban található sapkák megegyeznek az Azure-beli sapkákkal.
- A tárolási erőforrások esetében a Azure Stack a tárolási IOPS (bemeneti/kimeneti műveletek másodpercenkénti számát) implementálja, hogy elkerülje a bérlők által a tárolók általi alapszintű túlfogyasztást.
- A VM-lemezek esetében a Azure Stack lemez IOPS a virtuálisgép-méret függvénye a lemez típusa helyett. Ez azt jelenti, hogy egy Standard_Fs sorozatú virtuális gép esetében, függetlenül attól, hogy az SSD-t vagy a HDD-t választja a lemez típusához, a második adatlemez IOPS-korlátja 2300 IOPS.

A következő táblázat felsorolja a Azure Stack által támogatott virtuális gépeket, valamint azok konfigurációját:

| Type (Típus)            | Méret          | Támogatott méretek tartománya |
| ----------------| ------------- | ------------------------ |
|Általános rendeltetésű  |Alapszintű A        |[A0 – A4](azure-stack-vm-sizes.md#basic-a)                   |
|Általános rendeltetésű  |Standard A     |[A0 – A7](azure-stack-vm-sizes.md#standard-a)              |
|Általános rendeltetésű  |Av2-sorozat     |[A1_v2 - A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|Általános rendeltetésű  |D sorozat       |[D1 – D4](azure-stack-vm-sizes.md#d-series)              |
|Általános rendeltetésű  |Dv2 sorozat     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Általános rendeltetésű  |DS-sorozat      |[DS1 – DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Általános rendeltetésű  |DSv2-sorozat    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Memóriára optimalizált |D sorozat       |[D11 – D14](azure-stack-vm-sizes.md#mo-d)            |
|Memóriára optimalizált |DS-sorozat      |[DS11 – DS14](azure-stack-vm-sizes.md#mo-ds)|
|Memóriára optimalizált |Dv2 sorozat     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Memóriára optimalizált |DSv2-sorozat    |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|Compute-optimalizált|F sorozat       |[F1 – F16](azure-stack-vm-sizes.md#f-series)    |
|Compute-optimalizált|Fs sorozat      |[F1s – F16s](azure-stack-vm-sizes.md#fs-series)    |
|Compute-optimalizált|Fsv2 sorozat    |[F2s_v2 - F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |

A virtuálisgép-méretek és a hozzájuk kapcsolódó erőforrás-mennyiségek konzisztensek Azure Stack és az Azure között. Ez a konzisztencia magában foglalja a memória mennyiségét, a magok számát, valamint a létrehozható adatlemezek számát/méretét. Az azonos méretű virtuális gépek teljesítménye azonban egy adott Azure Stack-környezet mögöttes jellemzőitől függ.

## <a name="vm-extensions"></a>Virtuálisgép-bővítmények

Azure Stack a bővítmények egy kis készletét tartalmazza. A frissítések és további bővítmények a Marketplace Syndication használatával érhetők el.

Használja a következő PowerShell-szkriptet a Azure Stack-környezetben elérhető virtuálisgép-bővítmények listájának lekéréséhez:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Ha egy virtuális gép telepítésének kiépítése túl sokáig tart, hagyja meg a kiépítési időtúllépést ahelyett, hogy le kellene állítania a virtuális gép felszabadításának vagy törlésének folyamatát.

## <a name="api-versions"></a>API-verziók

A Azure Stack virtuális gépekkel kapcsolatos funkciói a következő API-verziókat támogatják:

![VM-erőforrástípusok](media/azure-stack-vm-considerations/vm-resoource-types.png)

A következő PowerShell-szkripttel kérheti le a Azure Stack-környezetben elérhető virtuálisgép-funkciók API-verzióit:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

A támogatott erőforrástípusok és API-verziók listája eltérő lehet, ha a Felhőbeli kezelő egy újabb verzióra frissíti a Azure Stack környezetet.

## <a name="windows-activation"></a>Windows-aktiválás

A Windows-termékeket a termék használati jogainak és a Microsoft licencfeltételek feltételeinek megfelelően kell használni. A Azure Stack a [virtuális gép aktiválását](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) használja a Windows Server rendszerű virtuális gépek aktiválásához.

- Azure Stack-gazdagép aktiválja a Windowst a Windows Server 2016 AVMA kulcsaival. A Windows Server 2012 R2 vagy újabb rendszert futtató virtuális gépek automatikusan aktiválódnak.
- A Windows Server 2012-es vagy korábbi verzióját futtató virtuális gépeket nem aktiválja automatikusan a rendszer, és a [MAK-aktiválás](https://technet.microsoft.com/library/ff793438.aspx)használatával kell aktiválni. A MAK-aktiválás használatához meg kell adnia a saját termékkulcsot.

Microsoft Azure KMS-aktiválást használ a Windows rendszerű virtuális gépek aktiválásához. Ha Azure Stackról az Azure-ba helyez át egy virtuális gépet, és az aktiválási problémákba ütközik, tekintse meg az [Azure Windows virtuális gépek aktiválásával kapcsolatos problémák](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems) További információ: [Windows-aktiválási hibák elhárítása Azure-beli virtuális gépeken](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) az Azure támogatási csapatának blogbejegyzésében.

## <a name="next-steps"></a>Következő lépések

[Windows rendszerű virtuális gép létrehozása a PowerShell-lel Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
