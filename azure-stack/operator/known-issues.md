---
title: Azure Stack hub ismert problémái
description: Ismerje meg Azure Stack hub-kiadások ismert problémáit.
author: sethmanheim
ms.topic: article
ms.date: 09/28/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 08/13/2020
ms.openlocfilehash: b52944255569197e9390db879f690f9e5d5a21d5
ms.sourcegitcommit: 703be61f2f1565bf478b8c184753869c29e5c33c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91495779"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack hub ismert problémái

Ez a cikk az Azure Stack hub kiadásában előforduló ismert problémákat sorolja fel. A lista új problémák észlelése esetén frissül.

Ha egy másik verzió ismert problémáit szeretné elérni, a bal oldali tartalomjegyzéknél válassza a verzió kiválasztása legördülő menüt.

::: moniker range=">=azs-1910"
> [!IMPORTANT]  
> A frissítés alkalmazása előtt tekintse át ezt a szakaszt.
::: moniker-end
::: moniker range="<azs-1910"
> [!IMPORTANT]  
> Ha a Azure Stack hub-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2005"
## <a name="update"></a>Frissítés

Az ismert Azure Stack hub-frissítési problémákkal kapcsolatban lásd: [frissítések hibaelhárítása Azure stack hub-ban](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózat

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

#### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Nem lehet törölni egy NSG, ha a hálózati adapterek nem csatlakoznak a futó virtuális géphez

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy NSG és egy futó virtuális géphez nem csatolt hálózati adapter társítása esetén az adott objektumhoz tartozó frissítési (PUT) művelet meghiúsul a hálózati vezérlő rétegében. A NSG a hálózati erőforrás-szolgáltató rétegben fogja frissíteni, de a hálózati vezérlőn nem, így a NSG sikertelen állapotba kerül.
- Szervizelés: csatolja a NSG társított hálózati adaptereket, amelyeket el kell távolítani a futó virtuális gépekkel, és meg kell szüntetnie a NSG, vagy el kell távolítania a NSG társított összes hálózati adaptert.
- Előfordulás: gyakori

#### <a name="denyalloutbound-rule-cannot-be-created"></a>DenyAllOutbound-szabály nem hozható létre

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gép létrehozása során nem hozható létre explicit **DenyAllOutbound** -szabály az internetre, mivel ez megakadályozza a virtuális gép telepítésének befejezéséhez szükséges kommunikációt a NSG.
- Szervizelés: engedélyezze a kimenő adatforgalmat a virtuális gépek létrehozásakor, és módosítsa a NSG, hogy letiltsa a szükséges forgalmat a virtuális gép létrehozása után.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjáró-termékváltozatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack hub-on](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)
  
### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer a forgalom átirányítása egy háttérbeli virtuális gépre adott helyzetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: Ha a **munkamenet-affinitást** egy terheléselosztó esetében engedélyezi, a 2 rekordos kivonat a PA IP-címet (fizikai cím IP) használja a virtuális gépekhez rendelt magánhálózati IP-címek helyett. Olyan esetekben, amikor a terheléselosztó felé irányuló forgalom egy VPN-en keresztül érkezik, vagy ha az összes ügyfél-virtuális gép (forrás IP-címe) ugyanazon a csomóponton található, és a munkamenet-affinitás is engedélyezve van, az összes forgalmat egyetlen háttérbeli virtuális gépre irányítja a rendszer.
- Előfordulás: gyakori

#### <a name="public-ip"></a>Nyilvános IP-cím

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy terheléselosztó **IdleTimeoutInMinutes** tartozó nyilvános IP-cím értéke nem módosítható. A művelet a nyilvános IP-címet sikertelen állapotba helyezi.
- Szervizelés: ahhoz, hogy a nyilvános IP-cím sikeres állapotba kerüljön, módosítsa a **IdleTimeoutInMinutes** értékét a nyilvános IP-címet az eredeti értékre hivatkozó terheléselosztó szabályban (az alapértelmezett érték 4 perc).
- Előfordulás: gyakori

