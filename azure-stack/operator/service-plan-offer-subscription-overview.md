---
title: Azure Stack hub-szolgáltatások, csomagok, ajánlatok, előfizetések áttekintése
description: Azure Stack hub szolgáltatások, csomagok, ajánlatok és előfizetések áttekintése.
author: BryanLa
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: 00a44e66563bbb038928c55f1f643f2ca0ffbd37
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93327472"
---
# <a name="azure-stack-hub-services-plans-offers-subscriptions-overview"></a>Azure Stack hub-szolgáltatások, csomagok, ajánlatok, előfizetések áttekintése

Az [Microsoft Azure stack hub](azure-stack-overview.md) egy hibrid felhőalapú platform, amely lehetővé teszi szolgáltatások nyújtását az adatközpontból. A szolgáltatások közé tartoznak a virtuális gépek (VM-EK), SQL Server adatbázisok, a SharePoint, az Exchange és még az [Azure Marketplace-elemek](azure-stack-marketplace-azure-items.md). Szolgáltatóként szolgáltatásokat biztosíthat a bérlőknek. Az üzleti vagy kormányzati szerveken belül helyszíni szolgáltatásokat is biztosíthat az alkalmazottak számára.

## <a name="overview"></a>Áttekintés

Azure Stack hub-kezelőként az ajánlatokat, csomagokat és előfizetéseket használva konfigurálhatja és kézbesítheti a szolgáltatásokat. Az ajánlatok egy vagy több csomagot tartalmaznak, és minden csomag egy vagy több szolgáltatást tartalmaz, amelyek mindegyike kvótával van konfigurálva. A csomagok létrehozásával és különböző ajánlatokban való kombinálásával a felhasználók előfizethetnek az ajánlatokra, és üzembe helyezhetik az erőforrásokat. Ez a struktúra lehetővé teszi a következők kezelését:

- Mely szolgáltatások és erőforrások férhetnek hozzá a felhasználókhoz.
- A felhasználók által felhasznált erőforrások mennyisége.
- Mely régiók férhetnek hozzá az erőforrásokhoz.

A szolgáltatás nyújtásához kövesse az alábbi magas szintű lépéseket:

1. Tervezze meg a szolgáltatás ajánlatát a használatával:

   - Olyan alapszolgáltatások, mint a számítás, a tárolás, a hálózatkezelés vagy a Key Vault.
   - Value-szolgáltatások hozzáadása, például Event Hubs, App Service, SQL Server vagy MySQL-kiszolgáló.

2. Hozzon létre egy csomagot, amely egy vagy több szolgáltatást tartalmaz. A csomag létrehozásakor válasszon ki vagy hozzon létre kvótákat, amelyek meghatározzák a csomagban lévő egyes szolgáltatások erőforrás-korlátozásait.
3. Hozzon létre egy ajánlatot, amely egy vagy több csomaggal rendelkezik. Az ajánlat tartalmazhat alapcsomagokat és opcionális kiegészítő csomagokat.

Az ajánlat létrehozása után a felhasználók előfizethetnek rá a szolgáltatások eléréséhez és az erőforrások üzembe helyezéséhez. A felhasználók tetszőleges számú ajánlatra fizethetnek. Az alábbi ábra egy egyszerű példát mutat be a két ajánlatra előfizetett felhasználóra. Minden ajánlat rendelkezik egy csomaggal vagy kettővel, és minden csomag adott szolgáltatásokhoz biztosít hozzáférést.

![Bérlői előfizetés ajánlatokkal és csomagokkal](media/azure-stack-key-features/image4.png)

## <a name="services"></a>Szolgáltatások

Olyan infrastruktúra- [szolgáltatási](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) szolgáltatásokat biztosíthat, amelyek lehetővé teszik a felhasználók számára, hogy igény szerinti számítástechnikai infrastruktúrát hozzanak létre, amelyet az Azure stack hub felhasználói portálján üzembe kell helyezni és felügyelni.

