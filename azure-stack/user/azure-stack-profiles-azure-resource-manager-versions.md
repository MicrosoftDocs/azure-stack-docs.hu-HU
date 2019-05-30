---
title: Erőforrás-szolgáltató API-ja verziókat támogatja az Azure Stackben profilok |} A Microsoft Docs
description: Ismerje meg az Azure Resource Manager API-verziókat támogatja az Azure Stackben profilok.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 21e1e8df3d5f43f91e391b8c39f3f5aca3aefea8
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269571"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Erőforrás-szolgáltató API-ja verziókat támogatja az Azure Stackben profilok

Ez a cikk az Azure Stack által használt minden egyes API-profil az erőforrás-szolgáltató és a verziószámok találja. Ez a cikk a táblázatokban a verziók mindegyik erőforrás-szolgáltató és a profilok az API-verziók esetében támogatott. Mindegyik erőforrás-szolgáltató erőforrás-típusok és adott verziószámok tartalmazza.

Az API-profil három elnevezési konvenciókat használja:

- **latest**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

Egy API-profilokat és verzió kiadási ütem az Azure Stack, ismertetését [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md).

> [!Note]
> A **legújabb** API-profil tartalmazza az erőforrás-szolgáltató API legújabb verzióját, és nem szerepel ebben a cikkben.

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>A 2019-03-01-hibrid profil áttekintése

| Erőforrás-szolgáltató | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>VPN-átjáró lesz 2017-10-01 |
| Microsoft.Storage (Data Plane) | 2017-10-01 |
| Microsoft.Storage (Control Plane) | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | (Nem módosítása) 2016-10-01 |
| Microsoft.Resources (Azure Resource Manager magát) | 2016-06-01 |
| Microsoft.Authorization (házirend műveletek) | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