## <a name="compute"></a>Compute

### <a name="issues-deploying-virtual-machine-scale-set-with-standard_ds2_v2-size-using-the-portal"></a>A virtuálisgép-méretezési csoport Standard_DS2_v2 mérettel való üzembe helyezésével kapcsolatos problémák a portál használatával

- Alkalmazható: Ez a probléma az 2005-es kiadásra vonatkozik.
- Ok: A portál hibája miatt a méretezési csoport létrehozása Standard_DS2_v2 mérettel meghiúsul.
- Szervizelés: a virtuálisgép-méretezési csoport VM-méretének üzembe helyezéséhez használja a PowerShellt vagy a CLI-t.

### <a name="issues-using-vm-extensions-in-ubuntu-server-2004"></a>A VM-bővítmények használata az Ubuntu Server 20,04-ben

- Alkalmazható: Ez a probléma az **Ubuntu Server 20,04 LTS**-re vonatkozik.
- Ok: egyes Linux-disztribúciók áttértek a Python 3,8-re, és eltávolította a `/usr/bin/python` teljes Python örökölt BelépésiPont. A Python 3. x verzióra váltott Linux-disztribúciós felhasználóknak biztosítaniuk kell a régi `/usr/bin/python` belépési pont létezését, mielőtt telepítené a bővítményeket a virtuális gépekre. Ellenkező esetben előfordulhat, hogy a bővítmény telepítése sikertelen lesz.
- Szervizelés: hajtsa végre a megoldás lépéseit a [Python 3-kompatibilis Linux Azure Virtual Machines rendszerekben a virtuálisgép-bővítmények használatával](/azure/virtual-machines/extensions/issues-using-vm-extensions-python-3) , de hagyja ki a 2. lépést, mert az Azure stack hub nem rendelkezik a **parancs futtatására** szolgáló funkcióval.

### <a name="nvv4-vm-size-on-portal"></a>NVv4 VM-méret a portálon

- Alkalmazható: a probléma a 2002-es és újabb verzióira vonatkozik.
- Ok: a virtuális gép létrehozási élménye során a virtuális gép méretét fogja látni: NV4as_v4. Azok az ügyfelek, akik rendelkeznek az AMD MI25-alapú Azure Stack hub GPU előzetes verziójához szükséges hardverrel, sikeresen üzembe helyezhetik a virtuális gépeket. Az összes többi ügyfélnél a virtuálisgép-mérettel rendelkező virtuális gép üzembe helyezése sikertelen lesz.
- Szervizelés: az Azure Stack hub GPU előzetes verziójának előkészítése alapján.

### <a name="consumed-compute-quota"></a>Felhasznált számítási kvóta

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új virtuális gép létrehozásakor előfordulhat, hogy az **előfizetés az ezen a helyen lévő összes regionális vCPU esetében egy hibaüzenetet kap. Ez az előfizetés az összes rendelkezésre álló, 50 teljes regionális vCPU használja.** Ez azt jelzi, hogy elérte az összes elérhető magok kvótáját.
- Szervizelés: kérje meg az operátort, hogy adjon meg egy kiegészítő kvótával rendelkező kiegészítő csomagot. Az aktuális csomag kvótájának szerkesztése nem fog működni, vagy nagyobb kvóta jelenik meg.
- Előfordulás: ritka

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>A virtuális gép áttekintő paneljén nem jelenik meg a számítógép helyes neve

- Alkalmazható: Ez a probléma minden kiadásra vonatkozik.
- Ok: Ha egy virtuális gép adatait tekinti meg az Áttekintés panelen, a számítógép neve **(nem érhető el)** jelenik meg. Ez a speciális lemezekből/lemez-pillanatképekről létrehozott virtuális gépek kialakításával történik, és a Piactéri rendszerképekhez is megjelenik.
- Szervizelés: Tekintse meg a **Tulajdonságok panelt** a **Beállítások**területen.

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack hub-környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartomány rendelkezésre állási készletében, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack hub-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack hub-telepítés frissítési folyamata során.

