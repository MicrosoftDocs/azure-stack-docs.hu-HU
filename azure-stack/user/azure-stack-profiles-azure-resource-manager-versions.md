---
title: A Azure Stack profilok által támogatott erőforrás-szolgáltatói API-verziók | Microsoft Docs
description: Ismerkedjen meg az Azure Stack profilok által támogatott Azure Resource Manager API-verziókkal.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: f7d32fde9cba051297165e2e491f5c92115a8a0d
ms.sourcegitcommit: 4eb1766c7a9d1ccb1f1362ae1211ec748a7d708c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69579039"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>A Azure Stack profiljai által támogatott erőforrás-szolgáltatói API-verziók

A jelen cikkben Azure Stack által használt API-profilok erőforrás-szolgáltatója és verziószáma megtalálható. A cikkben szereplő táblázatok felsorolják az egyes erőforrás-szolgáltatók és a profilok API-verziói által támogatott verziókat. Mindegyik erőforrás-szolgáltató erőforrás-típusokat és adott verziószámokat tartalmaz.

Az API-profil három elnevezési konvenciót használ:

- **latest**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

Az Azure Stack API-profiljainak és a verziók verziószámának magyarázatát lásd: [az API-verziók profiljainak kezelése a Azure stack](azure-stack-version-profiles.md).

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

| Erőforrástípus | API-verzió |
|----------------------------------|----------------------|
| Delegált szolgáltatói előfizetések | 2015-06-01 – előzetes verzió |
| Delegált használati összesítések | 2015-06-01 – előzetes verzió |
| Erőforrás-ráfordítás becslése | 2015-06-01 – előzetes verzió |
| Műveletek | 2015-06-01 – előzetes verzió |
| Előfizető használati összesítései | 2015-06-01 – előzetes verzió |
| Használati összesítések | 2015-06-01 – előzetes verzió |

### <a name="microsoftcompute"></a>Microsoft.Compute

Az Azure számítási API-k programozási szintű hozzáférést biztosítanak a virtuális gépekhez és az azokhoz kapcsolódó erőforrásokhoz. További információ: Azure-beli [számítás](/rest/api/compute/).

| Erőforrástípus | API-verzió |
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
| Virtuálisgép-méretezési csoportok | 2017-12-01 |
| Virtual Machine Scale Sets/bővítmények | 2017-12-01 |
| Virtual Machine Scale Sets/hálózati adapterek | 2017-12-01 |
| Virtual Machine Scale Sets/Virtual Machines | 2017-12-01|
| Virtual Machines méretezési készletek/virtualMachines/networkInterfaces | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Erőforrástípus | API-verzió |
|------------------|-------------|
| Válogatás | 2015-04-01 |
| Válogatott tartalom | 2015-04-01 |
| Válogatott tartalom kivonata | 2015-04-01 |
| Gyűjtemény elemei | 2015-04-01 |
| Műveletek | 2015-04-01 |
| Portál | 2015-04-01 |
| Keresés | 2015-04-01 |
| Javaslat | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Erőforrástípusok | API-verziók |
|--------------------|--------------------|
| Műveletek | 2015-04-01 |
| Eseménytípusok | 2015-04-01 |
| Események kategóriái | 2015-04-01 |
| Metrikadefiníciók | 2018-01-01 |
| Mérőszámok | 2018-01-01 |
| Diagnosztikai beállítások | 2017-05-01 – előzetes verzió |
| Diagnosztikai beállítások kategóriái | 2017-05-01 – előzetes verzió |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Kezelheti Key Vaultét, valamint a kulcsokat, a titkokat és a tanúsítványokat a Key Vaulton belül. További információkért tekintse meg a [Azure Key Vault REST API](/rest/api/keyvault/)-referenciát.

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
| DNS Zones | 2016-04-01 |
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
| Üzemelő példányok | 2018-05-01 |
| Üzembe helyezések/műveletek | 2018-05-01 |
| Hivatkozások | 2018-05-01 |
| Helyek | 2018-05-01 |
| Műveletek | 2018-05-01 |
| Szolgáltatók | 2018-05-01 |
| Erőforráscsoportok| 2018-05-01 |
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
| StorageAccounts | 2017-10-01 |
| Használati adatok | 2017-10-01 |

## <a name="next-steps"></a>További lépések

- [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)
- [A Azure Stack felhasználó PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-user.md)  
