---
title: Azure Stack 1904 ismert probléma | Microsoft Docs
description: Ismerje meg a Azure Stack 1904 ismert problémáit.
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/31/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f977c1671468257ac205c494f33ad46965f1f2eb
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248398"
---
# <a name="azure-stack-1904-known-issues"></a>Azure Stack 1904 ismert probléma

Ez a cikk a Azure Stack 1904-es kiadásának ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

> [!IMPORTANT]  
> A frissítés alkalmazása előtt tekintse át ezt a szakaszt.

## <a name="update-process"></a>Frissítési folyamat

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Azure Stack frissítés telepítésére tett kísérlet során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed** állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.
- Szervizelés: a 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti a problémát, ha a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli a fájlokat az előző kísérletből, és újra elindítja a letöltést.
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

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a rendszergazda és a felhasználói portálon a **Docker** keresésekor a rendszer helytelenül adja vissza az elemeket. Azure Stack nem érhető el. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
- Szervizelés: nincs megoldás.
- Előfordulás: gyakori

### <a name="marketplace-management"></a>Piactér-kezelés

- Alkalmazható: ez egy új probléma a 1904-es kiadásban.
- Ok: a piactér-felügyeleti képernyő nem látható, ha bejelentkezik a felügyeleti portálra.
- Szervizelés: frissítse a böngészőt.
- Előfordulás: időszakos

### <a name="marketplace-management"></a>Piactér-kezelés

- Alkalmazható: Ez a probléma a 1904-es verzióra vonatkozik.
- Ok: Ha a felügyeleti portál piactér-kezelés lapján a **Hozzáadás az Azure-ból** lehetőségre kattint, előfordulhat, hogy helytelenül szűrt eredmények jelennek meg.
- Szervizelés: az eredményeket a Name (név) oszlop alapján rendezi, és a rendszer kijavítja az eredményeket.
- Előfordulás: időszakos

### <a name="marketplace-management"></a>Piactér-kezelés

- Alkalmazható: Ez a probléma a 1904-es verzióra vonatkozik.
- Ok: amikor a felügyeleti portálon kiszűri a piactér-felügyelet eredményeit, a közzétevő legördülő lista duplikált közzétevők nevét fogja látni. 
- Szervizelés: válassza ki az összes ismétlődést, hogy az adott közzétevőnél elérhető Piactéri termékek megfelelő listáját tartalmazza.
- Előfordulás: időszakos

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a rendszergazda és a felhasználói portálon a **Docker** keresésekor a rendszer helytelenül adja vissza az elemeket. Azure Stack nem érhető el. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
- Szervizelés: nincs megoldás.
- Előfordulás: gyakori

### <a name="upload-blob"></a>BLOB feltöltése

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon, amikor megpróbál feltölteni egy blobot a OAuth (előzetes verzió) beállítással, a feladat hibaüzenettel meghiúsul.
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
- Szervizelés: használja a PowerShell, a CLI vagy egy Azure Resource Manager sablont, amellyel a háttér-készletet egy terheléselosztó-erőforrással társíthatja.
- Előfordulás: gyakori

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: Ha a felhasználói portálon **bejövő NAT-szabályt** próbál létrehozni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem frissül Load Balancer.**..
- Szervizelés: használja a PowerShell, a CLI vagy egy Azure Resource Manager sablont, amellyel a háttér-készletet egy terheléselosztó-erőforrással társíthatja.
- Előfordulás: gyakori

#### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **létrehozás Load Balancer** ablak egy **standard** Load Balancer SKU létrehozására szolgáló lehetőséget mutat be. Ez a beállítás Azure Stack esetén nem támogatott.
- Szervizelés: használja helyette az alapszintű Load Balancer beállításait.
- Előfordulás: gyakori

#### <a name="public-ip-address"></a>Nyilvános IP-cím

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói portálon a **nyilvános IP-cím létrehozása** ablak egy **szabványos** SKU létrehozására szolgáló lehetőséget mutat be. Azure Stack nem támogatja a **standard** SKU-t.
- Szervizelés: a nyilvános IP-címek helyett használja az **alapszintű** SKU-t.
- Előfordulás: gyakori

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>VM rendszerindítási diagnosztika

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg: **nem sikerült elindítani a virtuális gépet (VM-Name). Hiba: nem sikerült frissíteni a virtuális gép (VM-név) soros kimeneti beállításait**.
A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját.
- Szervizelés: hozza létre újra a Storage-fiókot a korábban használt névvel.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

#### <a name="centos"></a>CentOS

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuálisgép-méretezési csoport létrehozási felülete CentOS-alapú 7,2-as lehetőséget biztosít az üzembe helyezéshez. A CentOS 7,2 Azure Stack piactéren nem érhető el, ezért a telepítési hibák miatt a rendszer nem találja a rendszerképet.
- Szervizelés: válasszon másik operációs rendszert az üzemelő példányhoz, vagy használjon egy Azure Resource Manager sablont, amely egy másik, a piactéren az operátor általi üzembe helyezés előtt letöltött CentOS-rendszerképet ad meg.
- Előfordulás: gyakori

