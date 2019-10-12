---
title: Kubernetes hozzáadása a Azure Stack Marketplace-hez | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá Kubernetes a Azure Stack Marketplace-hez.
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 4fcfb0c8ef509ab827c15321cff5fc945230d69e
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283432"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Kubernetes hozzáadása a Azure Stack Marketplace-hez

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

> [!note]  
> A Azure Stack Kubernetes előzetes verzióban érhető el. Az előzetes verzió jelenleg nem támogatja Azure Stack leválasztott forgatókönyv használatát. Csak fejlesztési és tesztelési forgatókönyvek esetén használja a Piactéri elemeket.
A Kubernetes-t Piactéri elemekként is kínálhatja a felhasználók számára. A felhasználók ezután egyetlen, koordinált műveletben telepíthetik a Kubernetes.

A következő cikk a különálló Kubernetes-fürtök erőforrásainak üzembe helyezéséhez és kiépítéséhez Azure Resource Manager sablont használ. Mielőtt elkezdené, tekintse át a Azure Stack és a globális Azure-bérlői beállításokat. Gyűjtse össze a Azure Stack szükséges információkat. Adja hozzá a szükséges erőforrásokat a bérlőhöz és a Azure Stack Marketplace-hez. A fürt az Ubuntu-kiszolgálótól, az egyéni szkripttől és a Kubernetes-fürt Piactéri elemtől függ a piactéren.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Csomag, ajánlat és előfizetés létrehozása

Hozzon létre egy csomagot, egy ajánlatot és egy előfizetést a Kubernetes Marketplace-elemhez. Használhat meglévő csomagot és ajánlatot is.

1. Jelentkezzen be a [felügyeleti portálra.](https://adminportal.local.azurestack.external)

1. Hozzon létre egy csomagot alaptervként. Útmutatásért lásd: [terv létrehozása Azure Stackban](azure-stack-create-plan.md).

1. Hozzon létre egy ajánlatot. Útmutatásért lásd: [ajánlat létrehozása Azure Stackban](azure-stack-create-offer.md).

1. Válassza az **ajánlatok**lehetőséget, és keresse meg a létrehozott ajánlatot.

1. Válassza az **Áttekintés** lehetőséget az ajánlat panelen.

1. Válassza az **Állapot módosítása**lehetőséget. Válassza a **nyilvános**lehetőséget.

1. Válassza az **+ erőforrás létrehozása** > **ajánlatok és csomagok** > **előfizetés** létrehozásához előfizetést.

    a. Adja meg a **megjelenítendő nevet**.

    b. Adjon meg egy **felhasználót**. Használja a bérlőhöz társított Azure AD-fiókot.

    c. **Szolgáltató leírása**

    d. Állítsa a **címtár-bérlőt** az Azure stack Azure ad-bérlőre. 

    e. Válassza az **ajánlat**lehetőséget. Válassza ki a létrehozott ajánlat nevét. Jegyezze fel az előfizetés AZONOSÍTÓját.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Egyszerű szolgáltatásnév és hitelesítő adatok létrehozása AD FS

Ha Active Directory összevont szolgáltatásokat (AD FS) használ az Identitáskezelő szolgáltatáshoz, létre kell hoznia egy egyszerű szolgáltatásnevet a Kubernetes-fürtöt telepítő felhasználók számára. Egyszerű szolgáltatás létrehozása ügyfél-titok használatával. Útmutatásért lásd: [egyszerű szolgáltatásnév létrehozása az ügyfél titkos](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials)kódjával.

## <a name="add-an-ubuntu-server-image"></a>Ubuntu-kiszolgáló rendszerképének hozzáadása

Adja hozzá a következő Ubuntu Server-rendszerképet a piactérhez:

1. Jelentkezzen be a [felügyeleti portálra](https://adminportal.local.azurestack.external).

1. Válassza a **minden szolgáltatás**lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés**lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból**lehetőséget.

1. Írja be a `Ubuntu Server` (igen) kifejezést.

1. Válassza ki a kiszolgáló legújabb verzióját. Ellenőrizze a teljes verziót, és győződjön meg arról, hogy a legújabb verzióval rendelkezik:
    - **Közzétevő**: Canonical
    - **Ajánlat**: UbuntuServer
    - **Verzió**: 16.04.201806120 (vagy legújabb verzió)
    - **SKU**: 16,04 – LTS

1. Válassza a **Letöltés lehetőséget.**

## <a name="add-a-custom-script-for-linux"></a>Egyéni parancsfájl hozzáadása Linuxra

Adja hozzá a Kubernetes a piactéren:

1. Nyissa meg a [felügyeleti portált](https://adminportal.local.azurestack.external).

1. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés**lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból**lehetőséget.

1. Írja be a `Custom Script for Linux` (igen) kifejezést.

1. Válassza ki a parancsfájlt a következő profillal:
   - **Ajánlat**: egyéni parancsfájl a Linux 2,0-hez
   - **Verzió**: 2.0.6 (vagy legújabb verzió)
   - **Közzétevő**: Microsoft Corp

     > [!Note]  
     > A Linux rendszerhez készült egyéni parancsfájlok egynél több verziója is szerepelhet a listáról. Hozzá kell adnia az elem utolsó verzióját is.

1. Válassza a **Letöltés lehetőséget.**


## <a name="add-kubernetes-to-the-marketplace"></a>Kubernetes hozzáadása a piactérhez

1. Nyissa meg a [felügyeleti portált](https://adminportal.local.azurestack.external).

1. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza a **piactér-kezelés**lehetőséget.

1. Válassza **a + Hozzáadás az Azure-ból**lehetőséget.

1. Írja be a `Kubernetes` (igen) kifejezést.

1. Válassza a(z) `Kubernetes Cluster` lehetőséget.

1. Válassza a **Letöltés lehetőséget.**

    > [!note]  
    > 5 percet vehet igénybe, amíg a Piactéri elemek megjelennek a piactéren.

    ![Kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>A Kubernetes frissítése vagy eltávolítása 

A Kubernetes elem frissítésekor a piactéren eltávolítja az előző elemeket. A cikk utasításait követve adja hozzá a Kubernetes-frissítést a piactérhez.

A Kubernetes elemek eltávolítása:

1. Kapcsolódjon Azure Stack a PowerShell-lel operátorként. Útmutatásért lásd: [Kapcsolódás a Azure Stackhoz a PowerShell-lel kezelőként](azure-stack-powershell-configure-admin.md).

2. A gyűjtemény aktuális Kubernetes-fürtjének megkeresése.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Megjegyzés: az aktuális elem neve, például `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Az alábbi PowerShell-parancsmag használatával távolítsa el az elemeket:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Következő lépések

[Kubernetes üzembe helyezése Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[A Azure Stack szolgáltatásainak áttekintése](service-plan-offer-subscription-overview.md)
