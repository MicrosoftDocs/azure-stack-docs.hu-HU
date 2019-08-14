---
title: Azure Stack terv, ajánlat, kvóta és előfizetés áttekintése | Microsoft Docs
description: Felhőbeli operátorként szeretnék megérteni Azure Stack terveket, ajánlatokat, kvótákat és előfizetéseket.
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
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 10/12/2018
ms.openlocfilehash: 6f417b8afdf5315f581db52dc4b850f72e02b1f0
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991736"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Csomag, ajánlat, kvóta és előfizetés áttekintése

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

[Azure stack](azure-stack-overview.md) lehetővé teszi a szolgáltatások, például a virtuális gépek, a SQL Server adatbázisok, a SharePoint, az Exchange és még az [Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md)széles választékának biztosítását. Azure Stack kezelőként a csomagok, ajánlatok és kvóták használatával konfigurálhatja és kézbesítheti ezeket a szolgáltatásokat Azure Stackokban.

Az ajánlatok egy vagy több csomagot tartalmaznak, és az egyes csomagok egy vagy több szolgáltatást foglalnak magukban. A csomagok létrehozásával és különböző ajánlatokban való kombinálásával a következőket kezelheti:

- Mely szolgáltatások és erőforrások férhetnek hozzá a felhasználókhoz.
- A felhasználók által felhasznált erőforrások mennyisége.
- Mely régiók férhetnek hozzá az erőforrásokhoz.

Szolgáltatás nyújtásakor kövesse az alábbi magas szintű lépéseket:

1. Adja meg a felhasználók számára kézbesíteni kívánt szolgáltatást.
2. Hozzon létre egy csomagot, amely egy vagy több szolgáltatást tartalmaz. A csomag létrehozásakor válasszon ki vagy hozzon létre kvótákat, amelyek meghatározzák a csomagban lévő egyes szolgáltatások erőforrás-korlátozásait.
3. Hozzon létre egy ajánlatot, amely egy vagy több tervet tartalmaz. Az ajánlat tartalmazhat alapcsomagokat és opcionális kiegészítő csomagokat.

Az ajánlat létrehozása után a felhasználók előfizethetnek arra, hogy hozzáférjenek az ajánlat által biztosított szolgáltatásokhoz és erőforrásokhoz. A felhasználók tetszőleges számú ajánlatra fizethetnek. Az alábbi ábra egy egyszerű példát mutat be a két ajánlatra előfizetett felhasználóra. Minden ajánlat rendelkezik egy csomaggal vagy kettővel, és minden csomag hozzáférést biztosít számukra a szolgáltatásokhoz.

