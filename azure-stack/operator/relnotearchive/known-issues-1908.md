---
title: Azure Stack 1908 ismert probléma
description: Ismerkedjen meg Azure Stack 1908-es kiadásokban ismert problémákkal.
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5cf4a0bcbef1a5f1d34a979f26c35a10efadc8f8
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248328"
---
# <a name="azure-stack-1908-known-issues"></a>Azure Stack 1908 ismert probléma

Ez a cikk a Azure Stack kiadásának ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

Ha egy másik verzió ismert problémáit szeretné elérni, a bal oldali tartalomjegyzéknél válassza a verzió kiválasztása legördülő menüt.

> [!IMPORTANT]  
> A frissítés alkalmazása előtt tekintse át ezt a szakaszt.

> [!IMPORTANT]  
> Ha a Azure Stack-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="1908-update-process"></a>1908 frissítési folyamat

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: amikor a Azure Stack frissítés telepítését kísérli meg, a frissítés állapota meghiúsulhat, és az állapot **PreparationFailed** értékre vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
- Szervizelés: a 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti a problémát, ha a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy a [frissítések telepítése szakaszt](../azure-stack-apply-updates.md#install-updates-and-monitor-progress)követve manuálisan töltse fel a frissítési csomagot.
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
- Ok: a felügyeleti portálon a felhasználói előfizetések **zárolási** paneljén két butons van, amelyek az **előfizetést** mondják.
- Előfordulás: gyakori

### <a name="subscription-permissions"></a>Előfizetés engedélyei

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az előfizetéshez tartozó engedélyek nem tekinthetők meg az Azure Stack portálok használatával.
- Szervizelés: [az engedélyek ellenőrzéséhez használja a PowerShellt](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: gyakori

### <a name="storage-account-settings"></a>Tárfiók beállításai

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a Storage-fiók **konfigurációja** panel egy beállítást mutat be a **biztonsági átvitel típusának** módosításához. A szolgáltatás jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="upload-blob"></a>BLOB feltöltése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul.
- Szervizelés: töltse fel a blobot a SAS kapcsoló használatával.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózatkezelés

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network** panel a **szolgáltatási végpontok** használatát mutatja be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

### <a name="network-interface"></a>Hálózati adapter

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: nem lehet új hálózati adaptert hozzáadni egy **futó** állapotban lévő virtuális géphez.
- Szervizelés: állítsa le a virtuális gépet a hálózati adapter hozzáadása vagy eltávolítása előtt.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="local-network-gateway-deletion"></a>Helyi hálózati átjáró törlése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **helyi hálózati átjáró** törlése a következő hibaüzenetet jeleníti meg: **nem lehet aktív kapcsolatban álló helyi hálózati átjárót törölni**, még akkor is, ha nincs aktív internetkapcsolat.
- Megoldás: a probléma megoldása a 1907-es kiadásban jelenik meg. A probléma megkerülő megoldásként új helyi hálózati átjárót hoz létre ugyanazzal az IP-címmel, a címtartomány és a konfigurációs adatokkal egy másik névvel. A régi LNG törölhető, ha a környezet frissítve lett a 1907-es verziójára.
- Előfordulás: gyakori

#### <a name="alerts"></a>Riasztások

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **Virtual Network átjáró** panel egy lehetőséget mutat be a **riasztások** használatára. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, a létrehozáskor és a **Virtual Network átjáró** erőforrás menüjében megjelenik egy lehetőség az **aktív-aktív** konfiguráció engedélyezéséhez. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="vpn-troubleshooter"></a>VPN-hibakereső

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **kapcsolatok** panel egy **VPN-hibakereső** nevű funkciót mutat be. Ez a funkció jelenleg nem támogatott Azure Stackban.
- Előfordulás: gyakori

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a Virtual Network Gateway áttekintő lapján található dokumentációs hivatkozások Azure Stack helyett az Azure-specifikus dokumentációra mutatnak. Használja az alábbi hivatkozásokat az Azure Stack dokumentációhoz:

  - [Átjáró-termékváltozatok](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](../azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

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
- Ok: a virtuális gép diagnosztikai beállításainak paneljén van egy **fogadó lap, amely egy** alkalmazás-Insight- **fiókot** kér. Ez egy új panel eredménye, amely Azure Stackban még nem támogatott.
- Szervizelés: nincs.
- Előfordulás: gyakori