Az egyes erőforrástípusok a szolgáltatók a API-profilban verziók listáját lásd: [a 2019-03-01-hibrid profil részletei](#details-for-the-2019-03-01-hybrid-profile).

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>A 2019-03-01-hibrid profil részletei

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Szerepköralapú hozzáférés-vezérlés lehetővé teszi a műveletek a szervezeti felhasználók is igénybe vehet, az erőforrások kezelését. Szerepkörök definiálása, szerepkörök hozzárendelése a felhasználókhoz vagy csoportokhoz, és engedélyek adatainak beolvasása. További információkért lásd: [engedélyezési](/rest/api/authorization/).

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
| Delegált szolgáltatói előfizetés | 2015-06-01 - előzetes verzió |
| Delegált használati összesítések | 2015-06-01 - előzetes verzió |
| Erőforrás becsült költségek | 2015-06-01 - előzetes verzió |
| Műveletek | 2015-06-01 - előzetes verzió |
| Előfizető használati összesítések | 2015-06-01 - előzetes verzió |
| Használati összesítések | 2015-06-01 - előzetes verzió |

### <a name="microsoftcompute"></a>Microsoft.Compute

Az Azure Compute API-k, programozás alapú hozzáférést biztosít a virtuális gépek és az azokat támogató erőforrásokhoz. További információkért lásd: [Azure Compute](/rest/api/compute/).

| Erőforrás típusa | API-verzió |
|---------------------------------------------------------------|-------------|
| Rendelkezésre állási csoportok | 2017-12-01 |
| Helyszínek | 2017-12-01 |
| Helyek és műveletek | 2017-12-01 |
| Locations/publishers | 2017-12-01 |
| Helyek és használat | 2017-12-01 |
| Locations/vmSizes | 2017-12-01 |
| Műveletek | 2017-12-01 |
| Virtuális gépek | 2017-12-01 |
| Virtuálisgép-bővítmények | 2017-12-01 |
| Virtuálisgép-méretezési csoportok | 2017-12-01 |
| Virtual Machine Scale Sets/bővítmények | 2017-12-01 |
| Virtual Machine Scale Sets/hálózati adapterek | 2017-12-01 |
| Virtual Machine Scale Sets/virtuális gépek | 2017-12-01|
| Virtual Machines Scale Sets/virtualMachines/networkInterfaces | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Erőforrás típusa | API-verzió |
|------------------|-------------|
| Válogatás | 2015-04-01 |
| Válogatott tartalom | 2015-04-01 |
| Válogatott tartalom kivonata | 2015-04-01 |
| Katalóguselemek | 2015-04-01 |
| Műveletek | 2015-04-01 |
| Portál | 2015-04-01 |
| Keresés | 2015-04-01 |
| Javaslat | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Erőforrástípusok | API-verziók |
|--------------------|--------------------|
| Műveletek | 2015-04-01 |
| Eseménytípusok | 2015-04-01 |
| Esemény-kategóriák | 2015-04-01 |
| Metrikadefiníciók | 2018-01-01 |
| Mérőszámok | 2018-01-01 |
| Diagnosztikai beállítások | 2017-05-01-előzetes verzió |
| Diagnosztikai beállítások kategóriái | 2017-05-01-előzetes verzió |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

A Key Vault, valamint a kulcsok, titkos kulcsok és tanúsítványokat a Key Vault kezelése. További információkért lásd: a [Azure Key Vault REST API-referencia](/rest/api/keyvault/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Műveletek | 2016-10-01 |
| Tárolók | 2016-10-01 |
| Tárolók / hozzáférési házirendek | 2016-10-01 |
| Tárolók vagy titkos kódokkal | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

A műveleti hívás eredménye a rendelkezésre álló hálózati lista reprezentációját. További információkért lásd: [művelet REST API-val](/rest/api/operation/).

| Erőforrástípusok | API-verziók |
|---------------------------|--------------|
| Kapcsolatok | 2017-10-01 |
| DNS Zones | 2016-04-01 |
| Terheléselosztók | 2017-10-01 |
| Helyi hálózati átjáró | 2017-10-01 |
| Helyszínek | 2017-10-01|
| Location/operationResults | 2017-10-01 |
| Helyek és műveletek | 2017-10-01 |
| Helyek és használat |2017-10-01 |
| Hálózati illesztők | 2017-10-01 |
| Network Security Groups (Hálózati biztonsági csoportok) | 2017-10-01 |
| Műveletek | 2017-10-01 |
| Nyilvános IP-cím | 2017-10-01 |
| Útvonaltáblák | 2017-10-01 |
| Virtuális hálózati átjáró | 2017-10-01 |
| Virtuális hálózatok | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

Az Azure Resource Manager lehetővé teszi az üzembe helyezés és kezelés az Azure-megoldások infrastruktúrája. Az erőforráscsoportok kapcsolódó erőforrások rendezéséhez és helyezheti üzembe az erőforrásokat JSON-sablonok használatával. Az erőforrások Resource Manager használatával történő üzembe helyezéséről bevezető, tekintse meg a [Azure Resource Manager áttekintése](/azure/azure-resource-manager/resource-group-overview).

| Erőforrástípusok | API-verziók |
|-----------------------------------------|-------------------|
| Üzemelő példányok | 2018-05-01 |
| Az operations központi telepítések | 2018-05-01 |
| Hivatkozások | 2018-05-01 |
| Helyszínek | 2018-05-01 |
| Műveletek | 2018-05-01 |
| Szolgáltatók | 2018-05-01 |
| Erőforráscsoportok| 2018-05-01 |
| További források | 2018-05-01/ |
| Subscriptions | 2018-05-01 |
| Előfizetések vagy hely | 2016-06-01 |
| Előfizetések/operationresults | 2018-05-01 |
| Az előfizetések és szolgáltatók | 2018-05-01 |
| Subscriptions/ResourceGroups | 2018-05-01 |
| Előfizetés/resourceGroups/erőforrások | 2018-05-01 |
| Az előfizetések és erőforrások | 2018-05-01 |
| Előfizetések/tagNames | 2018-05-01 |
| Subscriptions/tagNames/tagValues | 2018-05-01 |
| Bérlők | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

A Storage erőforrás-szolgáltató (SRP) lehetővé teszi, hogy a storage-fiók és kulcsok programozott módon kezelheti. További információkért lásd: a [Azure Storage erőforrás-szolgáltató REST API-referencia](/rest/api/storagerp/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| Helyszínek | 2017-10-01 |
| Helyek és kvóták | 2017-10-01 |
| Műveletek | 2017-10-01 |
| StorageAccounts | 2017-10-01 |
| Használati adatok | 2017-10-01 |

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
