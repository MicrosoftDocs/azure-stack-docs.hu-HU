---
title: Az egység csomópont-műveleteinek méretezése Azure Stack hub-ban egy MDC
description: Megtudhatja, hogyan méretezheti az egység csomópontok műveleteit, beleértve a bekapcsolt, a kikapcsolást, a letiltást, a folytatást és a csomópontok állapotát Azure Stack hub integrált rendszerekben
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/26/2020
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 6413cec9b2464d5295feab0f3a3983083b9d04ea
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598521"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub---modular-data-center-mdc"></a>Skálázási egység csomópontjainak műveletei Azure Stack hub-moduláris adatközpontban (MDC)

Ez a cikk azt ismerteti, hogyan lehet megtekinteni egy méretezési egység állapotát. Megtekintheti az egység csomópontjait. Futtathat olyan csomópont-műveleteket, mint a bekapcsolás, a kikapcsolás, a Leállítás, a kiürítés, a folytatás és a javítás. Ezeket a csomópontokat jellemzően a részek cseréjekor, vagy a csomópontok helyreállításához használhatja.

> [!Important]  
> A cikkben ismertetett összes csomópont-műveletnek egyszerre egy csomópontot kell megcéloznia.

## <a name="view-the-node-status"></a>A csomópont állapotának megtekintése

A felügyeleti portálon megtekintheti a méretezési egység és a hozzá tartozó csomópontok állapotát.

Egy adott skálázási egység állapotának megtekintéséhez:

1. A **régió-kezelés** csempén válassza ki a régiót.
2. A bal oldalon az **infrastruktúra-erőforrások** területen válassza a **méretezési egységek** elemet.
3. Az eredmények között válassza ki a méretezési egységet.
4. A bal oldalon az **általános** területen válassza a **csomópontok** lehetőséget.

   Tekintse meg a következő információkat:

   - Az egyes csomópontok listája
   - Működési állapot (lásd az alábbi listát)
   - Energiaellátási állapot (fut vagy leállítva)
   - Kiszolgálói modell
   - A alaplapi felügyeleti vezérlő (BMC) IP-címe
   - Magok száma összesen
   - Memória teljes mennyisége

     ![méretezési egység állapota](media/azure-stack-node-actions/multinode-actions.png)

### <a name="node-operational-states"></a>Csomópont működési állapotai

| Állapot | Leírás |
|----------------------|-------------------------------------------------------------------|
| Futó | A csomópont aktívan részt vesz a skálázási egységben. |
| Leállítva | A csomópont nem érhető el. |
| Hozzáadása | A csomópontot aktívan felveszik a méretezési egységbe. |
| Javítása | A csomópont aktívan javítás alatt áll. |
| Karbantartás | A csomópont szüneteltetve van, és nem fut aktív felhasználói munkaterhelés. |
| Szervizelést igényel | Hiba észlelhető, amely megköveteli a csomópont javítását. |

## <a name="scale-unit-node-actions"></a>Méretezési egység csomópontjainak műveletei

Ha a méretezési egység csomópontjának adatait tekinti meg, olyan csomópont-műveleteket is végrehajthat, mint például:

 - Indítás és Leállítás (az aktuális energiaellátási állapottól függően).
 - Letiltás és folytatás (a műveleti állapottól függően).
 - Javítás.
 - Shutdown.

A csomópont működési állapota határozza meg, hogy mely lehetőségek érhetők el.

Telepítenie kell Azure Stack hub PowerShell-modulokat. Ezek a parancsmagok a **AZS. Fabric. admin** modulban találhatók. Az Azure Stack hub PowerShell-telepítésének telepítéséhez vagy ellenőrzéséhez tekintse meg a [PowerShell telepítése Azure stack hub](../../operator/powershell-install-az-module.md)-hoz című témakört.

## <a name="stop"></a>Leállítás

A **Leállítás** művelet kikapcsolja a csomópontot. Ez ugyanaz, mint a főkapcsoló gomb megnyomása. Nem küld leállítási jelet az operációs rendszernek. A tervezett leállítási műveletekhez először mindig próbálja meg a leállítási műveletet.

Ezt a műveletet általában akkor kell használni, ha egy csomópont nem válaszoló állapotban van.

A Leállítás művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Ha nem valószínű, hogy a leállítási művelet nem működik, próbálja megismételni a műveletet, és ha másodszor is használja a BMC webes felületet, akkor próbálkozzon újra.

További információ: [stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Indítás

Az **indítási** művelet bekapcsolja a csomópontot. Ugyanaz, mint a főkapcsoló gomb megnyomásakor.

Az indítási művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Ha nem valószínű, hogy az indítási művelet nem működik, próbálja megismételni a műveletet. Ha a művelet második alkalommal meghiúsul, használja helyette a BMC webes felületét.

További információ: [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Kiürítési

A **Drain** művelet az összes aktív munkaterhelést áthelyezi az adott skálázási egységben található többi csomópontra.

Ez a művelet jellemzően a részek kicserélése során használatos, például egy teljes csomópont pótlására.

> [!Important]
> Győződjön meg arról, hogy a tervezett karbantartási időszak során egy csomóponton Drain műveletet használ, ahol a felhasználók értesítést kaptak. Bizonyos körülmények között az aktív munkaterhelések megszakadnak.

A kiürítési művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

További információ: [disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Folytatás

A **Folytatás** művelet folytatja a letiltott csomópontot, és jelzi, hogy az aktív a munkaterhelés elhelyezéséhez. A csomóponton futó korábbi munkaterhelések nem működnek vissza. (Ha egy csomóponton egy Drain műveletet használ, a kikapcsolás nem szükséges. Ha a csomópontot visszakapcsolja, akkor az nem aktívként van megjelölve a munkaterhelés elhelyezéséhez. Ha elkészült, a folytatás műveletet kell használnia a csomópont aktívként való megjelöléséhez.)

A folytatás művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

További információ: [enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Javítás

A **javítási** művelet kijavítja a csomópontot. Csak a következő esetekben használható:

- Teljes csomópont cseréje (új adatlemezekkel vagy anélkül).
- A hardver-összetevő meghibásodása és cseréje után (ha a mező a cserélhető egységre vonatkozó dokumentációban szerepel).

> [!Important]  
> A csomópontok vagy az egyes hardver-összetevők cseréjének pontos lépéseiért tekintse meg az OEM hardver gyártójának cserélhető szoftveres dokumentációját. A cserélhető eszköz dokumentációja határozza meg, hogy szükséges-e a javítási művelet futtatása a hardveres összetevők cseréje után.

A javítási művelet futtatásakor meg kell adnia a BMC IP-címét.

A javítási művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Leállítás

A **leállítási** művelet először az összes aktív munkaterhelést áthelyezi a többi csomópontra ugyanazon a skálázási egységen belül. Ezután a művelet szabályosan leállítja a méretezési egység csomópontot.

A leállított csomópont elindítása után futtatnia kell a [Folytatás](#resume) műveletet. A csomóponton futó korábbi munkaterhelések nem működnek vissza.

Ha a leállítási művelet meghiúsul, próbálja megpróbálkozni a [kiürítési](#drain) művelettel, majd a leállítási művelettel.

A leállítási művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```

## <a name="next-steps"></a>További lépések

Tudnivalók [a Azure stack hub Fabric-kezelő modulról](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).