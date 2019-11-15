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
ms.openlocfilehash: 367a2cb5a07c0db2b272b8ffc951f51e5ed9cc0e
ms.sourcegitcommit: bb2bbfad8061f7677954f6ce5a435b4e6f9299b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74100030"
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

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: amikor a Azure Stack frissítés telepítését kísérli meg, a frissítés állapota meghiúsulhat, és az állapot **PreparationFailed**értékre vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
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

### <a name="subscriptions-lock-blade"></a>Előfizetések zárolása panel

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felügyeleti portálon a felhasználói előfizetések **zárolási** paneljén két butons van, amelyek az **előfizetést**mondják.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack portálok használatával.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="storage-account-settings"></a>Storage-fiók beállításai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiók **konfigurációja** panel egy beállítást mutat be a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="upload-blob"></a>BLOB feltöltése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózatkezelés

### <a name="network-security-groups"></a>Hálózati biztonsági csoportok
- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: nem hozható létre explicit **DenyAllOutbound** -szabály egy NSG, mivel ez megakadályozza a virtuális gép üzembe helyezéséhez szükséges összes belső kommunikációt az infrastruktúrával.
- Előfordulás: gyakori

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati illesztő

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: egy virtuális gép elsődleges NIC-je nem módosítható. Az elsődleges hálózati adapter törlése/leválasztása a virtuális gép indításával kapcsolatos problémákat eredményez.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="local-network-gateway-deletion"></a>Helyi hálózati átjáró törlése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **helyi hálózati átjáró** törlése a következő hibaüzenetet jeleníti meg: **nem lehet aktív kapcsolatban álló helyi hálózati átjárót törölni**, még akkor is, ha nincs aktív internetkapcsolat.
- Megoldás: a probléma megoldása a 1907-es kiadásban jelenik meg. A probléma megkerülő megoldásként új helyi hálózati átjárót hoz létre ugyanazzal az IP-címmel, a címtartomány és a konfigurációs adatokkal egy másik névvel. A régi LNG törölhető, ha a környezet frissítve lett a 1907-es verziójára.
- Előfordulás: gyakori

#### <a name="alerts"></a>Értesítések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, a létrehozáskor és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **kapcsolatok** panel egy **VPN-hibakereső**nevű funkciót mutat be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a Virtual Network Gateway áttekintő lapján található dokumentációs hivatkozások Azure Stack helyett az Azure-specifikus dokumentációra mutatnak. Használja az alábbi hivatkozásokat az Azure Stack dokumentációhoz:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-áramkörök](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Számítási szolgáltatások

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartományból álló rendelkezésre állási csoportba, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizelés: virtuálisgép-hozzáférés használata a Linux-bővítményhez SSH-kulcsok implementálása a kiépítés után vagy jelszó alapú hitelesítés használatával.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>A virtuálisgép-méretezési csoport alaphelyzetbe állításának jelszava nem működik

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a méretezési csoport felhasználói felületén megjelenik egy új jelszó kérése panel, de Azure Stack nem támogatja a jelszó alaphelyzetbe állítását egy méretezési csoporton.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Az esős felhő a méretezési csoport diagnosztikát

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuálisgép-méretezési csoport áttekintő lapja egy üres diagramot jelenít meg. Ha az üres diagramra kattint, megnyílik az "esős felhő" panel. Ez a méretezési csoport diagnosztikai információinak, például a CPU százalékának a diagramja, amely nem támogatott a jelenlegi Azure Stack buildben.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállításainak panelje

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.    
- Ok: a virtuális gép diagnosztikai beállításainak paneljén van egy **fogadó lap, amely egy** alkalmazás-Insight- **fiókot**kér. Ez egy új panel eredménye, amely Azure Stackban még nem támogatott.
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
- Ok: a 1907 Azure Stack frissítés telepítésére tett kísérlet során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
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
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack portálok használatával.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="storage-account-settings"></a>Storage-fiók beállításai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiók **konfigurációja** panel egy beállítást mutat be a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="upload-blob"></a>BLOB feltöltése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózatkezelés

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati illesztő

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="alerts"></a>Értesítések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, a létrehozáskor és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **kapcsolatok** panel egy **VPN-hibakereső**nevű funkciót mutat be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="network-connection-type"></a>Hálózati kapcsolattípus

