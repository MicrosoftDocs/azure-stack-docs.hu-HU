---
title: Azure Stack hub 1910 ismert problémái
description: Ismerje meg az Azure Stack hub 1910 ismert problémáit.
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: 0a23951d53d57b103c442ecf2b5b18f894e0f58b
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248327"
---
# <a name="azure-stack-hub-1910-known-issues"></a>Azure Stack hub 1910 ismert problémái

Ez a cikk Azure Stack hub-kiadások ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

> [!IMPORTANT]  
> Ha a Azure Stack hub-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update"></a>Frissítés

Az ismert Azure Stack hub-frissítési problémákkal kapcsolatban lásd: [frissítések hibaelhárítása Azure stack hub-ban](../azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

### <a name="duplicate-subscription-button-in-lock-blade"></a>Ismétlődő előfizetés gomb a zárolási panelen

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felügyeleti portálon a felhasználói előfizetések **zárolási** paneljének két gombja van, amelyek az **előfizetést** mondják.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack hub-portálon keresztül.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="storage-account-settings"></a>Tárfiók beállításai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiók **konfigurációja** panel egy beállítást mutat be a **biztonsági átvitel típusának** módosításához. A szolgáltatás jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="upload-blob-with-oauth-error"></a>BLOB feltöltése OAuth-hibával

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

### <a name="upload-blob-option-unsupported"></a>Nem támogatott blob-feltöltési lehetőség

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a feltöltési panelen, lehetőség van a **HRE** vagy a **kulcsos hitelesítés** kiválasztására, azonban a **HRE** nem támogatott az Azure stack hub-ban.
- Előfordulás: gyakori

### <a name="alert-for-network-interface-disconnected"></a>Hálózati adapter leválasztott riasztása

- Alkalmazható: a probléma a 1908-es vagy újabb verzióra vonatkozik.
- Ok: Ha egy kábel le van választva egy hálózati adapterről, a riasztás nem jelenik meg a felügyeleti portálon. Ezt a problémát az okozza, hogy ez a hiba a Windows Server 2019 rendszerben alapértelmezés szerint le van tiltva.
- Előfordulás: gyakori

### <a name="incorrect-tooltip-when-creating-vm"></a>Helytelen elemleírás a virtuális gép létrehozásakor

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a felügyelt lemez kiválasztásakor a lemez típusa prémium SSD, a legördülő lista az **operációs rendszer lemezét** jeleníti meg. A beállítás melletti elemleírás szerint **bizonyos operációsrendszer-lemezek ingyenesen elérhetők az ingyenes Azure-fiókkal**; Ez azonban a Azure Stack hub esetében nem érvényes. Emellett a lista olyan **ingyenes fiókot** is tartalmaz, amely Azure stack hub esetében nem érvényes.
- Előfordulás: gyakori

### <a name="delete-a-storage-container"></a>Storage-tároló törlése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor egy felhasználó egy tárolót próbál törölni, a művelet meghiúsul, ha a felhasználó nem kapcsol be **felülbírálási Azure Policy és RBAC szerepkör-beállításokat**.
- Szervizelés: Ellenőrizze, hogy a jelölőnégyzet be van-e jelölve a **Azure Policy felülbírálásához és a RBAC szerepkör-beállításokhoz**.
- Előfordulás: gyakori

### <a name="refresh-button-on-virtual-machines-fails"></a>Nem sikerül a frissítés gomb a virtuális gépeken

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor a **Virtual Machinesra** navigál, és a tetején található gomb használatával próbálkozik a frissítéssel, az állapotok nem fognak pontosan frissülni.
- Szervizelés: a rendszer 5 percenként automatikusan frissíti az állapotot, függetlenül attól, hogy a frissítés gombra kattintott-e. Várjon 5 percet, és keresse meg az állapotot.
- Előfordulás: gyakori

### <a name="storage-account-options"></a>Tárfiók-beállítások

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiókok neve **Storage-fiókként (blob, fájl, tábla, üzenetsor**) jelenik meg. Azure Stack hub esetében azonban a **fájl** nem támogatott.
- Előfordulás: gyakori

### <a name="storage-account-configuration"></a>Tárfiók konfigurálása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor létrehoz egy Storage-fiókot, és megtekinti annak **konfigurációját**, nem mentheti a konfigurációs módosításokat, mert Ajax-hibát eredményez.
- Előfordulás: gyakori

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>Az SQL erőforrás-szolgáltató kapacitás-figyelése megtartja a terhelést

- Alkalmazható: Ez a probléma a Azure Stack hub 1910-es vagy újabb verziójára vonatkozik, és az SQL Resource Provider 1.1.33.0 vagy korábbi verziója van telepítve.
- Ok: az SQL-erőforrás-szolgáltató jelenlegi verziója nem kompatibilis a 1910-es frissítésben szereplő legújabb portálok egyes módosításaival.
- Szervizelés: kövesse az erőforrás-szolgáltató frissítési folyamatát az SQL Resource Provider gyorsjavítás 1.1.47.0 alkalmazásához, miután a Azure Stack hub frissítve lett az 1910-es frissítésre (az[SQL RP-es verziójának 1.1.47.0](https://aka.ms/azurestacksqlrp11470)). A MySQL erőforrás-szolgáltató esetén azt is javasoljuk, hogy a MySQL erőforrás-szolgáltató gyorsjavítási 1.1.47.0 alkalmazza a Azure Stack hub 1910 frissítésre való frissítése után ([MySQL RP-verzió: 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)).
- Előfordulás: gyakori

### <a name="access-control-iam"></a>Access Control (IAM)

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a IAM kiterjesztés elavult. Az Azure Stack hub szolgáltatással szállított Ibiza-portál új viselkedést eredményez, amelynek hatására a RBAC bővítmény meghiúsul, ha a felhasználó megnyit egy olyan előfizetéshez tartozó **Access Control (iam)** panelt, amely nincs kiválasztva a globális előfizetés-választóban (a felhasználói portálon a címtár és az **előfizetés** ). A panel egy hurokban jeleníti meg a **betöltést** , és a felhasználó nem tud új szerepköröket hozzáadni az előfizetéshez. A **Hozzáadás** panel is megjeleníti a **betöltést** egy hurokban.
- Szervizelés: Ellenőrizze, hogy az előfizetés be van-e jelölve a **címtár + előfizetés** menüben. A menü a portál felső részén, az **Értesítések** gomb közelében található, vagy elérhető az **Összes erőforrás** panelen elhelyezett parancsikonon keresztül is. A panelen a következő jelenik meg: **Nem talál egy előfizetést? Nyissa meg a Címtár + Előfizetések beállítása elemet**. Az előfizetést ebben a menüben tudja kiválasztani.

### <a name="sql-resource-provider"></a>SQL erőforrás-szolgáltató

- Alkalmazható: a probléma a 1908-es vagy régebbi bélyegzők esetében érvényes.
- Ok: az SQL Resource Provider (RP) verziójának 1.1.47.0 telepítésekor a portálon nem láthatók az SQL RP-hez társított eszközök.
- Szervizelés: törölje az RP-t, frissítse a bélyeget, majd telepítse újra az SQL RP-t.

### <a name="activity-log-blade"></a>Activity napló panel

- Alkalmazható: a probléma a 1907-es vagy annál újabb rendszerű bélyegzők esetében érvényes. <!-- Note: Applies to 2002 as well -->
- Ok: a műveletnapló elérésekor a portál csak a bejegyzések első oldalát jeleníti meg. **További találatok betöltése** nem tölti be a hozzáadási bejegyzéseket.
- Szervizelés: állítsa be az időtartományt a szűrőben az első oldal után eltelt bejegyzések áttekintéséhez.

## <a name="networking"></a>Hálózatkezelés

### <a name="load-balancer"></a>Terheléselosztóval

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a rendelkezésre állási csoport virtuális gépeket egy terheléselosztó háttér-készletéhez adja hozzá, hibaüzenet jelenik meg a portálon, amely **nem tudta menteni** a terheléselosztó háttér-készletét. Ez egy kozmetikai probléma a portálon; a funkció továbbra is érvényben van, és a virtuális gépek sikeresen hozzá lettek adva a háttér-készlethez.
- Előfordulás: gyakori

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: nem hozható létre explicit **DenyAllOutbound** -szabály egy NSG, mivel ez megakadályozza a virtuális gép üzembe helyezéséhez szükséges összes belső kommunikációt az infrastruktúrával.
- Előfordulás: gyakori

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok** használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Nem lehet törölni egy NSG, ha a hálózati adapterek nem csatlakoznak a futó virtuális géphez

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy NSG és egy futó virtuális géphez nem csatolt hálózati adapter társítása esetén az adott objektumhoz tartozó frissítési (PUT) művelet meghiúsul a hálózati vezérlő rétegében. A NSG a hálózati erőforrás-szolgáltató rétegben fogja frissíteni, de a hálózati vezérlőn nem, így a NSG sikertelen állapotba kerül.
- Szervizelés: csatolja a NSG társított hálózati adaptereket, amelyeket el kell távolítani a futó virtuális gépekkel, és meg kell szüntetnie a NSG, vagy el kell távolítania a NSG társított összes hálózati adaptert.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati adapter

#### <a name="addingremoving-network-interface"></a>Hálózati adapter hozzáadása/eltávolítása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

#### <a name="primary-network-interface"></a>Elsődleges hálózati adapter

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy virtuális gép elsődleges NIC-je nem módosítható. Ha törli vagy leválasztja az elsődleges hálózati adaptert, a rendszer problémákba ütközik a virtuális gép indításakor.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="next-hop-type"></a>Következő ugrás típusa

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon az útválasztási táblázat létrehozásakor **Virtual Network átjáró** a következő ugrási típusú beállítások egyike jelenik meg. Ez azonban Azure Stack hub esetében nem támogatott.
- Előfordulás: gyakori

#### <a name="alerts"></a>Riasztások

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások** használatára. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, a létrehozáskor és a **Virtual Network átjáró** erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **kapcsolatok** panel egy **VPN-hibakereső** nevű szolgáltatást jelenít meg. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon megjelenik a **VPN-** átjáró erőforrás-kezelési funkciója és **mérőszámai** , azonban ez Azure stack hub esetében nem támogatott.
- Előfordulás: gyakori

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjáró-termékváltozatok](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack hub-on](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](../azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új Windowsos virtuális gép (VM) létrehozásakor a következő hiba jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="consumed-compute-quota"></a>Felhasznált számítási kvóta

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új virtuális gép létrehozásakor előfordulhat, hogy az **előfizetés az ezen a helyen lévő összes regionális vCPU esetében egy hibaüzenetet kap. Ez az előfizetés az összes rendelkezésre álló, 50 teljes regionális vCPU használja.** Ez azt jelzi, hogy elérte az összes elérhető magok kvótáját.
- Szervizelés: kérje meg az operátort, hogy adjon meg egy kiegészítő kvótával rendelkező kiegészítő csomagot. Az aktuális csomag kvótájának szerkesztése nem fog működni, vagy nagyobb kvóta jelenik meg.
- Előfordulás: ritka

### <a name="privileged-endpoint"></a>Emelt szintű végpont

- Alkalmazható: Ez a probléma a 1910-es és korábbi kiadásokra vonatkozik.
- Ok: nem lehet csatlakozni a rendszerjogosultságú végponthoz (ERC virtuális gépekhez) egy olyan számítógépről, amely a Windows nem angol nyelvű verzióját futtatja.
- Szervizelés: ez egy ismert probléma, amelyet a 1910-nál későbbi kiadásokban rögzítettek. Megkerülő megoldásként futtathatja a **New-PSSession** és a **ENTER-PSSession** PowerShell-parancsmagokat az **en-us** kulturális környezet használatával. példaként állítsa be a kulturális környezetet a következő parancsfájl használatával: https://resources.oreilly.com/examples/9780596528492/blob/master/Use-Culture.ps1 .
- Előfordulás: ritka

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack hub-környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartomány rendelkezésre állási készletében, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack hub-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack hub-telepítés frissítési folyamata során.