## <a name="storage"></a>Storage

### <a name="retention-period-reverts-to-0"></a>A megőrzési idő visszaállt 0-ra

- Alkalmazható: Ez a probléma a 2002-es és a 2005-es kiadásra vonatkozik.
- Ok: Ha nullától eltérő időszakot ad meg a megőrzési időtartam beállításban, a visszaállítja a 0 értékre (ez a beállítás alapértelmezett értéke) a 2002 vagy a 2005 frissítés során. A 0 nap beállítás a frissítés befejeződése után azonnal érvénybe lép, ami miatt az összes meglévő törölt Storage-fiók és a közelgő újonnan törölt tároló fiókok azonnal kimaradnak a megőrzéstől, és megjelölve az időszakos szemét-gyűjtésre (amely óránként fut).
- Szervizelés: manuálisan határozza meg a megőrzési időszakot a megfelelő időszakra. Minden olyan Storage-fiók, amely már be van gyűjtve az új megőrzési időszak megadása előtt, nem helyreállítható.  

## <a name="resource-providers"></a>Erőforrás-szolgáltatók

### <a name="sqlmysql"></a>SQL/MySQL

- Alkalmazható: Ez a probléma a 2002-es kiadásra vonatkozik.
- Ok: Ha a bélyegző az SQL Resource Provider (RP) 1.1.33.0 vagy korábbi verzióját tartalmazza, akkor a bélyegző frissítése után az SQL/MySQL-hez tartozó pengék nem töltődnek be.
- Szervizelés: frissítse az RP-et a verzió 1.1.47.0.

### <a name="app-service"></a>App Service

- Alkalmazható: Ez a probléma a 2002-es kiadásra vonatkozik.
- Ok: Ha a bélyegző App Service erőforrás-szolgáltató (RP) 1,7-es és régebbi verzióját tartalmazza, a bélyegző frissítése után a App Servicehoz tartozó pengék nem töltődnek be.
- Szervizelés: frissítse az RP-t a 2002-es verzióra.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2002"
## <a name="update"></a>Frissítés

A 2002-es frissítés alkalmazása után az „érvénytelen időforrással” kapcsolatos riasztás helytelenül jelenhet meg a felügyeleti portálon. Ez a vakriasztás figyelmen kívül hagyható, egy hamarosan megjelenő kiadásban ki lesz javítva. 

