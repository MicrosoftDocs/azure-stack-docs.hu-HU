---
title: Az időkiszolgáló konfigurálása Azure Stack központban
description: Megtudhatja, hogyan konfigurálhatja az időkiszolgálót Azure Stack központban.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 2/19/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: f1627be5d6986cd82fe21a58163f22eb0873eda6
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836104"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>Az Azure Stack hub időkiszolgálójának konfigurálása

A Kiemelt végpont (PEP) segítségével frissítheti Azure Stack hub időkiszolgálóját. Olyan állomásnevet használjon, amely két vagy több NTP-kiszolgáló IP-címére van feloldva.

Azure Stack hub az NTP használatával csatlakozik az internetes időkiszolgálóhoz. Az NTP-kiszolgálók pontos rendszeridőt biztosítanak. Az idő az Azure Stack hub fizikai hálózati kapcsolói, a hardver életciklus-gazdagép, az infrastruktúra-szolgáltatás és a virtuális gépek között használatos. Ha az óra nincs szinkronizálva, Azure Stack hub súlyos problémákat tapasztalhat a hálózattal és a hitelesítéssel kapcsolatban. A naplófájlok, dokumentumok és egyéb fájlok helytelen időbélyegzővel hozhatók létre.

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
    > Ez az eljárás nem frissíti az időkiszolgálót a fizikai kapcsolókon. Ha a Time Server nem Windows-alapú NTP-kiszolgáló, akkor hozzá kell adnia a jelzőt `0x8`.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

    Windows-alapú időkiszolgálón kívüli kiszolgálók esetén:

    ```PowerShell
    Set-AzsTimeSource -TimeServer "NEWTIMESERVERIP,0x8" -resync
    ```

4. Az esetleges hibákért tekintse át a parancs kimenetét.


## <a name="next-steps"></a>További lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack hub integrációs szempontjai](azure-stack-datacenter-integration.md)  
