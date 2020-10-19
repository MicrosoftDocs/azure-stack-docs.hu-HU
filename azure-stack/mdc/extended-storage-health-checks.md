---
title: Azure Stack – a moduláris adatközpont blob Storage-tárolójának kibővített tárterületének állapot-ellenőrzése
description: Ez a cikk útmutatást nyújt ahhoz, hogyan végezhető el az állapot-ellenőrzés a kibővített tárolóban a moduláris adatközpont blob Storage-tárolójában.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/01/2020
ms.openlocfilehash: 9d8e7915cb5c1ca9d0a073c750f67a5688157e74
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182958"
---
# <a name="extended-storage-health-checks"></a>Kiterjesztett tárterület állapotának ellenőrzése

Ez a cikk útmutatást nyújt az adatközpont kiterjesztett tárolási hardverének és a Azure Stack hub telepítésének állapotának ellenőrzéséhez.

Mielőtt elkezdené, tekintse át és kövesse a [belső vezérlőprogram frissítése a kiterjesztett tárolóhoz](extended-storage-firmware-updates.md)című témakör lépéseit.

Az utolsó lépés a rendszer leszállítása előtt. Kövesse a Rövid útmutató lépéseit.

## <a name="extended-storage-health"></a>Kiterjesztett tárolási állapot

Ez a szakasz útmutatást nyújt a kiterjesztett tárolási hardver állapotának ellenőrzéséhez.
Ellenőrizze az eseményeket, és értesítse a rendszergazdát, ha problémák merülnek fel. 


A fürt tárolási készletének állapotának vizsgálatához futtassa a következő parancsot:
```console
isi storagepool health
```

Ha például kifogástalan, a parancs a következőképpen néz ki:
```console
All pools are healthy.
Unprovisioned drives: none
```

Ha a sérült vagy hiányzó meghajtók vannak, a parancs eredménye a következőhöz hasonló:

```console
SmartPools Health
Name                  Health  Type Prot   Members          Down          Smartfailed
--------------------- ------- ---- ------ ---------------- ------------- -------------
a2000_200tb_800gb-    -M---
ssd-sed_16gb
 a2000_200tb_800gb-   -M---   HDD  3x     3,11,14,19,23,30 Nodes:        Nodes:
ssd-sed_16gb:24                           ,38,41,46:bay6,1 Drives:       Drives:
                                          0-11,16,19,
                                          25:bay6,10-11,16

OK = Ok, U = Too few nodes, M = Missing drives,
D = Some nodes or drives are down, S = Some nodes or drives are smartfailed,
R = Some nodes or drives need repair
Unprovisioned drives: none
```

A problémákkal kapcsolatos segítségért forduljon Microsoft ügyfélszolgálatahoz.

A következő parancs egy egyszerűsített nézetben ellenőrzi a fürt általános állapotát:
```console
isi status
```

Ha minden kifogástalan állapotú, egy zöld **OK** jelenik meg, ellenkező esetben a sárga figyelmeztetések vagy a vörös hibák a **fürt állapota** sorban vagy a következő táblázatban szereplő fürtcsomópont-azonosító sorokon jelennek meg.

Ha további információkra van szükség a fürt állapotáról, részletesebb parancs futtatható a kibővített nézetben:
```console
isi status -a
```

A problémákkal kapcsolatos segítségért forduljon Microsoft ügyfélszolgálatahoz.

## <a name="azure-stack-hub-health"></a>Azure Stack hub állapota

Ez a szakasz útmutatást nyújt az Azure Stack hub telepítésének állapotának ellenőrzéséhez.

A kiterjesztett tároló rendszerbe integrált Azure Stack központi telepítés általános áttekintéséhez futtassa a következő parancsfájlt, amely a következő: egy burkoló a következőkhöz:
- Kapcsolódás ERCS virtuális géphez a megadott hitelesítő adatok használatával
- A Test-AzureStack-debug parancs végrehajtása (amely a képernyőn megjelenő kimenetként közvetlenül adja meg a részletes állapotadatok adatait)

A szkript előfeltételei a következők:
- .\Invoke-ExtendedStorageConfiguration.ps1 parancsfájlt, amely a hardver életciklus-gazdagép (HLH) C:\OEMSoftware\ExtendedStorage\ mappájában található.
- $AzScred hitelesítő adat változó, amelyet fel kell tölteni a *tartományi*\cloudadmin hitelesítő adataival. Cserélje le a *tartományt* a tényleges tartománynévre, például a contoso névre.


```powershell
$AzScred = Get-Credential -Credential 'DOMAIN\cloudadmin'
.\Invoke-ExtendedStorageConfiguration.ps1 -TestAzureStack -AzureStackCred $AzScred
```

Tekintse át a kimenetet, és ellenőrizze a Azure Stack üzemelő példány általános állapotának állapotát, valamint az eredményeknek a **NAS** szolgáltatással való integrációjának adott állapotára vonatkozó szakaszait Azure Stackával.

A Azure Stack diagnosztikát részletesebben tekintse meg a [Azure stack hub rendszerállapotának ellenőrzése](../operator/azure-stack-diagnostic-test.md)című témakört.

## <a name="technical-support"></a>Technikai támogatás

A problémákkal kapcsolatos segítségért forduljon Microsoft ügyfélszolgálatahoz.

## <a name="next-steps"></a>Következő lépések

- [Belső vezérlőprogram frissítése](extended-storage-firmware-updates.md)
