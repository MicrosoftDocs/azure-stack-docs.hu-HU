---
title: Skálázásiegység-csomópontokkal kapcsolatos műveletek az Azure Stack Hubban
description: Ismerje meg a skálázási egység csomópontjainak műveleteit, beleértve a bekapcsolás, a kikapcsolás, a letiltás, a folytatás és a csomópontok állapotának megtekintését Azure Stack hub integrált rendszerekben.
author: PatAltimore
ms.topic: how-to
ms.date: 1/19/2021
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 1/19/2021
ms.openlocfilehash: 67f381a50d1b98361207a00a300921a636679326
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534164"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub"></a>Skálázásiegység-csomópontokkal kapcsolatos műveletek az Azure Stack Hubban

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

   - Az egyes csomópontok listája.
   - Működési állapot (lásd az alábbi listát).
   - Energiaellátás állapota (fut vagy leállítva).
   - Kiszolgálói modell.
   - A alaplapi felügyeleti vezérlő (BMC) IP-címe.
   - Magok száma összesen
   - A memória teljes mennyisége.
   
    A csomópont-műveletek a várt riasztásokat is növelhetik a felügyeleti portálon. 

![méretezési egység állapota](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Csomópont működési állapotai

| Állapot | Leírás |
|----------------------|-------------------------------------------------------------------|
| Futó | A csomópont aktívan részt vesz a skálázási egységben. |
| Leállítva | A csomópont nem érhető el. |
| Hozzáadása | A csomópontot aktívan felveszik a méretezési egységbe. |
| Javítása | A csomópont aktívan javítás alatt áll. |
| Karbantartás | A csomópont szüneteltetve van, és nem fut aktív felhasználói munkaterhelés. |
| Szervizelést igényel | Hiba észlelhető, amely megköveteli a csomópont javítását. |

### <a name="azure-stack-hub-shows-adding-status-after-an-operation"></a>Azure Stack hub a művelet utáni állapot hozzáadását mutatja

Azure Stack hub az operatív csomópont állapotát egy művelet, **például a** kiürítés, a folytatás, a javítás, a Leállítás vagy az indítás végrehajtása után jeleníti meg.
Ez akkor fordulhat elő, ha a háló erőforrás-szolgáltatói szerepkör-gyorsítótára nem frissült egy művelet után. 

A következő lépések alkalmazása előtt győződjön meg arról, hogy jelenleg nincs folyamatban a művelet. Frissítse a végpontot a környezetének megfelelően.

### <a name="az-modules"></a>[Az modulok](#tab/az1)

1. Nyissa meg a PowerShellt, és adja hozzá Azure Stack hub-környezetét. Ehhez [Azure stack hub PowerShell-t kell telepíteni](./powershell-install-az-module.md) a számítógépre.

    ```powershell
    Add-AzEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Connect-AzAccount -Environment AzureStack
    ```

2. Futtassa a következő parancsot a háló erőforrás-szolgáltatói szerepkörének újraindításához.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Ellenőrizze, hogy az érintett méretezési egység csomópontjának működési állapota a **futtatásra** módosult-e. Használhatja a felügyeleti portált vagy a következő PowerShell-parancsot:

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. Ha a csomópont működési állapota továbbra **is megjelenik, mert a** tovább gombra kattintva nyisson meg egy támogatási eseményt.

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm1)

1. Nyissa meg a PowerShellt, és adja hozzá Azure Stack hub-környezetét. Ehhez [Azure stack hub PowerShell-t kell telepíteni](./powershell-install-az-module.md) a számítógépre.

    ```powershell
    Add-AzureRMEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Add-AzureRMAccount -Environment AzureStack
    ```

2. Futtassa a következő parancsot a háló erőforrás-szolgáltatói szerepkörének újraindításához.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Ellenőrizze, hogy az érintett méretezési egység csomópontjának működési állapota a **futtatásra** módosult-e. Használhatja a felügyeleti portált vagy a következő PowerShell-parancsot:

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. Ha a csomópont működési állapota továbbra **is megjelenik, mert a** tovább gombra kattintva nyisson meg egy támogatási eseményt.

---



## <a name="scale-unit-node-actions"></a>Méretezési egység csomópontjainak műveletei

Ha a méretezési egység csomópontjának adatait tekinti meg, olyan csomópont-műveleteket is végrehajthat, mint például:

 - Indítás és Leállítás (az aktuális energiaellátási állapottól függően).
 - Letiltás és folytatás (a műveleti állapottól függően).
 - Javítás.
 - Shutdown.

A csomópont működési állapota határozza meg, hogy mely lehetőségek érhetők el.

