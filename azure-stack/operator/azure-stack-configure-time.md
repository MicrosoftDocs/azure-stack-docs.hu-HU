---
title: A Azure Stack időkiszolgálójának konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a Azure Stack időkiszolgálóját.
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
ms.openlocfilehash: cc432538715c1c990a9efe6473b33303deb78734
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72280533"
---
# <a name="configure-the-time-server-for-azure-stack"></a>A Azure Stack időkiszolgálójának konfigurálása

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*  

A rendszerjogosultságú végpont (PEP) használatával frissítheti Azure Stack időkiszolgálóját. Olyan állomásnevet használjon, amely két vagy több NTP-kiszolgáló IP-címére van feloldva.

A Azure Stack a Network Time Protocol (NTP) protokollt használja az internetes időkiszolgálókhoz való kapcsolódáshoz. Az NTP-kiszolgálók pontos rendszeridőt biztosítanak. Az idő Azure Stack fizikai hálózati kapcsolók, a hardver életciklus-gazdagép, az infrastruktúra-szolgáltatás és a virtuális gépek között használatos. Ha az óra nincs szinkronizálva, Azure Stack a hálózattal és a hitelesítéssel kapcsolatos súlyos problémák léphetnek fel. A naplófájlok, dokumentumok és egyéb fájlok helytelen időbélyegzővel hozhatók létre.

Legalább egy időkiszolgáló (NTP) szükséges a Azure Stack számára az idő szinkronizálásához. Azure Stack telepítésekor meg kell adnia egy NTP-kiszolgáló címeit. Az idő egy kritikus adatközpont-infrastruktúra szolgáltatás. Ha a szolgáltatás megváltozik, frissítenie kell az időt.

## <a name="configure-time"></a>Konfigurálás időpontja

1. [Kapcsolódjon a PEP-](azure-stack-privileged-endpoint.md)hez. 
    > [!Note]  
    > Egy támogatási jegy megnyitásával nem szükséges feloldani a Kiemelt végpontot.

2. Futtassa a következő parancsot az aktuálisan konfigurált NTP-kiszolgáló áttekintéséhez:

    ```PowerShell
    Get-AzsTimeSource
    ```

3. A következő parancs futtatásával frissítse Azure Stack az új NTP-kiszolgáló használatára és az idő azonnali szinkronizálására.

    > [!Note]  
    > Ez az eljárás nem frissíti az időkiszolgálót a fizikai kapcsolókon.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Az esetleges hibákért tekintse át a parancs kimenetét.


## <a name="next-steps"></a>Következő lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack integrációs megfontolások](azure-stack-datacenter-integration.md)  
