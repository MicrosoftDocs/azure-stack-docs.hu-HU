---
title: Azure Stack ismert problémák
description: Ismerkedjen meg Azure Stack kiadásokkal kapcsolatos ismert problémákkal.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 09/13/2019
ms.openlocfilehash: ba5b8358dced378f499c30f9b8d409497319fc86
ms.sourcegitcommit: 2c37ac8e88de19430080128bac1b70e33557d354
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71164238"
---
# <a name="azure-stack-known-issues"></a>Azure Stack ismert problémák

Ez a cikk a Azure Stack kiadásának ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

Ha egy másik verzió ismert problémáit szeretné elérni, a bal oldali tartalomjegyzéknél válassza a verzió kiválasztása legördülő menüt.

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> A frissítés alkalmazása előtt tekintse át ezt a szakaszt.
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> Ha a Azure Stack-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>1908 frissítési folyamat

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
- Szervizkiszolgáló A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy a [frissítések telepítése szakaszt](azure-stack-apply-updates.md#install-updates-and-monitor-progress)követve manuálisan töltse fel a frissítési csomagot.
- Előfordulása Közös

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizkiszolgáló Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulása Közös

### <a name="subscriptions-properties-blade"></a>Előfizetések tulajdonságai panel

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felügyeleti portálon az előfizetések **tulajdonságai panel** nem töltődik be megfelelően
- Szervizkiszolgáló Ezeket az előfizetéseket az **előfizetések áttekintése** panel **Essentials (alapvető** erőforrások) paneljén tekintheti meg.
- Előfordulása Közös

### <a name="subscriptions-lock-blade"></a>Előfizetések zárolása panel

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felügyeleti portálon a felhasználói előfizetések **zárolási** paneljének két butons van, amelyek az **előfizetést**mondják.
- Előfordulása Közös

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez.
- Szervizkiszolgáló [Az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulása Közös

### <a name="storage-account-settings"></a>Tárfiók-beállítások

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon, a Storage-fiók **konfigurációja** panelen megjelenik egy lehetőség a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

### <a name="upload-blob"></a>Blob feltöltése

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizkiszolgáló Töltse fel a blobot az SAS kapcsoló használatával.
- Előfordulása Közös

## <a name="networking"></a>Hálózat

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

### <a name="network-interface"></a>Hálózati adapter

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizkiszolgáló A hálózati adapter hozzáadása/eltávolítása előtt állítsa le a virtuális gépet.
- Előfordulása Közös

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="local-network-gateway-deletion"></a>Helyi hálózati átjáró törlése

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **helyi hálózati átjáró** törlése a következő hibaüzenetet jeleníti meg: **Aktív kapcsolatban álló helyi hálózati átjáró nem törölhető**, még akkor is, ha nincs aktív internetkapcsolat.
- Kockázatcsökkentő A probléma megoldása a 1907-es kiadásban jelenik meg. A probléma megkerülő megoldásként új helyi hálózati átjárót hoz létre ugyanazzal az IP-címmel, a címtartomány és a konfigurációs adatokkal egy másik névvel. A régi LNG törölhető, ha a környezet frissítve lett a 1907-es verziójára.
- Előfordulása Közös

#### <a name="alerts"></a>Riasztások

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon, és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portál **kapcsolatok** paneljén egy **VPN-hibakereső**nevű funkció látható. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A dokumentációs hivatkozásokat Virtual Network átjáró áttekintő oldalán, Azure Stack helyett az Azure-specifikus dokumentációra mutató hivatkozás. Használja az alábbi hivatkozásokat az Azure Stack dokumentációhoz:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-áramkörök](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **Nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: Nem sikerült frissíteni a virtuális gép (VM-név**) soros kimeneti beállításait. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizkiszolgáló Hozza létre újra a korábban használt nevű Storage-fiókot.
- Előfordulása Közös

### <a name="virtual-machine-scale-set"></a>Virtuális gép méretkészlete

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A virtuális gépeket 3 tartalék tartományba tartozó rendelkezésre állási csoportba kell létrehozni, és a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack környezet frissítési folyamata során.
- Szervizkiszolgáló Egy rendelkezésre állási csoportba egyetlen virtuális gépet hozhat létre, amelynek 2 tartalék tartománya sikeresen megtörtént. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizkiszolgáló A Linux-bővítményhez használja a VM-hozzáférést, hogy SSH-kulcsokat implementáljon a kiépítés után, vagy használjon jelszó alapú hitelesítést.
- Előfordulása Közös

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>A virtuálisgép-méretezési csoport alaphelyzetbe állításának jelszava nem működik

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A méretezési csoport felhasználói felületén megjelenik egy új jelszó alaphelyzetbe állítása panel, de Azure Stack a méretezési csoportokban még nem támogatja a jelszó visszaállítását.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Az esős felhő a méretezési csoport diagnosztikát

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A virtuálisgép-méretezési csoport áttekintő lapja egy üres diagramot jelenít meg. Ha az üres diagramra kattint, megnyílik az "esős felhő" panel. Ez a méretezési csoport diagnosztikai információinak, például a CPU százalékának a diagramja, amely nem támogatott a jelenlegi Azure Stack buildben.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállításainak panelje

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.    
- Ok: A virtuális gép diagnosztikai beállításainak paneljén egy **fogadó lap van, amely egy** **alkalmazás-Insight-fiókot**kér. Ez egy új panel eredménye, amely Azure Stackban még nem támogatott.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>1907 frissítési folyamat

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az 1907 Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
- Szervizkiszolgáló A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy manuálisan töltse fel a frissítési csomagot a [frissítések importálása és telepítése szakasz](azure-stack-apply-updates.md)alapján.
- Előfordulása Közös

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizkiszolgáló Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulása Közös

### <a name="subscriptions-properties-blade"></a>Előfizetések tulajdonságai panel

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felügyeleti portálon az előfizetések **tulajdonságai panel** nem töltődik be megfelelően
- Szervizkiszolgáló Ezeket az előfizetéseket az **előfizetések áttekintése** panel **Essentials (alapvető** erőforrások) paneljén tekintheti meg.
- Előfordulása Közös

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez.
- Szervizkiszolgáló [Az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulása Közös

### <a name="storage-account-settings"></a>Tárfiók-beállítások

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon, a Storage-fiók **konfigurációja** panelen megjelenik egy lehetőség a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

### <a name="upload-blob"></a>Blob feltöltése

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizkiszolgáló Töltse fel a blobot az SAS kapcsoló használatával.
- Előfordulása Közös

## <a name="networking"></a>Hálózat

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

### <a name="network-interface"></a>Hálózati adapter

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizkiszolgáló A hálózati adapter hozzáadása/eltávolítása előtt állítsa le a virtuális gépet.
- Előfordulása Közös

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="alerts"></a>Riasztások

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon, és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portál **kapcsolatok** paneljén egy **VPN-hibakereső**nevű funkció látható. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

### <a name="network-connection-type"></a>Hálózati kapcsolattípus

- Alkalmazható Ez a probléma a 1906-es vagy a 1907-es környezetre vonatkozik. 
- Ok: A felhasználói portálon a **AddConnection** panel egy lehetőséget mutat be a **VNet – VNet**használatára. Ez a funkció jelenleg nem támogatott Azure Stackban. 
- Előfordulása Közös 

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A dokumentációs hivatkozásokat Virtual Network átjáró áttekintő oldalán, Azure Stack helyett az Azure-specifikus dokumentációra mutató hivatkozás. Használja az alábbi hivatkozásokat az Azure Stack dokumentációhoz:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-áramkörök](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **Nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: Nem sikerült frissíteni a virtuális gép (VM-név**) soros kimeneti beállításait. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizkiszolgáló Hozza létre újra a korábban használt nevű Storage-fiókot.
- Előfordulása Közös

### <a name="virtual-machine-scale-set"></a>Virtuális gép méretkészlete

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A virtuális gépeket 3 tartalék tartományba tartozó rendelkezésre állási csoportba kell létrehozni, és a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack környezet frissítési folyamata során.
- Szervizkiszolgáló Egy rendelkezésre állási csoportba egyetlen virtuális gépet hozhat létre, amelynek 2 tartalék tartománya sikeresen megtörtént. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizkiszolgáló A Linux-bővítményhez használja a VM-hozzáférést, hogy SSH-kulcsokat implementáljon a kiépítés után, vagy használjon jelszó alapú hitelesítést.
- Előfordulása Közös

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>A virtuálisgép-méretezési csoport alaphelyzetbe állításának jelszava nem működik

- Alkalmazható Ez a probléma a 1906-es és a 1907-es kiadásra vonatkozik.
- Ok: A méretezési csoport felhasználói felületén megjelenik egy új jelszó alaphelyzetbe állítása panel, de Azure Stack a méretezési csoportokban még nem támogatja a jelszó visszaállítását.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Az esős felhő a méretezési csoport diagnosztikát

- Alkalmazható Ez a probléma a 1906-es és a 1907-es kiadásra vonatkozik.
- Ok: A virtuálisgép-méretezési csoport áttekintő lapja egy üres diagramot jelenít meg. Ha az üres diagramra kattint, megnyílik az "esős felhő" panel. Ez a méretezési csoport diagnosztikai információinak, például a CPU százalékának a diagramja, amely nem támogatott a jelenlegi Azure Stack buildben.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállításainak panelje

- Alkalmazható Ez a probléma a 1906-es és a 1907-es kiadásra vonatkozik.    
- Ok: A virtuális gép diagnosztikai beállításainak paneljén egy **fogadó lap van, amely egy** **alkalmazás-Insight-fiókot**kér. Ez egy új panel eredménye, amely Azure Stackban még nem támogatott.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>1906 frissítési folyamat

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az 1906 Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz. 
- Szervizkiszolgáló A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy manuálisan töltse fel a frissítési csomagot a [frissítések importálása és telepítése szakasz](azure-stack-apply-updates.md)alapján.
- Előfordulása Közös

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizkiszolgáló Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulása Közös

### <a name="subscription-resources"></a>Előfizetés erőforrásai

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Felhasználói előfizetések eredmények az árva erőforrások törlése.
- Szervizkiszolgáló Először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.
- Előfordulása Közös

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez.
- Szervizkiszolgáló [Az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulása Közös

### <a name="subscriptions-properties-blade"></a>Előfizetések tulajdonságai panel
- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felügyeleti portálon az előfizetések **tulajdonságai panel** nem töltődik be megfelelően
- Szervizkiszolgáló Ezek az előfizetések tulajdonságai az előfizetések áttekintése panel Essentials (alapvető erőforrások) paneljén tekinthetők meg.
- Előfordulása Közös

### <a name="storage-account-settings"></a>Tárfiók-beállítások

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon, a Storage-fiók **konfigurációja** panelen megjelenik egy lehetőség a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

### <a name="upload-blob"></a>Blob feltöltése

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizkiszolgáló Töltse fel a blobot az SAS kapcsoló használatával.
- Előfordulása Közös

### <a name="update"></a>frissítés

- Alkalmazható Ez a probléma a 1906 kiadásra vonatkozik.
- Ok: Az operátori portálon a gyorsjavítás állapotának frissítése helytelen állapotot mutat a frissítéshez. A kezdeti állapot azt jelzi, hogy a frissítést nem sikerült telepíteni, még akkor is, ha még folyamatban van.
- Szervizkiszolgáló Frissítse a portált, és az állapota "folyamatban" állapotba kerül.
- Előfordulása Időszakos

## <a name="networking"></a>Hálózat

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

### <a name="network-interface"></a>Hálózati adapter

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizkiszolgáló A hálózati adapter hozzáadása/eltávolítása előtt állítsa le a virtuális gépet.
- Előfordulása Közös

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="alerts"></a>Riasztások

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon, és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portál **kapcsolatok** paneljén egy **VPN-hibakereső**nevű funkció látható. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulása Közös

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A dokumentációs hivatkozásokat Virtual Network átjáró áttekintő oldalán, Azure Stack helyett az Azure-specifikus dokumentációra mutató hivatkozás. Használja az alábbi hivatkozásokat az Azure Stack dokumentációhoz:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-áramkörök](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Terheléselosztó

#### <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon egy **háttér-készletet** próbál hozzáadni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem tudta frissíteni a Load Balancer.** ...
- Szervizkiszolgáló A PowerShell, a CLI vagy a Resource Manager-sablon használatával társítsa a háttér-készletet egy terheléselosztó-erőforráshoz.
- Előfordulása Közös

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon egy **Load Balancer** **bejövő NAT-szabályát** kísérli meg létrehozni, a művelet meghiúsul, és a hibaüzenet **nem frissül Load Balancer.** ..
- Szervizkiszolgáló A PowerShell, a CLI vagy a Resource Manager-sablon használatával társítsa a háttér-készletet egy terheléselosztó-erőforráshoz.
- Előfordulása Közös

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **Nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: Nem sikerült frissíteni a virtuális gép (VM-név**) soros kimeneti beállításait. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizkiszolgáló Hozza létre újra a korábban használt nevű Storage-fiókot.
- Előfordulása Közös

### <a name="virtual-machine-scale-set"></a>Virtuális gép méretkészlete


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A virtuális gépeket 3 tartalék tartományba tartozó rendelkezésre állási csoportba kell létrehozni, és a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack környezet frissítési folyamata során.
- Szervizkiszolgáló Egy rendelkezésre állási csoportba egyetlen virtuális gépet hozhat létre, amelynek 2 tartalék tartománya sikeresen megtörtént. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizkiszolgáló A Linux-bővítményhez használja a VM-hozzáférést, hogy SSH-kulcsokat implementáljon a kiépítés után, vagy használjon jelszó alapú hitelesítést.
- Előfordulása Közös

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>A virtuálisgép-méretezési csoport alaphelyzetbe állításának jelszava nem működik

- Alkalmazható Ez a probléma a 1906 kiadásra vonatkozik.
- Ok: A méretezési csoport felhasználói felületén megjelenik egy új jelszó alaphelyzetbe állítása panel, de Azure Stack a méretezési csoportokban még nem támogatja a jelszó visszaállítását.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Az esős felhő a méretezési csoport diagnosztikát

- Alkalmazható Ez a probléma a 1906 kiadásra vonatkozik.
- Ok: A virtuálisgép-méretezési csoport áttekintő lapja egy üres diagramot jelenít meg. Ha az üres diagramra kattint, megnyílik az "esős felhő" panel. Ez a méretezési csoport diagnosztikai információinak, például a CPU százalékának a diagramja, amely nem támogatott a jelenlegi Azure Stack buildben.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállításainak panelje

- Alkalmazható Ez a probléma a 1906 kiadásra vonatkozik.
- Ok: A virtuális gép diagnosztikai beállításainak paneljén egy **fogadó lap van, amely egy** **alkalmazás-Insight-fiókot**kér. Ez egy új panel eredménye, amely Azure Stackban még nem támogatott.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-update-process"></a>1905 frissítési folyamat

### <a name="host-node-update-prerequisite-failure"></a>Gazdagép-csomópont frissítésének előfeltételi hibája

- Alkalmazható Ez a probléma az 1905-es frissítésre vonatkozik.
- Ok: Az 1905 Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota a **gazdagép csomópontjának frissítésének előfeltétele**miatt meghiúsul. Ezt általában egy olyan gazda-csomópont okozza, amely nem rendelkezik elegendő szabad lemezterülettel.
- Szervizkiszolgáló Forduljon Azure Stack támogatási szolgálathoz, és kérjen segítséget a gazdagép-csomóponton található lemezterület törléséhez.
- Előfordulása Nem gyakori

### <a name="preparation-failed"></a>Az előkészítés sikertelen

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az 1905 Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz. Az 1905-es frissítési csomag nagyobb, mint az előző frissítési csomagok, amelyek nagyobb valószínűséggel tehetik ezt a problémát.
- Szervizkiszolgáló A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy manuálisan töltse fel a frissítési csomagot a [frissítések importálása és telepítése szakasz](azure-stack-apply-updates.md)alapján.
- Előfordulása Közös

## <a name="portal"></a>Portál

### <a name="subscription-resources"></a>Előfizetés erőforrásai

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Felhasználói előfizetések eredmények az árva erőforrások törlése.
- Szervizkiszolgáló Először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.
- Előfordulása Közös

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez.
- Szervizkiszolgáló [Az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulása Közös

### <a name="marketplace-management"></a>Piactér-kezelés

- Alkalmazható Ez a probléma a 1904 és a 1905
- Ok: A piactér-felügyeleti képernyő nem látható, ha bejelentkezik a felügyeleti portálra.
- Szervizkiszolgáló Frissítse a böngészőt, vagy lépjen a **Beállítások** elemre, és válassza az **Alaphelyzetbe állítás lehetőséget az alapértelmezett beállítások**lehetőségnél.
- Előfordulása Időszakos

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a **Docker**-kereséshez a rendszergazda és a felhasználói portálon is keres, az elem helytelenül lesz visszaadva. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
- Szervizkiszolgáló Nincs megoldás.
- Előfordulása Közös

### <a name="upload-blob"></a>Blob feltöltése

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizkiszolgáló Töltse fel a blobot az SAS kapcsoló használatával.
- Előfordulása Közös

### <a name="template"></a>Sablon

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a sablon központi telepítési felhasználói felülete nem tölti fel a (z) _ karakterrel kezdődő nevű sablon paramétereit (aláhúzásjel).
- Szervizkiszolgáló Távolítsa el a "_" (aláhúzás) karaktert a sablon nevéből.
- Előfordulása Közös

## <a name="networking"></a>Hálózat

### <a name="load-balancer"></a>Terheléselosztó

#### <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon egy **háttér-készletet** próbál hozzáadni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem tudta frissíteni a Load Balancer.** ...
- Szervizkiszolgáló A PowerShell, a CLI vagy a Resource Manager-sablon használatával társítsa a háttér-készletet egy terheléselosztó-erőforráshoz.
- Előfordulása Közös

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon egy **Load Balancer** **bejövő NAT-szabályát** kísérli meg létrehozni, a művelet meghiúsul, és a hibaüzenet **nem frissül Load Balancer.** ..
- Szervizkiszolgáló A PowerShell, a CLI vagy a Resource Manager-sablon használatával társítsa a háttér-készletet egy terheléselosztó-erőforráshoz.
- Előfordulása Közös

#### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon, a **létrehozás Load Balancer** ablakban a **standard** Load Balancer SKU létrehozási lehetősége látható. Ez a beállítás Azure Stack esetén nem támogatott.
- Szervizkiszolgáló Ehelyett használja az **alapszintű** Load Balancer beállításait.
- Előfordulása Közös

### <a name="public-ip-address"></a>Nyilvános IP-cím

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **nyilvános IP-cím létrehozása** ablakban egy **szabványos** SKU létrehozására szolgáló lehetőség látható. Azure Stack nem támogatja a **standard** SKU-t.
- Szervizkiszolgáló Használja az **alapszintű** SKU-t a nyilvános IP-címhez.
- Előfordulása Közös

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **Nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: Nem sikerült frissíteni a virtuális gép (VM-név**) soros kimeneti beállításait.
A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizkiszolgáló Hozza létre újra a korábban használt nevű Storage-fiókot.
- Előfordulása Közös

### <a name="vm-resize"></a>Virtuális gép átméretezése

- Alkalmazható Ez a probléma a 1905 kiadásra vonatkozik.
- Ok: Nem sikerült átméretezni a felügyelt lemezes virtuális gépet. A virtuális gép átméretezésére irányuló kísérlet hibát jelez a következő kóddal: "InternalOperationError", "üzenet": "Belső hiba történt a művelet során."
- Szervizkiszolgáló A következő kiadásban dolgozunk. Jelenleg újra létre kell hoznia a virtuális gépet az új virtuálisgép-mérettel.
- Előfordulása Közös

### <a name="virtual-machine-scale-set"></a>Virtuális gép méretkészlete

#### <a name="centos"></a>CentOS

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A virtuálisgép-méretezési csoport létrehozási felülete a CentOS-alapú 7,2-es lehetőséget kínálja az üzembe helyezéshez. A CentOS 7,2 Azure Stack piactéren nem érhető el, ezért a telepítési hibák miatt a rendszer nem találja a rendszerképet.
- Szervizkiszolgáló Válasszon másik operációs rendszert a központi telepítéshez, vagy használjon egy Azure Resource Manager sablont, amely egy másik, a piactéren az operátor általi üzembe helyezés előtt letöltött CentOS-rendszerképet ad meg.
- Előfordulása Közös

#### <a name="remove-scale-set"></a>Méretezési csoport eltávolítása

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A méretezési csoportokat nem lehet eltávolítani a **virtuálisgép-méretezési** csoportok paneljéről.
- Szervizkiszolgáló Válassza ki az eltávolítani kívánt méretezési készletet, majd kattintson a **Törlés** gombra az **Áttekintés** ablaktáblán.
- Előfordulása Közös

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A virtuális gépeket 3 tartalék tartományba tartozó rendelkezésre állási csoportba kell létrehozni, és a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack környezet frissítési folyamata során.
- Szervizkiszolgáló Egy rendelkezésre állási csoportba egyetlen virtuális gépet hozhat létre, amelynek 2 tartalék tartománya sikeresen megtörtént. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>A méretezési csoport példányainak nézete nem töltődik be

- Alkalmazható Ez a probléma a 1904-es és a 1905-es kiadásra vonatkozik.
- Ok: Egy virtuálisgép-méretezési csoport példány nézetének panelje, amely a Azure Stack Portal-> irányítópultján található – > virtuálisgép-méretezési csoportok – > AnyScaleSet-instances – > AnyScaleSetInstance nem töltődik be, és egy síró Felhőbeli képet jelenít meg.
- Szervizkiszolgáló Jelenleg nincs szervizelés, és a javításon dolgozunk. Addig is használja a CLI-parancsot `az vmss get-instance-view` egy méretezési csoport példány nézetének lekéréséhez.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizkiszolgáló A Linux-bővítményhez használja a VM-hozzáférést, hogy SSH-kulcsokat implementáljon a kiépítés után, vagy használjon jelszó alapú hitelesítést.
- Előfordulása Közös

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="archive"></a>Archívum

Egy régebbi verzió archivált ismert problémáinak eléréséhez használja a bal oldalon a tartalomjegyzék legördülő menüjét, és válassza ki a megjeleníteni kívánt verziót.

## <a name="next-steps"></a>További lépések

- [A frissítési tevékenységre vonatkozó ellenőrzőlista áttekintése](release-notes-checklist.md)
- [Biztonsági frissítések listájának áttekintése](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1905"
[Azure stack ismert problémák régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)érheti el. Ezek az archivált dokumentumok kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ a Azure Stack támogatásáról: [Azure stack karbantartási szabályzat](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.
::: moniker-end