---
title: Windows rendszerű virtuális gép futtatása Azure Stackon | Microsoft Docs
description: Megtudhatja, hogyan futtathat Windows rendszerű virtuális gépeket Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 5f9d8de7c08e8cfa0ad2af9bcb8f898fc32848a3
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74690220"
---
# <a name="run-a-windows-virtual-machine-on-azure-stack"></a>Windows rendszerű virtuális gép futtatása Azure Stack

A virtuális gép (VM) Azure Stackban való kiépítéséhez szükség van néhány további összetevőre, például a hálózati és a tárolási erőforrásokra. Ez a cikk a Windows rendszerű virtuális gépek Azure-beli futtatásának ajánlott eljárásait ismerteti.

![Windows rendszerű virtuális gép architektúrája Azure Stack](./media/iaas-architecture-vm-windows/image1.png)

## <a name="resource-group"></a>Erőforráscsoport

Az [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) olyan logikai tároló, amely a kapcsolódó Azure stack erőforrásokat tárolja. Általánosságban elmondható, hogy az erőforrások élettartamuk alapján csoportosítják az erőforrásokat, és ki fogják kezelni őket.

Olyan szorosan társított erőforrásokat helyezzen el, amelyek ugyanazt az életciklust használják ugyanabba az [erőforráscsoporthoz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Az erőforráscsoportok segítségével csoportosan helyezhet üzembe és monitorozhat erőforrásokat, és a számlázási költségeket erőforráscsoportonként követheti. Az erőforrásokat készletként is törölheti, ami hasznos lehet a tesztelési környezetekben való üzembe helyezéshez. Jelentéssel bíró erőforrásneveket adjon meg, hogy egyszerűbben megkereshesse és azonosíthassa az egyes erőforrásokat és azok szerepkörét. További információ: [Az Azure-erőforrások ajánlott elnevezési konvenciói](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Virtuális gép

Kiépítheti a virtuális gépet a közzétett lemezképek listájából, vagy egy, az Azure Stack blob Storage-ba feltöltött egyéni felügyelt lemezképből vagy virtuális merevlemezből (VHD-fájlból).

A Azure Stack az Azure-tól eltérő virtuálisgép-méreteket biztosít. További információ: [Azure stack virtuális gépek méretei](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes). Ha Azure Stackra helyez át egy meglévő munkaterhelést, először a helyi kiszolgálókhoz/Azure-hoz legközelebb lévő virtuálisgép-mérettel kell kezdődnie. Ezután mérjük meg a tényleges számítási feladatok teljesítményét a processzor, a memória és a lemezes bemeneti/kimeneti műveletek (IOPS) alapján, és szükség szerint állítsa be a méretet.

## <a name="disks"></a>Lemezek

A költség az üzembe helyezett lemez kapacitásától függően változik. A IOPS és az átviteli sebesség (vagyis az adatátviteli sebesség) a virtuális gép méretétől függ, így a lemez kiépítésekor mindhárom tényezőt (kapacitás, IOPS és átviteli sebesség) figyelembe kell venni.

A lemez IOPS (bemeneti/kimeneti műveletek száma másodpercenként) a Azure Stack a virtuálisgép- [méret](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) függvénye a lemez típusa helyett. Ez azt jelenti, hogy egy Standard_Fs sorozatú virtuális gép esetében, függetlenül attól, hogy az SSD-t vagy a HDD-t választja a lemez típusához, a IOPS-korlát egyetlen további adatlemez esetében 2300 IOPS. A kényszerített IOPS korlátja a zajos szomszédok megelőzése. Nem biztos benne, hogy a IOPS egy adott virtuálisgép-méretet fog kapni.

Javasoljuk továbbá [Managed Disks](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations)használatát. A felügyelt lemezek egyszerűbbé teszik a lemezek kezelését a tárterület kezelésével. A felügyelt lemezek nem igényelnek tárfiókot. Egyszerűen adja meg a méretet és a lemez típusát, és az magas rendelkezésre állású erőforrásként lesz üzembe helyezve.

Az operációsrendszer-lemez Azure Stack blob Storage-ban tárolt VHD, így akkor is megmarad, ha a gazdagép nem működik. Azt javasoljuk, hogy hozzon létre egy vagy több [adatlemezt](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-disks)is, amelyek az alkalmazásadatok által használt állandó VHD-k. Ha lehetséges, az alkalmazásokat adatlemezre telepítse, ne az operációs rendszer lemezére. Előfordulhat, hogy néhány örökölt alkalmazásnak összetevőket kell telepítenie a C: meghajtón; Ebben az esetben [az operációsrendszer-lemezt átméretezheti a](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-expand-os-disk) PowerShell használatával.

A virtuális gép egy ideiglenes lemezzel is létrejön (a D: meghajtó Windows rendszeren). Ezt a lemezt a Azure Stack tárolási infrastruktúrájának egy ideiglenes kötetén tárolja a rendszer. Előfordulhat, hogy az újraindítások és más virtuálisgép-életciklusi események során törölve lett. Ez a lemez csak ideiglenes adatokat, például lapozófájlokat tárol.

## <a name="network"></a>Network (Hálózat)

A hálózati összetevők a következő erőforrásokat tartalmazzák:

-   **Virtuális hálózat**. Minden virtuális gép olyan virtuális hálózatba van telepítve, amely több alhálózatra is osztható.

-   **Hálózati adapter (NIC)** . A hálózati adapter teszi lehetővé a virtuális gép számára a virtuális hálózattal való kommunikációt. Ha több hálózati adapterre van szüksége a virtuális géphez, vegye figyelembe, hogy az egyes virtuálisgép- [méretekhez](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)a hálózati adapterek maximális száma van meghatározva.

-   **Nyilvános IP-cím/VIP**. A virtuális géppel folytatott kommunikációhoz nyilvános IP-cím szükséges, például Távoli asztal (RDP) használatával. A nyilvános IP-cím lehet dinamikus vagy statikus. Alapértelmezés szerint dinamikus.

-   Foglaljon le [statikus IP-címet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-reserved-public-ip) , ha olyan rögzített IP-cím szükséges, amely nem változik – például ha létre kell hoznia egy DNS-rekordot, vagy hozzá kell adnia az IP-címet egy biztonságos listához.

-   Létrehozhat egy teljes tartománynevet (FQDN) is az IP-címhez. Ezután regisztrálhat egy [CNAME rekordot](https://en.wikipedia.org/wiki/CNAME_record) a DNS-ben, amely a teljes tartománynévre mutat. További információ: [teljes tartománynév létrehozása a Azure Portalban](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-portal-create-fqdn).

-   **Hálózati biztonsági csoport (NSG)** . A NSG a virtuális gépek hálózati forgalmának engedélyezésére vagy letiltására szolgálnak. A NSG alhálózatokhoz vagy egyedi virtuálisgép-példányokhoz is társítható.

Minden NSG tartalmaz az [alapértelmezett szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)készletét, beleértve az összes bejövő internetes forgalmat blokkoló szabályt is. Az alapértelmezett szabályok nem törölhetők, azonban más szabályokkal felülírhatók. Az internetes forgalom engedélyezéséhez hozzon létre olyan szabályokat, amelyek engedélyezik a bejövő forgalmat adott portokra – például a HTTP-hez készült 80-as portot. Az RDP engedélyezéséhez adjon hozzá egy NSG-szabályt, amely engedélyezi a bejövő forgalmat a 3389-es TCP-porton.

## <a name="operations"></a>Műveletek

**Diagnosztika**. A monitorozás és a diagnosztika engedélyezése, beleértve az alapszintű egészségügyi mérőszámokat, a diagnosztikai infrastruktúra naplóit és a [rendszerindítási diagnosztikát](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). A rendszerindítási diagnosztika segít diagnosztizálni a rendszerindítási hibát, ha a virtuális gép nem indítható állapotba kerül. Hozzon létre egy Azure Storage-fiókot a naplók tárolásához. Egy standard helyileg redundáns tárolási (LRS) fiók elegendő a diagnosztikai naplókhoz. További információ: a [monitorozás és a diagnosztika engedélyezése](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data).

**Rendelkezésre állás**. Előfordulhat, hogy a virtuális gép újraindítást igényel a Azure Stack operátor által ütemezett tervezett karbantartás miatt. Az Azure-beli több virtuális gépre kiterjedő üzemi rendszerek magas rendelkezésre állása érdekében a virtuális gépeket egy [rendelkezésre állási csoportba](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) helyezik, amely több tartalék tartományon és frissítési tartományon keresztül terjed ki. A Azure Stack hub kisebb méretében a rendelkezésre állási csoportokban lévő tartalék tartomány a méretezési egység egyetlen csomópontja.  

Habár a Azure Stack hub infrastruktúrája már rugalmas a hibákhoz, az alapul szolgáló technológia (feladatátvételi fürtszolgáltatás) továbbra is leállást okoz az érintett fizikai kiszolgálókon futó virtuális gépeknél, ha hardverhiba van. Azure Stack hub támogatja a rendelkezésre állási csoport legfeljebb három tartalék tartománnyal való egységességét az Azure-ban.

|                   |             |
|-------------------|-------------|
| **Tartalék tartományok** | A rendelkezésre állási csoportba helyezett virtuális gépeket fizikailag el kell különíteni egymástól a több tartalék tartomány (Azure Stack hub-csomópontok) lehető legegyenletesebb kiterjedésével. Hardverhiba esetén a sikertelen tartalék tartományba tartozó virtuális gépek más tartalék tartományokban lesznek újraindítva. A többi virtuális gépről külön tartalék tartományokban lesznek tárolva, de ha lehetséges, ugyanabban a rendelkezésre állási készletben. Ha a hardver online állapotba kerül, a virtuális gépek újra lesznek egyenlítve a magas rendelkezésre állás fenntartása érdekében. |
| **Tartományok frissítése**| A frissítési tartományok egy másik módja, hogy az Azure magas rendelkezésre állást biztosít a rendelkezésre állási csoportokban. A frissítési tartomány a mögöttes hardver logikai csoportja, amely egyszerre végezhető el a karbantartásban. Az ugyanabban a frissítési tartományban található virtuális gépek a tervezett karbantartás során újraindulnak. Mivel a bérlők virtuális gépeket hoznak létre egy rendelkezésre állási csoporton belül, az Azure platform automatikusan elosztja a virtuális gépeket ezen frissítési tartományok között. <br>Azure Stack központban a virtuális gépek a fürt többi online gazdagépén át lesznek telepítve, mielőtt a rendszer a mögöttes gazdagépet frissíti. Mivel a gazdagép frissítése során nincs szükség bérlői állásidőre, a Azure Stack hub frissítési tartomány funkciója csak az Azure-hoz készült sablon-kompatibilitáshoz létezik. A rendelkezésre állási csoportba tartozó virtuális gépek a frissítési tartományuk alapján 0 értéket fognak látni a portálon. |

**Biztonsági másolatok** A Azure Stack IaaS virtuális gépek védelmére vonatkozó javaslatokért tekintse meg ezt a cikket.

**Virtuális gép leállítása**. Az Azure különbséget tesz a „leállított” és a „felszabadított” állapot között. A leállított virtuális gépek után fizetni kell, a felszabadítottak után azonban nem. A Azure Stack portálon a **Leállítás** gomb felszabadítja a virtuális gépet. Ha az operációs rendszerből állítja le, amikor be van jelentkezve, azzal a virtuális gépet leállítja, de **nem** szabadítja fel, tehát továbbra is fizetnie kell a díját.

**Virtuális gép törlése**. Ha töröl egy virtuális gépet, a rendszer nem törli a virtuális gépek lemezeit. Ez azt jelenti, hogy biztonságosan törölheti a virtuális gépet anélkül, hogy adatot vesztene. A tárolásért azonban továbbra is díjat kell fizetnie. A virtuális gép lemezének törléséhez törölje a felügyelt lemez objektumot. A véletlen törlés megelőzése érdekében használjon erőforrás- *zárolást* a teljes erőforráscsoport zárolásához vagy az egyes erőforrások, például a virtuális gépek zárolásához.

## <a name="security-considerations"></a>Biztonsági szempontok

Helyezze üzembe a virtuális gépeket [Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack) az Azure-erőforrások biztonsági állapotának központi áttekintéséhez. A Security Center monitorozza a potenciális biztonsági problémákat, és átfogó képet nyújt az üzemi környezet biztonsági állapotáról. A Security Center Azure-előfizetésenként külön konfigurálandó. A biztonsági adatgyűjtés engedélyezése az Azure- [előfizetés](https://docs.microsoft.com/azure/security-center/security-center-get-started)bevezetésének Security Center a standard témakörben leírtak szerint. Az adatgyűjtés engedélyezése esetén a Security Center automatikusan figyeli az előfizetés alatt létrehozott összes virtuális gépet.

**Javítási felügyelet**. A javítás felügyeletének konfigurálásához a virtuális gépen tekintse meg [ezt](https://docs.microsoft.com/azure-stack/user/vm-update-management) a cikket. Ha engedélyezve van, a Security Center ellenőrzi, hogy a biztonsági és kritikus frissítések hiányoznak-e. A virtuális gép [csoportházirend beállításainak](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) használatával engedélyezheti az automatikus rendszerfrissítéseket.

**Kártevő szoftver**. Ha engedélyezve van, a Security Center ellenőrzi, hogy van-e telepítve kártevőirtó szoftver. A Security Center segítségével telepíthet is kártevőirtó szoftvert az Azure Portalon belülről.

**Hozzáférés-vezérlés**. A [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) használatával szabályozhatja az Azure-erőforrásokhoz való hozzáférést. Az RBAC lehetővé teszi, hogy engedélyezési szerepköröket rendeljen a fejlesztő és üzemeltető csapata tagjaihoz. Az Olvasó szerepkör például áttekintheti az Azure-erőforrásokat, de nem hozhatja létre, nem kezelheti és nem törölheti őket. Bizonyos engedélyek egy Azure-erőforrástípus jellemzőek. A Virtuális gépek közreműködője szerepkör például újraindíthat vagy felszabadíthat egy virtuális gépet, alaphelyzetbe állíthatja a rendszergazdai jelszót, létrehozhat egy új virtuális gépet, stb. Az architektúra számára hasznos egyéb [beépített RBAC-szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) például a [DevTest Labs felhasználói](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) és [hálózati közreműködője](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!Note]  
> Az RBAC nem korlátozza a virtuális gépre bejelentkezett felhasználó által végezhető műveleteket. Azokat az engedélyeket a vendég operációs rendszeren lévő fiók típusa határozza meg.

**Naplókat**. A [tevékenység-naplók](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data?#activity-log) segítségével megtekintheti a kiépítési műveleteket és az egyéb virtuálisgép-eseményeket.

**Adattitkosítás**. A Azure Stack BitLocker 128 bites AES-titkosítást használ a tárolási alrendszerben lévő felhasználói és infrastrukturális adatok védelme érdekében. További információ: inaktív [adatok titkosítása Azure Stackban](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker).


## <a name="next-steps"></a>Következő lépések

- Azure Stack virtuális gépekkel kapcsolatos további tudnivalókért lásd: Azure Stack virtuálisgép- [funkciók](azure-stack-vm-considerations.md).  
- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](https://docs.microsoft.com/azure/architecture/patterns).
