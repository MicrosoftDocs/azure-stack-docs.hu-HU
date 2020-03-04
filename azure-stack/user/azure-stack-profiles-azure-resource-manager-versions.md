---
title: Az Azure Stack hub profiljai által támogatott erőforrás-szolgáltatói API-verziók
description: Ismerkedjen meg az Azure Stack hub-profilok által támogatott Azure Resource Manager API-verziókkal.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 3b3ea77dcbb59f2a0471225000aa48624bcb1a15
ms.sourcegitcommit: 390eac7abc94cea1405178e8d6a9358f6488f5d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78231672"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack-hub"></a>Az Azure Stack hub profiljai által támogatott erőforrás-szolgáltatói API-verziók

Az Azure Stack hub által használt összes API-profil erőforrás-szolgáltatóját és verziószámát ebben a cikkben találja. A cikkben szereplő táblázatok felsorolják az egyes erőforrás-szolgáltatók és a profilok API-verziói által támogatott verziókat. Mindegyik erőforrás-szolgáltató erőforrás-típusokat és adott verziószámokat tartalmaz.

Az API-profil három elnevezési konvenciót használ:

- **legújabb**
- **éééé-hh-nn-Hybrid**
- **éééé-hh-nn-profil**

Az Azure Stack hub API-profiljainak és verziószámának ismertetése: az [API-verziók profiljainak kezelése Azure stack hub-ban](azure-stack-version-profiles.md).

> [!NOTE]
> A **legújabb** API-profil az erőforrás-szolgáltató API legújabb verzióját tartalmazza, és nem szerepel ebben a cikkben.

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>Az 2019-03-01-hibrid profil áttekintése

| Erőforrás-szolgáltató | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>A VPN Gateway 2017-10-01 lesz |
| Microsoft. Storage (adatsík) | 2017-11-09 |
| Microsoft. Storage (vezérlési sík) | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01 (nem változik) |
| Microsoft. Resources (Azure Resource Manager saját maga) | 2016-06-01 |
| Microsoft. Authorization (házirend-műveletek) | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

