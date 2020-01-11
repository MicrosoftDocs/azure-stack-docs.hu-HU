---
title: Az Azure Stack hub időkiszolgálójának konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a Azure Stack hub időkiszolgálóját.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: bc139b43c2914f14cbe3bf998c20137e7a07dd25
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882742"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>Az Azure Stack hub időkiszolgálójának konfigurálása

A Kiemelt végpont (PEP) segítségével frissítheti Azure Stack hub időkiszolgálóját. Olyan állomásnevet használjon, amely két vagy több NTP-kiszolgáló IP-címére van feloldva.

Azure Stack hub a Network Time Protocol (NTP) protokollt használja az internetes időkiszolgálókhoz való kapcsolódáshoz. Az NTP-kiszolgálók pontos rendszeridőt biztosítanak. Az idő az Azure Stack hub fizikai hálózati kapcsolói, a hardver életciklus-gazdagép, az infrastruktúra-szolgáltatás és a virtuális gépek között használatos. Ha az óra nincs szinkronizálva, Azure Stack hub súlyos problémákat tapasztalhat a hálózattal és a hitelesítéssel kapcsolatban. A naplófájlok, dokumentumok és egyéb fájlok helytelen időbélyegzővel hozhatók létre.

A Azure Stack hub számára egy időkiszolgáló (NTP) megadása szükséges az idő szinkronizálásához. Azure Stack hub telepítésekor meg kell adnia egy NTP-kiszolgáló címeit. Az idő egy kritikus adatközpont-infrastruktúra szolgáltatás. Ha a szolgáltatás megváltozik, frissítenie kell az időt.

> [!NOTE]
> Azure Stack hub csak egyszeres kiszolgálóval (NTP) történő szinkronizálást támogatja. Nem adhat meg több NTPs az Azure Stack hub számára az idő szinkronizálásához.

## <a name="configure-time"></a>Konfigurálás időpontja

1. [Kapcsolódjon a PEP-](azure-stack-privileged-endpoint.md)hez. 
    > [!Note]  
    > Egy támogatási jegy megnyitásával nem szükséges feloldani a Kiemelt végpontot.

2. Futtassa a következő parancsot az aktuálisan konfigurált NTP-kiszolgáló áttekintéséhez:

    ```PowerShell
    Get-AzsTimeSource
    ```

3. A következő parancs futtatásával frissítse Azure Stack hub-t az új NTP-kiszolgáló használatára, és azonnal szinkronizálja az időt.

    > [!Note]  
    > Ez az eljárás nem frissíti az időkiszolgálót a fizikai kapcsolókon.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Az esetleges hibákért tekintse át a parancs kimenetét.


## <a name="next-steps"></a>Következő lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack hub integrációs szempontjai](azure-stack-datacenter-integration.md)  
