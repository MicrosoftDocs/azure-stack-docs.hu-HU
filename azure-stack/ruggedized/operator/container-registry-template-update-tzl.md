---
title: A tároló-beállításjegyzék frissítése Azure Stack hub-ban | Microsoft Docs
titleSuffix: Azure Stack
description: Ismerje meg, hogyan frissítheti Azure Stack hub tároló-beállításjegyzékét.
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
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 1239e12235debaa8ab6a3037c34ea27e11da0ce2
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96940035"
---
# <a name="update-the-container-registry-in-azure-stack-hub"></a>A tároló beállításjegyzékének frissítése Azure Stack központban

Azure Stack hub-felhasználók frissíthetik a tároló beállításjegyzékének üzembe helyezését egy újabb AK-alapú alaprendszerkép SKU-ra az alábbi utasítások használatával. A tároló beállításjegyzék-sablonjának virtuális gépe és szolgáltatása állapot nélküli, mivel az összes állapot és tároló lemezképét a blob Storage tárolja. A frissítés olyan egyszerű, mint a tároló beállításjegyzék-sablonjának üzembe helyezése az AK-alapú alaprendszerkép VHD-fájljának újabb verziójával, és a DNS átirányítása az új virtuális gépre. A régi és az új tároló beállításjegyzék-sablon virtuális gépei DNS-értékének frissítésére irányuló művelet az értékek propagálásakor az időszakos beállításjegyzék-kapcsolat kis ablakát fogja eredményezni.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operator"></a>Operátor

1.  A Azure Stack piactéren megjelenő, a legújabb AK-beli alaprendszerkép bevonása. Az AK alaprendszerképe havi ütemben frissül.

> ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image1.png)

### <a name="user"></a>Felhasználó

1.  Győződjön meg arról, hogy a tároló beállításjegyzék-sablon üzembe helyezéséhez használt AK alaprendszerkép SKU-jának tallózással keresse meg az erőforráscsoport központi telepítési rekordját, és válassza a **bemenetek** lehetőséget.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image2.png)

2.  Állapítsa meg, hogy a **Get-VMImageSku** függvénnyel elérhető-e az AK alaprendszerképének újabb SKU-azonosítói a `Import-Module .\pre-reqs.ps1` tároló beállításjegyzék-sablon parancsfájljainak használatával.

    ```powershell  
    PS C:\azurestack-galler-master\registry\Scripts> Get-VMImageSku -Location Shanghai
    
    Skus                  
    ----                  
    aks-ubuntu-1604-201909
    aks-ubuntu-1604-201910 
    ```

## <a name="parameters-required"></a>Szükséges paraméterek

| Paraméter | Részletek |
| --- | --- |
| Felhasználónév | A virtuális géphez való bejelentkezéshez adja meg a felhasználónevet. |
| Nyilvános SSH-kulcs | Adja meg a virtuális géppel való hitelesítéshez használt nyilvános SSH-kulcsot SSH protokoll használatával. |
| Méret | Válassza ki a telepítendő virtuális gép méretét. |
| Nyilvános IP-cím | Adja meg a virtuális gép IP-címének (dinamikus/statikus) nevét és típusát. |
| Tartománynévcímke | Adja meg a beállításjegyzékhez tartozó DNS-előtagot. Ez a teljes FQDN meg kell egyeznie a beállításjegyzékhez létrehozott PFX-tanúsítvány CN-értékével. |
| Replikák | Itt adhatja meg, hogy hány tároló-replika induljon el. |
| Rendszerkép SKU | A központi telepítéshez használni kívánt rendszerkép-SKU megadása. A **Get-VMImageSku** PowerShell-parancsmag FELSOROLJA az AK-alapú alaprendszerképhez elérhető SKU-ket. |
| Egyszerű szolgáltatásnév ügyfél-azonosítója | Az előző üzemelő példányban használt egyszerű szolgáltatásnév (SPN) alkalmazás AZONOSÍTÓjának meghatározása. |
| Egyszerű szolgáltatás jelszava/Jelszó megerősítése | A korábbi üzemelő példányban használt SPN-alkalmazás AZONOSÍTÓjának titkát kell megadnia. |
| Meglévő kiterjesztett Storage-fiók erőforrás-azonosítója | A Storage-fiók erőforrás-AZONOSÍTÓjának meghatározása az előző üzemelő példányban használt módon. |
| Meglévő háttérbeli blob-tároló | Adja meg a blob-tároló nevét az előző üzemelő példányban használt módon. |
| PFX-tanúsítvány Key Vault erőforrás-azonosítója | Az előző üzemelő példányban használt erőforrás-azonosító Microsoft Azure Key Vault. |
| PFX-tanúsítvány Key Vault titkos URL-cím | Az előző üzemelő példányban használt tanúsítvány URL-címének megadása. |
| PFX-Tanúsítvány ujjlenyomata | Az előző üzemelő példányban használt tanúsítvány ujjlenyomatának meghatározása. |

## <a name="installation"></a>Telepítés

1.  Telepítse a Container Registry sablon új példányát egy új erőforráscsoporthoz.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image3.png)

2.  Adja meg a legújabb SKU-kimenetet a `Get-VMImage` parancsfájlból, és használjon egy egyedi **dnsname** paramétert a virtuális gép konfigurációjának kezdeti telepítése során, használja ugyanazt a szolgáltatásnevet és titkos kulcsot, mint az első telepítés.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image4.png)

3.  Ugyanazokat a tárolási és Key Vault paramétereket használja, mint a tároló-és Key Vault-konfiguráció kezdeti telepítése.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image5.png)

1.  Miután telepítette az új tároló beállításjegyzék-sablonját, navigáljon a kezdeti erőforráscsoporthoz, és válassza ki a nyilvános IP-cím erőforrást.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image6.png)

1.  A nyilvános IP-cím erőforrásban navigáljon a konfigurációhoz, és módosítsa a DNS-név címkét úgy, hogy az az újonnan üzembe helyezett erőforráshoz is használható legyen. Megjegyzés: Ha módosította a DNS-név címkét, és a hívások **mentése** a Container Registryre lehetőséget választja, a művelet sikertelen lesz.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image7.png)
    
    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image8.png)

2.  Navigáljon a Container Registry sablon új példányának üzembe helyezéséhez használt új erőforráscsoporthoz, válassza ki a nyilvános IP-erőforrást, a konfigurációt, és frissítse a DNS-név címkéjét az eredeti telepítésben használt helyes névre, ebben a példában `myreg` válassza a **Save (Mentés**) lehetőséget.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image9.png)
    
    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-updating-tzl/image10.png)

3.  A következő 30 perc időszakos hozzáférést biztosít a tároló-beállításjegyzékhez, mivel a DNS-rekord propagálva lesz. Ellenőrizze a kapcsolatokat a Docker-beállításjegyzékbe való bejelentkezéssel, és egy rendszerkép kihúzásával/kitolásával.

## <a name="next-steps"></a>Következő lépések

[Azure Stack Marketplace – áttekintés](../../operator/azure-stack-marketplace.md)
