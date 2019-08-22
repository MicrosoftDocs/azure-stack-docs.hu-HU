---
title: Azure Stack 1907 ismert probléma | Microsoft Docs
description: Ismerje meg a Azure Stack 1907 ismert problémáit.
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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 0b305f07344540555b444017a04c1002bb8a08a8
ms.sourcegitcommit: 250689d6d09acc677bf59de76510d5d5f1c6190e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69896341"
---
# <a name="azure-stack-1907-known-issues"></a>Azure Stack 1907 ismert probléma

Ez a cikk a Azure Stack 1907-es kiadásának ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

> [!IMPORTANT]  
> A frissítés alkalmazása előtt tekintse át ezt a szakaszt.

## <a name="update-process"></a>Frissítési folyamat

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Az 1907 Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed**állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
- Szervizkiszolgáló A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy manuálisan töltse fel a frissítési csomagot a [frissítések importálása és telepítése szakasz](azure-stack-apply-updates.md#import-and-install-updates)alapján.
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
- Szervizkiszolgáló Ezeket az előfizetéseket az előfizetések **áttekintése** panel **Essentials (alapvető** erőforrások) paneljén tekintheti meg.
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

### <a name="upload-blob"></a>BLOB feltöltése

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
- Ok: A virtuális gép diagnosztikai beállításainak paneljén egy fogadó lap van, amely egy **alkalmazás-Insight-fiókot**kér. Ez egy új panel eredménye, amely Azure Stackban még nem támogatott.
- Szervizkiszolgáló Nincs.
- Előfordulása Közös

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>További lépések

- [A frissítési tevékenységre vonatkozó ellenőrzőlista áttekintése](azure-stack-release-notes-checklist.md)
- [Biztonsági frissítések listájának áttekintése](azure-stack-release-notes-security-updates-1907.md)
