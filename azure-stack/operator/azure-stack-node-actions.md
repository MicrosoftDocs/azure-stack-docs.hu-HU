---
title: Méretezési egység csomópontjának műveletei Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan tekintheti meg a csomópontok állapotát, és hogyan használhatja a csomópont-műveleteket a Azure Stack integrált rendszeren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: b75d9f37e2b2deacb5935bb6cda3c2586afb8baa
ms.sourcegitcommit: 314fd74caf356b157583d38d2b8b1dee30408b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70235014"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Méretezési egység csomópontjának műveletei Azure Stack

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk azt ismerteti, hogyan lehet megtekinteni egy méretezési egység állapotát. Megtekintheti az egység csomópontjait. Futtathat olyan csomópont-műveleteket, mint például a bekapcsolás, a kikapcsolás, a Leállítás, a kiürítés, a folytatás és a javítás. Ezeket a csomópontokat jellemzően a részek cseréjekor, vagy a csomópontok helyreállításához használhatja.

> [!Important]  
> A cikkben ismertetett összes csomópont-műveletnek egyszerre egy csomópontot kell megcéloznia.

## <a name="view-the-node-status"></a>A csomópont állapotának megtekintése

A felügyeleti portálon megtekintheti a méretezési egység és a hozzá tartozó csomópontok állapotát.

Egy adott skálázási egység állapotának megtekintéséhez:

1. A **régió-kezelés** csempén válassza ki a régiót.
2. A bal oldalon az **infrastruktúra-erőforrások**területen válassza a **méretezési egységek**elemet.
3. Az eredmények között válassza ki a méretezési egységet.
4. A bal oldalon az **általános**területen válassza a **csomópontok**lehetőséget.

   Tekintse meg a következő információkat:

   - Az egyes csomópontok listája
   - Működési állapot (lásd az alábbi listát)
   - Energiaellátás állapota (fut vagy leállítva)
   - Kiszolgálói modell
   - A alaplapi felügyeleti vezérlő (BMC) IP-címe
   - Magok száma összesen
   - Memória teljes mennyisége

