---
title: A Container Registry frissítése Azure Stack hub-MDC
titleSuffix: Azure Stack
description: Ismerje meg, hogyan frissítheti a tároló-beállításjegyzéket Azure Stack hub-ban a moduláris adatközpontok (MDC) számára.
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
ms.openlocfilehash: dafd9d485125d7c8da1524b71fddb75af7a4ebba
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598572"
---
# <a name="update-the-container-registry-in-azure-stack-hub---modular-data-center-mdc"></a>A Container Registry frissítése Azure Stack hub-moduláris adatközpontban (MDC)

Azure Stack hub-felhasználók frissíthetik a tároló beállításjegyzékének üzembe helyezését egy újabb AK-alapú alaprendszerkép SKU-ra az alábbi utasítások használatával. A tároló beállításjegyzék-sablonjának virtuális gépe és szolgáltatása állapot nélküli, mivel az összes állapot és tároló lemezképét a blob Storage tárolja. A frissítés olyan egyszerű, mint a tároló beállításjegyzék-sablonjának üzembe helyezése az AK-alapú alaprendszerkép VHD-fájljának újabb verziójával, és a DNS átirányítása az új virtuális gépre. A régi és az új tároló beállításjegyzék-sablon virtuális gépei DNS-értékének frissítésére irányuló művelet az értékek propagálásakor az időszakos beállításjegyzék-kapcsolat kis ablakát fogja eredményezni.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operator"></a>Operátor

- A Azure Stack piactéren megjelenő, a legújabb AK-beli alaprendszerkép bevonása. Az AK alaprendszerképe havi ütemben frissül.

  ![AK-alapú alaprendszerkép](./media/container-registry-template-updating-tzl/aks-base-image.png)

### <a name="user"></a>Felhasználó

1.  Győződjön meg arról, hogy a tároló beállításjegyzék-sablon üzembe helyezéséhez használt AK alaprendszerkép SKU-jának tallózással keresse meg az erőforráscsoport központi telepítési rekordját, és válassza a **bemenetek** lehetőséget.

    ![A "bemenetek" oldalt megjelenítő képernyőkép.](./media/container-registry-template-updating-tzl/inputs.png)

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

    ![Új tároló beállításjegyzék-sablonjának telepítése](./media/container-registry-template-updating-tzl/new-instance.png)

2.  Adja meg a legújabb SKU-kimenetet a `Get-VMImage` parancsfájlból, és használjon egy egyedi **dnsname** paramétert a virtuális gép konfigurációjának kezdeti telepítése során, használja ugyanazt a szolgáltatásnevet és titkos kulcsot, mint az első telepítés.

    ![SKU meghatározása](./media/container-registry-template-updating-tzl/sku.png)

3.  Ugyanazokat a tárolási és Key Vault paramétereket használja, mint a tároló-és Key Vault-konfiguráció kezdeti telepítése.

    ![Tároló hozzáadása](./media/container-registry-template-updating-tzl/storage.png)

1.  Miután telepítette az új tároló beállításjegyzék-sablonját, navigáljon a kezdeti erőforráscsoporthoz, és válassza ki a nyilvános IP-cím erőforrást.

    ![IP-cím kiválasztása](./media/container-registry-template-updating-tzl/ip.png)

1.  A nyilvános IP-cím erőforrásban navigáljon a konfigurációhoz, és módosítsa a DNS-név címkét úgy, hogy az az újonnan üzembe helyezett erőforráshoz is használható legyen. Megjegyzés: Ha módosította a DNS-név címkét, és a hívások **mentése** a Container Registryre lehetőséget választja, a művelet sikertelen lesz.

    ![DNS-címke módosítása](./media/container-registry-template-updating-tzl/dns.png)
    
    ![Módosítás mentése](./media/container-registry-template-updating-tzl/save.png)

2.  Navigáljon a Container Registry sablon új példányának üzembe helyezéséhez használt új erőforráscsoporthoz, válassza ki a nyilvános IP-erőforrást, a konfigurációt, és frissítse a DNS-név címkéjét az eredeti telepítésben használt helyes névre, ebben a példában `myreg` válassza a **Save (Mentés**) lehetőséget.

    ![Konfiguráció kiválasztása](./media/container-registry-template-updating-tzl/select-configuration.png)
    
    ![Konfiguráció mentése](./media/container-registry-template-updating-tzl/save-configuration.png)

3.  A következő 30 perc időszakos hozzáférést biztosít a tároló-beállításjegyzékhez, mivel a DNS-rekord propagálva lesz. Ellenőrizze a kapcsolatokat a Docker-beállításjegyzékbe való bejelentkezéssel, és egy rendszerkép kihúzásával/kitolásával.

## <a name="next-steps"></a>További lépések

[Azure Stack Marketplace – áttekintés](../../operator/azure-stack-marketplace.md)
