---
title: Azure Stack hub VM-funkciók
description: Ismerkedjen meg a különböző funkciókkal és szempontokkal, amikor Azure Stack hub-beli virtuális gépekkel dolgozik.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 2fbdc058781b4aefbcf4a289e907bcbb4b63f301
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546990"
---
# <a name="azure-stack-hub-vm-features"></a>Azure Stack hub VM-funkciók

Azure Stack hub virtuális gépek (VM-EK) igény szerinti, méretezhető számítási erőforrásokat biztosítanak. A virtuális gépek üzembe helyezése előtt megismerheti a Azure Stack hub-ban és Microsoft Azure elérhető virtuálisgép-funkciók közötti különbségeket. Ez a cikk ismerteti ezeket a különbségeket, és azonosítja a virtuális gépek központi telepítésének megtervezésével kapcsolatos legfontosabb szempontokat. Az Azure Stack hub és az Azure közötti magas szintű különbségek megismeréséhez tekintse meg a [legfontosabb szempontokat](azure-stack-considerations.md) ismertető cikket.

## <a name="vm-differences"></a>VIRTUÁLIS gépek közötti különbségek

| Jellemző | Azure (globális) | Azure Stack Hub |
| --- | --- | --- |
| Virtuálisgép-rendszerképek | Az Azure Marketplace-en lemezképek hozhatók létre virtuális gépek létrehozásához. Az Azure Marketplace-en elérhető rendszerképek listájának megtekintéséhez tekintse meg az [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) oldalát. | Alapértelmezés szerint nincsenek elérhető lemezképek az Azure Stack hub piactéren. A Azure Stack hub-felhő rendszergazdájának közzé kell tennie vagy le kell töltenie a lemezképeket a Azure Stack hub piactéren, mielőtt a felhasználók használni tudják őket. |
| VHD-generáció | A 2. generációs virtuális gépek olyan kulcsfontosságú funkciókat támogatnak, amelyek az egy virtuális gép létrehozásakor nem támogatottak. A szolgáltatások közé tartozik a megnövekedett memória, az Intel Software Guard Extensions (Intel SGX ENKLÁVÉHOZ) és a virtualizált állandó memória (vPMEM). A 2. generációs virtuális gépek előállítása olyan szolgáltatásokkal rendelkezik, amelyek még nem támogatottak az Azure-ban. További információ: [2. generációs virtuális gépek támogatása az Azure](/azure/virtual-machines/windows/generation-2) -ban  | Azure Stack hub csak egy virtuális gép létrehozását támogatja. A VHDX-ből a VHD-fájlformátumba konvertálhat egy virtuális gépet, és dinamikusan bővült egy rögzített méretű lemezre. A virtuális gép generációja nem módosítható. További információ: 2. [generációs virtuális gépek támogatása az Azure](/azure/virtual-machines/windows/generation-2)-ban. |
| Virtuálisgép-méretek | Az Azure számos méretben támogatja a virtuális gépeket. Az elérhető méretekről és lehetőségekről az Azure-beli [virtuális gépek méreteit](/azure/virtual-machines/sizes)ismertető témakörben tájékozódhat. | Az Azure Stack hub az Azure-ban elérhető virtuálisgép-méretek egy részhalmazát támogatja. A támogatott méretek listájának megtekintéséhez tekintse meg a jelen cikk [VM-méretek](#vm-sizes) című szakaszát. |
| Virtuális gépek kvótái | A [kvóták korlátait](/azure/azure-resource-manager/management/azure-subscription-service-limits#managing-limits) a Microsoft állítja be. | A Azure Stack hub-felhő rendszergazdájának kvótákat kell rendelnie, mielőtt a virtuális gépet felkínálják a felhasználóknak. |
| Virtuálisgép-bővítmények |Az Azure a virtuálisgép-bővítmények széles választékát támogatja. Az elérhető bővítmények megismeréséhez tekintse meg a virtuálisgép [-bővítmények és-szolgáltatások](/azure/virtual-machines/windows/extensions-features) című cikket.| Az Azure Stack hub az Azure-ban elérhető bővítmények egy részhalmazát támogatja, és mindegyik bővítmény rendelkezik adott verzióval. A Azure Stack hub Cloud admin kiválaszthatja, hogy mely bővítmények legyenek elérhetők a felhasználók számára. A támogatott bővítmények listájának megtekintéséhez tekintse meg a jelen cikk virtuálisgép- [bővítmények](#vm-extensions) című szakaszát. |
| Virtuálisgép-hálózat | A bérlői virtuális géphez hozzárendelt nyilvános IP-címek elérhetők az interneten keresztül.<br><br><br>Az Azure-beli virtuális gépek rögzített DNS-névvel rendelkeznek. | A bérlői virtuális géphez hozzárendelt nyilvános IP-címek csak a Azure Stack Development Kit környezeten belül érhetők el. A felhasználónak [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) -vagy [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) -kapcsolattal kell rendelkeznie a Azure stack Development Kithoz az Azure stack hub-ban létrehozott virtuális géphez való csatlakozáshoz.<br><br>Az adott Azure Stack hub-példányon belül létrehozott virtuális gépek DNS-neve a felhőalapú rendszergazda által konfigurált érték alapján történik. |
| Virtuális gép tárterülete | Támogatja a [felügyelt lemezeket.](/azure/virtual-machines/windows/managed-disks-overview) | A felügyelt lemezeket Azure Stack hub 1808-es és újabb verziói támogatják. |
| A virtuális gép lemezének teljesítménye | A lemez típusától és méretétől függ. | A virtuális gép azon virtuálisgép-méretétől függ, amelyhez a lemezek csatolva vannak. További információkért tekintse meg az [Azure stack hub által támogatott virtuálisgép-méreteket](azure-stack-vm-sizes.md) .
| API-verziók | Az Azure mindig a virtuálisgép-funkciók legújabb API-verzióit tartalmazza. | Azure Stack hub a szolgáltatások adott Azure-szolgáltatásait és adott API-verzióit támogatja. A támogatott API-verziók listájának megtekintéséhez tekintse meg a jelen cikk [API-verziók](#api-versions) című szakaszát. |
| Azure Instance Metadata szolgáltatás | Az Azure Instance Metadata Service információt nyújt a virtuális gép felügyeletére és beállítására használható virtuálisgép-példányok futtatásáról.  | Az Azure Instance Metadata Service nem támogatott Azure Stack hub-on. |
| Virtuális gépek rendelkezésreállási csoportjai|Több tartalék tartomány (2 vagy 3 régiónként).<br>Több frissítési tartomány.|Több tartalék tartomány (2 vagy 3 régiónként).<br>Egyetlen frissítési tartomány, élő áttelepítéssel a munkaterhelések megóvása érdekében a frissítés során. 20 frissítési tartomány támogatott a sablonok kompatibilitásához.<br>A virtuális gép és a rendelkezésre állási csoportnak azonos helyen és erőforráscsoporthoz kell lennie.|
| Virtuálisgép-méretezési csoportok|Az autoskálázás támogatott.|Az autoskálázás nem támogatott.<br><br>További példányok hozzáadása egy méretezési csoporthoz a portál, a Resource Manager-sablonok vagy a PowerShell használatával. |
| Felhőbeli tanúsító | Válassza ki a Azure Stack központban elérhető Storage-fiók tulajdonságaiból származó végpontokat. | A [Felhőbeli tanúsító](/windows-server/failover-clustering/deploy-cloud-witness) a feladatátvevő fürt Kvórumának olyan típusa, amely Microsoft Azure használatával szavaz a fürt kvórumáról.<br>Az Azure Stack hub-hoz képest a globális Azure-beli végpontok a következőkhöz hasonlóak:<br>Globális Azure esetén:<br>`https://mywitness.blob.core.windows.net/`<br>Azure Stack hub esetében:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Virtuálisgép-diagnosztika | A Linux rendszerű virtuális gépek diagnosztikája támogatott. | A Linux rendszerű virtuális gépek diagnosztikája nem támogatott Azure Stack központban. Ha olyan linuxos virtuális gépet telepít, amelyen engedélyezve van a virtuálisgép-diagnosztika, a telepítés sikertelen lesz. A központi telepítés akkor is meghiúsul, ha a Linux virtuális gép alapmetrikáit a diagnosztikai beállítások segítségével engedélyezi. |
| Beágyazott virtualizációs VM-méretek | Támogatott | Nem támogatott |

## <a name="vm-sizes"></a>A virtuális gépek mérete

Az Azure Stack hub erőforrás-korlátokat szab, hogy elkerülje az erőforrások felhasználását (a kiszolgáló helyi és szolgáltatási szintjén). Ezek a korlátok javítják a bérlői élményt azáltal, hogy csökkentik a többi bérlő erőforrás-felhasználását.

- A virtuális gépről kifelé irányuló hálózati forgalomhoz rendelkezésre állnak a sávszélesség-sapkák. A Azure Stack hub-beli sapkák ugyanazok, mint az Azure-ban.
- A tárolási erőforrások esetében a Azure Stack hub a tárolási IOPS (bemeneti/kimeneti műveletek másodpercenkénti száma) alkalmazza, hogy elkerülje a bérlők által a tárolók általi alapszintű túlfogyasztást.
- A VM-lemezek esetében a Azure Stack hub IOPS a virtuális gép méretétől függ a lemez típusa helyett. Ez azt jelenti, hogy egy Standard_Fs sorozatú virtuális gép esetében, függetlenül attól, hogy az SSD-t vagy a HDD-t választja a lemez típusához, a második adatlemez IOPS-korlátja 2300 IOPS.
- A virtuális géphez csatolt ideiglenes lemezek nem állandóak, és elvesznek a vezérlési sík műveleteinél, például az átméretezés vagy a leállítás után.

A következő táblázat felsorolja az Azure Stack hub által támogatott virtuális gépeket, valamint azok konfigurációját:

| Típus            | Méret          | Támogatott méretek tartománya |
| ----------------| ------------- | ------------------------ |
|Általános célú  |Alapszintű A        |[A0 – A4](azure-stack-vm-sizes.md#basic-a)                   |
|Általános célú  |Standard A     |[A0 – A7](azure-stack-vm-sizes.md#standard-a)              |
|Általános célú  |Av2-sorozat     |[A1_v2 – A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|Általános célú  |D-sorozat       |[D1 – D4](azure-stack-vm-sizes.md#d-series)              |
|Általános célú  |Dv2-sorozat     |[D1_v2 – D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Általános célú  |DS-sorozat      |[DS1 – DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Általános célú  |DSv2-sorozat    |[DS1_v2 – DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Memóriaoptimalizált |D-sorozat       |[D11 – D14](azure-stack-vm-sizes.md#mo-d)            |
|Memóriaoptimalizált |DS-sorozat      |[DS11 – DS14](azure-stack-vm-sizes.md#mo-ds)|
|Memóriaoptimalizált |Dv2-sorozat     |[D11_v2 – DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Memóriaoptimalizált |DSv2-sorozat    |[DS11_v2 – DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|Számításra optimalizált|F-sorozat       |[F1 – F16](azure-stack-vm-sizes.md#f-series)    |
|Számításra optimalizált|FS sorozat      |[F1s – F16s](azure-stack-vm-sizes.md#fs-series)    |
|Számításra optimalizált|Fsv2 sorozat    |[F2s_v2 – F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |

A virtuálisgép-méretek és a hozzájuk tartozó erőforrás-mennyiségek konzisztensek Azure Stack hub és az Azure között. Ez a konzisztencia magában foglalja a memória mennyiségét, a magok számát, valamint a létrehozható adatlemezek számát/méretét. Az azonos méretű virtuális gépek teljesítménye azonban egy adott Azure Stack hub-környezet mögöttes jellemzőitől függ.

## <a name="vm-extensions"></a>Virtuálisgép-bővítmények

Azure Stack hub a bővítmények egy kis készletét tartalmazza. A frissítések és további bővítmények a Marketplace Syndication használatával érhetők el.

Használja az alábbi PowerShell-szkriptet az Azure Stack hub-környezetben elérhető virtuálisgép-bővítmények listájának lekéréséhez:

```powershell
Get-AzVmImagePublisher -Location local | `
  Get-AzVMExtensionImageType | `
  Get-AzVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Ha egy virtuális gép telepítésének kiépítése túl sokáig tart, hagyja meg a kiépítési időtúllépést ahelyett, hogy le kellene állítania a virtuális gép felszabadításának vagy törlésének folyamatát.

## <a name="api-versions"></a>API-verziók

Az Azure Stack hub virtuálisgép-szolgáltatásai a következő API-verziókat támogatják:

"2017-12-01", "2017-03-30", "2016-03-30", "2015-06-15"

Az alábbi PowerShell-szkripttel beolvashatja az Azure Stack hub-környezetben elérhető virtuálisgép-funkciók API-verzióit:

```powershell
Get-AzResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

A támogatott erőforrástípusok és API-verziók listája eltérő lehet, ha a Felhőbeli kezelő egy újabb verzióra frissíti az Azure Stack hub-környezetet.

## <a name="windows-activation"></a>A Windows aktiválása

A Windows-termékeket a termék használati jogainak és a Microsoft licencfeltételek feltételeinek megfelelően kell használni. Azure Stack hub a Windows Server rendszerű virtuális gépek aktiválásához [automatikus VM-aktiválást](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn303421(v%3dws.11)) (AVMA) használ.

- Azure Stack hub-gazdagép aktiválja a Windowst a Windows Server 2016 AVMA kulcsaival. A Windows Server 2012 R2 vagy újabb rendszert futtató virtuális gépek automatikusan aktiválódnak.
- A Windows Server 2012-es vagy korábbi verzióját futtató virtuális gépeket nem aktiválja automatikusan a rendszer, és a [MAK-aktiválás](/previous-versions/tn-archive/ff793438(v=technet.10))használatával kell aktiválni. A MAK-aktiválás használatához meg kell adnia a saját termékkulcsot.

Microsoft Azure KMS-aktiválást használ a Windows rendszerű virtuális gépek aktiválásához. Ha Azure Stack hubhoz helyez át egy virtuális gépet az Azure-ba, és az aktiválási problémákba ütközik, tekintse meg az [Azure Windows VM aktiválási problémáinak elhárítása](/azure/virtual-machines/windows/troubleshoot-activation-problems) További információ: [Windows-aktiválási hibák elhárítása Azure-beli virtuális gépeken](/archive/blogs/mast/troubleshooting-windows-activation-failures-on-azure-vms) az Azure támogatási csapatának blogbejegyzésében.

## <a name="high-availability"></a>Magas rendelkezésre állás

Előfordulhat, hogy a virtuális gép újraindítást igényel az Azure Stack hub-kezelő által ütemezett tervezett karbantartás miatt. Az Azure-beli több virtuális gépre kiterjedő üzemi rendszerek magas rendelkezésre állása érdekében a virtuális gépeket egy [rendelkezésre állási csoportba](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) helyezik, amely több tartalék tartományon és frissítési tartományon keresztül terjed ki. A Azure Stack hub kisebb méretében a rendelkezésre állási csoportokban lévő tartalék tartomány a méretezési egység egyetlen csomópontja.  

Habár a Azure Stack hub infrastruktúrája már rugalmas a hibákhoz, az alapul szolgáló technológia (feladatátvételi fürtszolgáltatás) továbbra is leállást okoz az érintett fizikai kiszolgálókon futó virtuális gépeknél, ha hardverhiba van. Azure Stack hub támogatja a rendelkezésre állási csoport legfeljebb három tartalék tartománnyal való egységességét az Azure-ban.

|                   |             |
|-------------------|-------------|
| **Tartalék tartományok** | A rendelkezésre állási csoportba helyezett virtuális gépeket fizikailag el kell különíteni egymástól a több tartalék tartomány (Azure Stack hub-csomópontok) lehető legegyenletesebb kiterjedésével. Hardverhiba esetén a sikertelen tartalék tartományba tartozó virtuális gépek más tartalék tartományokban lesznek újraindítva. A többi virtuális gépről külön tartalék tartományokban lesznek tárolva, de ha lehetséges, ugyanabban a rendelkezésre állási készletben. Ha a hardver online állapotba kerül, a virtuális gépek újra lesznek egyenlítve a magas rendelkezésre állás fenntartása érdekében. |
| **Frissítési tartományok**| A frissítési tartományok egy másik módja, hogy az Azure magas rendelkezésre állást biztosít a rendelkezésre állási csoportokban. A frissítési tartomány a mögöttes hardver logikai csoportja, amely egyszerre végezhető el a karbantartásban. Az ugyanabban a frissítési tartományban található virtuális gépek a tervezett karbantartás során újraindulnak. Mivel a bérlők virtuális gépeket hoznak létre egy rendelkezésre állási csoporton belül, az Azure platform automatikusan elosztja a virtuális gépeket ezen frissítési tartományok között. <br>Azure Stack központban a virtuális gépek a fürt többi online gazdagépén át lesznek telepítve, mielőtt a rendszer a mögöttes gazdagépet frissíti. Mivel a gazdagép frissítése során nincs szükség bérlői állásidőre, a Azure Stack hub frissítési tartomány funkciója csak az Azure-hoz készült sablon-kompatibilitáshoz létezik. A rendelkezésre állási csoportba tartozó virtuális gépek a frissítési tartományuk alapján 0 értéket fognak látni a portálon. |

## <a name="next-steps"></a>Következő lépések

[Windows rendszerű virtuális gép létrehozása a PowerShell-lel Azure Stack hub-ban](azure-stack-quick-create-vm-windows-powershell.md)
