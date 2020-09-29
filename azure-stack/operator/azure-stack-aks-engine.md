---
title: Adja hozzá az Azure Kubernetes Services (ak) motorjának előfeltételeit Azure Stack hub Marketplace-hez
description: Megtudhatja, hogyan adhatja hozzá az AK-motor előfeltételeit az Azure Stack hub Marketplace-hez.
author: mattbriggs
ms.topic: article
ms.date: 09/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/16/2020
ms.openlocfilehash: 810aa253eb4872a58ea2e52070bb42eaaa1f6aaa
ms.sourcegitcommit: 719569bb9e3f9924494a9229b4f2d211ae3f4f74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90717988"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-hub-marketplace"></a>Adja hozzá az Azure Kubernetes Services (ak) motorjának előfeltételeit az Azure Stack hub Marketplace-hez

Beállíthatja a felhasználók számára az Azure Kubernetes Services (ak) motorját. Adja hozzá a cikkben ismertetett elemeket az Azure Stack hubhoz. A felhasználók ezután egyetlen, koordinált műveletben telepíthetik a Kubernetes-fürtöt. Ez a cikk végigvezeti azokon a lépéseken, amelyekkel az AK-motor elérhetővé tehető a felhasználók számára a csatlakoztatott és a leválasztott környezetekben. Az KABAi motor a szolgáltatási elv identitása, valamint a piactéren, az egyéni szkriptek és az AK alapképétől függ. Az KABAi motorhoz a [Azure stack Hub 1910](release-notes.md?view=azs-1910&preserve-view=true) -es vagy újabb verzióját kell használnia.

> [!NOTE]  
> A Azure Stack hub és az AK motor verziószámának leképezése az [AK-motor kibocsátási megjegyzései](/azure-stack/user/kubernetes-aks-engine-release-notes#aks-engine-and-azure-stack-version-mapping)között található.

## <a name="check-your-users-service-offering"></a>A felhasználó szolgáltatási ajánlatának megkeresése

A felhasználóknak egy csomagra, ajánlatra és előfizetésre van szükségük Azure Stack hub számára, amely elegendő hellyel rendelkezik. A felhasználók általában legfeljebb hat virtuális gépet telepíthetnek a három főkiszolgálóból és három feldolgozó csomópontból álló fürtökön. Érdemes meggyőződni arról, hogy elég nagy mennyiségű kvótával rendelkeznek.

Ha további információra van szüksége a szolgáltatási ajánlatok megtervezéséről és beállításáról, tekintse meg az [Azure stack hub szolgáltatással foglalkozó témakör áttekintését](service-plan-offer-subscription-overview.md) .

## <a name="create-a-service-principal-and-credentials"></a>Egyszerű szolgáltatásnév és hitelesítő adatok létrehozása

A Kubernetes-fürtön az egyszerű szolgáltatásnév (SPN) és a szerepköralapú engedélyek szükségesek az Azure Stack központban.

### <a name="create-an-spn-in-azure-ad"></a>Egyszerű szolgáltatásnév létrehozása az Azure AD-ben

Ha Azure Active Directoryt (Azure AD-t) használ az Identity Management szolgáltatáshoz, létre kell hoznia egy egyszerű szolgáltatásnevet a Kubernetes-fürtöt telepítő felhasználók számára. Hozzon létre egy egyszerű szolgáltatást az ügyfél titkos kódjával. Útmutatásért tekintse meg az [ügyfél titkos hitelesítő adatait használó egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential)című témakört.

### <a name="create-an-spn-in-ad-fs"></a>Egyszerű szolgáltatásnév létrehozása AD FSban

Ha Active Directory összevont szolgáltatásokat (AD FS) használ az Identitáskezelő szolgáltatáshoz, létre kell hoznia egy egyszerű szolgáltatásnevet a Kubernetes-fürtöt telepítő felhasználók számára. Hozzon létre egy egyszerű szolgáltatást az ügyfél titkos kódjával. Útmutatásért lásd: [egyszerű szolgáltatásnév létrehozása az ügyfél titkos](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)kódjával.

## <a name="add-the-aks-base-image"></a>Az AK-alapú alaprendszerkép hozzáadása

Az AK alaprendszerképét hozzáadhatja a piactérhez az elem Azure-ból való beolvasásával. Ha azonban az Azure Stack hub le van választva, az alábbi utasításokat követve [töltheti le a piactér-elemeket az Azure-ból](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) az elem hozzáadásához. Adja hozzá az 5. lépésben megadott elemeket.

Adja hozzá a következő elemeket a piactérhez:

1. Jelentkezzen be a felügyeleti portálra `https://adminportal.local.azurestack.external` .

1. Válassza a **minden szolgáltatás**lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés**lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból**lehetőséget.

1. Írja be a következő szöveget: `AKS Base`.

1. Válassza ki a rendszerkép azon verzióját, amely megfelel az AK-motor verziójának. A [támogatott Kubernetes-verziókban](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions)az AK alaprendszerképének az AK-alapú motor verziójára vonatkozó listáját találhatja. 

    A listában válassza a következőket:
    - **Közzétevő**: Azure Kubernetes Service
    - **Ajánlat**: AK
    - **Név**: AK Base Ubuntu 16,04-LTS rendszerkép-disztribúció, augusztus 2020 (2020.09.14 vagy Version, amely az AK-motorhoz van leképezve)
    - **Verzió**: 2020.09.14

1. Válassza a **Letöltés lehetőséget.**

## <a name="add-a-custom-script-extension"></a>Egyéni parancsfájl-kiterjesztés hozzáadása

Az egyéni szkriptet hozzáadhatja a piactérhez úgy, hogy beolvassa az elemet az Azure-ból. Ha azonban az Azure Stack hub le van választva, az elem hozzáadásához kövesse a [Marketplace-elemek letöltése az Azure-ból](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) című témakört.  Adja hozzá az 5. lépésben megadott elemeket.

1. Nyissa meg a felügyeleti portált `https://adminportal.local.azurestack.external` .

1. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés**lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból**lehetőséget.

1. Írja be a következő szöveget: `Custom Script for Linux`.

1. Válassza ki a parancsfájlt a következő profillal:
   - **Ajánlat**: egyéni parancsfájl a Linux 2,0-hez
   - **Verzió**: 2.0.6 (vagy legújabb verzió)
   - **Közzétevő**: Microsoft Corp

     > [!Note]  
     > A Linux rendszerhez készült egyéni szkript több verziója is szerepelhet a felsorolásban. Hozzá kell adnia az elem utolsó verzióját is.

1. Válassza a **Letöltés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

[Mi a Azure Stack hub AK-motorja?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Az Azure Stack hub szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md)
