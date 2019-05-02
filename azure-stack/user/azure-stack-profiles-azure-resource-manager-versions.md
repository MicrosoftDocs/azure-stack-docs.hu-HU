---
title: Erőforrás-szolgáltató API-ja verziókat támogatja az Azure Stackben profilok |} A Microsoft Docs
description: Ismerje meg a profilok az Azure Stackben által támogatott Azure Resource Manager-verzió.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 0ede2889c77b1d48cf1456652e8fcfa4c7a5d9c3
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985713"
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

## <a name="overview-of-2018---03-01-hybrid"></a>2018 - 03-01-hibrid áttekintése

| Erőforrás-szolgáltató | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>VPN-átjáró lesz 2017-03-01 |
| Microsoft.Storage (Data Plane) | 2017-04-17 |
| Microsoft.Storage (Control Plane) | 2016-01-01 |
| Microsoft. Web | 2016-08-01<br>Ez az a legújabb (jelen pillanatban) az Azure-ban |
| Microsoft.KeyVault | (Nem módosítása) 2016-10-01 |
| Microsoft.Resources (Azure Resource Manager magát) | 2016-02-01 |
| Microsoft.Authorization (házirend műveletek) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Szabályzat | 2016-10-01 |
| További források | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Subscriptions | 2016-10-01 |

