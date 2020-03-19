---
title: Kötetek törlése Azure Stack HCI-ben
description: Kötetek törlése Azure Stack HCI-ben a Windows felügyeleti központ és a PowerShell használatával.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/17/2020
ms.openlocfilehash: cf556a9b6c130907e8607d8e5b9436b71756a3d4
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511895"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Kötetek törlése Azure Stack HCI-ben

> A következőkre vonatkozik: Windows Server 2019

Ez a témakör útmutatást nyújt a kötetek [közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) -fürtön való törléséhez a Windows felügyeleti központon keresztül.

Tekintse meg a Windows felügyeleti központot használó kötetek törlésének rövid videóját.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Kötet törlése a Windows felügyeleti központban

1. A Windows felügyeleti központban kapcsolódjon egy Közvetlen tárolóhelyek fürthöz, majd válassza a **kötetek** elemet az **eszközök** ablaktáblán.
2. A **kötetek** lapon válassza a **leltár** fület, majd válassza ki a törölni kívánt kötetet.
3. A kötetek részletei lap tetején válassza a **Törlés**lehetőséget.
4. A megerősítések párbeszédpanelen jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy törölni kívánja a kötetet, majd válassza a **Törlés**lehetőséget.

## <a name="delete-volumes-using-powershell"></a>Kötetek törlése a PowerShell használatával

A **Remove-VirtualDisk** parancsmag használatával törölheti közvetlen tárolóhelyek köteteit. Ezzel a parancsmaggal törölheti a **VirtualDisk** objektumot, és visszaküldheti a **VirtualDisk** objektumot közzétevő tárolóhoz használt területet.

Először indítsa el a PowerShellt a felügyeleti számítógépen, és futtassa a **Get-VirtualDisk** parancsmagot a **CimSession** paraméterrel, amely egy közvetlen tárolóhelyek-fürt vagy-kiszolgáló csomópontjának neve, például *clustername.microsoft.com*: 

```PowerShell
Get-VirtualDisk -CimSession clustername.microsoft.com
```

Ez a függvény a **-FriendlyName** paraméter lehetséges értékeinek listáját adja vissza, amelyek a fürtben lévő kötetek neveinek felelnek meg.

### <a name="example"></a>Példa

A *Volume1* nevű tükrözött kötet törléséhez futtassa a következő parancsot a PowerShellben:

```PowerShell
Remove-VirtualDisk -FriendlyName "Volume1"
```

A rendszer felszólítja, hogy erősítse meg, hogy végre szeretné hajtani a műveletet, és törli az összes olyan adatát, amelyet a kötet tartalmaz. Válassza az Y vagy az N lehetőséget.

   > [!WARNING]
   > Ez nem helyreállítható művelet. Ez a példa véglegesen töröl egy **VirtualDisk** kötet objektumot.

## <a name="next-steps"></a>Következő lépések

A további alapvető tárolási felügyeleti feladatokkal kapcsolatos részletes útmutatásért lásd még:

- [Kötetek tervezése Közvetlen tárolóhelyek](../concepts/plan-volumes.md)
- [Kötetek létrehozása a Közvetlen tárolóhelyekban](create-volumes.md)
- [Kötetek kiterjesztése a Közvetlen tárolóhelyekban](extend-volumes.md)