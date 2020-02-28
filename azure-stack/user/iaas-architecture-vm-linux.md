---
title: Linux rendszerű virtuális gép futtatása Azure Stack hub-on
description: Megtudhatja, hogyan futtathat Linux rendszerű virtuális gépet Azure Stack hub-on.
author: mattbriggs
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: f969435900f290aaae10942e223ade15cacf4769
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704964"
---
# <a name="run-a-linux-virtual-machine-on-azure-stack-hub"></a>Linux rendszerű virtuális gép futtatása Azure Stack hub-on

A virtuális gép (VM) üzembe helyezése Azure Stack hub-ban (például az Azure-ban) további összetevőket igényel, a virtuális gép mellett, beleértve a hálózatkezelési és tárolási erőforrásokat is. Ez a cikk a Linux rendszerű virtuális gépek Azure Stack hub-on való futtatásának ajánlott eljárásait ismerteti.

![Linux rendszerű virtuális gép architektúrája Azure Stack hub-on](./media/iaas-architecture-vm-linux/image1.png)

## <a name="resource-group"></a>Erőforráscsoport

Az [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) olyan logikai tároló, amely a kapcsolódó Azure stack hub-erőforrásokat tárolja. Általánosságban véve csoportba az erőforrásokat az élettartamuk alapján, és fogják kezelni őket.

