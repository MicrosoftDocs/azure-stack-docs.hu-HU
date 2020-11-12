---
title: Azure Stack hub ismert problémái
description: Ismerje meg Azure Stack hub-kiadások ismert problémáit.
author: sethmanheim
ms.topic: article
ms.date: 11/11/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: f1f38d309814e40422783cd4903086c736d19978
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545774"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack hub ismert problémái

Ez a cikk Azure Stack hub-kiadások ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

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

::: moniker range="azs-2008"
## <a name="update"></a>Frissítés

Az ismert Azure Stack hub-frissítési problémákkal kapcsolatban lásd: [frissítések hibaelhárítása Azure stack hub-ban](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>A frissítés nem tudta telepíteni a Microsoft. AzureStack. számítás. Installer csomagot a CA virtuális gépre.

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a frissítés során egy folyamat zárolja az új tartalmat, amelyet a CA virtuális gépre kell másolni. Ha a frissítés sikertelen, a zárolás fel lesz szabadítva.
- Szervizelés: a frissítés folytatása.
- Előfordulás: ritka

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózatkezelés

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

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

## <a name="compute"></a>Compute

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2005"
## <a name="update"></a>Frissítés

Az ismert Azure Stack hub-frissítési problémákkal kapcsolatban lásd: [frissítések hibaelhárítása Azure stack hub-ban](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>A frissítés nem tudta telepíteni a Microsoft. AzureStack. számítás. Installer csomagot a CA virtuális gépre.

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a frissítés során egy folyamat zárolja az új tartalmat, amelyet a CA virtuális gépre kell másolni. Ha a frissítés sikertelen, a zárolás fel lesz szabadítva.
- Szervizelés: a frissítés folytatása.
- Előfordulás: ritka

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózatkezelés

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

- Alkalmazható: Ez a probléma az **Ubuntu Server 20,04 LTS** -re vonatkozik.
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
- Szervizelés: Tekintse meg a **Tulajdonságok panelt** a **Beállítások** területen.

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

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>A frissítés nem tudta telepíteni a Microsoft. AzureStack. számítás. Installer csomagot a CA virtuális gépre.

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a frissítés során egy folyamat zárolja az új tartalmat, amelyet a CA virtuális gépre kell másolni. Ha a frissítés sikertelen, a zárolás fel lesz szabadítva.
- Szervizelés: a frissítés folytatása.
- Előfordulás: ritka

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
- Ok: a felhasználói portálon a Storage-fiókok neve **Storage-fiókként (blob, fájl, tábla, üzenetsor** ) jelenik meg. Azure Stack hub esetében azonban a **fájl** nem támogatott.
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

## <a name="networking"></a>Hálózatkezelés

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
- Szervizelés: Tekintse meg a **Tulajdonságok panelt** a **Beállítások** területen.

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

::: moniker range=">=azs-2002"
## <a name="archive"></a>Archívum

Egy régebbi verzió archivált ismert problémáinak eléréséhez használja a bal oldalon a tartalomjegyzék legördülő menüjét, és válassza ki a megjeleníteni kívánt verziót.

## <a name="next-steps"></a>Következő lépések

- [A frissítési tevékenységre vonatkozó ellenőrzőlista áttekintése](release-notes-checklist.md)
- [Biztonsági frissítések listájának áttekintése](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-known-issues"></a>1910 archivált ismert problémák
::: moniker-end
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

::: moniker range="<azs-2002"
[A Azure stack hub ismert problémáinak régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)érheti el. Ezek az archivált dokumentumok kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ az Azure Stack hub támogatásáról: [Azure stack hub karbantartási szabályzata](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.
::: moniker-end
