---
title: Adja hozzá az Azure Kubernetes Services (ak) motorjának előfeltételeit a Azure Stack Marketplace-hez | Microsoft Docs
description: Megtudhatja, hogyan adhatja hozzá a Azure Stack Marketplace-re vonatkozó előfeltételeket a piactérhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: ee19c6ee32960c52bcf7a4918c3d1e48406129c3
ms.sourcegitcommit: 31e04af4d405215ef200aba0b40d601fc5ca7662
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74391545"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-marketplace"></a>Adja hozzá az Azure Kubernetes Services (ak) motorjának előfeltételeit a Azure Stack Marketplace-hez

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Engedélyezheti a felhasználók számára az Azure Kubernetes Services (ak) motor beállítását úgy, hogy hozzáadja a jelen cikkben ismertetett elemeket a Azure Stackhoz. A felhasználók ezután egyetlen, koordinált műveletben telepíthetik a Kubernetes-fürtöt. Ez a cikk végigvezeti azokon a lépéseken, amelyekkel az AK-motor elérhetővé tehető a felhasználók számára a csatlakoztatott és a leválasztott környezetekben. Az KABAi motor a szolgáltatási elv identitása, valamint a piactéren, az egyéni szkriptek és az AK alapképétől függ.

## <a name="check-your-users-service-offering"></a>A felhasználó szolgáltatási ajánlatának megkeresése

A felhasználóknak egy csomagra, ajánlatra és előfizetésre van szükségük, hogy elegendő lemezterülettel Azure Stack. A felhasználók általában legfeljebb hat virtuális gépet telepíthetnek a három főkiszolgálóból és három feldolgozó csomópontból álló fürtökön. Érdemes meggyőződni arról, hogy elég nagy mennyiségű kvótával rendelkeznek.

Ha további információra van szüksége a szolgáltatási ajánlatok megtervezéséről és beállításáról, tekintse meg az [ajánlati szolgáltatások áttekintése Azure stack](service-plan-offer-subscription-overview.md)

## <a name="create-a-service-principal-and-credentials"></a>Egyszerű szolgáltatásnév és hitelesítő adatok létrehozása

A Kubernetes-fürtön az egyszerű szolgáltatásnév (SPN) és a szerepköralapú engedélyek szükségesek a Azure Stack.

### <a name="create-an-spn-in-azure-ad"></a>Egyszerű szolgáltatásnév létrehozása az Azure AD-ben

Ha Azure Active Directoryt (Azure AD-t) használ az Identity Management szolgáltatáshoz, létre kell hoznia egy egyszerű szolgáltatásnevet a Kubernetes-fürtöt telepítő felhasználók számára. Hozzon létre egy egyszerű szolgáltatást az ügyfél titkos kódjával. Útmutatásért tekintse meg az [ügyfél titkos hitelesítő adatait használó egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential)című témakört.

### <a name="create-an-spn-in-ad-fs"></a>Egyszerű szolgáltatásnév létrehozása AD FSban

Ha Active Directory összevont szolgáltatásokat (AD FS) használ az Identitáskezelő szolgáltatáshoz, létre kell hoznia egy egyszerű szolgáltatásnevet a Kubernetes-fürtöt telepítő felhasználók számára. Hozzon létre egy egyszerű szolgáltatást az ügyfél titkos kódjával. Útmutatásért lásd: [egyszerű szolgáltatásnév létrehozása az ügyfél titkos](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)kódjával.

## <a name="add-the-aks-base-image"></a>Az AK-alapú alaprendszerkép hozzáadása

Az AK alaprendszerképét hozzáadhatja a piactérhez az elem Azure-ból való beolvasásával. Ha azonban a Azure Stack le van választva, az alábbi utasításokat követve [töltheti le a Marketplace-elemeket az Azure-ból](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) az elem hozzáadásához. Adja hozzá az 5. lépésben megadott elemeket.

Adja hozzá a következő elemeket a piactérhez:

1. Jelentkezzen be a [felügyeleti portálra](https://adminportal.local.azurestack.external).

1. Válassza a **minden szolgáltatás**lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés**lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból**lehetőséget.

1. Írja be a `AKS Base` (igen) kifejezést.

1. Válassza ki a rendszerkép azon verzióját, amely megfelel az AK-motor verziójának. A [támogatott Kubernetes-verziókban](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)az AK alaprendszerképének az AK-alapú motor verziójára vonatkozó listáját találhatja. 

    A listában válassza a következőket:
    - **Közzétevő**: Azure Kubernetes Service
    - **Ajánlat**: AK
    - **Verzió**: AK alaprendszerkép 16,04 – LTS rendszerkép-disztribúció, október 2019 (2019.10.24 vagy Version, amely az AK-motorhoz van leképezve)

1. Válassza a **Letöltés lehetőséget.**

## <a name="add-a-custom-script-extension"></a>Egyéni parancsfájl-kiterjesztés hozzáadása

Az egyéni szkriptet hozzáadhatja a piactérhez úgy, hogy beolvassa az elemet az Azure-ból. Ha azonban a Azure Stack le van választva, az elem hozzáadásához kövesse a [Marketplace-elemek letöltése az Azure-ból](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) című témakört.  Adja hozzá az 5. lépésben megadott elemeket.

1. Nyissa meg a [felügyeleti portált](https://adminportal.local.azurestack.external).

1. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés**lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból**lehetőséget.

1. Írja be a `Custom Script for Linux` (igen) kifejezést.

1. Válassza ki a parancsfájlt a következő profillal:
   - **Ajánlat**: egyéni parancsfájl a Linux 2,0-hez
   - **Verzió**: 2.0.6 (vagy legújabb verzió)
   - **Közzétevő**: Microsoft Corp

     > [!Note]  
     > A Linux rendszerhez készült egyéni szkript több verziója is szerepelhet a felsorolásban. Hozzá kell adnia az elem utolsó verzióját is.

1. Válassza a **Letöltés lehetőséget.**

## <a name="next-steps"></a>További lépések

[Mi a Azure Stack AK-motorja?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[A Azure Stack szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md)
