---
title: Kubernetes hozzáadása Azure Stack hub Marketplace-hez
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan adhat hozzá Kubernetes a Azure Stack hub Marketplace-hez.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 03/24/2020v
ms.openlocfilehash: a8b29a59de27ed8fd65357249e336d0532b63f6d
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246010"
---
# <a name="add-kubernetes-to-azure-stack-hub-marketplace"></a>Kubernetes hozzáadása Azure Stack hub Marketplace-hez

> [!note]  
> A fürtök Kubernetes való üzembe helyezéséhez csak az Azure Stack hub Marketplace-elemmel használható. Azure Stack hub támogatott Kubernetes-fürtök esetén használja [az AK-motort](azure-stack-aks-engine.md).

A Kubernetes-t Piactéri elemekként is kínálhatja a felhasználók számára. A felhasználók ezután egyetlen, koordinált műveletben telepíthetik a Kubernetes.

Ez a cikk egy önálló Kubernetes-fürt erőforrásainak üzembe helyezéséhez és kiépítéséhez Azure Resource Manager sablont használ. Mielőtt elkezdené, tekintse át a Azure Stack hub és a globális Azure-bérlői beállításokat. Gyűjtse össze az Azure Stack hub szükséges adatait. Adja hozzá a szükséges erőforrásokat a bérlőhöz és Azure Stack hub Marketplace-hez. A fürt egy Ubuntu-kiszolgálótól, egyéni parancsfájltól és a Kubernetes-fürt Marketplace-elemtől függ Azure Stack hub piactéren.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Csomag, ajánlat és előfizetés létrehozása

Hozzon létre egy csomagot, egy ajánlatot és egy előfizetést a Kubernetes Marketplace-elemhez. Használhat meglévő csomagot és ajánlatot is.

1. Jelentkezzen be a felügyeleti portálra `https://adminportal.local.azurestack.external` .

1. Hozzon létre egy csomagot alaptervként. Útmutatásért lásd: [terv létrehozása Azure stack központban](azure-stack-create-plan.md).

1. Hozzon létre egy ajánlatot. Útmutatásért tekintse meg az [ajánlat létrehozása Azure stack hub-ban](azure-stack-create-offer.md)című témakört.

1. Válassza az **ajánlatok** lehetőséget, és keresse meg a létrehozott ajánlatot.

1. Válassza az **Áttekintés** lehetőséget az ajánlat panelen.

1. Válassza az **Állapot módosítása** lehetőséget. Válassza a **Nyilvános** lehetőséget.

1. Előfizetés létrehozásához válassza az **+ erőforrás létrehozása**  >  **ajánlatok és csomagok**  >  **előfizetését** .

    a. Adja meg a **megjelenítendő nevet**.

    b. Adjon meg egy **felhasználót**. Használja a bérlőhöz társított Azure AD-fiókot.

    c. **Szolgáltató leírása**

    d. Állítsa be a **címtár-bérlőt** az Azure stack hub Azure ad-bérlője számára. 

    e. Válassza az **ajánlat** lehetőséget. Válassza ki a létrehozott ajánlat nevét. Jegyezze fel az előfizetés AZONOSÍTÓját.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Egyszerű szolgáltatásnév és hitelesítő adatok létrehozása AD FS

Ha Active Directory összevont szolgáltatásokat (AD FS) használ az Identitáskezelő szolgáltatáshoz, létre kell hoznia egy egyszerű szolgáltatásnevet a Kubernetes-fürtöt telepítő felhasználók számára. Egyszerű szolgáltatás létrehozása ügyfél-titok használatával. Útmutatásért lásd: [egyszerű szolgáltatásnév létrehozása az ügyfél titkos](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)kódjával.

## <a name="add-an-ubuntu-server-image"></a>Ubuntu-kiszolgáló rendszerképének hozzáadása

Adja hozzá a következő Ubuntu Server-rendszerképet a Azure Stack hub Marketplace-hez:

1. Jelentkezzen be a felügyeleti portálra `https://adminportal.local.azurestack.external` .

1. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés** lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból** lehetőséget.

1. Írja be a következő szöveget: `Ubuntu Server`.

1. Válassza ki a kiszolgáló legújabb verzióját. Ellenőrizze a teljes verziót, és győződjön meg arról, hogy a legújabb verzióval rendelkezik:
    - **Közzétevő**: Canonical
    - **Ajánlat**: UbuntuServer
    - **Verzió**: 16.04.201806120 (vagy legújabb verzió)
    - **SKU**: 16,04 – LTS

1. Válassza a **Letöltés lehetőséget.**

## <a name="add-a-custom-script-for-linux"></a>Egyéni parancsfájl hozzáadása Linuxra

Adja hozzá a Kubernetes a Azure Stack hub piactérről:

1. Nyissa meg a felügyeleti portált `https://adminportal.local.azurestack.external` .

1. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés** lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból** lehetőséget.

1. Írja be a következő szöveget: `Custom Script for Linux`.

1. Válassza ki a parancsfájlt a következő profillal:
   - **Ajánlat**: egyéni parancsfájl a Linux 2,0-hez
   - **Verzió**: 2.0.6 (vagy legújabb verzió)
   - **Közzétevő**: Microsoft Corp

     > [!Note]  
     > A Linux rendszerhez készült egyéni parancsfájlok egynél több verziója is szerepelhet a listáról. Hozzá kell adnia az elem utolsó verzióját.

1. Válassza a **Letöltés lehetőséget.**

## <a name="add-kubernetes-to-the-marketplace"></a>Kubernetes hozzáadása a piactérhez

1. Nyissa meg a felügyeleti portált `https://adminportal.local.azurestack.external` .

1. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés** lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból** lehetőséget.

1. Írja be a következő szöveget: `Kubernetes`.

1. Válassza a(z) `Kubernetes Cluster` lehetőséget.

1. Válassza a **Letöltés lehetőséget.**

    > [!note]  
    > A Marketplace-elemek Azure Stack hub piactéren való megjelenésének öt percet is igénybe vehet.

    ![Kubernetes-elemek Azure Stack hub piactéren](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>A Kubernetes frissítése vagy eltávolítása

A Kubernetes elem frissítésekor a Azure Stack hub piactéren eltávolítja az előző elemeket. Az alábbi útmutatást követve adja hozzá a Kubernetes-frissítést Azure Stack hub Marketplace-hez.

A Kubernetes elemek eltávolítása:

1. Kapcsolódjon Azure Stack hubhoz a PowerShell-lel kezelőként. Útmutatásért lásd: [kapcsolódás Azure stack hubhoz a PowerShell-lel kezelőként](azure-stack-powershell-configure-admin.md).

2. A gyűjtemény aktuális Kubernetes-fürtjének megkeresése.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Jegyezze fel az aktuális elem nevét, például: `Microsoft.AzureStackKubernetesCluster.0.3.0` .

4. Az alábbi PowerShell-parancsmag használatával távolítsa el az elemeket:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Következő lépések

[Kubernetes üzembe helyezése Azure Stack hubhoz](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Az Azure Stack hub szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md)
