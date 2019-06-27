---
title: Ismert problémák az Azure Stack 1904 |} A Microsoft Docs
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 68a0d80f02e84affa551b296548a09768eba7e1a
ms.sourcegitcommit: 6876ccb85c20794969264a1b27e479f4e938f990
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406804"
---
# <a name="azure-stack-1904-known-issues"></a>Az Azure Stack 1904 ismert problémák

Ez a cikk az Azure Stack 1904 kiadás ismert problémákat ismerteti. A lista frissül új problémák azonosítva.

> [!IMPORTANT]  
> Tekintse át az ebben a szakaszban a frissítés alkalmazása előtt.

## <a name="update-process"></a>Frissítési eljárás

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Próbál telepíteni egy Azure Stack-frissítést, ha a frissítés állapota sikertelen lehet, hogy, és az állapotváltozáshoz **PreparationFailed**. A frissítés erőforrás-szolgáltató (URP) nem képes megfelelően viheti át a fájlokat a storage-tárolót egy belső infrastruktúrát megosztáshoz feldolgozási okozza.
- Szervizelési: 1901 (1.1901.0.95) verziójával kezdődően is használhatja a probléma megoldásához kattintva **frissítés most** újra (nem **folytatása**). A URP ezután törli a fájlokat az előző próbálkozás, és a letöltés ismét elindul.
- Előfordulás: Közös

## <a name="portal"></a>Portál

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

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A rendszergazda és a felhasználói portálon, ha a **Docker**, az elem nem megfelelően adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
- Szervizelési: Nincs megoldás.
- Előfordulás: Közös

### <a name="marketplace-management"></a>Marketplace-en kezelése

- Alkalmazható: Ez az új probléma 1904 kiadással.
- OK: A piactér felügyeleti képernyő a látható esetén nem jelentkezik be a felügyeleti portálon.
- Szervizelési: Frissítse a böngészőt.
- Előfordulás: Időszakos

### <a name="marketplace-management"></a>Marketplace-en kezelése

- Alkalmazható: A probléma 1904 vonatkozik.
- OK: Az eredmények szűrésekor a **hozzáadása az Azure-ból** panelen a felügyeleti portálon a Marketplace-en felügyeleti lap helytelen szűrt eredmények jelenhetnek meg.
- Szervizelési: A neve oszlop szerint rendezheti az eredményeket, és az eredmények ki kell javítani.
- Előfordulás: Időszakos

### <a name="marketplace-management"></a>Marketplace-en kezelése

- Alkalmazható: A probléma 1904 vonatkozik.
- OK: Ha a felügyeleti portálon a Marketplace-en felügyeleti eredményez, látni fogja a duplikált kiadó nevek alapján a közzétevő legördülő. 
- Szervizelési: Válassza ki az összes a Piactéri termékek publisher a rendelkezésre álló helyes lista az ismétlődéseket.
- Előfordulás: Időszakos

### <a name="docker-extension"></a>Docker-bővítmény

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A rendszergazda és a felhasználói portálon, ha a **Docker**, az elem nem megfelelően adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, hibaüzenet jelenik meg.
- Szervizelési: Nincs megoldás.
- Előfordulás: Közös

### <a name="upload-blob"></a>Blob feltöltése

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, ha feltölt egy blobot a OAuth(preview) beállítás használatával próbál a feladat sikertelen, egy hibaüzenet.
- Szervizelési: Töltse fel a blob SAS lehetőséggel.
- Előfordulás: Közös

## <a name="networking"></a>Hálózat

### <a name="load-balancer"></a>Terheléselosztó

#### <a name="add-backend-pool"></a>Háttérkészlet hozzáadása

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, ha megpróbálja hozzáadni egy **Háttérkészlet** , egy **terheléselosztó**, a művelet meghiúsul, a hibaüzenet **nem sikerült frissíteni a Load Balancer...** .
- Szervizelési: Használja a Powershellt, CLI vagy az Azure Resource Manager-sablon a háttérkészlet társítása egy terheléselosztó-erőforráshoz.
- Előfordulás: Közös

#### <a name="create-inbound-nat"></a>Bejövő NAT létrehozása

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon, ha megpróbál létrehozni egy **bejövő NAT-szabály** számára egy **Load Balancer**, a művelet meghiúsul, a hibaüzenet **nem sikerült frissíteni a Load Balancer...** .
- Szervizelési: Használja a Powershellt, CLI vagy az Azure Resource Manager-sablon a háttérkészlet társítása egy terheléselosztó-erőforráshoz.
- Előfordulás: Közös

#### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **Load Balancer létrehozása** ablakban látható lehetőség hozzon létre egy **Standard** terheléselosztó Termékváltozat. Ez a beállítás nem támogatott az Azure Stackben.
- Szervizelési: Az alapszintű load balancer beállítások használata.
- Előfordulás: Közös

