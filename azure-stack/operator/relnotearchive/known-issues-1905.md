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
ROBOTS: NOINDEX
ms.openlocfilehash: 1cd35416188cbd10827dd74fa6ba702d67c675ad
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248389"
---
# <a name="azure-stack-1905-known-issues"></a>Azure Stack 1905 ismert probléma

Ez a cikk a Azure Stack 1905-es kiadásának ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

> [!IMPORTANT]  
> A frissítés alkalmazása előtt tekintse át ezt a szakaszt.

## <a name="update-process"></a>Frissítési folyamat

### <a name="host-node-update-prerequisite-failure"></a>Gazdagép-csomópont frissítésének előfeltételi hibája

- Alkalmazható: Ez a probléma az 1905-es frissítésre vonatkozik.
- Ok: amikor a 1905 Azure Stack frissítés telepítését kísérli meg, a frissítés állapota a **gazdagép csomópontjának frissítésének előfeltétele** miatt sikertelen lehet. Ezt általában egy olyan gazda-csomópont okozza, amely nem rendelkezik elegendő szabad lemezterülettel.
- Szervizelés: forduljon a Azure Stack támogatási szolgálathoz, és kérjen segítséget a gazdagép-csomóponton található lemezterület törléséhez.
- Előfordulás: nem gyakori

### <a name="preparation-failed"></a>Az előkészítés sikertelen

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a 1905 Azure Stack frissítés telepítésére tett kísérlet során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed** állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz. Az 1905-es frissítési csomag nagyobb, mint az előző frissítési csomagok, amelyek nagyobb valószínűséggel tehetik ezt a problémát.
- Szervizelés: a 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti a problémát, ha a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy manuálisan töltse fel a frissítési csomagot a [frissítések importálása és telepítése szakasz](../azure-stack-apply-updates.md)alapján.
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
- Szervizelés: frissítse a böngészőt, vagy lépjen a **Beállítások** elemre, és válassza az **Alaphelyzetbe állítás lehetőséget az alapértelmezett beállítások** lehetőségnél.
- Előfordulás: időszakos

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a rendszergazda és a felhasználói portálon a **Docker** keresésekor a rendszer helytelenül adja vissza az elemeket. Azure Stack nem érhető el. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
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

### <a name="load-balancer"></a>Terheléselosztóval

#### <a name="add-backend-pool"></a>Háttér-készlet hozzáadása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, ha **háttér-készletet** próbál hozzáadni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem tudta frissíteni a Load Balancer.**...
- Szervizelés: használja a PowerShellt, a CLI-t vagy a Resource Manager-sablont a háttérbeli készlet terheléselosztó-erőforrással való hozzárendeléséhez.
- Előfordulás: gyakori

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon **bejövő NAT-szabályt** próbál létrehozni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem frissül Load Balancer.**..
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

## <a name="compute"></a>Compute

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

## <a name="next-steps"></a>Következő lépések

- [A frissítési tevékenységre vonatkozó ellenőrzőlista áttekintése](../release-notes-checklist.md)
- [Biztonsági frissítések listájának áttekintése](../release-notes-security-updates.md)
