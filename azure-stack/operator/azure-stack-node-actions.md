---
title: Csomópont kapcsolatos műveletek méretezése az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, megtekintheti a csomópont aktuális állapotát, és használja a a power kikapcsolása, letiltása és csomópont műveleteket az Azure Stackkel integrált rendszerek folytatása.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 01/22/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: ppacent
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: cd7e66961a0b9a80150a3d3e132efd29485cdb66
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293255"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Skálázási egység csomópont műveletek az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a skálázási egység állapotának megtekintése. Az egység csomópontok tekintheti meg. Is futtatásakor csomópont műveleteket, például power a kapacitást, állítsa le, kiürítési, folytatása, és javítása. A csomópont műveletek általában részből áll, vagy egy csomópont helyreállítását segíti a mező helyettesítő során használ.

> [!Important]  
> Ebben a cikkben ismertetett összes csomópont művelet egy csomópont kell célozhat meg egyszerre.

## <a name="view-the-node-status"></a>A csomópont állapotának megtekintése

A felügyeleti portálon megtekintheti a méretezési egység és a kapcsolódó csomópontjainak állapotát.

Egy adott skálázási egység állapotának megtekintéséhez:

1. Az a **régiók kezelése** csempére, válassza ki a régiót.
2. A bal oldali alatt **infrastruktúra-erőforrások**válassza **egységig**.
3. Az eredmények közül válassza ki a skálázási egység.
4. A bal oldali alatt **általános**válassza **csomópontok**.

   A következő információk megtekintése:

   - Az egyes csomópontok listája
   - Működési állapot (lásd alább)
   - Kiemelt állapotát (fut vagy Leállítva)
   - kiszolgáló-modell
   - Az alaplapi felügyeleti vezérlőnek (BMC) IP-címe
   - Magok teljes száma
   - teljes memóriamennyiség

![a skálázási egység állapota](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Csomópont működési állapotok

| status | Leírás |
|----------------------|-------------------------------------------------------------------|
| Fut | A csomópont tevékenyen részt vesz a skálázási egység. |
| Leállítva | A csomópont nem érhető el. |
| Hozzáadás | A csomópont aktívan ad hozzá a skálázási egység. |
| Javítás | A csomópont aktívan javítása folyamatban van. |
| Karbantartás | A csomópont fel van függesztve, és nincsenek aktív felhasználói munkaterhelés fut-e. |
| Szervizelés szükséges | Hibát észlelt, amely megköveteli, hogy a csomópont nem működik helyesen. |

## <a name="scale-unit-node-actions"></a>Skálázási egység csomópont műveletek

Megtekintheti a skálázási egység csomópont kapcsolatos információkat, például is elvégezheti csomópont műveletek:
 - Indítás és leállítás (függően a jelenlegi power állapot)
 - Tiltsa le, és folytatni (függően műveletek állapota)
 - Javítás
 - Leállítás

A csomópont a működési állapota határozza meg, milyen beállítások érhetők el.

Azure Stack PowerShell-modulok telepítenie kell. A rendszer ezeket a parancsmagokat a **Azs.Fabric.Admin** modul. Telepítse, vagy az Azure Stack PowerShell telepítésének ellenőrzésének, lásd: [Azure Stack PowerShell telepítése](azure-stack-powershell-install.md).

## <a name="stop"></a>Leállítás

A **leállítása** művelet kikapcsolja a csomópontot. Legyen ugyanaz, mintha a főkapcsoló. Az operációs rendszer rendszerleállítási jelzés nem küldi. A tervezett stop műveletek mindig előbb a a leállítási művelet. 

Ez a művelet általában akkor használatos, amikor egy csomópont lefagyott állapotban van, és már nem válaszol a kérelmekre.

A leállítási művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

A valószínűtlen eset, hogy a leállítási művelet nem működik, majd próbálja megismételni a műveletet, és ha a második alkalommal sikertelen lesz inkább a BMC webes felületén.

További információkért lásd: [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Indítás

A **start** művelet bekapcsolja a csomóponton. Legyen ugyanaz, mintha a főkapcsoló. 
 
A kezdő művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

A valószínűtlen eset, hogy a kezdő művelet nem működik, majd próbálja megismételni a műveletet, és ha a második alkalommal sikertelen lesz inkább a BMC webes felületén.

További információkért lásd: [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Kiürítési

A **kiürítési** művelet áthelyezi az összes aktív munkafeladatokat a többi csomópontot, hogy adott skálázási egységben.

Ez a művelet általában a mező helyettesítő részből áll, például egy egész csomóponttal cseréjén során használatos.

> [!Important]
> Győződjön meg arról, hogy használja-e egy csomópont kiürítési művelet tervezett karbantartási időszakban, ahol a felhasználók értesítette. Bizonyos körülmények között az aktív munkafeladatokat tapasztalhatnak megszakítások.

A kiürítési művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

További információkért lásd: [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Folytatás

A **folytatása** művelet folytatja a letiltott csomópontot, és munkaterhelés-elhelyezés aktív jelöli meg. A csomóponton futó korábbi számítási feladatok nem feladat-visszavételt. (Ha használja a kiürítési művelet egy csomóponton mindenképpen általi kikapcsolását. Ha a csomópont újra, nincs ellátva munkaterhelés-elhelyezés aktívként. Ha elkészült, kell használnia a művelet folytatása jelölje meg aktívként a csomópontra.)

A művelet folytatása futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssorba, és futtassa a következő parancsmagot:

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

További információkért lásd: [engedélyezése – AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Javítás

A **javítása** művelet helyreállít egy csomópontot. Használja azt csak az alábbi esetek valamelyike:
 - Teljes csomópont helyettesítő (vagy anélkül új adatlemezek)
 - Miután összetevő hardverhiba, valamint helyettesítő (Ha a mező helyettesítő egységet (FRU) dokumentációjában javasolt).

> [!Important]  
> Pontos lépéseit a OEM hardver szállítójával FRU dokumentációjában talál, amikor szüksége van egy csomópontot vagy egyes hardverösszetevők. A FRU dokumentáció fogja megadni, hogy kell-e a javítási műveletet, hardverkomponensek cseréje után futtassa. 

A javítási művelet futtatásakor adja meg a BMC IP-címet kell. 

Futtasson javítási műveletet, és nyisson meg egy rendszergazda jogú PowerShell-parancssorban futtassa a következő parancsmagot:

  ```PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Leállítás

A **leállítási** művelet fist helyezi át az összes aktív munkafeladatokat a többi csomópontot az azonos skálázási egységben. Majd a művelet szabályosan leállítja a méretezési egység csomópont.

Egy csomópont, amely be lett állítva, elindítása után futtatnia kell a [folytatása](#resume) művelet. A csomóponton futó korábbi számítási feladatok nem feladat-visszavételt.

Ha a leállítási művelet sikertelen, próbálja meg a [kiürítési](#drain) műveletet a leállítási művelet követ.

A leállítási művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

  ```PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>További lépések

Az Azure Stack-Fabric rendszergazdai modul kapcsolatos további információkért lásd: [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
