---
title: Kötetek törlése Azure Stack HCI-ben
description: Kötetek törlése Azure Stack HCI-ben a Windows felügyeleti központ és a PowerShell használatával.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 7a90948cccb75bb8bb1578101d530c46852e99d6
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866522"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Kötetek törlése Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör útmutatást nyújt a kötetek Azure Stack HCI-fürtön való törléséhez a Windows felügyeleti központ használatával.

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

- [Kötetek megtervezése](../concepts/plan-volumes.md)
- [Kötetek létrehozása](create-volumes.md)
- [Kötetek kiterjesztése](extend-volumes.md)