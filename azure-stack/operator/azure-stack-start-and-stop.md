---
title: Indítás és leállítás
titleSuffix: Azure Stack
description: Útmutató Azure Stack elindításához és leállításához.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: dbd85b4d7b63edb89cc327fd5b1f5592c24db508
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954383"
---
# <a name="start-and-stop-azure-stack"></a>Azure Stack elindítása és leállítása

Kövesse a cikkben ismertetett eljárásokat a Azure Stack szolgáltatások megfelelő leállításához és újraindításához. A *Leállítás* után a rendszer fizikailag leállítja és kikapcsolja a teljes Azure stack környezetet. *Indítsa el* a hatásköröket az összes infrastruktúra-szerepkörön, és adja vissza a bérlői erőforrásokat a leállítás előtt.

## <a name="stop-azure-stack"></a>Azure Stack leállítása

Azure Stack leállítása vagy leállítása a következő lépésekkel:

1. Készítse elő az Azure Stack-környezet bérlői erőforrásain futó összes munkaterhelést a közelgő leállításhoz.

2. Nyisson meg egy kiemelt jogosultságú végponti munkamenetet (PEP) egy olyan gépről, amely hálózati hozzáféréssel rendelkezik a Azure Stack ERCS virtuális gépekhez. Útmutatásért lásd: [a privilegizált végpont használata Azure Stackban](azure-stack-privileged-endpoint.md).

3. A PEP-ből futtassa a következőt:

    ```powershell
      Stop-AzureStack
    ```

4. Várjon, amíg az összes fizikai Azure Stack csomópont ki nem kapcsol.

> [!Note]
> A fizikai csomópontok energiaellátási állapotát az eredeti berendezésgyártó (OEM) azon utasításait követve ellenőrizheti, akik a Azure Stack hardvert szolgáltatták.

## <a name="start-azure-stack"></a>Kezdés Azure Stack

Indítsa el Azure Stack a következő lépésekkel. Kövesse az alábbi lépéseket a Azure Stack leállításának módjától függetlenül.

1. Kapcsolja be a Azure Stack-környezet egyes fizikai csomópontjait. Ellenőrizze a fizikai csomópontok bekapcsolási utasításait a Azure Stack hardverét szolgáltató SZÁMÍTÓGÉPGYÁRTÓ utasításait követve.

2. Várjon, amíg a Azure Stack infrastruktúra szolgáltatás elindul. Azure Stack infrastrukturális szolgáltatások két órát igényelhetnek a kezdési folyamat befejezéséhez. A [ **Get-ActionStatus** parancsmaggal](#get-the-startup-status-for-azure-stack)ellenőrizheti Azure stack indítási állapotát.

3. Győződjön meg arról, hogy az összes bérlői erőforrás vissza lett állítva a leállítás előtt megadott állapotba. Előfordulhat, hogy a munkaterhelés-kezelő indítása után újra kell konfigurálni a bérlői erőforrásokon futó munkaterheléseket.

## <a name="get-the-startup-status-for-azure-stack"></a>Azure Stack indítási állapotának beolvasása

Szerezze be a Azure Stack indítási rutin indítását a következő lépésekkel:

1. Nyisson meg egy kiemelt jogosultságú végponti munkamenetet egy olyan gépről, amely hálózati hozzáféréssel rendelkezik a Azure Stack ERCS virtuális gépekhez.

2. A PEP-ből futtassa a következőt:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Azure Stack indításának és leállításának hibakeresése

Ha az infrastruktúra és a bérlői szolgáltatások nem indulnak el sikeresen két órával a Azure Stack környezetének bekapcsolása után, hajtsa végre az alábbi lépéseket.

1. Nyisson meg egy kiemelt jogosultságú végponti munkamenetet egy olyan gépről, amely hálózati hozzáféréssel rendelkezik a Azure Stack ERCS virtuális gépekhez.

2. Futtassa a következőt:

    ```powershell
      Test-AzureStack
      ```

3. Tekintse át a kimenetet, és javítsa ki az esetleges állapottal kapcsolatos hibákat. További információ: [Azure stack érvényesítési tesztének futtatása](azure-stack-diagnostic-test.md).

4. Futtassa a következőt:

    ```powershell
      Start-AzureStack
    ```

5. Ha a **Start-AzureStack** futtatása hibát eredményez, forduljon a Microsoft ügyfélszolgálatahoz.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure stack diagnosztikai eszközökről](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
