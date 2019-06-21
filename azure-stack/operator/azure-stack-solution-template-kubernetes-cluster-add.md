---
title: Adja hozzá a Kubernetes az Azure Stack piactéren |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá a Kubernetes az Azure Stack piactéren.
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
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 65ffa6cc2a9c2d08e5be6733db06a4235829838a
ms.sourcegitcommit: 759a01b566597a71b18fca25902cacc983a5a63b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67298033"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Adja hozzá a Kubernetes az Azure Stack piactéren

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió. Csak a Piactéri elem használata fejlesztéshez és teszteléshez. A [AKS motor](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md) éles környezetben is használható.

Elérhetővé teheti Kubernetes Piactéri elem, a felhasználók számára. A felhasználók számára, majd telepítheti Kubernetes egyetlen, koordinált műveletben.

A következő cikkben tekintse meg telepítése és jogosultságok kiosztása az erőforrások különálló Kubernetes-fürthöz tartozó Azure Resource Manager-sablon használatával. A Kezdés előtt ellenőrizze az Azure Stack és az Azure-bérlő globális beállításokat. Az Azure Stack a szükséges információkat gyűjt. Adja hozzá a szükséges erőforrásokat a bérlőn, és az Azure Stack piactéren. A fürt egy Ubuntu-kiszolgálót, egyéni parancsfájlt és a Kubernetes-fürt Piactéri elem kell lennie a Marketplace-en függ.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Csomag, ajánlat és előfizetés létrehozása

Hozzon létre egy csomag, ajánlat és a Kubernetes Piactéri elem előfizetést. Megadhatja egy meglévő csomagot használja, és elérhetővé teheti.

1. Jelentkezzen be a [felügyeleti portálján.](https://adminportal.local.azurestack.external)

1. Hozzon létre egy csomag az alapszintű csomag. Útmutatásért lásd: [csomag létrehozása az Azure Stack](azure-stack-create-plan.md).

1. Hozzon létre egy ajánlatot. Útmutatásért lásd: [ajánlat létrehozása az Azure Stack](azure-stack-create-offer.md).

1. Válassza ki **kínál**, és keresse meg a létrehozott ajánlat.

1. Válassza ki **áttekintése** az ajánlat panelen.

1. Válassza ki **Állapotváltozáshoz**. Válassza ki **nyilvános**.

1. Válassza ki **+ erőforrás létrehozása** > **ajánlatok és csomagok** > **előfizetés** -előfizetés létrehozása.

    a. Adjon meg egy **megjelenítendő név**.

    b. Adjon meg egy **felhasználói**. A tenanthoz társított Azure AD-fiókot használni.

    c. **Társzolgáltató leírása**

    d. Állítsa be a **címtárbérlő** az Azure stack az Azure AD-bérlőhöz. 

    e. Válassza ki **ajánlat**. Válassza ki a létrehozott ajánlat nevét. Jegyezze fel az előfizetés-azonosító.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Hozzon létre egy egyszerű szolgáltatást, és a hitelesítő adatokat az AD FS-ben

Ha az Active Directory összevonási szolgáltatásokban (AD FS) az identity management szolgáltatás használja, szüksége lesz a létre szolgáltatásnevet a Kubernetes-fürt üzembe helyezése felhasználók számára. Ügyfél titkos kulcs használatával egyszerű szolgáltatás létrehozása. Útmutatásért lásd: [ügyfélkódot használatával egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret).

## <a name="add-an-ubuntu-server-image"></a>Egy Ubuntu server-rendszerkép hozzáadása

Adja hozzá a következő Ubuntu Server-lemezképet a Marketplace-en:

1. Jelentkezzen be a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás**, majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `Ubuntu Server` (igen) kifejezést.

1. Válassza ki a kiszolgálót a legújabb verziója. A teljes verziószám, és győződjön meg arról, hogy a legújabb verzióval rendelkezik:
    - **Közzétevő**: Canonical
    - **Ajánlat**: UbuntuServer
    - **Verzió**: 16.04.201806120 (vagy a legújabb verzió)
    - **TERMÉKVÁLTOZAT**: 16.04-LTS

1. Válassza ki **letöltése.**

## <a name="add-a-custom-script-for-linux"></a>Adjon hozzá egy egyéni parancsfájl linuxhoz

Adja hozzá a Kubernetes a Marketplace-ről:

1. Nyissa meg a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás** , majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `Custom Script for Linux` (igen) kifejezést.

1. Válassza ki a parancsprogramot, a következő profilhoz:
   - **Ajánlat**: Egyéni parancsfájl 2.0 linuxhoz
   - **Verzió**: 2.0.6-os (vagy a legújabb verzió)
   - **Közzétevő**: Microsoft Corp

     > [!Note]  
     > Egyéni parancsfájl Linux több verziója is szerepelhet. Adja hozzá a legfrissebb verziója, az elem kell.

1. Válassza ki **letöltése.**


## <a name="add-kubernetes-to-the-marketplace"></a>Kubernetes felvétele a Marketplace-en

1. Nyissa meg a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás** , majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `Kubernetes` (igen) kifejezést.

1. Válassza a(z) `Kubernetes Cluster` lehetőséget.

1. Válassza ki **letöltése.**

    > [!note]  
    > A Piactéri elem jelenik meg a Marketplace-en öt percig is eltarthat.

    ![Kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Frissítheti vagy eltávolíthatja a Kubernetes 

Amikor frissíti a Kubernetes elem, akkor eltávolítja az előző elemet a Marketplace-en. Az útmutatást követve ebben a cikkben a Kubernetes frissítése a Marketplace-en való hozzáadásához.

A Kubernetes-elem eltávolítása:

1. Csatlakozhat az Azure Stack PowerShell-kezelőként. További útmutatást lásd: [csatlakozhat az Azure Stack a PowerShell-lel kezelőként](azure-stack-powershell-configure-admin.md).

2. Keresse meg az aktuális Kubernetes-fürt elemet a katalógusban.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Vegye figyelembe például az aktuális elem neve `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. A következő PowerShell-parancsmag segítségével eltávolítja az elemet:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>További lépések

[A Kubernetes az Azure Stack üzembe helyezése](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Az Azure Stackben szolgáltatásajánlások áttekintése](azure-stack-offer-services-overview.md)
