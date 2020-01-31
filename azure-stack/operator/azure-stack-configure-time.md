---
title: Az Azure Stack hub időkiszolgálójának konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a Azure Stack hub időkiszolgálóját.
author: ihenkel
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: d2dbc9ea7968ed3ad1911dd908966b6ac1e2a9c9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76879193"
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