- Alkalmazható: a probléma bármely 1906-es vagy 1907-es környezetre érvényes. 
- Ok: a felhasználói portálon a **AddConnection** panel egy lehetőséget mutat be a **VNet – VNet**használatára. Ez a funkció jelenleg nem támogatott Azure Stackban. 
- Előfordulás: gyakori 

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a Virtual Network Gateway áttekintő lapján található dokumentációs hivatkozások Azure Stack helyett az Azure-specifikus dokumentációra mutatnak. Használja az alábbi hivatkozásokat az Azure Stack dokumentációhoz:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-áramkörök](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Számítási szolgáltatások

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartományból álló rendelkezésre állási csoportba, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizelés: virtuálisgép-hozzáférés használata a Linux-bővítményhez SSH-kulcsok implementálása a kiépítés után vagy jelszó alapú hitelesítés használatával.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>A virtuálisgép-méretezési csoport alaphelyzetbe állításának jelszava nem működik

- Alkalmazható: Ez a probléma a 1906-es és a 1907-as kiadásra vonatkozik.
- Ok: a méretezési csoport felhasználói felületén megjelenik egy új jelszó kérése panel, de Azure Stack nem támogatja a jelszó alaphelyzetbe állítását egy méretezési csoporton.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Az esős felhő a méretezési csoport diagnosztikát

- Alkalmazható: Ez a probléma a 1906-es és a 1907-as kiadásra vonatkozik.
- Ok: a virtuálisgép-méretezési csoport áttekintő lapja egy üres diagramot jelenít meg. Ha az üres diagramra kattint, megnyílik az "esős felhő" panel. Ez a méretezési csoport diagnosztikai információinak, például a CPU százalékának a diagramja, amely nem támogatott a jelenlegi Azure Stack buildben.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállításainak panelje

- Alkalmazható: Ez a probléma a 1906-es és a 1907-as kiadásra vonatkozik.    
- Ok: a virtuális gép diagnosztikai beállításainak paneljén van egy **fogadó lap, amely egy** alkalmazás-Insight- **fiókot**kér. Ez egy új panel eredménye, amely Azure Stackban még nem támogatott.
- Szervizelés: nincs.
- Előfordulás: gyakori

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>1906 frissítési folyamat

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a 1906 Azure Stack frissítés telepítésére tett kísérlet során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz. 
- Szervizelés: a 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti a problémát, ha a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy manuálisan töltse fel a frissítési csomagot a [frissítések importálása és telepítése szakasz](azure-stack-apply-updates.md)alapján.
- Előfordulás: gyakori

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

### <a name="subscription-resources"></a>Előfizetés erőforrásai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói előfizetések törlése árva erőforrásokkal jár.
- Szervizelés: először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack portálok használatával.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="subscriptions-properties-blade"></a>Előfizetések tulajdonságai panel
- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felügyeleti portálon az előfizetések **tulajdonságai panel** nem töltődik be megfelelően
- Szervizelés: ezeket az előfizetéseket az előfizetések áttekintése panel Essentials (alapvető erőforrások) paneljén tekintheti meg
- Előfordulás: gyakori

### <a name="storage-account-settings"></a>Storage-fiók beállításai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiók **konfigurációja** panel egy beállítást mutat be a **biztonsági átvitel típusának**módosításához. A szolgáltatás jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="upload-blob"></a>BLOB feltöltése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

### <a name="update"></a>Frissítés

- Alkalmazható: Ez a probléma az 1906-es kiadásra vonatkozik.
- Ok: a kezelő portálon a gyorsjavítás állapotának frissítése helytelen állapotot mutat a frissítéshez. A kezdeti állapot azt jelzi, hogy a frissítést nem sikerült telepíteni, még akkor is, ha még folyamatban van.
- Szervizelés: frissítse a portált, és az állapota "folyamatban" állapotba kerül.
- Előfordulás: időszakos

## <a name="networking"></a>Hálózatkezelés

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok**használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati illesztő

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="alerts"></a>Értesítések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások**használatára. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, a létrehozáskor és a **Virtual Network átjáró**erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **kapcsolatok** panel egy **VPN-hibakereső**nevű funkciót mutat be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a Virtual Network Gateway áttekintő lapján található dokumentációs hivatkozások Azure Stack helyett az Azure-specifikus dokumentációra mutatnak. Használja az alábbi hivatkozásokat az Azure Stack dokumentációhoz:

  - [Átjárók SKU-ban](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-áramkörök](azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Háttér-készlet hozzáadása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, ha **háttér-készletet** próbál hozzáadni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem tudta frissíteni a Load Balancer.** ...
- Szervizelés: használja a PowerShellt, a CLI-t vagy a Resource Manager-sablont a háttérbeli készlet terheléselosztó-erőforrással való hozzárendeléséhez.
- Előfordulás: gyakori

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon **bejövő NAT-szabályt** próbál létrehozni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem frissül Load Balancer.** ..
- Szervizelés: használja a PowerShellt, a CLI-t vagy a Resource Manager-sablont a háttérbeli készlet terheléselosztó-erőforrással való hozzárendeléséhez.
- Előfordulás: gyakori

## <a name="compute"></a>Számítási szolgáltatások

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**. A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartományból álló rendelkezésre állási csoportba, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizelés: virtuálisgép-hozzáférés használata a Linux-bővítményhez SSH-kulcsok implementálása a kiépítés után vagy jelszó alapú hitelesítés használatával.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>A virtuálisgép-méretezési csoport alaphelyzetbe állításának jelszava nem működik

- Alkalmazható: Ez a probléma az 1906-es kiadásra vonatkozik.
- Ok: a méretezési csoport felhasználói felületén megjelenik egy új jelszó kérése panel, de Azure Stack nem támogatja a jelszó alaphelyzetbe állítását egy méretezési csoporton.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Az esős felhő a méretezési csoport diagnosztikát

- Alkalmazható: Ez a probléma az 1906-es kiadásra vonatkozik.
- Ok: a virtuálisgép-méretezési csoport áttekintő lapja egy üres diagramot jelenít meg. Ha az üres diagramra kattint, megnyílik az "esős felhő" panel. Ez a méretezési csoport diagnosztikai információinak, például a CPU százalékának a diagramja, amely nem támogatott a jelenlegi Azure Stack buildben.
- Szervizelés: nincs.
- Előfordulás: gyakori

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállításainak panelje

- Alkalmazható: Ez a probléma az 1906-es kiadásra vonatkozik.
- Ok: a virtuális gép diagnosztikai beállításainak paneljén van egy **fogadó lap, amely egy** alkalmazás-Insight- **fiókot**kér. Ez egy új panel eredménye, amely Azure Stackban még nem támogatott.
- Szervizelés: nincs.
- Előfordulás: gyakori

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

- Alkalmazható: Ez a probléma az 1905-es frissítésre vonatkozik.
- Ok: amikor a 1905 Azure Stack frissítés telepítését kísérli meg, a frissítés állapota a **gazdagép csomópontjának frissítésének előfeltétele**miatt sikertelen lehet. Ezt általában egy olyan gazda-csomópont okozza, amely nem rendelkezik elegendő szabad lemezterülettel.
- Szervizelés: forduljon a Azure Stack támogatási szolgálathoz, és kérjen segítséget a gazdagép-csomóponton található lemezterület törléséhez.
- Előfordulás: nem gyakori

### <a name="preparation-failed"></a>Az előkészítés sikertelen

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a 1905 Azure Stack frissítés telepítésére tett kísérlet során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz. Az 1905-es frissítési csomag nagyobb, mint az előző frissítési csomagok, amelyek nagyobb valószínűséggel tehetik ezt a problémát.
- Szervizelés: a 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti a problémát, ha a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy manuálisan töltse fel a frissítési csomagot a [frissítések importálása és telepítése szakasz](azure-stack-apply-updates.md)alapján.
- Előfordulás: gyakori

## <a name="portal"></a>Portál

### <a name="subscription-resources"></a>Előfizetés erőforrásai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói előfizetések törlése árva erőforrásokkal jár.
- Szervizelés: először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack portálok használatával.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="marketplace-management"></a>Piactér-kezelés

- Alkalmazható: Ez a probléma a 1904-es és a 1905-es verzióra vonatkozik
- Ok: a piactér-felügyeleti képernyő nem látható, ha bejelentkezik a felügyeleti portálra.
- Szervizelés: frissítse a böngészőt, vagy lépjen a **Beállítások** elemre, és válassza az **Alaphelyzetbe állítás lehetőséget az alapértelmezett beállítások**lehetőségnél.
- Előfordulás: időszakos

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a rendszergazda és a felhasználói portálon a **Docker**keresésekor a rendszer helytelenül adja vissza az elemeket. Azure Stack nem érhető el. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
- Szervizelés: nincs megoldás.
- Előfordulás: gyakori

### <a name="upload-blob"></a>BLOB feltöltése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

### <a name="template"></a>Sablon

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a sablon központi telepítési felhasználói felülete nem tölti fel a (z) _ karakterrel kezdődően a sablon neveit (aláhúzásjel).
- Szervizelés: távolítsa el a "_" (aláhúzás) karaktert a sablon nevéből.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózatkezelés

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Háttér-készlet hozzáadása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, ha **háttér-készletet** próbál hozzáadni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem tudta frissíteni a Load Balancer.** ...
- Szervizelés: használja a PowerShellt, a CLI-t vagy a Resource Manager-sablont a háttérbeli készlet terheléselosztó-erőforrással való hozzárendeléséhez.
- Előfordulás: gyakori

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon **bejövő NAT-szabályt** próbál létrehozni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem frissül Load Balancer.** ..
- Szervizelés: használja a PowerShellt, a CLI-t vagy a Resource Manager-sablont a háttérbeli készlet terheléselosztó-erőforrással való hozzárendeléséhez.
- Előfordulás: gyakori

#### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **létrehozás Load Balancer** ablak egy **standard** Load Balancer SKU létrehozására szolgáló lehetőséget mutat be. Ez a beállítás Azure Stack esetén nem támogatott.
- Szervizelés: használja helyette az **alapszintű** Load Balancer beállításait.
- Előfordulás: gyakori

### <a name="public-ip-address"></a>Nyilvános IP-cím

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **nyilvános IP-cím létrehozása** ablak egy **szabványos** SKU létrehozására szolgáló lehetőséget mutat be. Azure Stack nem támogatja a **standard** SKU-t.
- Szervizelés: használja az **alapszintű** SKU-t a nyilvános IP-címhez.
- Előfordulás: gyakori

## <a name="compute"></a>Számítási szolgáltatások

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**.
A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="vm-resize"></a>Virtuális gép átméretezése

- Alkalmazható: Ez a probléma az 1905-es kiadásra vonatkozik.
- Ok: nem sikerült a felügyelt lemezes virtuális gép átméretezése. A virtuális gép átméretezésére irányuló kísérlet hibát generál a "code": "InternalOperationError", "Message": "a művelet belső hibája."
- Szervizelés: a következő kiadásban dolgozunk. Jelenleg újra létre kell hoznia a virtuális gépet az új virtuálisgép-mérettel.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="centos"></a>CentOS

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuálisgép-méretezési csoport létrehozási felülete CentOS-alapú 7,2-as lehetőséget biztosít az üzembe helyezéshez. A CentOS 7,2 Azure Stack piactéren nem érhető el, ezért a telepítési hibák miatt a rendszer nem találja a rendszerképet.
- Szervizelés: válasszon másik operációs rendszert az üzemelő példányhoz, vagy használjon egy Azure Resource Manager sablont, amely egy másik, a piactéren az operátor általi üzembe helyezés előtt letöltött CentOS-rendszerképet ad meg.
- Előfordulás: gyakori

#### <a name="remove-scale-set"></a>Méretezési csoport eltávolítása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a méretezési csoport nem távolítható el a **virtuálisgép-méretezési** csoportok paneljéről.
- Szervizelés: válassza ki az eltávolítani kívánt méretezési készletet, majd kattintson a **Törlés** gombra az **Áttekintés** ablaktáblán.
- Előfordulás: gyakori

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartományból álló rendelkezésre állási csoportba, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>A méretezési csoport példányainak nézete nem töltődik be

- Alkalmazható: Ez a probléma a 1904-es és a 1905-es kiadásra vonatkozik.
- Ok: egy virtuálisgép-méretezési csoport példány nézetének panelje, amely a Azure Stack Portal-> irányítópultján található – > virtuálisgép-méretezési csoportok – > AnyScaleSet-instances – > AnyScaleSetInstance nem töltődik be, és megjelenít egy síró Felhőbeli képet.
- Szervizelés: jelenleg nincs szervizelés, és a javításon dolgozunk. Addig is használja a CLI-parancsot `az vmss get-instance-view` egy méretezési csoport példány nézetének lekéréséhez.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizelés: virtuálisgép-hozzáférés használata a Linux-bővítményhez SSH-kulcsok implementálása a kiépítés után vagy jelszó alapú hitelesítés használatával.
- Előfordulás: gyakori

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

## <a name="next-steps"></a>Következő lépések

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