Telepítenie kell Azure Stack hub PowerShell-modulokat. Ezek a parancsmagok a **AZS. Fabric. admin** modulban találhatók. Az Azure Stack hub PowerShell-telepítésének telepítéséhez vagy ellenőrzéséhez tekintse meg a [PowerShell telepítése Azure stack hub](powershell-install-az-module.md)-hoz című témakört.

## <a name="stop"></a>Leállítás

A **Leállítás** művelet kikapcsolja a csomópontot. Ez ugyanaz, mint a főkapcsoló gomb megnyomása. Nem küld leállítási jelet az operációs rendszernek. A tervezett leállítási műveletekhez először mindig próbálja meg a leállítási műveletet.

Ezt a műveletet általában akkor kell használni, ha egy csomópont már nem válaszol a kérelmekre.

A Leállítás művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Ha nem valószínű, hogy a leállítási művelet nem működik, próbálja megismételni a műveletet, és ha másodszor is használja a BMC webes felületet, akkor próbálkozzon újra.

További információ: [stop-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Indítás

Az **indítási** művelet bekapcsolja a csomópontot. Ugyanaz, mint a főkapcsoló gomb megnyomásakor.

Az indítási művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Ha nem valószínű, hogy az indítási művelet nem működik, próbálja megismételni a műveletet. Ha a művelet második alkalommal meghiúsul, használja helyette a BMC webes felületét.

További információ: [Start-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Kiürítési

A **Drain** művelet az összes aktív munkaterhelést áthelyezi az adott skálázási egységben található többi csomópontra.

Ez a művelet jellemzően a részek kicserélése során használatos, például egy teljes csomópont pótlására.

> [!Important]
> Győződjön meg arról, hogy a tervezett karbantartási időszak során egy csomóponton Drain műveletet használ, ahol a felhasználók értesítést kaptak. Bizonyos körülmények között az aktív munkaterhelések megszakadnak.

A kiürítési művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

További információ: [disable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Folytatás

A **Folytatás** művelet folytatja a letiltott csomópontot, és jelzi, hogy az aktív a munkaterhelés elhelyezéséhez. A csomóponton futó korábbi munkaterhelések nem működnek vissza. (Ha egy csomóponton egy Drain műveletet használ, a kikapcsolás nem szükséges. Ha a csomópontot visszakapcsolja, akkor az nem aktívként van megjelölve a munkaterhelés elhelyezéséhez. Ha elkészült, a folytatás műveletet kell használnia a csomópont aktívként való megjelöléséhez.)

A folytatás művelet futtatásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

További információ: [enable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Javítás

> [!CAUTION]  
> A belső vezérlőprogram-simítás kritikus fontosságú a cikkben ismertetett művelet sikeressége szempontjából. Ha ez a lépés hiányzik, a rendszer instabillá válhat, ami csökkenti a teljesítményt, a biztonsági fenyegetéseket vagy a hibát, amikor Azure Stack hub Automation üzembe helyezi az operációs rendszert. A hardver cseréjekor mindig tekintse meg a hardveres partner dokumentációját, és győződjön meg arról, hogy az alkalmazott belső vezérlőprogram megfelel az [Azure stack hub felügyeleti portálján](azure-stack-updates.md)megjelenő OEM-verziónak.<br><br>
További információt és a partneri dokumentációra mutató hivatkozásokat a [hardver-összetevők cseréje](azure-stack-replace-component.md)című témakörben talál.

| Hardveres partner | Region | URL-cím |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Mind | [Cisco integrált rendszer Microsoft Azure Stack hub üzemeltetési útmutatóhoz](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Kibocsátási megjegyzések a Microsoft Azure Stack hub-hoz készült Cisco integrált rendszerhez](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Mind | [Cloud for Microsoft Azure Stack hub 14G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud for Microsoft Azure Stack hub 13G (fiók és bejelentkezés szükséges)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Japán | [A Fujitsu felügyelt szolgáltatás támogatási szolgálata (fiók és bejelentkezés szükséges)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [A Fujitsu informatikai termékek és rendszerek támogatása](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [Fujitsu MySupport (fiók és bejelentkezés szükséges)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Mind | [Microsoft Azure Stack hub HPE-ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | Mind | [ThinkAgile SXM – legjobb receptek](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

A **javítási** művelet kijavítja a csomópontot. Csak a következő esetekben használható:

- Teljes csomópont cseréje (új adatlemezekkel vagy anélkül).
- A hardver-összetevő meghibásodása és cseréje után (ha a mező a cserélhető egység [a következő) dokumentációjában szerepel.

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

## <a name="next-steps"></a>Következő lépések

- [Az Azure Stack PowerShell telepítése](./powershell-install-az-module.md)
- [Tudnivalók a Azure Stack hub Fabric-kezelő modulról](/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0&preserve-view=true)
- [Csomópont-hozzáadási műveletek figyelése](./azure-stack-add-scale-node.md#monitor-add-node-operations)
