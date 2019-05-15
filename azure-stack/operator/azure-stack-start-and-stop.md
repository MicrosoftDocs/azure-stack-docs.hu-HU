---
title: Elindítása és leállítása az Azure Stackben |} A Microsoft Docs
description: Megtudhatja, hogyan indítása és leállítása az Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: e4806dffd30786384adf92a50c3ac6879d8d4526
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618548"
---
# <a name="start-and-stop-azure-stack"></a>Elindítása és leállítása az Azure Stackben
Ez a cikk megfelelően állítsa le és indítsa újra az Azure Stack-szolgáltatásokat az eljárást kell követnie. Leállítás fizikailag a teljes Azure Stack-környezet lesz kikapcsolásához. Bekapcsolja az összes infrastruktúra-szerepkörök a indítsa el, és adja vissza a bérlői erőforrásokat az áramellátási állapot, leállítás előtt voltak.

## <a name="stop-azure-stack"></a>Állítsa le az Azure Stack 

Állítsa le az Azure Stack az alábbi lépéseket követve:

1. Az Azure Stack-környezet bérlői erőforrások a közelgő leállítás futó összes számítási feladatainak előkészítése. 

2. Nyissa meg egy emelt szintű végpont munkamenet (EGP) hálózati hozzáféréssel rendelkező számítógépen az Azure Stack ERCS virtuális gépekre. Útmutatásért lásd: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).

3. Futtassa az EGP:

    ```powershell
      Stop-AzureStack
    ```

4. Várjon, amíg az összes fizikai az Azure Stack csomópontok, a power ki.

> [!Note]  
> Ellenőrizheti a fizikai csomópont power állapotát, a számítógépgyártó (OEM) ki az Azure Stack hardverét megadott utasítások szerint. 

## <a name="start-azure-stack"></a>Indítsa el az Azure Stack 

Indítsa el az Azure Stack az alábbi lépéseket követve. Kövesse az alábbi lépéseket, függetlenül attól, milyen az Azure Stack leállt.

1. Az egyes fizikai csomópontjainak az Azure Stack környezettel Power. Győződjön meg arról a fizikai csomópontok utasításokat bekapcsolási a a számítógépgyártó (OEM) ki az Azure Stackhez készült hardver megadott utasítások szerint.

2. Várjon, amíg elindítja az Azure Stack infrastruktúra-szolgáltatásokat. Az Azure Stack infrastruktúra-szolgáltatások, az indítási folyamat befejezése két óra lehet szükség. Az Azure Stack kezdő állapotának ellenőrzéséhez a [ **Get-ActionStatus** parancsmag](#get-the-startup-status-for-azure-stack).

3. Győződjön meg arról, hogy az összes bérlői erőforrás vissza az állapot, leállítás előtt voltak. Bérlői erőforrások futó számítási feladatokat kell előfordulhat, hogy a munkaterhelés-kezelő indítás után konfigurálni.

## <a name="get-the-startup-status-for-azure-stack"></a>Az Azure stack-beli indítási állapotának beolvasása

Az indítási lekérése az Azure Stack indítási rutin a következő lépésekkel:

1. Munkamenetet nyit meg egy emelt szintű végpont hálózati hozzáféréssel rendelkező számítógépen az Azure Stack ERCS virtuális gépekre.

2. Futtassa az EGP:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Indítási és leállítási az Azure Stack hibaelhárítása

Az alábbi lépések végrehajtásával, ha az infrastrukturális és bérlői szolgáltatások nem sikerült elindítani a 2 órával később, a power az Azure Stack környezettel a. 

1. Munkamenetet nyit meg egy emelt szintű végpont hálózati hozzáféréssel rendelkező számítógépen az Azure Stack ERCS virtuális gépekre.

2. Futtassa a következőt: 

    ```powershell
      Test-AzureStack
      ```

3. Tekintse át a kimenetet, és bármely állapotával kapcsolatos hibák elhárításához. További információkért lásd: [futtatása az Azure Stack teszt](azure-stack-diagnostic-test.md).

4. Futtassa a következőt:

    ```powershell
      Start-AzureStack
    ```

5. Ha fut **Start-AzureStack** eredményez a hiba, kérjen segítséget a Microsoft Services. 

## <a name="next-steps"></a>További lépések 

Tudjon meg többet [Azure Stack diagnosztikai eszközök](azure-stack-diagnostics.md)
