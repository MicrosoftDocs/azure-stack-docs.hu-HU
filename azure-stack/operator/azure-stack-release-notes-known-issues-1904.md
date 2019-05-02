---
title: Az Azure Stack kibocsátási megjegyzések – ismert problémák a 1904 |} A Microsoft Docs
description: Ismerje meg az Azure Stack 1904 ismert problémáiról.
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
ms.date: 05/02/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/02/2019
ms.openlocfilehash: 8b061b3c6843540bcf1d6a6379b9181a9d757c5d
ms.sourcegitcommit: 91c5056cb6d9bbd852132bebfbefa05b6b4d6cb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64988115"
---
# <a name="azure-stack-1904-known-issues"></a>Az Azure Stack 1904 ismert problémák

Ez a cikk az Azure Stack 1904 kiadás ismert problémákat ismerteti. A lista frissül új problémák azonosítva.

> [!IMPORTANT]  
> Tekintse át az ebben a szakaszban a frissítés alkalmazása előtt.

## <a name="portal"></a>Portál

### <a name="add-on-plans"></a>Kiegészítő csomagok

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik.
- Szervizelési: Nincs megoldás.
- Előfordulás: Közös

### <a name="administrative-subscriptions"></a>Felügyeleti előfizetés

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A két felügyeleti előfizetés verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **mérés** előfizetését, és **fogyasztás** előfizetés.
- Szervizelési: Ezek az előfizetések 1905 a kezdő- és idővel a törölt felfüggesztjük. Ha ezen a két előfizetés futó erőforrások, létrehozhatja a felhasználói előfizetések 1905 előtt.
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

- Alkalmazható: A probléma 1904 vonatkozik.
- OK: A piactér felügyeleti képernyő esetén nem látható, jelentkezzen be a felügyeleti portálon.
- Szervizelési: Frissítse a böngészőt.
- Előfordulás: Időszakos

### <a name="upload-blob"></a>Blob feltöltése

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, ha feltölt egy blobot a OAuth(preview) beállítás használatával próbál a feladat sikertelen, egy hibaüzenet.
- Szervizelési: Töltse fel a blob SAS lehetőséggel.
- Előfordulás: Közös

## <a name="networking"></a>Hálózat

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Háttérkészlet hozzáadása
- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, ha megpróbálja hozzáadni egy **Háttérkészlet** , egy **terheléselosztó**, a művelet meghiúsul, a hibaüzenet "nem sikerült frissíteni a Load Balancer...".
- Szervizelési: A PowerShell, parancssori felület vagy a Resource Manager-sablon a háttérkészlet társítása egy terheléselosztó-erőforráshoz.
- Előfordulás: Közös

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása
- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, ha megpróbál létrehozni egy **bejövő NAT-szabály** számára egy **Load Balancer**, a művelet meghiúsul, a hibaüzenet "Nem sikerült frissíteni a Load Balancer...".
- Szervizelési: A PowerShell, parancssori felület vagy a Resource Manager-sablon a háttérkészlet társítása egy terheléselosztó-erőforráshoz.
- Előfordulás: Közös

#### <a name="create-load-balancer"></a>Load Balancer létrehozása 
- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **Load Balancer létrehozása** ablakban látható lehetőség hozzon létre egy **Standard** Load Balancer Termékváltozat. Ez a beállítás nem támogatott az Azure Stackben.
- Szervizelési: Ehelyett használja az alapszintű Load Balancer beállításait.
- Előfordulás: Közös

### <a name="public-ip-address"></a>Nyilvános IP-cím

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **nyilvános IP-cím létrehozása** ablakban látható lehetőség hozzon létre egy **Standard** Termékváltozat. A **Standard** Termékváltozat nem támogatott az Azure Stackben.
- Szervizelési: Használja helyette alapszintű Termékváltozat nyilvános IP-címet.
- Előfordulás: Közös

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Virtuális gép rendszerindítási diagnosztikája

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Amikor egy új Windows virtuális gép (VM) hoz létre, a következő hiba jelenhet meg: **Nem sikerült elindítani a virtuális gép (vm-name). Hiba: Nem sikerült frissíteni a virtuális gép (vm-name) soros kimeneti beállításait**.
A hiba akkor fordul elő, ha a virtuális gép rendszerindítási diagnosztika engedélyezése, de a rendszerindítás-diagnosztikai tárfiók törlése.
- Szervizelési: Hozza létre újból a korábban használt azonos nevű tárfiók.
- Előfordulás: Közös

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A virtuális gép méretezési beállítása (VMSS) létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. CentOS 7.2 nem érhető el az Azure Stacken.
- Szervizelési: Az üzembe helyezéshez használt egy másik operációs rendszert, vagy adjon meg egy másik CentOS lemezképet a marketplace-ről telepítené az üzemeltető által letöltött Azure Resource Manager sablonnal.
- Előfordulás: Közös

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem engedélyezi az SSH-kulcsokat használhatja a bejelentkezni.
- Szervizelési: A Linux-bővítményt a Virtuálisgép-hozzáférési használja az SSH-kulcsok megvalósításához a kiépítés után, vagy a jelszóalapú hitelesítés használatára.
- Előfordulás: Közös

## <a name="infrastructure-backup"></a>Infrastruktúra biztonsági mentése

<!--Bug 3615401 - scheduler config lost; new issue in YYMM;  hectorl-->
Miután engedélyezte az automatikus biztonsági mentést, a Feladatütemező szolgáltatás hiányzóra tiltva váratlanul. A biztonsági mentési hálózativezérlő-szolgáltatás észleli, hogy az automatikus biztonsági mentés le vannak tiltva, és figyelmeztetés a felügyeleti portálon. Ez a figyelmeztetés várható, ha az automatikus biztonsági mentés le vannak tiltva.

- Alkalmazható: Ez az új probléma 1904 kiadással.
- OK: A probléma van a szolgáltatásban, amely az ütemezési konfiguráció elvesztését eredményezi egy hiba miatt. Ez a hiba nem változik, a tárolási helyét, a felhasználónevet, a jelszó vagy a titkosítási kulcs.
- Szervizelési: A probléma megoldásához nyissa meg a biztonsági mentési infrastruktúra erőforrás-szolgáltató a biztonsági mentés vezérlő beállítások panelre, és válassza **engedélyezze az automatikus biztonsági mentés**. Ellenőrizze, hogy a kívánt gyakoriság és megőrzési időszak beállítása.
- Előfordulás: Alacsony

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>További lépések

- [Felülvizsgálati tevékenység ellenőrzőlista](azure-stack-release-notes-checklist.md)
- [Tekintse át a biztonsági frissítések listája](azure-stack-release-notes-security-updates-1904.md)
