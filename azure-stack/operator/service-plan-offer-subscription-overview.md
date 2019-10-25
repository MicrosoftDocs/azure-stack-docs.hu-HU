---
title: Azure Stack szolgáltatások, csomagok, ajánlatok, előfizetések áttekintése | Microsoft Docs
description: Azure Stack szolgáltatások, csomagok, ajánlatok és előfizetések áttekintése.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: 2242da61eaa457a8cc234fb3310787eb26b9148e
ms.sourcegitcommit: acebda8a42ac8ecdeba490fc1738e9041479dab0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72814072"
---
# <a name="azure-stack-services-plans-offers-subscriptions-overview"></a>Azure Stack szolgáltatások, csomagok, ajánlatok, előfizetések áttekintése

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A [Microsoft Azure stack](azure-stack-overview.md) egy hibrid felhőalapú platform, amely lehetővé teszi szolgáltatások nyújtását az adatközpontból. A szolgáltatások közé tartoznak a virtuális gépek (VM-EK), SQL Server adatbázisok, a SharePoint, az Exchange és még az [Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md). Szolgáltatóként szolgáltatásokat biztosíthat a bérlőknek. Az üzleti vagy kormányzati szerveken belül helyszíni szolgáltatásokat is biztosíthat az alkalmazottak számára.

## <a name="overview"></a>Áttekintés

Azure Stack kezelőként az ajánlatokat, csomagokat és előfizetéseket használó szolgáltatásokat konfigurálhatja és kézbesítheti. Az ajánlatok egy vagy több csomagot tartalmaznak, és minden csomag egy vagy több szolgáltatást tartalmaz, amelyek mindegyike kvótával van konfigurálva. A csomagok létrehozásával és különböző ajánlatokban való kombinálásával a felhasználók előfizethetnek az ajánlatokra, és üzembe helyezhetik az erőforrásokat. Ez a struktúra lehetővé teszi a következők kezelését:

- Mely szolgáltatások és erőforrások férhetnek hozzá a felhasználókhoz.
- A felhasználók által felhasznált erőforrások mennyisége.
- Mely régiók férhetnek hozzá az erőforrásokhoz.

A szolgáltatás nyújtásához kövesse az alábbi magas szintű lépéseket:

1. Tervezze meg a szolgáltatás ajánlatát a használatával:

   - Olyan alapszolgáltatások, mint a számítás, a tárolás, a hálózatkezelés vagy a Key Vault.
   - Kiegészítő szolgáltatások, például App Service, SQL Server vagy MySQL-kiszolgáló.

2. Hozzon létre egy csomagot, amely egy vagy több szolgáltatást tartalmaz. A csomag létrehozásakor válasszon ki vagy hozzon létre kvótákat, amelyek meghatározzák a csomagban lévő egyes szolgáltatások erőforrás-korlátozásait.
3. Hozzon létre egy ajánlatot, amely egy vagy több csomaggal rendelkezik. Az ajánlat tartalmazhat alapcsomagokat és opcionális kiegészítő csomagokat.

Az ajánlat létrehozása után a felhasználók előfizethetnek rá a szolgáltatások eléréséhez és az erőforrások üzembe helyezéséhez. A felhasználók tetszőleges számú ajánlatra fizethetnek. Az alábbi ábra egy egyszerű példát mutat be a két ajánlatra előfizetett felhasználóra. Minden ajánlat rendelkezik egy csomaggal vagy kettővel, és minden csomag adott szolgáltatásokhoz biztosít hozzáférést.

![Bérlői előfizetés ajánlatokkal és csomagokkal](media/azure-stack-key-features/image4.png)

## <a name="services"></a>Szolgáltatások

Olyan infrastruktúra- [szolgáltatási](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) szolgáltatásokat biztosíthat, amelyek lehetővé teszik a felhasználók számára, hogy igény szerinti számítástechnikai infrastruktúrát hozzanak létre, amelyet a Azure stack felhasználói portálon üzembe kell helyezni és felügyelni.

