---
title: Azure Stack 1905 ismert probléma | Microsoft Docs
description: Ismerje meg a Azure Stack 1905 ismert problémáit.
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 619bd20b3ed9affb93bceed7b253c15bdb1e3609
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008734"
---
# <a name="azure-stack-1905-known-issues"></a>Azure Stack 1905 ismert probléma

Ez a cikk a Azure Stack 1905-es kiadásának ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

> [!IMPORTANT]  
> A frissítés alkalmazása előtt tekintse át ezt a szakaszt.

## <a name="update-process"></a>Frissítési folyamat

### <a name="host-node-update-prerequisite-failure"></a>Gazdagép-csomópont frissítésének előfeltételi hibája

- Alkalmazható Ez a probléma az 1905-es frissítésre vonatkozik.
- Ok: Az 1905 Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota a **gazdagép csomópontjának frissítésének**előfeltétele miatt meghiúsul. Ezt általában egy olyan gazda-csomópont okozza, amely nem rendelkezik elegendő szabad lemezterülettel.
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
- Szervizkiszolgáló Frissítse a böngészőt, vagy lépjen a **Beállítások** elemre, és válassza az Alaphelyzetbe állítás lehetőséget az **alapértelmezett beállítások**lehetőségnél.
- Előfordulása Időszakos

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a Docker-kereséshez a rendszergazda és a felhasználóiportálon is keres, az elem helytelenül lesz visszaadva. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
- Szervizkiszolgáló Nincs megoldás.
- Előfordulása Közös

### <a name="upload-blob"></a>BLOB feltöltése

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
- Ok: Ha a felhasználói portálon egy **háttér** -készletet próbál hozzáadni egy Load Balancerhoz, a művelet meghiúsul, és a hibaüzenet **nem tudta frissíteni a Load Balancer.** ...
- Szervizkiszolgáló A PowerShell, a CLI vagy a Resource Manager-sablon használatával társítsa a háttér-készletet egy terheléselosztó-erőforráshoz.
- Előfordulása Közös

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon egy **Load Balancer** **bejövő NAT** -szabályát kísérli meg létrehozni, a művelet meghiúsul, és a hibaüzenet **nem frissül Load Balancer.** ..
- Szervizkiszolgáló A PowerShell, a CLI vagy a Resource Manager-sablon használatával társítsa a háttér-készletet egy terheléselosztó-erőforráshoz.
- Előfordulása Közös

#### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon, a **létrehozás Load Balancer** ablakban a **standard** Load Balancer SKU létrehozási lehetősége látható. Ez a beállítás Azure Stack esetén nem támogatott.
- Szervizkiszolgáló Ehelyett használja az alapszintű Load Balancer beállításait.
- Előfordulása Közös

### <a name="public-ip-address"></a>Nyilvános IP-cím

- Alkalmazható Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: A felhasználói portálon a **nyilvános IP-cím létrehozása** ablakban egy **szabványos** SKU létrehozására szolgáló lehetőség látható. Azure Stack nem támogatja a **standard** SKU-t.
- Szervizkiszolgáló Használja az alapszintű SKU-t a nyilvános IP-címhez.
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
- Ok: A méretezési csoportokat nem lehet eltávolítani a **virtuálisgép** -méretezési csoportok paneljéről.
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

## <a name="next-steps"></a>További lépések

- [A frissítési tevékenységre vonatkozó ellenőrzőlista áttekintése](azure-stack-release-notes-checklist.md)
- [Biztonsági frissítések listájának áttekintése](azure-stack-release-notes-security-updates-1905.md)