Olyan szorosan társított erőforrásokat helyezzen el, amelyek ugyanazt az életciklust használják ugyanabba az [erőforráscsoporthoz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Az erőforráscsoportok segítségével csoportosan helyezhet üzembe és monitorozhat erőforrásokat, és a számlázási költségeket erőforráscsoportonként követheti. Az erőforrásokat készletként is törölheti, ami hasznos lehet a tesztelési környezetekben való üzembe helyezéshez. Jelentéssel bíró erőforrásneveket adjon meg, hogy egyszerűbben megkereshesse és azonosíthassa az egyes erőforrásokat és azok szerepkörét. További információ: [Az Azure-erőforrások ajánlott elnevezési konvenciói](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Virtuális gép

Kiépítheti a virtuális gépet a közzétett lemezképek listájából, vagy egy, a Azure Stack hub blob Storage-ba feltöltött egyéni felügyelt lemezképből vagy virtuális merevlemezből (VHD-fájlból). Azure Stack hub támogatja a különböző népszerű Linux-disztribúciók, például a CentOS, a Debian, a Red Hat Enterprise, az Ubuntu és a SUSE futtatását. További információ: [Linux on Azure stack hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-linux). Azt is megteheti, hogy az Azure Stack hub piactéren elérhető, közzétett Linux-rendszerképek egyikét is kijelöli.

Az Azure Stack hub különböző virtuálisgép-méreteket kínál az Azure-ból. További információ: [Azure stack hub virtuális gépei méretei](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes). Ha egy meglévő munkaterhelést Azure Stack hubhoz helyez át, kezdje a helyi kiszolgálók/Azure-hoz legközelebb lévő virtuálisgép-mérettel. Ezután mérjük meg a tényleges számítási feladatok teljesítményét a processzor, a memória és a lemezes bemeneti/kimeneti műveletek (IOPS) alapján, és szükség szerint állítsa be a méretet.

## <a name="disks"></a>Lemezek

A költség az üzembe helyezett lemez kapacitásától függően változik. A IOPS és az átviteli sebesség (vagyis az adatátviteli sebesség) a virtuális gép méretétől függ, így a lemez kiépítésekor mindhárom tényezőt (kapacitás, IOPS és átviteli sebesség) figyelembe kell venni.

A lemez IOPS (bemeneti/kimeneti műveletek száma másodpercenként) az Azure Stack hub-ban a virtuálisgép- [méret](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) függvénye a lemez típusa helyett. Ez azt jelenti, hogy egy Standard_Fs sorozatú virtuális gép esetében, függetlenül attól, hogy az SSD-t vagy a HDD-t választja a lemez típusához, a IOPS-korlát egyetlen további adatlemez esetében 2300 IOPS. A kényszerített IOPS korlátja a zajos szomszédok megelőzése. Nem biztos benne, hogy a IOPS egy adott virtuálisgép-méretet fog kapni.

Javasoljuk továbbá [Managed Disks](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations)használatát. A felügyelt lemezek egyszerűbbé teszik a lemezek kezelését a tárterület kezelésével. A felügyelt lemezek nem igényelnek tárfiókot. Egyszerűen adja meg a méretet és a lemez típusát, és az magas rendelkezésre állású erőforrásként lesz üzembe helyezve.

Az operációsrendszer-lemez [Azure stack hub-tárolóban](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-overview)tárolt VHD, így akkor is megmarad, ha a gazdagép nem működik. Linux rendszerű virtuális gépek esetén az operációsrendszer-lemez/dev/sda1. Azt javasoljuk, hogy hozzon létre egy vagy több [adatlemezt](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-disks)is, amelyek az alkalmazásadatok által használt állandó VHD-k.

A létrehozott VHD-k nincsenek formázva. A lemez formázásához jelentkezzen be a virtuális gépre. A Linux-rendszerhéjban az adatlemezek/dev/SDC,/dev/SDD és így tovább jelennek meg. A lsblk futtatásával listázhatja a blokkoló eszközöket, beleértve a lemezeket is. Adatlemez használatához hozzon létre egy partíciót és egy fájlrendszert, majd csatlakoztassa a lemezt. Például:

```bash
# Create a partition.
sudo fdisk /dev/sdc \# Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Adatlemez hozzáadásakor a rendszer hozzárendel egy logikaiegység-szám (LUN) azonosítót a lemezhez. Megadhatja a logikai egység AZONOSÍTÓját is – például egy lemez cseréjét, és meg szeretné őrizni a logikai egység AZONOSÍTÓját, vagy ha van olyan alkalmazás, amely egy adott LUN-azonosítót keres. Ne feledje azonban, hogy az egyes lemezek LUN azonosítóinak egyedinek kell lenniük.

A VM egy ideiglenes lemezzel jön létre. Ezt a lemezt a Azure Stack hub tárolási infrastruktúrájának ideiglenes kötetén tárolja a rendszer. Előfordulhat, hogy az újraindítások és más virtuálisgép-életciklusi események során törölve lett. Ez a lemez csak ideiglenes adatokat, például lapozófájlokat tárol. Linux rendszerű virtuális gépek esetén az ideiglenes lemez/dev/sdb1, és a/mnt/Resource vagy a/mnt. csatlakoztatása

## <a name="network"></a>Hálózat

A hálózati összetevők a következő erőforrásokat tartalmazzák:

-   **Virtuális hálózat**. Minden virtuális gép olyan virtuális hálózatba van telepítve, amely több alhálózatra is osztható.

-   **Hálózati adapter (NIC)** . A hálózati adapter teszi lehetővé a virtuális gép számára a virtuális hálózattal való kommunikációt. Ha több hálózati adapterre van szüksége a virtuális géphez, vegye figyelembe, hogy az egyes virtuálisgép- [méretekhez](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)a hálózati adapterek maximális száma van meghatározva.

-   **Nyilvános IP-cím/VIP**. A virtuális géppel folytatott kommunikációhoz nyilvános IP-cím szükséges, például Távoli asztal (RDP) használatával. A nyilvános IP-cím lehet dinamikus vagy statikus. Alapértelmezés szerint dinamikus. Ha több hálózati adapterre van szüksége a virtuális géphez, vegye figyelembe, hogy az egyes virtuálisgép- [méretekhez](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)a hálózati adapterek maximális száma van meghatározva.

-   Létrehozhat egy teljes tartománynevet (FQDN) is az IP-címhez. Ezután regisztrálhat egy [CNAME rekordot](https://en.wikipedia.org/wiki/CNAME_record) a DNS-ben, amely a teljes tartománynévre mutat. További információ: [teljes tartománynév létrehozása a Azure Portalban](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-portal-create-fqdn).

-   **Hálózati biztonsági csoport (NSG).** Hálózati biztonsági csoportok használatával engedélyezheti vagy tilthatja le a virtuális gépekre irányuló hálózati forgalmat. Az NSG-k társíthatók alhálózatokhoz vagy Virtuálisgép-példányokhoz.

Minden NSG tartalmaz az [alapértelmezett szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)készletét, beleértve az összes bejövő internetes forgalmat blokkoló szabályt is. Az alapértelmezett szabályok nem törölhetők, azonban más szabályokkal felülírhatók. Az internetes forgalom engedélyezéséhez hozzon létre olyan szabályokat, amelyek engedélyezik a bejövő forgalmat adott portokra – például a HTTP-hez készült 80-as portot. Az SSH engedélyezéséhez adjon hozzá egy NSG-szabályt, amely engedélyezi a bejövő forgalmat a 22-es TCP-porton.

## <a name="operations"></a>Műveletek

**SSH**. Linux virtuális gép létrehozása előtt hozzon létre egy 2048 bites RSA nyilvános-titkos kulcspárt. A virtuális gép létrehozásakor használja a nyilvánoskulcs-fájlt. További információkért lásd: [az SSH és a Linux használata az Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys)-ban.

**Diagnosztika**. A monitorozás és a diagnosztika engedélyezése, beleértve az alapszintű egészségügyi mérőszámokat, a diagnosztikai infrastruktúra naplóit és a [rendszerindítási diagnosztikát](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). A rendszerindítási diagnosztika segít diagnosztizálni a rendszerindítási hibát, ha a virtuális gép nem indítható állapotba kerül. Hozzon létre egy Azure Storage-fiókot a naplók tárolásához. Egy standard helyileg redundáns tárolási (LRS) fiók elegendő a diagnosztikai naplókhoz. További információ: a [monitorozás és a diagnosztika engedélyezése](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data).

**Rendelkezésre állás**. Előfordulhat, hogy a virtuális gép újraindítást igényel az Azure Stack hub-kezelő által ütemezett tervezett karbantartás miatt. A magasabb rendelkezésre állás érdekében helyezzen üzembe több virtuális gépet egy [rendelkezésre állási csoporton](https://docs.microsoft.com/azure-stack/operator/app-service-deploy-ha)belül.

**Biztonsági másolatok** Az Azure Stack hub IaaS virtuális gépek védelmére vonatkozó javaslatokért tekintse meg [ezt a](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-protect) cikket.

**Virtuális gép leállítása**. Az Azure különbséget tesz a „leállított” és a „felszabadított” állapot között. A leállított virtuális gépek után fizetni kell, a felszabadítottak után azonban nem. A Azure Stack hub portálon a **Leállítás** gomb felszabadítja a virtuális gépet. Ha az operációs rendszerből állítja le, amikor be van jelentkezve, azzal a virtuális gépet leállítja, de **nem** szabadítja fel, tehát továbbra is fizetnie kell a díját.

**Virtuális gép törlése**. Ha töröl egy virtuális gépet, a rendszer nem törli a virtuális gépek lemezeit. Ez azt jelenti, hogy biztonságosan törölheti a virtuális gépet anélkül, hogy adatot vesztene. A tárolásért azonban továbbra is díjat kell fizetnie. A virtuális gép lemezének törléséhez törölje a felügyelt lemez objektumot. A véletlen törlés megelőzése érdekében használjon erőforrás- [zárolást](https://docs.microsoft.com/azure/resource-group-lock-resources) a teljes erőforráscsoport zárolásához vagy az egyes erőforrások, például a virtuális gépek zárolásához.

## <a name="security-considerations"></a>Biztonsági szempontok

Helyezze üzembe a virtuális gépeket [Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack) az Azure-erőforrások biztonsági állapotának központi áttekintéséhez. A Security Center monitorozza a potenciális biztonsági problémákat, és átfogó képet nyújt az üzemi környezet biztonsági állapotáról. A Security Center Azure-előfizetésenként külön konfigurálandó. A biztonsági adatgyűjtés engedélyezése az Azure- [előfizetés](https://docs.microsoft.com/azure/security-center/security-center-get-started)bevezetésének Security Center a standard témakörben leírtak szerint. Az adatgyűjtés engedélyezése esetén a Security Center automatikusan figyeli az előfizetés alatt létrehozott összes virtuális gépet.

**Javítási felügyelet**. A javítás felügyeletének konfigurálásához a virtuális gépen tekintse meg [ezt](https://docs.microsoft.com/azure-stack/user/vm-update-management) a cikket. Ha engedélyezve van, a Security Center ellenőrzi, hogy a biztonsági és kritikus frissítések hiányoznak-e. A virtuális gép [csoportházirend beállításainak](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) használatával engedélyezheti az automatikus rendszerfrissítéseket.

**Kártevő szoftver**. Ha engedélyezve van, a Security Center ellenőrzi, hogy van-e telepítve kártevőirtó szoftver. A Security Center segítségével telepíthet is kártevőirtó szoftvert az Azure Portalon belülről.

**Hozzáférés-vezérlés**. A [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) használatával szabályozhatja az Azure-erőforrásokhoz való hozzáférést. Az RBAC lehetővé teszi, hogy engedélyezési szerepköröket rendeljen a fejlesztő és üzemeltető csapata tagjaihoz. Az Olvasó szerepkör például áttekintheti az Azure-erőforrásokat, de nem hozhatja létre, nem kezelheti és nem törölheti őket. Bizonyos engedélyek egy Azure-erőforrástípus jellemzőek. A Virtuális gépek közreműködője szerepkör például újraindíthat vagy felszabadíthat egy virtuális gépet, alaphelyzetbe állíthatja a rendszergazdai jelszót, létrehozhat egy új virtuális gépet, stb. Az architektúra számára hasznos egyéb [beépített RBAC-szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) például a [DevTest Labs felhasználói](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) és [hálózati közreműködője](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!Note]  
> Az RBAC nem korlátozza a virtuális gépre bejelentkezett felhasználó által végezhető műveleteket. Azokat az engedélyeket a vendég operációs rendszeren lévő fiók típusa határozza meg.

**Naplókat**. A [tevékenység-naplók](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data?#activity-log) segítségével megtekintheti a kiépítési műveleteket és az egyéb virtuálisgép-eseményeket.

**Adattitkosítás**. Azure Stack hub a tárolás alrendszer szintjén védi a felhasználói és az infrastrukturális adatok védelmét a REST titkosítás használatával. Azure Stack hub tárolási alrendszer titkosítása a BitLocker és a 128 bites AES titkosítás használatával történik. További részletekért tekintse meg [ezt](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker) a cikket.

## <a name="next-steps"></a>Következő lépések

- Azure Stack hub virtuális gépekkel kapcsolatos további tudnivalókért lásd: [Azure stack hub](azure-stack-vm-considerations.md)virtuálisgép-funkciók.  
- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](https://docs.microsoft.com/azure/architecture/patterns).