Az ismert Azure Stack hub-frissítési problémákkal kapcsolatban lásd: [frissítések hibaelhárítása Azure stack hub-ban](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

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

- Alkalmazható: a probléma a 1908-es és újabb verzióira vonatkozik.
- Ok: Ha egy kábel le van választva egy hálózati adapterről, a riasztás nem jelenik meg a felügyeleti portálon. Ezt a problémát az okozza, hogy ez a hiba a Windows Server 2019 rendszerben alapértelmezés szerint le van tiltva.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózat

### <a name="denyalloutbound-rule-cannot-be-created"></a>DenyAllOutbound-szabály nem hozható létre

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gép létrehozása során nem hozható létre explicit **DenyAllOutbound** -szabály az internetre, mivel ez megakadályozza a virtuális gép telepítésének befejezéséhez szükséges kommunikációt a NSG.
- Szervizelés: engedélyezze a kimenő adatforgalmat a virtuális gépek létrehozásakor, és módosítsa a NSG, hogy letiltsa a szükséges forgalmat a virtuális gép létrehozása után.
- Előfordulás: gyakori

### <a name="icmp-protocol-not-supported-for-nsg-rules"></a>NSG-szabályok nem támogatják az ICMP protokollt

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: bejövő vagy kimenő hálózati biztonsági szabály létrehozásakor a **protokoll** beállítás egy **ICMP** -beállítást mutat be. Ez Azure Stack hub esetében jelenleg nem támogatott. Ez a probléma rögzített, és nem fog megjelenni a következő Azure Stack hub-kiadásban.
- Előfordulás: gyakori

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Nem lehet törölni egy NSG, ha a hálózati adapterek nem csatlakoznak a futó virtuális géphez

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy NSG és egy futó virtuális géphez nem csatolt hálózati adapter társítása esetén az adott objektumhoz tartozó frissítési (PUT) művelet meghiúsul a hálózati vezérlő rétegében. A NSG a hálózati erőforrás-szolgáltató rétegben fogja frissíteni, de a hálózati vezérlőn nem, így a NSG sikertelen állapotba kerül.
- Szervizelés: csatolja a NSG társított hálózati adaptereket, amelyeket el kell távolítani a futó virtuális gépekkel, és meg kell szüntetnie a NSG, vagy el kell távolítania a NSG társított összes hálózati adaptert.
- Előfordulás: gyakori

### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer a forgalom átirányítása egy háttérbeli virtuális gépre adott helyzetekben 

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: Ha a **munkamenet-affinitást** egy terheléselosztó esetében engedélyezi, a 2 rekordos kivonat a PA IP-címet (fizikai cím IP) használja a virtuális gépekhez rendelt magánhálózati IP-címek helyett. Olyan esetekben, amikor a terheléselosztó felé irányuló forgalom egy VPN-en keresztül érkezik, vagy ha az összes ügyfél-virtuális gép (forrás IP-címe) ugyanazon a csomóponton található, és a munkamenet-affinitás is engedélyezve van, az összes forgalmat egyetlen háttérbeli virtuális gépre irányítja a rendszer.
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

### <a name="public-ip"></a>Nyilvános IP-cím

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy terheléselosztó **IdleTimeoutInMinutes** tartozó nyilvános IP-cím értéke nem módosítható. A művelet a nyilvános IP-címet sikertelen állapotba helyezi.
- Szervizelés: ahhoz, hogy a nyilvános IP-cím sikeres állapotba kerüljön, módosítsa a **IdleTimeoutInMinutes** értékét a nyilvános IP-címet az eredeti értékre hivatkozó terheléselosztó szabályban (az alapértelmezett érték 4 perc).
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjáró-termékváltozatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack hub-on](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="cannot-create-a-virtual-machine-scale-set-with-standard_ds2_v2-vm-size-on-portal"></a>Nem hozható létre virtuálisgép-méretezési csoport Standard_DS2_v2 virtuális gép méretével a portálon

- Alkalmazható: Ez a probléma az 2002-es kiadásra vonatkozik.
- Ok: van egy portál hibája, amely megakadályozza a virtuálisgép-méretezési csoport létrehozását a Standard_DS2_v2 virtuális gép méretével. Ha létrehoz egy hibát, a (z) "{" code ":" DeploymentFailed ", az" üzenet ":" legalább egy erőforrás-telepítési művelet sikertelen volt. A részletekért listázza az üzembehelyezési műveleteket. Tekintse https://aka.ms/arm-debug meg a használati adatokat. "," Részletek ": [{" code ":" BadRequest "," üzenet ":" {\r\n \" hiba \" : {\R\n \" kód \" : \" NetworkProfileValidationError \" , \r\n \" üzenet: a \" \" virtuális gép mérete Standard_DS2_v2 nem szerepel a VM-méretezési csoport/Subscriptions/x/resourceGroups/RGVMSS/Providers/Microsoft.Compute/virtualMachineScaleSets/vmss. Megengedett méretek:. \" \r\n} \r\n} "}]}"
- Szervizelés: virtuálisgép-méretezési csoport létrehozása PowerShell-lel vagy Resource Manager-sablonnal.

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>A virtuális gép áttekintő paneljén nem jelenik meg a számítógép helyes neve

- Alkalmazható: Ez a probléma minden kiadásra vonatkozik.
- Ok: Ha egy virtuális gép adatait tekinti meg az Áttekintés panelen, a számítógép neve **(nem érhető el)** jelenik meg. Ez a speciális lemezekből/lemez-pillanatképekről létrehozott virtuális gépek kialakításával történik, és a Piactéri rendszerképekhez is megjelenik.
- Szervizelés: Tekintse meg a **Tulajdonságok panelt** a **Beállítások**területen.

### <a name="nvv4-vm-size-on-portal"></a>NVv4 VM-méret a portálon

- Alkalmazható: Ez a probléma a 2002-es és újabb kiadásokra vonatkozik.
- Ok: a virtuális gép létrehozási élménye során a virtuális gép méretét fogja látni: NV4as_v4. Azok az ügyfelek, akik rendelkeznek az AMD MI25-alapú Azure Stack hub GPU előzetes verziójához szükséges hardverrel, sikeresen üzembe helyezhetik a virtuális gépeket. Az összes többi ügyfélnél a virtuálisgép-mérettel rendelkező virtuális gép üzembe helyezése sikertelen lesz.
- Szervizelés: az Azure Stack hub GPU előzetes verziójának előkészítése alapján.

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új virtuális gép (VM) létrehozásakor a következő hiba jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

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

## <a name="storage"></a>Storage

### <a name="retention-period-revert-to-0"></a>Megőrzési időszak visszavonása 0-ra

- Alkalmazható: Ez a probléma a 2002-es és a 2005-es kiadásra vonatkozik.
- Ok: Ha korábban a 0 értéktől eltérő időszakot adott meg a megőrzési időtartam beállításában, a rendszer visszaállítja a 0 értéket (ez a beállítás alapértelmezett értéke) a 2002 és a 2005 frissítés során. És a 0 nap beállítása a frissítés befejeződése után is érvénybe immdiately, így az összes meglévő törölt Storage-fiók és a közelgő újonnan törölt Storage-fiókok azonnal nem maradnak meg a megőrzéstől, és meg vannak jelölve az időszakos szemét-gyűjtésre (amely óránként fut). 
- Szervizelés: manuálisan határozza meg a megőrzési időszakot a megfelelő időszakra. Azonban az új megőrzési időszak megadását megelőzően az összes Storage-fiók már be van gyűjtve.  

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

Az ismert Azure Stack hub-frissítési problémákkal kapcsolatban lásd: [frissítések hibaelhárítása Azure stack hub-ban](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

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
- Szervizelés: Ellenőrizze, hogy az előfizetés be van-e jelölve a **címtár + előfizetés** menüben. A menü a portál felső részén, az **Értesítések** gomb közelében található, vagy elérhető az **Összes erőforrás** panelen elhelyezett parancsikonon keresztül is. A panelen a következő jelenik meg: **Nem talál egy előfizetést? Nyissa meg a Címtár + Előfizetések beállítása elemet**. Az előfizetést ebben a menüben tudja kiválasztani.

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

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon megjelenik a **VPN-** átjáró erőforrás-kezelési funkciója és **mérőszámai** , azonban ez Azure stack hub esetében nem támogatott.
- Előfordulás: gyakori

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjáró-termékváltozatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
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

::: moniker range=">=azs-1910"
## <a name="archive"></a>Archívum

Egy régebbi verzió archivált ismert problémáinak eléréséhez használja a bal oldalon a tartalomjegyzék legördülő menüjét, és válassza ki a megjeleníteni kívánt verziót.

## <a name="next-steps"></a>További lépések

- [A frissítési tevékenységre vonatkozó ellenőrzőlista áttekintése](release-notes-checklist.md)
- [Biztonsági frissítések listájának áttekintése](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1908"
## <a name="1908-archived-known-issues"></a>1908 archivált ismert problémák
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-known-issues"></a>1907 archivált ismert problémák
::: moniker-end
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

::: moniker range="<azs-1910"
[A Azure stack hub ismert problémáinak régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)érheti el. Ezek az archivált dokumentumok kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ az Azure Stack hub támogatásáról: [Azure stack hub karbantartási szabályzata](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.
::: moniker-end
