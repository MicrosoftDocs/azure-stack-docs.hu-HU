---
title: Azure Stack hub ismert problémái
description: Ismerje meg Azure Stack hub-kiadások ismert problémáit.
author: sethmanheim
ms.topic: article
ms.date: 05/05/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 31ef3ee64eb98b34160e95fee0a228fc32cee589
ms.sourcegitcommit: 7c10a45a8de0c5c7649e5329ca5b69a0791e37b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83721878"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack hub ismert problémái

Ez a cikk az Azure Stack hub kiadásában előforduló ismert problémákat sorolja fel. A lista új problémák észlelése esetén frissül.

Ha egy másik verzió ismert problémáit szeretné elérni, a bal oldali tartalomjegyzéknél válassza a verzió kiválasztása legördülő menüt.

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> A frissítés alkalmazása előtt tekintse át ezt a szakaszt.
::: moniker-end
::: moniker range="<azs-1907"
> [!IMPORTANT]  
> Ha a Azure Stack hub-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2002"
## <a name="update"></a>Frissítés

A 2002-es frissítés alkalmazása után az „érvénytelen időforrással” kapcsolatos riasztás helytelenül jelenhet meg a felügyeleti portálon. Ez a vakriasztás figyelmen kívül hagyható, egy hamarosan megjelenő kiadásban ki lesz javítva. 

Más ismert Azure Stack hub-frissítési problémák esetén tekintse [meg a Azure stack hub hibaelhárítási frissítései](azure-stack-troubleshooting.md)című témakört.

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack hub-portálon keresztül.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="storage-account-options"></a>Tárfiók-beállítások

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiókok neve **Storage-fiókként (blob, fájl, tábla, üzenetsor**) jelenik meg. Azure Stack hub esetében azonban a **fájl** nem támogatott.
- Előfordulás: gyakori

### <a name="create-managed-disk-snapshot"></a>Felügyelt lemez pillanatképének létrehozása

- Alkalmazható: Ez a probléma a 2002-es kiadásra vonatkozik.
- Ok: a felhasználói portálon a felügyelt lemez pillanatképének létrehozásakor a **Fiók típusa** mező üres. Ha üres fióktípus esetén kiválasztja a **Létrehozás** gombot, a pillanatkép létrehozása sikertelen lesz.
- Szervizelés: válasszon egy számlatípust a **Fiók típusa** legördülő listából, majd hozza létre a pillanatképet.
- Előfordulás: gyakori

### <a name="alert-for-network-interface-disconnected"></a>Hálózati adapter leválasztott riasztása

- Alkalmazható: a probléma a 1908-es vagy újabb verzióra vonatkozik.
- Ok: Ha egy kábel le van választva egy hálózati adapterről, a riasztás nem jelenik meg a felügyeleti portálon. Ezt a problémát az okozza, hogy ez a hiba a Windows Server 2019 rendszerben alapértelmezés szerint le van tiltva.
- Előfordulás: gyakori

### <a name="access-control-iam"></a>Access Control (IAM)

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a IAM kiterjesztés elavult. Az Azure Stack hub szolgáltatással szállított Ibiza-portál új viselkedést eredményez, amelynek hatására a RBAC bővítmény meghiúsul, ha a felhasználó megnyit egy olyan előfizetéshez tartozó **Access Control (iam)** panelt, amely nincs kiválasztva a globális előfizetés-választóban (a felhasználói portálon a címtár és az**előfizetés** ). A panel egy hurokban jeleníti meg a **betöltést** , és a felhasználó nem tud új szerepköröket hozzáadni az előfizetéshez. A **Hozzáadás** panel is megjeleníti a **betöltést** egy hurokban.
- Szervizelés: Ellenőrizze, hogy az előfizetés be van-e jelölve a **címtár + előfizetés** menüben. A menü a portál tetején, az értesítések gomb közelében, vagy a **minden erőforrás** **panelen** található parancsikonon keresztül érhető el, és nem jelenik meg az **előfizetés? Nyissa meg a könyvtár és előfizetés beállításait**. Az előfizetést ebben a menüben kell kiválasztani.

