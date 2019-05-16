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
ms.date: 05/15/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/15/2019
ms.openlocfilehash: 86817d0d22854bf2bb0d2372f2a25e15a3de7c48
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712299"
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

- Alkalmazható: Ez az új probléma 1904 kiadással.
- OK: A piactér felügyeleti képernyő a látható esetén nem jelentkezik be a felügyeleti portálon.
- Szervizelési: Frissítse a böngészőt.
- Előfordulás: Időszakos

### <a name="marketplace-management"></a>Marketplace-en kezelése

- Alkalmazható: A probléma 1904 vonatkozik.
- OK: A piactér felügyeleti lapján adminisztrátori portál "Hozzáadása az Azure" panelen eredmények szűrésekor helytelen szűrt eredmények jelenhet meg. 
- Szervizelési: Kijavítjuk neve oszlop szerint rendezheti az eredményeket, és az eredményeket. 
- Előfordulás: Időszakos


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
- OK: A virtuális gép méretezési beállítása (VMSS) létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. CentOS 7.2 nem érhető el az Azure Stacken.
- Szervizelési: Az üzembe helyezéshez használt egy másik operációs rendszert, vagy adjon meg egy másik CentOS lemezképet a marketplace-ről telepítené az üzemeltető által letöltött Azure Resource Manager sablonnal.
- Előfordulás: Közös

#### <a name="remove-scale-set"></a>Méretezési csoport eltávolítása

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: A méretezési készlet nem távolítható el a **Virtual Machine Scale Sets** panelen.
- Szervizelési: Válassza ki a méretezési csoport, hogy el kívánja távolítani, majd kattintson a **törlése** gombra a **áttekintése** ablaktáblán.
- Előfordulás: Közös

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH-hozzáférés

- Alkalmazható: A probléma minden támogatott verziója vonatkozik.
- OK: Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem engedélyezi az SSH-kulcsokat használhatja a bejelentkezni.
- Szervizelési: A Linux-bővítményt a Virtuálisgép-hozzáférési használja az SSH-kulcsok megvalósításához a kiépítés után, vagy a jelszóalapú hitelesítés használatára.
- Előfordulás: Közös

### <a name="compute-host-agent-alert"></a>Gazdagép-ügynök riasztás COMPUTE

- Alkalmazható: Ez az új probléma 1904 kiadással.
- OK: A **számítási gazdagép-ügynök** figyelmeztetés jelenik meg, a skálázási egységben egy csomópont újraindítása után. Az újraindítás az indítási alapértelmezés szerint a számítási ügynök szolgáltatás módosítja.
- Szervizelési:
  - Ez a riasztás figyelmen kívül hagyható. Az ügynök nem válaszol nincs hatással az operátor és felhasználói műveletek vagy felhasználói alkalmazás. A riasztás 24 óra múlva újra megjelenik, ha manuálisan le van zárva.
  - A Microsoft ügyfélszolgálatához a szolgáltatás indítási beállítás módosításával javítani tudja a problémát. Ehhez a támogatási jegy megnyitása. Ha a csomópont újraindítása után a rendszer újra egy új riasztás jelenik meg.
- Előfordulás: Közös

## <a name="app-service"></a>App Service

- Bérlők az előfizetésben az első Azure-függvény létrehozása előtt a storage erőforrás-szolgáltatót regisztrálnia kell.
- Néhány bérlői portál felhasználói élményeket egy alkalmazással a portál keretrendszer való inkompatibilitás miatt megszakadt 1903; alapvetően felhasználói üzembehelyezési pontok, az éles és a helyrendszer-bővítmények tesztelése. A probléma megkerüléséhez használja a [Azure App Service PowerShell-modul](/azure/app-service/deploy-staging-slots#automate-with-powershell) vagy a [Azure CLI-vel](/cli/azure/webapp/deployment/slot?view=azure-cli-latest). A portál felülete az Azure App Service az Azure Stack 1.6-os (6. frissítés) a soron következő kiadásban lesz visszaállítva.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>További lépések

- [Felülvizsgálati tevékenység ellenőrzőlista](azure-stack-release-notes-checklist.md)
- [Tekintse át a biztonsági frissítések listája](azure-stack-release-notes-security-updates-1904.md)
