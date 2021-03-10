---
title: Kötetek törlése Azure Stack HCI-ben és a Windows Serverben
description: Kötetek törlése Azure Stack HCI-ben és Windows Serveren a Windows felügyeleti központtal és a PowerShell-lel.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: fdc7fd4ae102871d534d846dd653af077a75d49a
ms.sourcegitcommit: 02a4c34fb829e053016912a4fffcc51e32685425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102532484"
---
# <a name="deleting-volumes-in-azure-stack-hci-and-windows-server"></a>Kötetek törlése Azure Stack HCI-ben és a Windows Serverben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör a kötetek Windows felügyeleti központtal vagy a PowerShell használatával történő törlésére vonatkozó utasításokat tartalmazza.

## <a name="delete-volumes-with-windows-admin-center"></a>Kötetek törlése a Windows felügyeleti központtal

1. A Windows felügyeleti központban kapcsolódjon egy fürthöz, majd válassza a bal oldali **eszközök** panel **kötetek** elemét.
2. A **kötetek** lapon válassza a **leltár** fület, majd válassza ki a törölni kívánt kötetet.
3. A kötetek részletei lap tetején válassza a **Törlés** lehetőséget.
4. A megerősítések párbeszédpanelen jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy törölni kívánja a kötetet, majd válassza a **Törlés** lehetőséget.

   :::image type="content" source="media/delete-volumes/delete-volume.png" alt-text="Válassza ki a törölni kívánt kötetet, válassza a Törlés lehetőséget, majd erősítse meg, hogy törölni kívánja a köteten lévő összes adatát." lightbox="media/delete-volumes/delete-volume.png":::

## <a name="delete-volumes-with-powershell"></a>Kötetek törlése a PowerShell-lel

A **Remove-VirtualDisk** parancsmag használatával törölje a **VirtualDisk** objektumot, és küldje vissza a **VirtualDisk** -objektumot kihelyező tárolóhoz használt területet.

Először indítsa el a PowerShellt a felügyeleti számítógépen, és futtassa a **Get-VirtualDisk** parancsmagot a **CimSession** paraméterrel, amely a fürt vagy a kiszolgáló csomópontjának neve, például *clustername.contoso.com*:

```PowerShell
Get-VirtualDisk -CimSession clustername.contoso.com
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