![méretezési egység állapota](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Csomópont működési állapotai

| State | Leírás |
|----------------------|-------------------------------------------------------------------|
| Fut | A csomópont aktívan részt vesz a skálázási egységben. |
| Leállítva | A csomópont nem érhető el. |
| Hozzáadás | A csomópontot aktívan felveszik a méretezési egységbe. |
| Javítás | A csomópont aktívan javítás alatt áll. |
| Karbantartás | A csomópont szüneteltetve van, és nem fut aktív felhasználói munkaterhelés. |
| Szervizelést igényel | Hiba észlelhető, amely megköveteli a csomópont javítását. |

## <a name="scale-unit-node-actions"></a>Méretezési egység csomópontjainak műveletei

Ha a méretezési egység csomópontjának adatait tekinti meg, olyan csomópont-műveleteket is végrehajthat, mint például a következők:
 - Indítás és Leállítás (az aktuális energiaellátási állapottól függően)
 - Letiltás és folytatás (a műveletek állapotától függően)
 - Javítás
 - Leállítás

A csomópont működési állapota határozza meg, hogy mely lehetőségek érhetők el.

Azure Stack PowerShell-modulokat kell telepítenie. Ezek a parancsmagok a **AZS. Fabric. admin** modulban találhatók. Az Azure Stack PowerShell telepítésének telepítéséhez vagy ellenőrzéséhez tekintse meg a [PowerShell telepítése Azure Stackhoz](azure-stack-powershell-install.md)című témakört.

## <a name="stop"></a>Leállítás

A **Leállítás** művelet kikapcsolja a csomópontot. Ugyanaz, mint a főkapcsoló gomb megnyomásakor. Nem küld leállítási jelet az operációs rendszernek. A tervezett leállítási műveletekhez először mindig próbálja meg a leállítási műveletet. 

Ezt a műveletet általában akkor kell használni, ha egy csomópont lefagyott állapotban van, és a továbbiakban nem válaszol a kérelmekre.

A Leállítás művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Ha nem valószínű, hogy a leállítási művelet nem működik, próbálja megismételni a műveletet, és ha másodszor is használja a BMC webes felületet, akkor próbálkozzon újra.

További információ: [stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Start

Az **indítási** művelet bekapcsolja a csomópontot. Ugyanaz, mint a főkapcsoló gomb megnyomásakor. 
 
Az indítási művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Ha nem valószínű, hogy az indítási művelet nem működik, próbálja megismételni a műveletet, és ha a művelet második alkalommal is sikertelen, használja a BMC webes felületet.

További információ: [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Kiürítési

A **Drain** művelet az összes aktív munkaterhelést áthelyezi az adott skálázási egységben található többi csomópontra.

Ez a művelet jellemzően a részek cseréjekor használatos, például egy teljes csomópont cseréjekor.

> [!Important]
> Győződjön meg arról, hogy a tervezett karbantartási időszak során egy csomóponton Drain műveletet használ, ahol a felhasználók értesítést kaptak. Bizonyos körülmények között az aktív munkaterhelések megszakadnak.

A kiürítési művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

További információ: [disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Folytatás

A **Folytatás** művelet folytatja a letiltott csomópontot, és jelzi, hogy az aktív a munkaterhelés elhelyezéséhez. A csomóponton futó korábbi munkaterhelések nem tesznek eleget a feladat-visszavételen. (Ha egy csomóponton egy Drain műveletet használ, a kikapcsolás nem szükséges. Ha a csomópontot visszakapcsolja a szolgáltatásba, az nem aktívként van megjelölve a munkaterhelés elhelyezéséhez. Ha elkészült, a folytatás műveletet kell használnia a csomópont aktívként való megjelöléséhez.)

A folytatás művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

További információ: [enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Javítás

> [!CAUTION]  
> A belső vezérlőprogram-simítás kritikus fontosságú a cikkben ismertetett művelet sikeressége szempontjából. Ez a lépés hiányzik a rendszer instabillá, a teljesítmény csökkenése, a biztonsági szálak vagy a Azure Stack Automation az operációs rendszer központi telepítésének megakadályozása érdekében. Mindig tekintse meg a hardveres partner dokumentációját, amikor lecseréli a hardvert, hogy az alkalmazott belső vezérlőprogram megfelel a [Azure stack felügyeleti portálon](azure-stack-updates.md)megjelenő OEM-verziónak.<br>
További információt és a partneri dokumentációra mutató hivatkozásokat a [hardver-összetevők cseréje](azure-stack-replace-component.md)című témakörben talál.

| Hardveres partner | Régió | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Összes | [A Cisco integrált rendszer Microsoft Azure Stack üzemeltetési útmutatóhoz](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Kibocsátási megjegyzések a Microsoft Azure Stack Cisco integrált rendszeréhez](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Összes | [Felhő Microsoft Azure Stack 14G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Felhő Microsoft Azure Stack 13G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPÁN | [A Fujitsu felügyelt szolgáltatás támogatási szolgálata (fiók és bejelentkezés szükséges)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EURÓPA, KÖZEL-KELET ÉS AFRIKA | [A Fujitsu informatikai termékek és rendszerek támogatása](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [Fujitsu MySupport (fiók és bejelentkezés szükséges)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Összes | [Microsoft Azure Stack HPE ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | Összes | [ThinkAgile SXM – legjobb receptek](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

A **javítási** művelet kijavítja a csomópontot. Csak a következő esetekben használható:
 - Teljes csomópont cseréje (új adatlemezekkel vagy anélkül)
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

A leállított csomópont elindítása után futtatnia kell a [Folytatás](#resume) műveletet. A csomóponton futó korábbi munkaterhelések nem tesznek eleget a feladat-visszavételen.

Ha a leállítási művelet meghiúsul, próbálja megpróbálkozni a [kiürítési](#drain) művelettel, majd a leállítási művelettel.

A leállítási művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Stack Fabric felügyeleti modulról, tekintse meg a következőt: [AZS. Fabric. admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
