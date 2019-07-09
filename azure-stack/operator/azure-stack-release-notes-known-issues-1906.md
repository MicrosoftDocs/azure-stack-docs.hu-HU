---
title: Ismert problémák az Azure Stack 1906 |} A Microsoft Docs
description: Ismerje meg az Azure Stack 1906 ismert problémáiról.
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
ms.date: 06/28/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 96a29ab11ffc15b35c7035576fa68544caef594b
ms.sourcegitcommit: 1c4eda123857d714109e38bb853eb1ce49af5f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67648049"
---
# <a name="azure-stack-1906-known-issues"></a>Az Azure Stack 1906 ismert problémák

Ez a cikk az Azure Stack 1906 kiadás ismert problémákat ismerteti. A lista frissül új problémák azonosítva.

> [!IMPORTANT]  
> Tekintse át az ebben a szakaszban a frissítés alkalmazása előtt.

## <a name="update-process"></a>Frissítési eljárás

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: 1906 az Azure Stack frissítésének megkísérlésekor a frissítés állapota sikertelen lehet, hogy, és az állapotváltozáshoz **PreparationFailed**. A frissítés erőforrás-szolgáltató (URP) nem képes megfelelően viheti át a fájlokat a storage-tárolót egy belső infrastruktúrát megosztáshoz feldolgozási okozza. 
- Szervizelési: 1901 (1.1901.0.95) verziójával kezdődően is használhatja a probléma megoldásához kattintva **frissítés most** újra (nem **folytatása**). A URP ezután a fájlokat az előző próbálkozás megtisztítja, és letöltését újraindítja. Ha a probléma tartósan fennáll, javasoljuk, hogy manuálisan feltöltve az alábbi frissítési csomag a [importálása és telepítési frissítések szakasz](azure-stack-apply-updates.md#import-and-install-updates).
- Előfordulás: Közös

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Felügyeleti előfizetés

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A két felügyeleti előfizetés verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **mérés** előfizetését, és **fogyasztás** előfizetés.
- Szervizelési: Ha ezen a két előfizetés futó erőforrások, létrehozhatja a felhasználói előfizetések.
- Előfordulás: Közös

### <a name="subscription-resources"></a>Előfizetési erőforrások

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Felhasználói előfizetések eredmények az árva erőforrások törlése.
- Szervizelési: Először törölje a felhasználó vagy a teljes erőforráscsoportot, és ezután törölje a felhasználói előfizetések.
- Előfordulás: Közös

### <a name="subscription-permissions"></a>Előfizetése engedélyei

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez.
- Szervizelési: Használat [engedélyek ellenőrzése érdekében PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Előfordulás: Közös

### <a name="storage-account-settings"></a>Tárfiók-beállítások

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, a storage-fiók **konfigurációs** panelen módosítsa lehetőség jelenik meg **biztonsági átviteli típus**. A funkció jelenleg nem támogatott az Azure Stackben.
- Előfordulás: Közös

### <a name="upload-blob"></a>Blob feltöltése

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, amikor megpróbálja feltölteni egy blob használatával a **OAuth(preview)** beállítást, a feladat egy hibaüzenettel meghiúsul.
- Szervizelési: Töltse fel a blob SAS lehetőséggel.
- Előfordulás: Közös

### <a name="template"></a>Sablon

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a sablon központi telepítés felhasználói felület nem tölti fel a "_" (aláhúzás karakterrel) kezdődő sablonnevek paramétereit.
- Szervizelési: Távolítsa el a sablon nevét a "_" (aláhúzás karakterrel).
- Előfordulás: Közös

## <a name="networking"></a>Hálózat

### <a name="service-endpoints"></a>Szolgáltatásvégpontok

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **virtuális hálózat** panelen jelenik meg egy lehetőség, amellyel **Szolgáltatásvégpontok**. Ez a funkció jelenleg nem támogatott az Azure Stackben.
- Előfordulás: Közös

### <a name="network-interface"></a>Hálózati illesztő

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Új hálózati adaptert, amely a virtuális gép nem adható hozzá egy **futó** állapota.
- Szervizelési: Állítsa le a virtuális gép hálózati adapter hozzáadása/eltávolítása előtt.
- Előfordulás: Közös

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="alerts"></a>Riasztások

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **virtuális hálózati átjáró** panelen jelenik meg egy lehetőség, amellyel **riasztások**. Ez a funkció jelenleg nem támogatott az Azure Stackben.
- Előfordulás: Közös

#### <a name="active-active"></a>Aktív-aktív

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, létrehozásakor, és az erőforrások menüjének **virtuális hálózati átjáró**, látni fogja engedélyezni **aktív-aktív** konfigurációja. Ez a funkció jelenleg nem támogatott az Azure Stackben.
- Előfordulás: Közös

#### <a name="vpn-troubleshooter"></a>VPN hibaelhárítója

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **kapcsolatok** panelen jelenik meg a szolgáltatás **VPN hibaelhárító**. Ez a funkció jelenleg nem támogatott az Azure Stackben.
- Előfordulás: Közös

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A virtuális hálózati átjáró áttekintés oldalán a dokumentációs hivatkozások helyett az Azure Stack Azure-ra vonatkozó dokumentáció mutató hivatkozás. Az Azure Stack-dokumentáció, használja az alábbi hivatkozásokat:

  - [Átjáró-termékváltozatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magas rendelkezésre állású kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása az Azure Stackben](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Az ExpressRoute-Kapcsolatcsoportok](azure-stack-connect-expressroute.md)
  - [Adja meg az egyéni IPsec/IKE-szabályzatok](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Terheléselosztó

#### <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, ha megpróbálja hozzáadni egy **Háttérkészlet** , egy **terheléselosztó**, a művelet meghiúsul, a hibaüzenet **nem sikerült frissíteni a Load Balancer...** .
- Szervizelési: A PowerShell, parancssori felület vagy a Resource Manager-sablon a háttérkészlet társítása egy terheléselosztó-erőforráshoz.
- Előfordulás: Közös

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, ha megpróbál létrehozni egy **bejövő NAT-szabály** számára egy **Load Balancer**, a művelet meghiúsul, a hibaüzenet **nem sikerült frissíteni a Load Balancer...** .
- Szervizelési: A PowerShell, parancssori felület vagy a Resource Manager-sablon a háttérkészlet társítása egy terheléselosztó-erőforráshoz.
- Előfordulás: Közös

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Virtuális gép rendszerindítási diagnosztikája

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Amikor egy új Windows virtuális gép (VM) hoz létre, a következő hiba jelenhet meg: **Nem sikerült elindítani a virtuális gép (vm-name). Hiba: Nem sikerült frissíteni a virtuális gép (vm-name) soros kimeneti beállításait**. A hiba akkor fordul elő, ha a virtuális gép rendszerindítási diagnosztika engedélyezése, de a rendszerindítás-diagnosztikai tárfiók törlése.
- Szervizelési: Hozza létre újból a korábban használt azonos nevű tárfiók.
- Előfordulás: Közös

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="centos"></a>CentOS

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A virtual machine scale set létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. CentOS 7.2 nem érhető el az Azure Stacken.
- Szervizelési: Az üzembe helyezéshez használt egy másik operációs rendszert, vagy adjon meg egy másik CentOS lemezképet a marketplace-ről telepítené az üzemeltető által letöltött Azure Resource Manager sablonnal.
- Előfordulás: Közös

#### <a name="remove-scale-set"></a>Méretezési csoport eltávolítása

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A méretezési készlet nem távolítható el a **a Virtual machine scale sets** panelen.
- Szervizelési: Válassza ki a méretezési csoport, hogy el kívánja távolítani, majd kattintson a **törlése** gombra a **áttekintése** ablaktáblán.
- Előfordulás: Közös

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása során a javítások és frissítések 4 csomópontos Azure Stack-környezetekben

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A rendelkezésre állási csoport 3 tartalék tartomány a virtuális gépek létrehozása és a egy virtuálisgép-méretezési csoport létrehozása a példány sikertelen lesz, és állítsa be a **FabricVmPlacementErrorUnsupportedFaultDomainSize** hiba történt a frissítés során az egy 4 csomópontos az Azure Stackben környezet.
- Szervizelési: Egy rendelkezésre állási csoport sikeresen 2 hibatűrési tartományt is létrehozhat önálló virtuális gépek. Azonban scale set-példány létrehozása nem még nem érhető el a frissítés során az egy 4 csomópontos Azure Stacken.

#### <a name="scale-set-instance-view-blade-does-not-load"></a>Méretezési csoport példány megtekintési paneljén nem tölt be

- Alkalmazható: A probléma a 1904-es, 1905 és 1906 kiadások vonatkozik.
- OK: Az Azure Stack portálon található virtuális gép méretezési példányt megtekintési paneljén **irányítópult > virtuálisgép-méretezési csoportok > AnyScaleSet > példányok > AnyScaleSetInstance** nem sikerül betölteni, és a egy "(rainy) megjeleníti felhő"rendszerképet.
- Szervizelési: Jelenleg nem nincs szervizelés, és folyamatban van egy javítás. Addig használja a CLI-paranccsal `az vmss get-instance-view` beolvasni egy méretezési csoport példányait tartalmazó nézetet.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem engedélyezi az SSH-kulcsokat használhatja a bejelentkezni.
- Szervizelési: A Linux-bővítményt a Virtuálisgép-hozzáférési használja az SSH-kulcsok megvalósításához a kiépítés után, vagy a jelszóalapú hitelesítés használatára.
- Előfordulás: Közös

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Virtual machine scale set jelszó alaphelyzetbe állítása nem működik.

- Alkalmazható: A probléma a 1906 kiadásra vonatkozik.
- OK: A méretezési felhasználói felületén megjelenik egy új reset jelszó panel, de az Azure Stack nem támogatja új jelszó kérésekor még egy méretezési.
- Szervizelési: Nincs.
- Előfordulás: Közös

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>A méretezési csoport (rainy) felhő diagnosztika beállítása

- Alkalmazható: A probléma a 1906 kiadásra vonatkozik.
- OK: A virtuális gép méretezési csoport – áttekintés oldalra egy üres diagram mutatja. Az üres diagram a hivatkozásra kattintva megnyílik egy "(rainy) felhő" panel. Ez a diagram a méretezési csoport diagnosztikai információkat, például processzorhasználat, és nem támogatott az aktuális Azure Stack-build a szolgáltatás.
- Szervizelési: Nincs.
- Előfordulás: Közös

### <a name="virtual-machine-diagnostic-settings-blade"></a>Virtuális gép diagnosztikai beállítások panel

- Alkalmazható: A probléma a 1906 kiadásra vonatkozik.
- OK: A virtuális gép diagnosztikai beállítások panelen egy **fogadó** lap, amely bekéri azt a egy **Application Insight fiók**. Ez az eredmény egy új panel, amely még nem támogatott az Azure Stackben.
- Szervizelési: Nincs.
- Előfordulás: Közös

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>További lépések

- [Felülvizsgálati tevékenység ellenőrzőlista](azure-stack-release-notes-checklist.md)
- [Tekintse át a biztonsági frissítések listája](azure-stack-release-notes-security-updates-1906.md)
