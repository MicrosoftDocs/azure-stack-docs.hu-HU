---
title: Skálázási egység lemezének állapotának ellenőrzése
description: Útmutató a méretezési egység Node lemezének állapotának ellenőrzéséhez
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: bebff3ff8970a43d0893f057a94ec3dfbf0a7fc9
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391006"
---
# <a name="verifying-scale-unit-node-disk-health"></a>A skálázási egység lemezének állapotának ellenőrzése

1.  Kapcsolódjon a privilegizált végponthoz (PEP).

    1.  A PEP-hez való kapcsolódásra vonatkozó utasításokért tekintse meg a Privileged Access munkaállomás és a privilegizált végpontok hozzáférését ismertető témakört.

    1.  Csatlakozás után adja meg a PEP-munkamenetet `Enter-PSSession -Session $pep` .

2.  A virtuális lemez állapotának beolvasása.

    1.  Futtassa `Get-VirtualDisk -cimsession "S-Cluster"` a parancsot a virtuális lemez állapotának ellenőrzéséhez.

        Ha a rendszer nem ad vissza **OperationalStatus** és **órákban kifejezve** **, akkor** várjon néhány **percet, majd** futtassa újra a parancsot.
        
        ![](media/image-57.png)
        
    1.  A futtatásával `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` ellenőrizze, hogy az összes futó tárolási feladat befejeződött-e.
    
    1.  Győződjön meg arról, hogy a rendszer nem ad vissza eredményt. Ha a feladatok még futnak, ahogy azt a **JobState** is mutatja, vagy az összes feladat befejezettként van megjelölve, akkor várjon egy újabb 10 percet, majd futtassa újra ugyanezt a parancsot. A végső állapotnak a felsorolt feladatok egyikének kell lennie.
    
    1.  Szükség esetén további tárolási állapot-ellenőrzési lépések találhatók a [virtuális lemezes javítás állapotának ellenőrzése Azure stack hub PowerShell használatával](https://docs.microsoft.com/azure-stack/operator/azure-stack-replace-disk?view=azs-2002&check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