#### <a name="remove-scale-set"></a>Méretezési csoport eltávolítása

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a méretezési csoport nem távolítható el a **Virtual Machine Scale sets** panelről.
- Szervizelés: válassza ki az eltávolítani kívánt méretezési készletet, majd kattintson a **Törlés** gombra az **Áttekintés** ablaktáblán.
- Előfordulás: gyakori

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása a javítás és a frissítés során 4 csomópontos Azure Stack környezetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gépek létrehozása 3 tartalék tartományból álló rendelkezésre állási csoportba, a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack-környezet frissítési folyamata során.
- Szervizelés: egyetlen virtuális gépet hozhat létre egy rendelkezésre állási csoportba 2 tartalék tartománnyal. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- Szervizelés: virtuálisgép-hozzáférés használata a Linux-bővítményhez SSH-kulcsok implementálása a kiépítés után vagy jelszó alapú hitelesítés használatával.
- Előfordulás: gyakori

### <a name="compute-host-agent-alert"></a>Számítási gazdagép ügynökének riasztása

- Alkalmazható: ez egy új probléma a 1904-es kiadásban.
- Ok: A **számítási gazdagép ügynöke** figyelmeztetést jelenít meg egy csomópontnak a skálázási egységben való újraindítása után. Az újraindítás megváltoztatja a számítási gazdagép ügynöke szolgáltatás alapértelmezett indítási beállítását. Ez a riasztás az alábbi példához hasonlóan néz ki:

   ```shell
   NAME  
   Compute Host Agent is not responding to calls.
   SEVERITY  
   Warning
   STATE  
   Active
   CREATED TIME  
   5/16/2019, 10:08:23 AM
   UPDATED TIME  
   5/22/2019, 12:27:27 PM
   COMPONENT  
   M#####-NODE02
   DESCRIPTION  
   Could not communicate with the Compute Host Agent running on node: M#####-NODE02
   REMEDIATION  
   Please disable Compute Host Agent feature flag and collect logs for further diagnosis.
   ```

- Szervizkiszolgáló
  - Ez a riasztás figyelmen kívül hagyható. Az ügynök nem válaszol az operátorra és a felhasználói műveletekre, illetve a felhasználói alkalmazásokra. Ha a riasztást manuálisan lezárták, a riasztás 24 óra elteltével újra megjelenik.
  - A probléma a 1904-es legújabb [Azure stack gyorsjavításban](https://support.microsoft.com/help/4505688)van kijavítva.
- Előfordulás: gyakori

### <a name="virtual-machine-scale-set-instance-view"></a>Virtuálisgép-méretezési csoport példányainak nézete

- Alkalmazható: Ez a probléma a 1904-es és a 1905-as kiadásra vonatkozik.
- Ok: a Azure stack-portálon található méretezési csoport példány nézetének **panelje az irányítópult**  >  **virtuálisgép-méretezési** csoportok  >  **AnyScaleSet-példányok**  >  **AnyScaleSetInstance** nem töltődik be.
- Szervizelés: jelenleg nincs szervizelés, és a javításon dolgozunk. Addig is használja a CLI parancsmagot `az vmss get-instance-view` egy virtuálisgép-méretezési csoport példány nézetének lekéréséhez.

### <a name="user-image-service"></a>Felhasználói rendszerkép szolgáltatás
- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a felhasználói rendszerkép létrehozása nem sikerült, mert a rendszerkép-szolgáltatás helytelen állapotba kerül. A felhasználói rendszerkép létrehozási és törlési műveletei sikertelenek lesznek. A felhasználói lemezkép törlése a következő hiba miatt meghiúsulhat: "hiba: belső Lemezkezelés hiba történt."
- Szervizelés: nincs megoldás. Nyisson meg egy támogatási jegyet a Microsofttal.

## <a name="storage"></a>Tárolás

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) nem támogatott a Azure Stackban, és **$Null** azonosítóval rendelkező lemez létrehozását eredményezi. Ez megakadályozza, hogy a virtuális gépen műveleteket végezzen, például indítás és leállítás. A lemez nem jelenik meg a felhasználói felületen, és nem jelenik meg az API-n keresztül. Ezen a ponton nem lehet kijavítani a virtuális gépet, és azt törölni kell.
- Szervizelés: a lemezek megfelelő átalakításához kövesse a [Konvertálás felügyelt lemezekre útmutatót](../../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks).

## <a name="app-service"></a>App Service

- A bérlőknek regisztrálniuk kell a tárolási erőforrás-szolgáltatót, mielőtt az első Azure-függvényt létrehozná az előfizetésben.
- Néhány bérlői portál felhasználói felülete a 1903-es portál-keretrendszerrel való inkompatibilitás miatt megszakadt. a központi telepítéshez használható UX, az éles környezetben történő tesztelés és a helyi bővítmények. A probléma megkerüléséhez használja a [Azure app Service PowerShell-modult](/azure/app-service/deploy-staging-slots#automate-with-powershell) vagy az [Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest&preserve-view=true)-t. A portálon a [Azure App Service Azure stack-ról 1,6-re (Update 6) történő](../azure-stack-app-service-release-notes-update-six.md)frissítésével állítható vissza.

## <a name="next-steps"></a>Következő lépések

- [A frissítési tevékenységre vonatkozó ellenőrzőlista áttekintése](../release-notes-checklist.md)
- [Biztonsági frissítések listájának áttekintése](../release-notes-security-updates.md)