Több a szolgáltatók a API-profilban az egyes erőforrástípusok verzióját, lásd: [részleteit a 2018-03-01-hibrid](#details-for-the-2018-03-01-hybrid) profilt.

## <a name="details-for-the-2018-03-01-hybrid"></a>A 2018-03-01-hibrid részletei

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Szerepköralapú hozzáférés-vezérlés használatával kezelheti a a szervezeti felhasználók végezhetnek el az erőforrásokon. Ez számos műveletet lehetővé teszi a Szerepkörök definiálása, szerepköröket hozzárendelni felhasználók vagy csoportok és engedélyek adatainak beolvasása. További információkért lásd: [engedélyezési](/rest/api/authorization/).

| Erőforrástípusok | API-verziók |
|---------------------|--------------------|
| Zárolások | 2017-04-01 |
| Műveletek | 2015-07-01 |
| Engedélyek | 2015-07-01 |
| Szabályzat-hozzárendelések | 2016-12-01 (2017. 06. 01-előzetes verzió) |
| Szabályzatdefiníciók | 2016-12-01 |
| Szolgáltatói műveletek | 2015-07-01-előzetes verzió |
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
| Rendelkezésre állási csoportok | 2016-03-30 |
| Helyek | 2016-03-30 |
| Helyek és műveletek | 2016-03-30 |
| Locations/publishers | 2016-03-30 |
| Helyek és használat | 2016-03-30 |
| Locations/vmSizes | 2016-03-30 |
| Műveletek | 2016-03-30 |
| Virtuális gépek | 2016-03-30 |
| Virtuálisgép-bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtual Machine Scale Sets/bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets/hálózati adapterek | 2016-03-30 |
| Virtual Machine Scale Sets/virtuális gépek | 2016-03-30 |
| Virtual Machines Scale Sets/virtualMachines/networkInterfaces | 2016-03-30 |

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

A kulcs kezelése tárolók, valamint a kulcsok, titkos kódok és tanúsítványok a kulcstartók belül. További információkért lásd: a [Azure Key Vault REST API-referencia](/rest/api/keyvault/).

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
| Kapcsolatok | 2015-06-15 |
| DNS-zónák | 2016-04-01 |
| Terheléselosztók | 2015-06-15 |
| Helyi hálózati átjáró | 2015-06-15 |
| Helyek | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| Helyek és műveletek | 2016-04-01 |
| Helyek és használat | 2016-04-01 |
| Hálózati illesztők | 2015-06-15 |
| Network Security Groups (Hálózati biztonsági csoportok) | 2015-06-15 |
| Műveletek | 2015-06-15 |
| Nyilvános IP-cím | 2015-06-15 |
| Útvonaltáblák | 2015-06-15 |
| Virtuális hálózati átjáró | 2015-06-15 |
| Virtuális hálózatok | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Az Azure Resource Manager üzembe helyezése és kezelése az Azure-megoldások infrastruktúrája lehetővé teszi. Az erőforráscsoportok kapcsolódó erőforrások rendezéséhez, és az erőforrások JSON-sablonok üzembe helyezése. Az erőforrások Resource Manager használatával történő üzembe helyezéséről bevezető, tekintse meg a [Azure Resource Manager áttekintése](/azure/azure-resource-manager/resource-group-overview).

| Erőforrástípusok | API-verziók |
|-----------------------------------------|-------------------|
| Alkalmazásregisztráció | 2015-01-01 |
| Erőforrásnév ellenőrzése | 2016-09-01 |
| Delegált szolgáltatók | 2015-01-01 |
| Delegált szolgáltatók és ajánlatokkal. | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Központi telepítés | 2016-09-01 |
| Az operations központi telepítések | 2016-09-01 |
| Extensions Metadata | 2015-01-01 |
| Hivatkozások | 2016-09-01 |
| Helyek | 2015-01-01 |
| Ajánlatok | 2015-01-01 |
| Műveletek | 2015-01-01 |
| Szolgáltatók | 2017-08-01 |
| Erőforráscsoportok | 2016-09-01 |
| További források | 2016-09-01 |
| Subscriptions | 2016-09-01 |
| Előfizetések/hely | 2016-09-01 |
| Előfizetések/műveleti eredmények | 2016-09-01 |
| Az előfizetések és szolgáltatók | 2017-08-01 |
| Előfizetések vagy erőforráscsoportok | 2016-09-01 |
| Előfizetés/resourceGroups/erőforrások | 2016-09-01 |
| Az előfizetések és erőforrások | 2016-09-01 |
| Előfizetések/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Bérlők | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

A Storage erőforrás-szolgáltató (SRP) lehetővé teszi, hogy a storage-fiók és kulcsok programozással felügyelheti. További információkért lásd: a [Azure Storage erőforrás-szolgáltató REST API-referencia](/rest/api/storagerp/).

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Név foglaltságának ellenőrzése | 2016-01-01 |
| Helyek | 2016-01-01 |
| Helyek és kvóták | 2016-01-01 |
| Műveletek | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Használati adatok | 2016-01-01 |

## <a name="details-for-the-2018-03-01-hybrid"></a>A 2018-03-01-hibrid részletei

### <a name="microsoft-authorization"></a>Microsoft-engedély

| Erőforrástípusok | API-verziók |
|---------------------|---------------------------------|
| Zárolások | 2017-04-01 |
| Műveletek | 2015-07-01 |
| Engedélyek | 2015-07-01 |
| Szabályzat-hozzárendelések | 2016-12-01 (2017. 06. 01-előzetes verzió) |
| Szabályzatdefiníciók | 2016-12-01 |
| Szolgáltatói műveletek | 2015-07-01-előzetes verzió |
| Szerepkör-hozzárendelések | 2015-07-01 |
| Szerepkör-definíciók | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Erőforrás típusa | API-verzió |
|---------------------------------------------------------------|-------------|
| Rendelkezésre állási csoportok | 2016-03-30 |
| Helyek | 2016-03-30 |
| Helyek és műveletek | 2016-03-30 |
| Locations/publishers | 2016-03-30 |
| Helyek és használat | 2016-03-30 |
| Locations/vmSizes | 2016-03-30 |
| Műveletek | 2016-03-30 |
| Virtuális gépek | 2016-03-30 |
| Virtuálisgép-bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Virtual Machine Scale Sets/bővítmények | 2016-03-30 |
| Virtual Machine Scale Sets/hálózati adapterek | 2016-03-30 |
| Virtual Machine Scale Sets/virtuális gépek | 2016-03-30 |
| Virtual Machines Scale Sets/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Erőforrástípusok | API-verziók |
|---------------------------|--------------|
| Kapcsolatok | 2015-06-15 |
| DNS-zónák | 2016-04-01 |
| Terheléselosztók | 2015-06-15 |
| Helyi hálózati átjáró | 2015-06-15 |
| Helyek | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| Helyek és műveletek | 2016-04-01 |
| Helyek és használat | 2016-04-01 |
| Hálózati illesztők | 2015-06-15 |
| Network Security Groups (Hálózati biztonsági csoportok) | 2015-06-15 |
| Műveletek | 2015-06-15 |
| Nyilvános IP-cím | 2015-06-15 |
| Útvonaltáblák | 2015-06-15 |
| Virtuális hálózati átjáró | 2015-06-15 |
| Virtuális hálózatok | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Erőforrástípusok | API-verziók |
|-----------------------------------------|--------------|
| Alkalmazásregisztráció | 2015-01-01 |
| Erőforrásnév ellenőrzése | 2016-09-01 |
| Delegált szolgáltatók | 2015-01-01 |
| Delegált szolgáltatók és ajánlatokkal. | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Központi telepítés | 2016-09-01 |
| Az operations központi telepítések | 2016-09-01 |
| Extensions Metadata | 2015-01-01 |
| Hivatkozások | 2016-09-01 |
| Helyek | 2015-01-01 |
| Ajánlatok | 2015-01-01 |
| Műveletek | 2015-01-01 |
| Szolgáltatók | 2017-08-01 |
| Erőforráscsoportok | 2016-09-01 |
| További források | 2016-09-01 |
| Subscriptions | 2016-09-01 |
| Előfizetések/hely | 2016-09-01 |
| Előfizetések/műveleti eredmények | 2016-09-01 |
| Az előfizetések és szolgáltatók | 2017-08-01 |
| Előfizetések vagy erőforráscsoportok | 2016-09-01 |
| Előfizetés/resourceGroups/erőforrások | 2016-09-01 |
| Az előfizetések és erőforrások | 2016-09-01 |
| Előfizetések/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Bérlők | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Erőforrástípusok | API-verziók |
|-------------------------|--------------|
| Név foglaltságának ellenőrzése | 2016-01-01 |
| Helyek | 2016-01-01 |
| Helyek és kvóták | 2016-01-01 |
| Műveletek | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Használati adatok | 2016-01-01 |

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
