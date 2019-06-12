---
title: Ismert problémák az Azure Stack 1905 |} A Microsoft Docs
description: Ismerje meg az Azure Stack 1905 ismert problémáiról.
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
ms.date: 06/05/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: 38c7ec337ba8cdb73925b1c07f77331c05b25d8a
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66836661"
---
# <a name="azure-stack-1905-known-issues"></a>Az Azure Stack 1905 ismert problémák

Ez a cikk az Azure Stack 1905 kiadás ismert problémákat ismerteti. A lista frissül új problémák azonosítva.

> [!IMPORTANT]  
> Tekintse át az ebben a szakaszban a frissítés alkalmazása előtt.

## <a name="update-process"></a>Frissítési eljárás

### <a name="host-node-update-prerequisite-failure"></a>Csomópont frissítés előfeltétel-ellenőrzési hiba

- Alkalmazható: A probléma 1905 frissítésére vonatkozik.
- OK: Az 1905 Azure Stack frissítésének megkísérlésekor a frissítés állapota miatt meghiúsulhat **fogadó csomópont frissítése előfeltétel**. Ez általában egy elegendő szabad lemezterülettel rendelkező gazdacsomópont okozzák.
- Szervizelési: Lépjen kapcsolatba az Azure Stack-támogatás törlésével szabadítson fel lemezterületet a gazdacsomópont támogatásra.
- Előfordulás: Nem szokványos

### <a name="preparation-failed"></a>Az előkészítés sikertelen

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: 1905 az Azure Stack frissítésének megkísérlésekor a frissítés állapota sikertelen lehet, hogy, és az állapotváltozáshoz **PreparationFailed**. A frissítés erőforrás-szolgáltató (URP) nem képes megfelelően viheti át a fájlokat a storage-tárolót egy belső infrastruktúrát megosztáshoz feldolgozási okozza. A 1905 csomag, nagyobb korábbi frissítési csomagokat, amelyek fordul elő.
- Szervizelési: 1901 (1.1901.0.95) verziójával kezdődően is használhatja a probléma megoldásához kattintva **frissítés most** újra (nem **folytatása**). A URP ezután a fájlokat az előző próbálkozás megtisztítja, és letöltését újraindítja. Ha a probléma tartósan fennáll, javasoljuk, hogy manuálisan feltöltve az alábbi frissítési csomag a [importálása és telepítési frissítések szakasz](azure-stack-apply-updates.md#import-and-install-updates).
- Előfordulás: Közös

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Felügyeleti előfizetés

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A két felügyeleti előfizetés verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **mérés** előfizetését, és **fogyasztás** előfizetés.
- Szervizelési: Ezek az előfizetések 1906 a kezdő- és idővel a törölt felfüggesztjük. Ha ezen a két előfizetés futó erőforrások, létrehozhatja a felhasználói előfizetések 1906 előtt.
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

### <a name="marketplace-management"></a>Marketplace-en kezelése

- Alkalmazható: A probléma 1904 és 1905 vonatkozik
- OK: A piactér felügyeleti képernyő a látható esetén nem jelentkezik be a felügyeleti portálon.
- Szervizelési: Frissítse a böngészőt, vagy keresse fel **beállítások** , és jelölje be **alapértelmezett beállításainak visszaállítása**.
- Előfordulás: Időszakos

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A rendszergazda és a felhasználói portálon, ha a **Docker**, az elem nem megfelelően adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
- Szervizelési: Nincs megoldás.
- Előfordulás: Közös

### <a name="upload-blob"></a>Blob feltöltése

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, amikor megpróbálja feltölteni egy blob használatával a **OAuth(preview)** beállítást, a feladat egy hibaüzenettel meghiúsul.
- Szervizelési: Töltse fel a blob SAS lehetőséggel.
- Előfordulás: Közös

## <a name="networking"></a>Hálózat

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

#### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **Load Balancer létrehozása** ablakban látható lehetőség hozzon létre egy **Standard** terheléselosztó Termékváltozat. Ez a beállítás nem támogatott az Azure Stackben.
- Szervizelési: Használja a **alapszintű** tölti be a terheléselosztó beállítások.
- Előfordulás: Közös

### <a name="public-ip-address"></a>Nyilvános IP-cím

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **nyilvános IP-cím létrehozása** ablakban látható lehetőség hozzon létre egy **Standard** Termékváltozat. A **Standard** Termékváltozat nem támogatott az Azure Stackben.
- Szervizelési: Használja a **alapszintű** Termékváltozat nyilvános IP-cím.
- Előfordulás: Közös

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Virtuális gép rendszerindítási diagnosztikája

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Amikor egy új Windows virtuális gép (VM) hoz létre, a következő hiba jelenhet meg: **Nem sikerült elindítani a virtuális gép (vm-name). Hiba: Nem sikerült frissíteni a virtuális gép (vm-name) soros kimeneti beállításait**.
A hiba akkor fordul elő, ha a virtuális gép rendszerindítási diagnosztika engedélyezése, de a rendszerindítás-diagnosztikai tárfiók törlése.
- Szervizelési: Hozza létre újból a korábban használt azonos nevű tárfiók.
- Előfordulás: Közös

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="centos"></a>CentOS

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A virtual machine scale set létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. CentOS 7.2 nem érhető el az Azure Stack piactéren, amely hatására a hibával kapcsolatos üzembehelyezési problémák hívott, hogy a kép nem található.
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

#### <a name="vmss-instance-view-blade-doesnt-load"></a>Nem tölt be a VMSS-példány megtekintési paneljén
 
- Alkalmazható: A probléma 1904-es és 1905 kiadásra vonatkozik.
- OK: Egy méretezési csoport Azure Stack portálon található példány megtekintési paneljén -> irányítópult -> virtuálisgép-méretezési csoportok -> AnyScaleSet – példányok > AnyScaleSetInstance sikertelen a Betöltés a felhő Síró.
- Szervizelési: Jelenleg nem nincs szervizelés, és folyamatban van egy javítás. Addig használja CLI parancsmag az vmss get-instance-view-VMSS példányait tartalmazó nézetet beolvasása

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem engedélyezi az SSH-kulcsokat használhatja a bejelentkezni.
- Szervizelési: A Linux-bővítményt a Virtuálisgép-hozzáférési használja az SSH-kulcsok megvalósításához a kiépítés után, vagy a jelszóalapú hitelesítés használatára.
- Előfordulás: Közös

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>További lépések

- [Felülvizsgálati tevékenység ellenőrzőlista](azure-stack-release-notes-checklist.md)
- [Tekintse át a biztonsági frissítések listája](azure-stack-release-notes-security-updates-1905.md)
