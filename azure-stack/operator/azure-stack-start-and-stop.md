---
title: Indítás és leállítás
titleSuffix: Azure Stack Hub
description: Útmutató Azure Stack hub elindításához és leállításához.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/02/2019
ms.author: inhenkel
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 524c951717075b293b7829a31ac093a9061f46e8
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509500"
---
# <a name="start-and-stop-azure-stack-hub"></a>Azure Stack hub elindítása és leállítása

Az ebben a cikkben ismertetett eljárásokkal a Azure Stack hub szolgáltatásainak megfelelő leállítására és újraindítására van lehetőség. A *Leállítás* után a rendszer fizikailag leállítja és kikapcsolja a teljes Azure stack hub-környezetet. *Indítsa el* a hatásköröket az összes infrastruktúra-szerepkörön, és adja vissza a bérlői erőforrásokat a leállítás előtt.

## <a name="stop-azure-stack-hub"></a>Azure Stack hub leállítása

Állítsa le vagy állítsa le Azure Stack hubot a következő lépésekkel:

1. Készítse elő az Azure Stack hub-környezet bérlői erőforrásain futó összes munkaterhelést a közelgő leállításhoz.

2. Nyisson meg egy kiemelt jogosultságú végponti munkamenetet (PEP) egy olyan gépről, amely hálózati hozzáféréssel rendelkezik az Azure Stack hub ERCS virtuális gépekhez. Útmutatásért lásd: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

3. A PEP-ből futtassa a következőt:

    ```powershell
      Stop-AzureStack
    ```

4. Várjon, amíg az összes fizikai Azure Stack hub-csomópont ki nem kapcsol.

> [!Note]
> A fizikai csomópontok energiaellátási állapotát az eredeti berendezésgyártó (OEM) azon utasításait követve ellenőrizheti, akik a Azure Stack hub hardvert szolgáltatták.

## <a name="start-azure-stack-hub"></a>Azure Stack hub elindítása

Indítsa el Azure Stack hubot a következő lépésekkel. Kövesse az alábbi lépéseket, függetlenül attól, hogy Azure Stack hub leállt.

1. Kapcsolja be az Azure Stack hub-környezet összes fizikai csomópontját. Ellenőrizze a fizikai csomópontok bekapcsolási utasításait az Azure Stack hub hardverét szolgáltató SZÁMÍTÓGÉPGYÁRTÓ utasításait követve.

2. Várjon, amíg a Azure Stack hub infrastruktúra-szolgáltatás elindul. Azure Stack hub infrastruktúra-szolgáltatásai két órát igényelhetnek a kezdési folyamat befejezéséhez. Azure Stack hub indítási állapotát a [ **Get-ActionStatus** parancsmaggal](#get-the-startup-status-for-azure-stack-hub)ellenőrizheti.

3. Győződjön meg arról, hogy az összes bérlői erőforrás vissza lett állítva a leállítás előtt megadott állapotba. Előfordulhat, hogy a munkaterhelés-kezelő indítása után újra kell konfigurálni a bérlői erőforrásokon futó munkaterheléseket.

## <a name="get-the-startup-status-for-azure-stack-hub"></a>Azure Stack hub indítási állapotának beolvasása

Szerezze be a Azure Stack hub indítási rutinjának indítását az alábbi lépésekkel:

1. Nyisson meg egy kiemelt jogosultságú végponti munkamenetet egy olyan gépről, amely hálózati hozzáféréssel rendelkezik az Azure Stack hub ERCS virtuális gépekhez.

2. A PEP-ből futtassa a következőt:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack-hub"></a>Azure Stack hub indítási és leállítási hibáinak megoldása

Ha az infrastruktúra és a bérlői szolgáltatások nem indulnak el sikeresen két órával az Azure Stack hub-környezet bekapcsolása után, hajtsa végre az alábbi lépéseket.

1. Nyisson meg egy kiemelt jogosultságú végponti munkamenetet egy olyan gépről, amely hálózati hozzáféréssel rendelkezik az Azure Stack hub ERCS virtuális gépekhez.

2. Futtassa a következőt:

    ```powershell
      Test-AzureStack
      ```

3. Tekintse át a kimenetet, és javítsa ki az esetleges állapottal kapcsolatos hibákat. További információ: [Azure stack hub ellenőrzési tesztének futtatása](azure-stack-diagnostic-test.md).

4. Futtassa a következőt:

    ```powershell
      Start-AzureStack
    ```

5. Ha a **Start-AzureStack** futtatása hibát eredményez, forduljon a Microsoft ügyfélszolgálatahoz.

## <a name="next-steps"></a>Következő lépések

További információ az [Azure stack hub diagnosztikai eszközeiről](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