A Microsofttól és más külső szolgáltatóktól származó Azure Stack platformként is üzembe helyezheti a [szolgáltatásként](https://azure.microsoft.com/overview/what-is-paas/) nyújtott szolgáltatások (Pásti) szolgáltatásait. A megadható Pásti-szolgáltatások közé tartoznak a következők:

- [APP SERVICE](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [MySQL-kiszolgáló](azure-stack-mysql-resource-provider-deploy.md)

A különböző felhasználók számára összetett megoldások integrálására és kiépítésére is használhatja a szolgáltatásokat.

### <a name="quotas"></a>Kvóták

A felhő kapacitásának kezeléséhez előre konfigurált *kvótákat*használhat, vagy létrehozhat egy új kvótát a csomag minden szolgáltatásához. A kvóták azt a felső erőforrás-korlátot határozzák meg, amelyet a felhasználói előfizetés tud kiépíteni vagy felhasználni. Egy kvóta megszabhatja például, hogy egy felhasználó legfeljebb öt virtuális gépet hozhat létre.

> [!IMPORTANT]
> Akár két óráig is eltarthat, amíg az új kvóták elérhetővé válnak a felhasználói portálon, vagy a módosított kvóta érvénybe léptetése előtt.

A kvótákat régiónként is beállíthatja. Például egy olyan csomag, amely számítási szolgáltatásokat biztosít az A régióhoz, két virtuális gép kvótája lehet.

>[!NOTE]
>A Azure Stack Development Kit (ASDK) esetében csak egy régió ( *helyi*) érhető el.

További információ a [Azure Stackban található kvóták típusairól](azure-stack-quota-types.md).

## <a name="plans"></a>Csomagok

A Csomagok egy vagy több szolgáltatás csoportjai. Azure Stack operátorként a felhasználók számára ajánlatos [terveket hozhat létre](azure-stack-create-plan.md) . A felhasználói előfizethetnek az ajánlataira, hogy használhassák a bennük foglalt csomagokat és szolgáltatásokat. A csomagok létrehozásakor ügyeljen arra, hogy beállítja a kvótákat, definiálja az alapterveket, és vegye figyelembe a választható kiegészítő csomagokat is.

### <a name="base-plan"></a>Alapcsomag

Ajánlat létrehozásakor a szolgáltatás rendszergazdája egy alapcsomagot is tartalmazhat. Ezek az alapcsomagok alapértelmezés szerint bekerülnek, amikor egy felhasználó Előfizet erre az ajánlatra. Amikor egy felhasználó előfizet, hozzáfér az ezen alapcsomagokban megadott összes erőforrás-szolgáltatóhoz (a megfelelő kvótákkal együtt).

### <a name="add-on-plans"></a>Kiegészítő csomagok

A kiegészítő csomagok az ajánlathoz hozzáadott opcionális csomagok. Alapértelmezés szerint az előfizetés nem tartalmazza a bővítmények csomagjait. A kiegészítő csomagok olyan ajánlatokban elérhető további csomagok (kvótákkal), amelyeket az előfizető hozzáadhat az előfizetésekhez. Létrehozhat például egy alapszintű csomagot korlátozott erőforrásokkal a próbaverzióhoz, valamint egy kiegészítő tervet, amely nagyobb mennyiségű erőforrást biztosít a szolgáltatás bevezetését elfogadó ügyfeleknek.

## <a name="offers"></a>Ajánlatok

Az ajánlatok egy vagy több, az Ön által létrehozott csomag csoportjai, hogy a felhasználók előfizethetnek rájuk. Például: az Alpha ajánlat az A csomagot is tartalmazhatja, amely a számítási szolgáltatásokat és a B tervet tartalmazza, amely a tárolási és hálózati szolgáltatások készletét biztosítja.

[Ajánlat létrehozásakor](azure-stack-create-offer.md)meg kell adnia legalább egy alapcsomagot, de létrehozhat olyan kiegészítő csomagokat is, amelyeket a felhasználók hozzáadhatnak az előfizetéshez.

Az ajánlatok megtervezése során tartsa szem előtt a következő szempontokat:

**Próbaverziós ajánlatok**: próbaverziós ajánlatokat használ új felhasználók számára, akik később további szolgáltatásokra frissíthetnek. Egy próbaverziós ajánlat létrehozásához hozzon létre egy kisebb [alapcsomagot](service-plan-offer-subscription-overview.md#base-plan) egy opcionálisan nagyobb kiegészítő csomaggal. Alternatív megoldásként létrehozhat egy kisméretű alapcsomagból álló próbaverziós ajánlatot, és egy külön ajánlatot, amely nagyobb "díjköteles" csomaggal rendelkezik.

**Kapacitás megtervezése**: érdemes lehet olyan felhasználókra vonatkozni, akik nagy mennyiségű erőforrást megragadnak, és a rendszer minden felhasználó számára akadoznak. A teljesítmény elősegítése érdekében [a terveit a kvóták használatával állíthatja be](service-plan-offer-subscription-overview.md#plans) a maximális kihasználtság érdekében.

**Delegált szolgáltatók**: engedélyezheti másoknak, hogy ajánlatokat hozzanak létre a környezetben. Ha például szolgáltató, akkor ezt a lehetőséget [delegálhatja](azure-stack-delegated-provider.md) a viszonteladóknak. Vagy ha Ön szervezet, delegálhat más részlegeknek/leányvállalatoknak is.

## <a name="subscriptions"></a>Előfizetések

Az előfizetések lehetővé teszik a felhasználóknak az ajánlatok elérését. Ha Ön Azure Stack szolgáltató, a felhasználók (bérlők) vásárolják meg a szolgáltatásait az ajánlatokra való feliratkozással. Ha Ön egy szervezet Azure Stack operátora, a felhasználók (alkalmazottak) előfizethetnek az Ön által kínált szolgáltatásokra a fizetés nélkül.

A felhasználók új előfizetéseket hoznak létre, és hozzáférést kapnak a meglévő előfizetésekhez, ha bejelentkeznek a Azure Stackba. Az egyes előfizetések egyetlen ajánlattal való társítást jelölnek. Az egyik előfizetéshez rendelt ajánlat (és annak csomagjai és kvótái) nem oszthatók meg más előfizetésekkel. A felhasználó által létrehozott összes erőforrás egy előfizetéshez van társítva.

### <a name="default-provider-subscription"></a>Alapértelmezett szolgáltatói előfizetés

A ASDK telepítésekor a rendszer automatikusan létrehozza az alapértelmezett szolgáltatói előfizetést. Ez az előfizetés a Azure Stack kezelésére, további erőforrás-szolgáltatók üzembe helyezésére, valamint csomagok és ajánlatok létrehozására használható a felhasználók számára. Biztonsági és licencelési okokból nem használható az ügyfél-munkaterhelések és alkalmazások futtatására. Az alapértelmezett szolgáltatói előfizetés kvótája nem módosítható.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a csomagok, ajánlatok és előfizetések létrehozásáról, kezdje a [terv létrehozásával](azure-stack-create-plan.md).