#### <a name="public-ip-address"></a>Nyilvános IP-cím

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A felhasználói portálon a **nyilvános IP-cím létrehozása** ablakban látható lehetőség hozzon létre egy **Standard** Termékváltozat. A **Standard** Termékváltozat nem támogatott az Azure Stackben.
- Szervizelési: Használja a **alapszintű** Termékváltozat ehelyett nyilvános IP-címek.
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
- OK: A méretezési készlet nem távolítható el a **Virtual Machine Scale Sets** panelen.
- Szervizelési: Válassza ki a méretezési csoport, hogy el kívánja távolítani, majd kattintson a **törlése** gombra a **áttekintése** ablaktáblán.
- Előfordulás: Közös

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Hibák létrehozása során a javítások és frissítések 4 csomópontos Azure Stack-környezetekben

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A rendelkezésre állási csoport 3 tartalék tartomány a virtuális gépek létrehozása és a egy virtuálisgép-méretezési csoport létrehozása a példány sikertelen lesz, és állítsa be a **FabricVmPlacementErrorUnsupportedFaultDomainSize** hiba történt a frissítés során az egy 4 csomópontos az Azure Stackben környezet.
- Szervizelési: Egy rendelkezésre állási csoport sikeresen 2 hibatűrési tartományt is létrehozhat önálló virtuális gépek. Azonban scale set-példány létrehozása nem még nem érhető el a frissítés során az egy 4 csomópontos Azure Stacken.

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem engedélyezi az SSH-kulcsokat használhatja a bejelentkezni.
- Szervizelési: A Linux-bővítményt a Virtuálisgép-hozzáférési használja az SSH-kulcsok megvalósításához a kiépítés után, vagy a jelszóalapú hitelesítés használatára.
- Előfordulás: Közös

### <a name="compute-host-agent-alert"></a>Gazdagép-ügynök riasztás COMPUTE

- Alkalmazható: Ez az új probléma 1904 kiadással.
- OK: A **számítási gazdagép-ügynök** figyelmeztetés jelenik meg, a skálázási egységben egy csomópont újraindítása után. Az újraindítás az indítási alapértelmezés szerint a számítási ügynök szolgáltatás módosítja. Ez a riasztás a következő példához hasonlóan néz ki:

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

- Szervizelési:
  - Ez a riasztás figyelmen kívül hagyható. Az ügynök nem válaszol nincs hatással az operátor és felhasználói műveletek vagy felhasználói alkalmazás. A riasztás 24 óra múlva újra megjelenik, ha manuálisan le van zárva.
  - A probléma kijavítása a legújabb [Azure Stack-gyorsjavítás a 1904](https://support.microsoft.com/help/4505688).
- Előfordulás: Közös

### <a name="virtual-machine-scale-set-instance-view"></a>Virtuális gép méretezési csoport példányait tartalmazó nézet

- Alkalmazható: A probléma a 1904-es és 1905 kiadások vonatkozik.
- OK: Az Azure Stack portálon található méretezési példányt megtekintési paneljén **irányítópult** > **a Virtual machine scale sets**  >   **AnyScaleSet - példányok** > **AnyScaleSetInstance** nem sikerül betölteni.
- Szervizelési: Jelenleg nem nincs szervizelés, és folyamatban van egy javítás. Addig használja a parancssori felület parancsmag `az vmss get-instance-view` beolvasni a virtuális gép méretezési példányait tartalmazó nézetet.

## <a name="storage"></a>Storage

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) nem támogatott az Azure Stackben és a lemez létrehozása eredményez **$null** azonosítóját. Ez megakadályozza a virtuális Gépen, például az Indítás és leállítás műveleteket végez. A lemez nem jelenik meg a felhasználói felületen, és akkor jelenik meg az API-n keresztül. Ezen a ponton a virtuális gép nem állítható helyre, és törölni kell.
- Szervizelési: A lemezek megfelelően átalakításához kövesse a [konvertálása a felügyelt lemezek útmutató](../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks).

## <a name="app-service"></a>App Service

- Bérlők az előfizetésben az első Azure-függvény létrehozása előtt a storage erőforrás-szolgáltatót regisztrálnia kell.
- Néhány bérlői portál felhasználói élményeket egy alkalmazással a portál keretrendszer való inkompatibilitás miatt megszakadt 1903; alapvetően felhasználói üzembehelyezési pontok, az éles és a helyrendszer-bővítmények tesztelése. A probléma megkerüléséhez használja a [Azure App Service PowerShell-modul](/azure/app-service/deploy-staging-slots#automate-with-powershell) vagy a [Azure CLI-vel](/cli/azure/webapp/deployment/slot?view=azure-cli-latest). A portál felülete visszaállítja a frissítés telepítésének [Azure App Service az Azure Stackben 1.6-os (6. frissítés) a](azure-stack-app-service-release-notes-update-six.md).

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>További lépések

- [Felülvizsgálati tevékenység ellenőrzőlista](azure-stack-release-notes-checklist.md)
- [Tekintse át a biztonsági frissítések listája](azure-stack-release-notes-security-updates-1904.md)