## <a name="networking"></a>Hálózat

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: nem hozható létre explicit **DenyAllOutbound** -szabály egy NSG, mivel ez megakadályozza a virtuális gép üzembe helyezéséhez szükséges összes belső kommunikációt az infrastruktúrával.
- Előfordulás: gyakori

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: bejövő vagy kimenő hálózati biztonsági szabály létrehozásakor a **protokoll** beállítás egy **ICMP** -beállítást mutat be. Ez Azure Stack hub esetében jelenleg nem támogatott. Ez a probléma rögzített, és nem fog megjelenni a következő Azure Stack hub-kiadásban.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati illesztő

#### <a name="addingremoving-network-interface"></a>Hálózati adapter hozzáadása/eltávolítása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

#### <a name="primary-network-interface"></a>Elsődleges hálózati adapter

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy virtuális gép elsődleges NIC-je nem módosítható. Ha törli vagy leválasztja az elsődleges hálózati adaptert, a rendszer problémákba ütközik a virtuális gép indításakor.
- Előfordulás: gyakori

### <a name="public-ip"></a>Nyilvános IP-cím

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy terheléselosztó **IdleTimeoutInMinutes** tartozó nyilvános IP-cím értéke nem módosítható. A művelet a nyilvános IP-címet sikertelen állapotba helyezi.
- Szervizelés: ahhoz, hogy a nyilvános IP-cím sikeres állapotba kerüljön, módosítsa a **IdleTimeoutInMinutes** értékét a nyilvános IP-címet az eredeti értékre hivatkozó terheléselosztó szabályban (az alapértelmezett érték 4 perc).
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack hub-on](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>A virtuális gép áttekintő paneljén nem jelenik meg a számítógép helyes neve

- Alkalmazható: Ez a probléma minden kiadásra vonatkozik.
- Ok: Ha egy virtuális gép adatait tekinti meg az Áttekintés panelen, a számítógép neve **(nem érhető el)** jelenik meg. Ez a speciális lemezekről vagy lemez-pillanatképekről létrehozott virtuális gépek tervezése.
- Szervizelés: Tekintse meg a **Tulajdonságok panelt** a **Beállítások**területen.

### <a name="nvv4-vm-size-on-portal"></a>NVv4 VM-méret a portálon

- Alkalmazható: a probléma a 2002-es és újabb verzióira vonatkozik.
- Ok: a virtuális gép létrehozási élménye során a virtuális gép méretét fogja látni: NV4as_v4. Azok az ügyfelek, akik rendelkeznek az AMD Mi25-alapú Azure Stack hub GPU előzetes verziójához szükséges hardverrel, sikeresen üzembe helyezhetik a virtuális gépeket. Az összes többi ügyfélnél a virtuálisgép-mérettel rendelkező virtuális gép üzembe helyezése sikertelen lesz.
- Szervizelés: az Azure Stack hub GPU előzetes verziójának előkészítése alapján.

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új virtuális gép (VM) létrehozásakor a következő hiba jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori


- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha egy leállított virtuális gépet próbál elindítani, a következő hibaüzenet jelenhet meg: a **VM Diagnostics Storage-fiókja (diagnosticstorageaccount) nem található. Győződjön meg arról, hogy a Storage-fiók nincs törölve**. A hiba akkor fordul elő, ha olyan virtuális gépet próbál elindítani, amelyen engedélyezve van a rendszerindítási diagnosztika, de a rendszer törli a hivatkozott rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="consumed-compute-quota"></a>Felhasznált számítási kvóta

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új virtuális gép létrehozásakor előfordulhat, hogy az **előfizetés az ezen a helyen lévő összes regionális vCPU esetében egy hibaüzenetet kap. Ez az előfizetés az összes rendelkezésre álló, 50 teljes regionális vCPU használja.** Ez azt jelzi, hogy elérte az összes elérhető magok kvótáját.
- Szervizelés: kérje meg az operátort, hogy adjon meg egy kiegészítő kvótával rendelkező kiegészítő csomagot. Az aktuális csomag kvótájának szerkesztése nem fog működni, vagy nagyobb kvóta jelenik meg.
- Előfordulás: ritka

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack hub-környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartomány rendelkezésre állási készletében, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack hub-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack hub-telepítés frissítési folyamata során.

### <a name="sql-vm"></a>SQL-ALAPÚ VIRTUÁLIS GÉP

#### <a name="storage-account-creating-failure-when-configuring-auto-backup"></a>Nem sikerült létrehozni a Storage-fiókot az automatikus biztonsági mentés konfigurálásakor

- Alkalmazható: Ez a probléma a 2002-es verzióra vonatkozik.
- Ok: Ha az SQL virtuális gépek automatikus biztonsági mentését egy új Storage-fiókkal konfigurálja, a hiba történt a **központi telepítési sablon érvényesítésekor. A "SqlAutobackupStorageAccountKind" sablon-paramétere nem található.**
- Szervizelés: alkalmazza a legújabb 2002-es gyorsjavítást.

#### <a name="auto-backup-cannot-be-configured-with-tls-12-enabled"></a>Az automatikus biztonsági mentés nem konfigurálható a TLS 1,2 engedélyezve

- Alkalmazható: Ez a probléma a 2002-es és újabb verziójú új telepítések, illetve a TLS 1,2-mel rendelkező korábbi verziók esetében érvényes.
- Ok: Ha egy meglévő Storage-fiókkal állítja be az SQL virtuális gépek automatikus biztonsági mentését, a hiba **SQL Server IaaS-ügynökkel meghiúsul: az alapul szolgáló kapcsolat bezárult: váratlan hiba történt a küldéskor.**
- Előfordulás: gyakori

## <a name="resource-providers"></a>Erőforrás-szolgáltatók

### <a name="sqlmysql"></a>SQL/MySQL

- Alkalmazható: Ez a probléma a 2002-es kiadásra vonatkozik. 
- Ok: Ha a bélyegző az SQL Resource Provider (RP) 1.1.33.0 vagy korábbi verzióját tartalmazza, akkor a bélyegző frissítése után az SQL/MySQL-hez tartozó pengék nem töltődnek be.
- Szervizelés: frissítse az RP-et a verzió 1.1.47.0

### <a name="app-service"></a>App Service

- Alkalmazható: Ez a probléma a 2002-es kiadásra vonatkozik.
- Ok: Ha a bélyegző App Service erőforrás-szolgáltató (RP) 1,7-es és régebbi verzióját tartalmazza, a bélyegző frissítése után a App Servicehoz tartozó pengék nem töltődnek be.
- Szervizelés: frissítse az RP-t a [2020](azure-stack-app-service-update.md)-es verzióra.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1910"
## <a name="update"></a>Frissítés

Az ismert Azure Stack hub-frissítési problémákkal kapcsolatban lásd: [frissítések hibaelhárítása az Azure stack hub-ban](azure-stack-troubleshooting.md).

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

### <a name="duplicate-subscription-button-in-lock-blade"></a>Ismétlődő előfizetés gomb a zárolási panelen

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felügyeleti portálon a felhasználói előfizetések **zárolási** paneljének két gombja van, amelyek az **előfizetést**mondják.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack hub-portálon keresztül.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="storage-account-settings"></a>Tárfiók beállításai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiók **konfigurációja** panel egy beállítást mutat be a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="upload-blob-with-oauth-error"></a>BLOB feltöltése OAuth-hibával

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

### <a name="upload-blob-option-unsupported"></a>Nem támogatott blob-feltöltési lehetőség

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a feltöltési panelen, lehetőség van a **HRE** vagy a **kulcsos hitelesítés**kiválasztására, azonban a **HRE** nem támogatott az Azure stack hub-ban.
- Előfordulás: gyakori

### <a name="alert-for-network-interface-disconnected"></a>Hálózati adapter leválasztott riasztása

- Alkalmazható: a probléma a 1908-es vagy újabb verzióra vonatkozik.
- Ok: Ha egy kábel le van választva egy hálózati adapterről, a riasztás nem jelenik meg a felügyeleti portálon. Ezt a problémát az okozza, hogy ez a hiba a Windows Server 2019 rendszerben alapértelmezés szerint le van tiltva.
- Előfordulás: gyakori

### <a name="incorrect-tooltip-when-creating-vm"></a>Helytelen elemleírás a virtuális gép létrehozásakor

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a felügyelt lemez kiválasztásakor a lemez típusa prémium SSD, a legördülő lista az **operációs rendszer lemezét**jeleníti meg. A beállítás melletti elemleírás szerint **bizonyos operációsrendszer-lemezek ingyenesen elérhetők az ingyenes Azure-fiókkal**; Ez azonban a Azure Stack hub esetében nem érvényes. Emellett a lista olyan **ingyenes fiókot** is tartalmaz, amely Azure stack hub esetében nem érvényes.
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
- Ok: a IAM kiterjesztés elavult. Az Azure Stack hub szolgáltatással szállított Ibiza-portál új viselkedést eredményez, amelynek hatására a RBAC bővítmény meghiúsul, ha a felhasználó megnyit egy olyan előfizetéshez tartozó **Access Control (iam)** panelt, amely nincs kiválasztva a globális előfizetés-választóban (a felhasználói portálon a címtár és az**előfizetés** ). A panel egy hurokban jeleníti meg a **betöltést** , és a felhasználó nem tud új szerepköröket hozzáadni az előfizetéshez. A **Hozzáadás** panel is megjeleníti a **betöltést** egy hurokban.
- Szervizelés: Ellenőrizze, hogy az előfizetés be van-e jelölve a **címtár + előfizetés** menüben. A menü a portál tetején, az értesítések gomb közelében, vagy a **minden erőforrás** **panelen** található parancsikonon keresztül érhető el, és nem jelenik meg az **előfizetés? Nyissa meg a könyvtár és előfizetés beállításait**. Az előfizetést ebben a menüben kell kiválasztani.

### <a name="sql-resource-provider"></a>SQL erőforrás-szolgáltató

- Alkalmazható: a probléma a 1908-es vagy régebbi bélyegzők esetében érvényes.
- Ok: az SQL Resource Provider (RP) verziójának 1.1.47.0 telepítésekor a portálon nem láthatók az SQL RP-hez társított eszközök.
- Szervizelés: törölje az RP-t, frissítse a bélyeget, majd telepítse újra az SQL RP-t.

### <a name="activity-log-blade"></a>Activity napló panel

- Alkalmazható: a probléma a 1907-es vagy annál újabb rendszerű bélyegzők esetében érvényes. <!-- Note: Applies to 2002 as well -->
- Ok: a műveletnapló elérésekor a portál csak a bejegyzések első oldalát jeleníti meg. **További találatok betöltése** nem tölti be a hozzáadási bejegyzéseket.
- Szervizelés: állítsa be az időtartományt a szűrőben az első oldal után eltelt bejegyzések áttekintéséhez.

## <a name="networking"></a>Hálózat

### <a name="load-balancer"></a>Terheléselosztó

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a rendelkezésre állási csoport virtuális gépeket egy terheléselosztó háttér-készletéhez adja hozzá, hibaüzenet jelenik meg a portálon, amely **nem tudta menteni**a terheléselosztó háttér-készletét. Ez egy kozmetikai probléma a portálon; a funkció továbbra is érvényben van, és a virtuális gépek sikeresen hozzá lettek adva a háttér-készlethez.
- Előfordulás: gyakori

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: nem hozható létre explicit **DenyAllOutbound** -szabály egy NSG, mivel ez megakadályozza a virtuális gép üzembe helyezéséhez szükséges összes belső kommunikációt az infrastruktúrával.
- Előfordulás: gyakori

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati illesztő

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
- Ok: a felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, a létrehozáskor és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **kapcsolatok** panel egy **VPN-hibakereső**nevű szolgáltatást jelenít meg. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon megjelenik a **VPN-** átjáró erőforrás-kezelési funkciója és **mérőszámai** , azonban ez Azure stack hub esetében nem támogatott.
- Előfordulás: gyakori

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack hub-on](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

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

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>1908 frissítési folyamat

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az Azure Stack hub frissítésének telepítésére tett kísérlet során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
- Szervizelés: a 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti a problémát, ha a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy a [frissítések telepítése szakaszt](azure-stack-apply-updates.md#install-updates-and-monitor-progress)követve manuálisan töltse fel a frissítési csomagot.
- Előfordulás: gyakori

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

### <a name="subscriptions-properties-blade"></a>Előfizetések tulajdonságai panel

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felügyeleti portálon az előfizetések **tulajdonságai panel** nem töltődik be megfelelően
- Szervizelés: ezeket az előfizetéseket az **előfizetések áttekintése** panel **Essentials (alapvető** erőforrások) paneljén tekintheti meg.
- Előfordulás: gyakori

### <a name="duplicate-subscription-button-in-lock-blade"></a>Ismétlődő előfizetés gomb a zárolási panelen

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felügyeleti portálon a felhasználói előfizetések **zárolási** paneljén két gomb található az **előfizetés**címkével.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack hub-portálon keresztül.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="storage-account-settings"></a>Tárfiók beállításai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiók **konfigurációja** panel egy beállítást mutat be a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="upload-blob"></a>BLOB feltöltése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

### <a name="alert-for-network-interface-disconnected"></a>Hálózati adapter leválasztott riasztása

- Alkalmazható: Ez a probléma az 1908-es kiadásra vonatkozik.
- Ok: Ha egy kábel le van választva egy hálózati adapterről, a riasztás nem jelenik meg a felügyeleti portálon. Ezt a problémát az okozza, hogy ez a hiba a Windows Server 2019 rendszerben alapértelmezés szerint le van tiltva.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózat

### <a name="load-balancer"></a>Load Balancer

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: amikor rendelkezésre állási csoport virtuális gépeket ad hozzá egy Load Balancer háttér-készletéhez, a portálon hibaüzenet jelenik meg, amely **nem tudta menteni a terheléselosztó háttér-** készletét. Ez egy kozmetikai probléma a portálon, a funkció továbbra is érvényben van, és a virtuális gépek sikeresen hozzá lettek adva a háttérrendszer-készlethez. 
- Előfordulás: gyakori

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: nem hozható létre explicit **DenyAllOutbound** -szabály egy NSG, mivel ez megakadályozza a virtuális gép üzembe helyezéséhez szükséges összes belső kommunikációt az infrastruktúrával.
- Előfordulás: gyakori

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati illesztő

#### <a name="addingremoving-network-interface"></a>Hálózati adapter hozzáadása/eltávolítása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

#### <a name="primary-network-interface"></a>Elsődleges hálózati adapter

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="next-hop-type"></a>Következő ugrás típusa

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon az útválasztási táblázat létrehozásakor **Virtual Network átjáró** a következő ugrási típusú beállítások egyike jelenik meg. Ez azonban Azure Stack hub esetében nem támogatott.
- Előfordulás: gyakori

#### <a name="alerts"></a>Riasztások

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, a létrehozáskor és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **kapcsolatok** panel egy **VPN-hibakereső**nevű funkciót mutat be. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack hub-on](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack hub-környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartomány rendelkezésre állási készletében, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack hub-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack hub frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizelés: virtuálisgép-hozzáférés használata a Linux-bővítményhez SSH-kulcsok implementálása a kiépítés után vagy jelszó alapú hitelesítés használatával.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>A virtuálisgép-méretezési csoport alaphelyzetbe állításának jelszava nem működik

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a méretezési csoport felhasználói felületén megjelenik egy új jelszó alaphelyzetbe állítása panel, de az Azure Stack hub még nem támogatja a jelszó visszaállítását egy méretezési csoporton.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Az esős felhő a méretezési csoport diagnosztikát

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuálisgép-méretezési csoport áttekintő lapja egy üres diagramot jelenít meg. Ha az üres diagramra kattint, megnyílik az "esős felhő" panel. Ez a méretezési csoport diagnosztikai információinak, például a CPU százalékának a diagramja, és nem támogatott a jelenlegi Azure Stack hub-buildben.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállításainak panelje

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.    
- Ok: a virtuális gép diagnosztikai beállításainak paneljén van egy **fogadó lap, amely egy** alkalmazás-Insight- **fiókot**kér. Ez egy új panel eredménye, amely Azure Stack hub-ban még nem támogatott.
- Szervizelés: nincs.
- Előfordulás: gyakori

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>1907 frissítési folyamat

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: amikor a 1907 Azure Stack hub frissítésének telepítését kísérli meg, a frissítés állapota meghiúsulhat, és az állapot **PreparationFailed**értékre vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
- Szervizelés: a 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti a problémát, ha a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy manuálisan töltse fel a frissítési csomagot a [frissítések importálása és telepítése szakasz](azure-stack-apply-updates.md)alapján.
- Előfordulás: gyakori

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

### <a name="subscriptions-properties-blade"></a>Előfizetések tulajdonságai panel

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felügyeleti portálon az előfizetések **tulajdonságai panel** nem töltődik be megfelelően
- Szervizelés: ezeket az előfizetéseket az **előfizetések áttekintése** panel **Essentials (alapvető** erőforrások) paneljén tekintheti meg.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack hub-portálon keresztül.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="storage-account-settings"></a>Tárfiók beállításai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiók **konfigurációja** panel egy beállítást mutat be a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="upload-blob"></a>BLOB feltöltése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózat

### <a name="load-balancer"></a>Load Balancer

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: amikor rendelkezésre állási csoport virtuális gépeket ad hozzá egy Load Balancer háttér-készletéhez, a portálon hibaüzenet jelenik meg, amely **nem tudta menteni a terheléselosztó háttér-** készletét. Ez egy kozmetikai probléma a portálon, a funkció továbbra is érvényben van, és a virtuális gépek sikeresen hozzá lettek adva a háttérrendszer-készlethez. 
- Előfordulás: gyakori

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: nem hozható létre explicit **DenyAllOutbound** -szabály egy NSG, mivel ez megakadályozza a virtuális gép üzembe helyezéséhez szükséges összes belső kommunikációt az infrastruktúrával.
- Előfordulás: gyakori

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati illesztő

#### <a name="addingremoving-network-interface"></a>Hálózati adapter hozzáadása/eltávolítása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

#### <a name="primary-network-interface"></a>Elsődleges hálózati adapter

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="next-hop-type"></a>Következő ugrás típusa

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon az útválasztási táblázat létrehozásakor **Virtual Network átjáró** a következő ugrási típusú beállítások egyike jelenik meg. Ez azonban Azure Stack hub esetében nem támogatott.
- Előfordulás: gyakori

#### <a name="alerts"></a>Riasztások

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, a létrehozáskor és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **kapcsolatok** panel egy **VPN-hibakereső**nevű funkciót mutat be. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban.
- Előfordulás: gyakori

### <a name="network-connection-type"></a>Hálózati kapcsolattípus

- Alkalmazható: a probléma bármely 1906-es vagy 1907-es környezetre érvényes. 
- Ok: a felhasználói portálon a **AddConnection** panel egy lehetőséget mutat be a **VNet – VNet**használatára. Ez a funkció jelenleg nem támogatott Azure Stack hub-ban. 
- Előfordulás: gyakori 

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack hub-on](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack hub-környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartomány rendelkezésre állási készletében, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack hub-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack hub frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizelés: virtuálisgép-hozzáférés használata a Linux-bővítményhez SSH-kulcsok implementálása a kiépítés után vagy jelszó alapú hitelesítés használatával.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>A virtuálisgép-méretezési csoport alaphelyzetbe állításának jelszava nem működik

- Alkalmazható: Ez a probléma a 1906-es és a 1907-as kiadásra vonatkozik.
- Ok: a méretezési csoport felhasználói felületén megjelenik egy új jelszó alaphelyzetbe állítása panel, de az Azure Stack hub még nem támogatja a jelszó visszaállítását egy méretezési csoporton.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Az esős felhő a méretezési csoport diagnosztikát

- Alkalmazható: Ez a probléma a 1906-es és a 1907-as kiadásra vonatkozik.
- Ok: a virtuálisgép-méretezési csoport áttekintő lapja egy üres diagramot jelenít meg. Ha az üres diagramra kattint, megnyílik az "esős felhő" panel. Ez a méretezési csoport diagnosztikai információinak, például a CPU százalékának a diagramja, és nem támogatott a jelenlegi Azure Stack hub-buildben.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="issues-creating-resources"></a>Erőforrások létrehozásával kapcsolatos problémák

- Alkalmazható: Ez a probléma az 1906-es kiadásra vonatkozik.
- Ok: a 1906-es ismert probléma az egyéni szerepkörökkel és az erőforrások létrehozásához szükséges engedélyek kiosztásával. Előfordulhat, hogy az erőforrások létrehozásakor problémák lépnek fel, még akkor is, ha rendelkezik a megfelelő engedélyekkel.
- Szervizelés: frissítsen a 1907-es verzióra a probléma enyhítése érdekében.
- Előfordulás: gyakori

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállításainak panelje

- Alkalmazható: Ez a probléma a 1906-es és a 1907-as kiadásra vonatkozik.    
- Ok: a virtuális gép diagnosztikai beállításainak paneljén van egy **fogadó lap, amely egy** alkalmazás-Insight- **fiókot**kér. Ez egy új panel eredménye, amely Azure Stack hub-ban még nem támogatott.
- Szervizelés: nincs.
- Előfordulás: gyakori

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1907"
## <a name="archive"></a>Archívum

Egy régebbi verzió archivált ismert problémáinak eléréséhez használja a bal oldalon a tartalomjegyzék legördülő menüjét, és válassza ki a megjeleníteni kívánt verziót.

## <a name="next-steps"></a>Következő lépések

- [A frissítési tevékenységre vonatkozó ellenőrzőlista áttekintése](release-notes-checklist.md)
- [Biztonsági frissítések listájának áttekintése](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1906"
## <a name="1906-archived-known-issues"></a>1906 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-known-issues"></a>1905 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-known-issues"></a>1904 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-known-issues"></a>1903 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-known-issues"></a>1902 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-known-issues"></a>1901 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-known-issues"></a>1811 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-known-issues"></a>1809 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-known-issues"></a>1808 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-known-issues"></a>1807 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-known-issues"></a>1805 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-known-issues"></a>1804 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-known-issues"></a>1803 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-known-issues"></a>1802 archivált ismert problémák
::: moniker-end

::: moniker range="<azs-1907"
[A Azure stack hub ismert problémáinak régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)érheti el. Ezek az archivált dokumentumok kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ az Azure Stack hub támogatásáról: [Azure stack hub karbantartási szabályzata](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.
::: moniker-end