![Bérlői előfizetés ajánlatokkal és csomagokkal](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Csomagok

A Csomagok egy vagy több szolgáltatás csoportjai. Azure Stack operátorként a felhasználók számára ajánlatos [terveket hozhat létre](azure-stack-create-plan.md) . A felhasználói előfizethetnek az ajánlataira, hogy használhassák a bennük foglalt csomagokat és szolgáltatásokat. A csomagok létrehozásakor ügyeljen arra, hogy beállítja a kvótákat, definiálja az alapterveket, és vegye figyelembe a választható kiegészítő csomagokat is.

### <a name="quotas"></a>Kvóták

A felhő kapacitásának kezeléséhez használhatja az előre konfigurált kvótákat, vagylétrehozhat egy új kvótát a csomag minden szolgáltatásához. A kvóták azt a felső erőforrás-korlátot határozzák meg, amelyet a felhasználói előfizetés tud kiépíteni vagy felhasználni. Egy kvóta például lehetővé teheti, hogy egy felhasználó legfeljebb öt virtuális gépet hozzon létre.

> [!IMPORTANT]
> Akár két óráig is eltarthat, amíg az új kvóták elérhetővé válnak a felhasználói portálon, vagy a módosított kvóta érvénybe léptetése előtt.

A kvótákat régiónként is konfigurálhatja. Például egy olyan csomag, amely számítási szolgáltatásokat biztosít az A régióhoz, két virtuális gép kvótája lehet.

>[!NOTE]
>A Azure Stack Development Kitban csak egy régió ( *Local*) érhető el.

További információ a [Azure Stackban található kvóták típusairól](azure-stack-quota-types.md).

### <a name="base-plan"></a>Alapcsomag

Ajánlat létrehozásakor a szolgáltatás rendszergazdája egy alapcsomagot is tartalmazhat. Ezek az alapcsomagok alapértelmezés szerint bekerülnek, amikor egy felhasználó Előfizet erre az ajánlatra. Amikor egy felhasználó előfizet, hozzáfér az ezen alapcsomagokban megadott összes erőforrás-szolgáltatóhoz (a megfelelő kvótákkal együtt).

### <a name="add-on-plans"></a>Kiegészítő csomagok

A kiegészítő csomagok az ajánlathoz hozzáadott opcionális csomagok. A bővítményi csomagok alapértelmezés szerint nem szerepelnek az előfizetésben. A kiegészítő csomagok olyan ajánlatokban elérhető további csomagok (kvótákkal), amelyeket az előfizető hozzáadhat az előfizetésekhez. Létrehozhat például egy alapszintű csomagot korlátozott erőforrásokkal a próbaverzióhoz, valamint egy kiegészítő tervet, amely nagyobb mennyiségű erőforrást biztosít a szolgáltatás bevezetését elfogadó ügyfeleknek.

## <a name="offers"></a>Ajánlatok

Az ajánlatok egy vagy több, az Ön által létrehozott csomag csoportjai, hogy a felhasználók előfizethetnek rájuk. Az ajánlat alfaja például az A csomagot is tartalmazhatja, amely a számítási szolgáltatásokat és a B tervet tartalmazza, amely a tárolási és hálózati szolgáltatások készletét biztosítja.

[Ajánlat létrehozásakor](azure-stack-create-offer.md)meg kell adnia legalább egy alapcsomagot, de létrehozhat olyan kiegészítő csomagokat is, amelyeket a felhasználók hozzáadhatnak az előfizetéshez.

## <a name="subscriptions"></a>Előfizetések

Az előfizetés azt ismerteti, hogy a felhasználók hogyan férhetnek hozzá az ajánlatokhoz. Ha Ön Azure Stack szolgáltató, a felhasználók (bérlők) vásárolják meg szolgáltatásait az ajánlatokra való feliratkozással. Ha Ön egy szervezet Azure Stack operátora, a felhasználók (alkalmazottak) előfizethetnek az Ön által kínált szolgáltatásokra a fizetés nélkül.

Egy ajánlattal rendelkező felhasználó minden kombinációja egyedi előfizetés. Egy felhasználó több ajánlatra is rendelkezhet, de az egyes előfizetések csak egyetlen ajánlatra érvényesek. A csomagok, ajánlatok és kvóták csak egyedi előfizetésre érvényesek, nem oszthatók meg az előfizetések között. A felhasználó által létrehozott összes erőforrás egy előfizetéshez van társítva.

### <a name="default-provider-subscription"></a>Alapértelmezett szolgáltatói előfizetés

Az alapértelmezett szolgáltatói előfizetés automatikusan létrejön a Azure Stack Development Kit telepítésekor. Ez az előfizetés a Azure Stack kezelésére, további erőforrás-szolgáltatók üzembe helyezésére, valamint csomagok és ajánlatok létrehozására használható a felhasználók számára. Biztonsági és licencelési okokból nem ajánlott az ügyfél-munkaterhelések és alkalmazások futtatására használni.

## <a name="next-steps"></a>További lépések

A csomagokkal és ajánlatokkal kapcsolatos további információkért tekintse meg [a terv létrehozása](azure-stack-create-plan.md)című témakört.