Az API-profilban található szolgáltatókhoz tartozó egyes erőforrástípusok verzióinak listáját az [2019-03-01-hibrid profil részletei](#details-for-the-2019-03-01-hybrid-profile)című részben tekintheti meg.

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>Az 2019-03-01-Hybrid profil részletei

### <a name="microsoftauthorization"></a>Microsoft.Authorization

A szerepköralapú hozzáférés-vezérlés lehetővé teszi, hogy kezelje a szervezete felhasználói által az erőforrásokra vonatkozó műveleteket. Szerepköröket határozhat meg, szerepköröket rendelhet hozzá a felhasználókhoz vagy csoportokhoz, és információkat kérhet le az engedélyekről. További információ: [Engedélyezés](/rest/api/authorization/).

| Erőforrástípusok | API-verziók |
|---------------------|--------------------|
| Zárolások | 2016-09-01 |
| Műveletek | 2015-07-01 |
| Engedélyek | 2015-07-01 |
| Szabályzat-hozzárendelések | 2016-12-01 |
| Szabályzatdefiníciók | 2016-12-01 |
| Szolgáltatói műveletek | 2015-07-01 |
| Szerepkör-hozzárendelések | 2015-07-01 |
| Szerepkör-definíciók | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Erőforrás típusa | API-verzió |
|----------------------------------|----------------------|
| Delegált szolgáltatói előfizetések | 2015-06-01 – előzetes verzió |
| Delegált használati összesítések | 2015-06-01 – előzetes verzió |
| Erőforrás-ráfordítás becslése | 2015-06-01 – előzetes verzió |
| Műveletek | 2015-06-01 – előzetes verzió |
| Előfizető használati összesítései | 2015-06-01 – előzetes verzió |
| Használati összesítések | 2015-06-01 – előzetes verzió |

### <a name="microsoftcompute"></a>Microsoft.Compute

Az Azure számítási API-k programozási szintű hozzáférést biztosítanak a virtuális gépekhez és az azokhoz kapcsolódó erőforrásokhoz. További információ: Azure-beli [számítás](/rest/api/compute/).

| Erőforrás típusa | API-verzió |
|---------------------------------------------------------------|-------------|
| Rendelkezésre állási csoportok | 2017-12-01 |
| Helyek | 2017-12-01 |
| Helyszínek/műveletek | 2017-12-01 |
| Helyszínek/közzétevők | 2017-12-01 |
| Helyszínek/használat | 2017-12-01 |
| Helyszínek/méreteinek listáján | 2017-12-01 |
| Műveletek | 2017-12-01 |
| Virtuális gépek | 2017-12-01 |
| Virtual Machines/bővítmények | 2017-12-01 |
| Virtual Machine Scale Sets | 2017-12-01 |
| Virtual Machine Scale Sets/bővítmények | 2017-12-01 |
| Virtual Machine Scale Sets/hálózati adapterek | 2017-12-01 |
| Virtual Machine Scale Sets/Virtual Machines | 2017-12-01|
| Virtual Machines méretezési készletek/virtualMachines/networkInterfaces | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Erőforrás típusa | API-verzió |
|------------------|-------------|
| Válogatott | 2015-04-01 |
| Kurátori tartalom | 2015-04-01 |
| Kurátori kivonat | 2015-04-01 |
| Gyűjtemény elemei | 2015-04-01 |
| Műveletek | 2015-04-01 |
| Portál | 2015-04-01 |
| Keresés | 2015-04-01 |
| Javaslom | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Erőforrástípusok | API-verziók |
|--------------------|--------------------|
| Műveletek | 2015-04-01 |
| Események típusai | 2015-04-01 |
| Események kategóriái | 2015-04-01 |
| Metrika-definíciók | 2018-01-01 |
| Mérőszámok | 2018-01-01 |
| Diagnosztikai beállítások | 2017-05-01 – előzetes verzió |
| Diagnosztikai beállítások kategóriái | 2017-05-01 – előzetes verzió |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Kezelheti Key Vaultét, valamint a kulcsokat, a titkokat és a tanúsítványokat a Key Vaulton belül. További információkért tekintse meg a [Azure Key Vault REST API-referenciát](/rest/api/keyvault/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Műveletek | 2016-10-01 |
| Tárolók | 2016-10-01 |
| Tárolók/hozzáférési szabályzatok | 2016-10-01 |
| Tárolók/titkok | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

A műveleti hívás eredménye az elérhető hálózati Felhőbeli műveletek listájának ábrázolása. További információ: [Operation REST API](/rest/api/operation/).

| Erőforrástípusok | API-verziók |
|---------------------------|--------------|
| Kapcsolatok | 2017-10-01 |
| DNS-zónák | 2016-04-01 |
| Terheléselosztók | 2017-10-01 |
| Helyi hálózati átjáró | 2017-10-01 |
| Helyek | 2017-10-01|
| Hely/operationResults | 2017-10-01 |
| Helyszínek/műveletek | 2017-10-01 |
| Helyszínek/használat |2017-10-01 |
| Hálózati illesztők | 2017-10-01 |
| Network Security Groups (Hálózati biztonsági csoportok) | 2017-10-01 |
| Műveletek | 2017-10-01 |
| Nyilvános IP-cím | 2017-10-01 |
| Útvonaltáblák | 2017-10-01 |
| Virtuális hálózati átjáró | 2017-10-01 |
| Virtuális hálózatok | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager lehetővé teszi az Azure-megoldások infrastruktúrájának üzembe helyezését és kezelését. Az erőforráscsoportok kapcsolódó erőforrásai és az erőforrások JSON-sablonokkal való üzembe helyezése is megszervezhető. Az erőforrások Resource Managerrel való üzembe helyezésének és kezelésének bevezetését az [Azure Resource Manager áttekintésében](/azure/azure-resource-manager/resource-group-overview)találhatja meg.

| Erőforrástípusok | API-verziók |
|-----------------------------------------|-------------------|
| Központi telepítés | 2018-05-01 |
| Üzembe helyezések/műveletek | 2018-05-01 |
| Hivatkozások | 2018-05-01 |
| Helyek | 2018-05-01 |
| Műveletek | 2018-05-01 |
| Szolgáltatók | 2018-05-01 |
| ResourceGroups| 2018-05-01 |
| További források | 2018-05-01/ |
| Előfizetések | 2018-05-01 |
| Előfizetések/helyszínek | 2016-06-01 |
| Előfizetések/operationresults | 2018-05-01 |
| Előfizetések/szolgáltatók | 2018-05-01 |
| Előfizetések/ResourceGroups | 2018-05-01 |
| Előfizetések/resourceGroups/erőforrások | 2018-05-01 |
| Előfizetések/erőforrások | 2018-05-01 |
| Előfizetések/tagNames | 2018-05-01 |
| Subscriptions/tagNames/tagValues | 2018-05-01 |
| Bérlők | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

A Storage erőforrás-szolgáltató (összegző csomag) lehetővé teszi a Storage-fiók és a kulcsok programozott módon történő kezelését. További információ: az [Azure Storage erőforrás-szolgáltató REST API referenciája](/rest/api/storagerp/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| Helyek | 2017-10-01 |
| Helyszínek/kvóták | 2017-10-01 |
| Műveletek | 2017-10-01 |
| storageAccounts | 2017-10-01 |
| használat | 2017-10-01 |

## <a name="next-steps"></a>Következő lépések

- [A PowerShell telepítése Azure Stack hubhoz](../operator/azure-stack-powershell-install.md)
- [Az Azure Stack hub PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