A Microsofttól és külső szolgáltatóktól származó Azure Stack hub szolgáltatásként is üzembe helyezheti a [platformot](https://azure.microsoft.com/overview/what-is-paas/) . Az üzembe helyezhető Pásti-szolgáltatások közé tartoznak a következők:

- [Event Hubs](event-hubs-rp-overview.md)
- [APP SERVICE](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [MySQL-kiszolgáló](azure-stack-mysql-resource-provider-deploy.md)

A különböző felhasználók számára összetett megoldások integrálására és kiépítésére is használhatja a szolgáltatásokat.

### <a name="quotas"></a>Kvóták

A felhő kapacitásának kezeléséhez előre konfigurált *kvótákat* használhat, vagy létrehozhat egy új kvótát a csomag minden szolgáltatásához. A kvóták azt a felső erőforrás-korlátot határozzák meg, amelyet a felhasználói előfizetés tud kiépíteni vagy felhasználni. Egy kvóta megszabhatja például, hogy egy felhasználó legfeljebb öt virtuális gépet hozhat létre.

> [!IMPORTANT]
> Akár két óráig is eltarthat, amíg az új kvóták elérhetővé válnak a felhasználói portálon, vagy a módosított kvóta érvénybe léptetése előtt.

A kvótákat régiónként is beállíthatja. Például egy olyan csomag, amely számítási szolgáltatásokat biztosít az A régióhoz, két virtuális gép kvótája lehet.

>[!NOTE]
>A Azure Stack Development Kit (ASDK) esetében csak egy régió ( *helyi* ) érhető el.

További információ a [Azure stack hub kvótáinak típusairól](azure-stack-quota-types.md).

## <a name="plans"></a>Tervek

A Csomagok egy vagy több szolgáltatás csoportjai. Azure Stack hub-kezelőként létre kell [hoznia](azure-stack-create-plan.md) a felhasználóknak kínált terveket. A felhasználói előfizethetnek az ajánlataira, hogy használhassák a bennük foglalt csomagokat és szolgáltatásokat. A csomagok létrehozásakor ügyeljen arra, hogy beállítja a kvótákat, definiálja az alapterveket, és vegye figyelembe a választható kiegészítő csomagokat is.

### <a name="base-plan"></a>Alapcsomag

Ajánlat létrehozásakor a szolgáltatás rendszergazdája egy alapcsomagot is tartalmazhat. Ezek az alapcsomagok alapértelmezés szerint bekerülnek, amikor egy felhasználó Előfizet erre az ajánlatra. Amikor egy felhasználó előfizet, hozzáfér az ezen alapcsomagokban megadott összes erőforrás-szolgáltatóhoz (a megfelelő kvótákkal együtt).

> [!NOTE]
> Ha egy ajánlat több alapcsomaggal rendelkezik, a csomagok együttes tárolókapacitása nem haladhatja meg a tárolási kvótát.

### <a name="add-on-plans"></a>Kiegészítő csomagok

A kiegészítő csomagok az ajánlathoz hozzáadott opcionális csomagok. Alapértelmezés szerint az előfizetés nem tartalmazza a bővítmények csomagjait. A kiegészítő csomagok olyan ajánlatokban elérhető további csomagok (kvótákkal), amelyeket az előfizető hozzáadhat az előfizetésekhez. Létrehozhat például egy alapszintű csomagot korlátozott erőforrásokkal a próbaverzióhoz, valamint egy kiegészítő tervet, amely nagyobb mennyiségű erőforrást biztosít a szolgáltatás bevezetését elfogadó ügyfeleknek.

## <a name="offers"></a>Ajánlatok

Az ajánlatok egy vagy több, az Ön által létrehozott csomag csoportjai, hogy a felhasználók előfizethetnek rájuk. Például: az Alpha ajánlat az A csomagot is tartalmazhatja, amely a számítási szolgáltatásokat és a B tervet tartalmazza, amely a tárolási és hálózati szolgáltatások készletét biztosítja.

[Ajánlat létrehozásakor](azure-stack-create-offer.md)meg kell adnia legalább egy alapcsomagot, de létrehozhat olyan kiegészítő csomagokat is, amelyeket a felhasználók hozzáadhatnak az előfizetéshez.

Az ajánlatok megtervezése során tartsa szem előtt a következő szempontokat:

**Próbaverziós ajánlatok** : próbaverziós ajánlatokat használ új felhasználók számára, akik később további szolgáltatásokra frissíthetnek. Egy próbaverziós ajánlat létrehozásához hozzon létre egy kisebb [alapcsomagot](service-plan-offer-subscription-overview.md#base-plan) egy opcionálisan nagyobb kiegészítő csomaggal. Alternatív megoldásként létrehozhat egy kisméretű alapcsomagból álló próbaverziós ajánlatot, és egy külön ajánlatot, amely nagyobb "díjköteles" csomaggal rendelkezik.

**Kapacitás megtervezése** : érdemes lehet olyan felhasználókra vonatkozni, akik nagy mennyiségű erőforrást megragadnak, és a rendszer minden felhasználó számára akadoznak. A teljesítmény elősegítése érdekében [a terveit a kvóták használatával állíthatja be](service-plan-offer-subscription-overview.md#plans) a maximális kihasználtság érdekében.

**Delegált szolgáltatók** : engedélyezheti másoknak, hogy ajánlatokat hozzanak létre a környezetben. Ha például szolgáltató, akkor ezt a lehetőséget [delegálhatja](azure-stack-delegated-provider.md) a viszonteladóknak. Vagy ha Ön szervezet, delegálhat más részlegeknek/leányvállalatoknak is.

## <a name="subscriptions"></a>Előfizetések

Az előfizetések lehetővé teszik a felhasználóknak az ajánlatok elérését. Ha Ön a szolgáltató Azure Stack hub operátora, akkor a felhasználók (bérlők) megvásárolhatják szolgáltatásait az ajánlatokra való feliratkozással. Ha Ön egy szervezet Azure Stack hub-operátora, akkor a felhasználók (alkalmazottak) fizethetnek az Ön által kínált szolgáltatásokra a fizetés nélkül.

A felhasználók új előfizetéseket hoznak létre, és hozzáférést kapnak a meglévő előfizetésekhez Azure Stack hubhoz való bejelentkezéssel. Az egyes előfizetések egyetlen ajánlattal való társítást jelölnek. Az egyik előfizetéshez rendelt ajánlat (és annak csomagjai és kvótái) nem oszthatók meg más előfizetésekkel. A felhasználó által létrehozott összes erőforrás egy előfizetéshez van társítva.

Azure Stack hub-kezelőként megtekintheti a bérlői előfizetésekkel kapcsolatos információkat, de nem férhet hozzá az előfizetések tartalmához, ha az adott előfizetés bérlői rendszergazdája explicit módon hozzáadja a RBAC-hez. Ez lehetővé teszi, hogy a bérlők a Azure Stack hub-kezelő és a bérlői szóközök között kikényszerítsék a hatalom és a felelősség elkülönítését. 

Ez alól kivételt képez az az eset, amikor az előfizetés tulajdonosa nem tudja biztosítani az operátornak az előfizetéshez való hozzáférést, így a rendszergazdának el kell fogadnia az előfizetést, ahogy azt a [Azure stack hub felhasználói előfizetés számlázási tulajdonosának módosítása](azure-stack-change-subscription-owner.md)című cikkben ismertetett módon tárgyaljuk.

Ha az Azure Stack hub-példánya le van választva, és két különböző tartománya van, ahol a felhasználók a 2. tartományba tartoznak, akkor előfordulhat, hogy egyes előfizetések a felügyeleti portálon jelennek meg, de nem jelennek meg a felhasználói portálon. Ennek kijavításához az 1. tartományba tartozó felhasználók a 2. tartományban lévő előfizetések helyes RBAC állítja be.

### <a name="default-provider-subscription"></a>Alapértelmezett szolgáltatói előfizetés

A ASDK telepítésekor a rendszer automatikusan létrehozza az alapértelmezett szolgáltatói előfizetést. Ez az előfizetés használható Azure Stack hub kezelésére, további erőforrás-szolgáltatók üzembe helyezésére, valamint csomagok és ajánlatok létrehozására a felhasználók számára. Biztonsági és licencelési okokból nem használható az ügyfél-munkaterhelések és alkalmazások futtatására. Az alapértelmezett szolgáltatói előfizetés kvótája nem módosítható.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a csomagok, ajánlatok és előfizetések létrehozásáról, kezdje a [terv létrehozásával](azure-stack-create-plan.md).
