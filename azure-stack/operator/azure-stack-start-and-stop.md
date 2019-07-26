---
title: Azure Stack elindítása és leállítása | Microsoft Docs
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
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 993c0c668a8894c82eddbf79e93b1722d3a3d8f4
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494020"
---
# <a name="start-and-stop-azure-stack"></a>Elindítása és leállítása az Azure Stackben
A jelen cikkben ismertetett eljárásokkal Azure Stack szolgáltatások megfelelő leállítására és újraindítására van lehetőség. A Leállítás fizikailag kikapcsolja a teljes Azure Stack környezetet. A rendszer elindítja az összes infrastruktúra-szerepkörre vonatkozó hatásköröket, és visszaadja a bérlői erőforrásokat a leállításuk előtti állapotba.

## <a name="stop-azure-stack"></a>Azure Stack leállítása 

Azure Stack leállítása a következő lépésekkel:

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

1. Kapcsolja be a Azure Stack-környezet egyes fizikai csomópontjait. Ellenőrizze a fizikai csomópontok bekapcsolási utasításait az eredeti berendezésgyártó (OEM) utasításait követve, akik a Azure Stack hardverét adták meg.

2. Várjon, amíg a Azure Stack infrastruktúra szolgáltatás elindul. Azure Stack infrastrukturális szolgáltatások két órát igényelhetnek a kezdési folyamat befejezéséhez. A [ **Get-ActionStatus** parancsmaggal](#get-the-startup-status-for-azure-stack)ellenőrizheti Azure stack indítási állapotát.

3. Győződjön meg arról, hogy az összes bérlői erőforrás vissza lett állítva a leállítás előtti állapotba. Előfordulhat, hogy a munkaterhelés-kezelő indítása után újra kell konfigurálni a bérlői erőforrásokon futó munkaterheléseket.

## <a name="get-the-startup-status-for-azure-stack"></a>Azure Stack indítási állapotának beolvasása

Szerezze be a Azure Stack indítási rutin indítását a következő lépésekkel:

1. Nyisson meg egy kiemelt jogosultságú végponti munkamenetet egy olyan gépről, amely hálózati hozzáféréssel rendelkezik a Azure Stack ERCS virtuális gépekhez.

2. A PEP-ből futtassa a következőt:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Azure Stack indításának és leállításának hibakeresése

Hajtsa végre az alábbi lépéseket, ha az infrastruktúra és a bérlői szolgáltatások nem indulnak el sikeresen 2 órával a Azure Stack környezetének bekapcsolása után. 

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

5. Ha a **Start-AzureStack** futtatása hibát eredményez, forduljon a Microsoft ügyfélszolgálatához. 

## <a name="next-steps"></a>További lépések 

További információ a [Azure stack diagnosztikai eszközökről](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)
