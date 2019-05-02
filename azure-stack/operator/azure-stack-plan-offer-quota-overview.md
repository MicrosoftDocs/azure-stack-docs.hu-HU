---
title: Az Azure Stack csomag, ajánlat, kvóta és előfizetés áttekintése |} A Microsoft Docs
description: Felhő-felelősként szeretnék megtudni az Azure Stack csomagok, ajánlatok, kvóták és előfizetések.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 10/12/2018
ms.openlocfilehash: 8aa75e3a66fbec2494a653fb62405fe6c9134d7d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985213"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Csomag, ajánlat, kvóta és előfizetés áttekintése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

[Az Azure Stack](azure-stack-overview.md) lehetővé teszi, hogy számos különböző szolgáltatások, például a virtuális gépek, SQL Server-adatbázisok, a SharePoint, Exchange, sőt akár [Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md). Az Azure Stack operátorait szerint konfigurálja, és ezek a szolgáltatások biztosításához az Azure Stackben csomagok, ajánlatok és kvóták használatával.

Ajánlatok tartalmaznak egy vagy több terveket, és minden csomag egy vagy több szolgáltatást is magában foglal. Csomagok létrehozása, és azokat összefűzhet ajánlatáról, kezelheti:

- Melyik szolgáltatások és erőforrások a felhasználók férhetnek hozzá.
- A felhasználók akkor használhatnak erőforrások mennyiségét.
- Mely régiók rendelkezik az erőforrásokhoz való hozzáférést.

Ha valamilyen szolgáltatás biztosításához, kövesse az alábbi magas szintű lépéseket:

1. Adjon hozzá egy szolgáltatás, amely a felhasználók számára szeretné.
2. Hozzon létre egy csomagot, amely rendelkezik egy vagy több szolgáltatást. Amikor csomagot hoz létre, válassza ki, vagy a kvóták, amelyek meghatározzák az egyes szolgáltatások erőforráskorlátok a csomag létrehozása.
3. Hozzon létre egy ajánlatot, amely egy vagy több csomag tartalmazza. Az ajánlat alapcsomagok és a választható kiegészítő csomagok tartalmazhatnak.

Miután létrehozta az ajánlatra, a felhasználók előfizethetnek hozzáférjen a szolgáltatásokhoz és erőforrásokhoz, az ajánlat biztosít. Felhasználók is előfizethetnek azokra annyi konfigurálhatnak. Az alábbi ábra egy olyan felhasználó, aki két ajánlatok előfizetett egyszerű példát mutat be. Minden ajánlat esetében a szolgáltatáscsomag vagy a kettő, és minden a terv elérhetik a szolgáltatásokat.

![Bérlő előfizetési ajánlatok és csomagok](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Tervek

A Csomagok egy vagy több szolgáltatás csoportjai. Az Azure Stack operátorait szerint Ön [terveket hozhat létre](azure-stack-create-plan.md) nyújtani a felhasználóknak. A felhasználói előfizethetnek az ajánlataira, hogy használhassák a bennük foglalt csomagokat és szolgáltatásokat. Tervek létrehozásakor ügyeljen arra, hogy a kvóták beállítása, a alapcsomagok definiálása és fontolja meg, beleértve a választható kiegészítő csomagok.

### <a name="quotas"></a>Kvóták

Használhatja, amelyek segítségével kezelheti a felhő kapacitása, előre konfigurált *kvóták*, vagy hozzon létre egy új kvótát az egyes szolgáltatások egy csomagban. Kvóták, amely a felhasználói előfizetés is üzembe helyezése és felhasználása felső erőforráskorlátok adja meg. Például kvóta lehetővé teheti a felhasználó legfeljebb öt virtuális gépek (VM) létrehozásához.

> [!IMPORTANT]
> Új kvóták csak akkor érhető el, a felhasználói portálon, vagy előtt módosított kvóták érvényesítése akár két órát is igénybe vehet.

Beállíthatja, hogy kvótái régiónként. Például terület A számítási szolgáltatásokat nyújt a csomagot lehet két virtuális gépet egy kvótáját.

>[!NOTE]
>Az az Azure Stack Development Kit, csak egy régió (nevű *helyi*) érhető el.

Tudjon meg többet [kvótatípusok az Azure Stackben](azure-stack-quota-types.md).

### <a name="base-plan"></a>Alapcsomag

Az ajánlat létrehozásakor a szolgáltatás-rendszergazda tartalmazhat egy alapcsomagot. Ezek alapcsomagok szerepelnek alapértelmezés szerint, amikor a felhasználó az ajánlat. Amikor a felhasználó, az e alap terveket (és a megfelelő kvóták) a megadott összes erőforrás-szolgáltató hozzáféréssel rendelkeznek.

### <a name="add-on-plans"></a>Kiegészítő csomagok

Kiegészítő csomagok tervekben választható ajánlat ad hozzá. Az előfizetés alapértelmezés szerint nem szerepelnek a kiegészítő csomagok. Kiegészítő csomagok további tervek (és kvóták), egy előfizető hozzáadhat saját előfizetések ajánlat érhető el. Például egy alapcsomagot a korlátozott erőforrásokkal ingyenes próbaverziójára, és a egy kiegészítő csomag további lényeges erőforrásokkal is kínálnak az ügyfelek számára, akik úgy dönt, hogy a szolgáltatás fogad el.

## <a name="offers"></a>Ajánlatok

Ajánlatok egy vagy több tervek, hogy a felhasználók előfizethetnek az őket létrehozó csoportjai. Például ajánlat Alpha terv A, amely biztosítja a számítási szolgáltatások és a terv B, amely biztosítja a tárolási és hálózati szolgáltatások tartalmazhat.

Ha Ön [hozzon létre egy ajánlatot](azure-stack-create-offer.md), akkor tartalmaznia kell legalább egy alapcsomagot, de is létrehozhat, amelyek felhasználókat adhat hozzá az előfizetés kiegészítő csomagok.

## <a name="subscriptions"></a>Subscriptions

Az előfizetés egy, hogy a felhasználók miként férhetnek hozzá az ajánlatok. Ha Ön az Azure Stack operátorait szolgáltató, a a felhasználók (bérlők) a szolgáltatások megvásárlása az ajánlatok való feliratkozással. Ha egy szervezet az Azure Stack operátorait, a felhasználók (alkalmazottak) előfizethetnek anélkül kínált szolgáltatásokat.

Egy felhasználó egy ajánlatot az egyes kombinációja egy egyedi előfizetésre szükség. Felhasználók még több ajánlatok, előfizetések, de az egyes előfizetésekhez csak egyszer veheti igénybe az vonatkozik. Csomagok, ajánlatok és kvóták csak az egyedi előfizetés a alkalmazni,:, előfizetések között nem lehet megosztani. Egy felhasználó által létrehozott minden erőforrás egy előfizetéssel társítva.

### <a name="default-provider-subscription"></a>Alapértelmezett szolgáltatói előfizetés

Az alapértelmezett szolgáltatója előfizetést az Azure Stack Development Kit központi telepítésekor automatikusan létrejön. Ez az előfizetés kezelése az Azure Stack, további erőforrás-szolgáltató üzembe helyezése és csomagokat és ajánlatokat felhasználók létrehozásához használható. Biztonsági és licencelési okokból azt nem használandó ügyfél számítási feladatok és alkalmazások futtatásához.

## <a name="next-steps"></a>További lépések

További információ a csomagok és ajánlatok típusa: [hozzon létre egy csomagot](azure-stack-create-plan.md).
