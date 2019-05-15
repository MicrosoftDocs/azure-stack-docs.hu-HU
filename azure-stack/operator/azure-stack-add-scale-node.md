---
title: Az Azure Stack méretezési csomópontok hozzáadása |} A Microsoft Docs
description: Az Azure Stackben egységig csomópontok hozzáadása.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 7a2ba164c5d35bc1f5b52d3ff68c18b7b3e47dcd
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618933"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Kiegészítő skálázási egység csomópontok hozzáadása az Azure Stackben

Az Azure Stack-operátorok egy további fizikai számítógép hozzáadása egy meglévő méretezési egység teljes kapacitás növelheti. A fizikai számítógép is nevezzük a skálázási egység csomópont. Minden új méretezési egység csomópontot ad hozzá a Processzor típusa, memória, és lemez számát és méretét a csomópontok, amelyek már megvannak a skálázási egységben homogén kell lennie.

A skálázási egység csomópont hozzáadásához járnak el az Azure Stackben, és futtassa a hardvert gyártó (OEM) az eszközök. Az OEM azokat az eszközöket a gazdagépen hardver életciklus (HLH.) Győződjön meg arról, hogy az új fizikai számítógép megegyezik a meglévő csomópontok azonos belső vezérlőprogram szinten fut.

Az alábbi folyamatábrája bemutatja az általános folyamat a skálázási egység csomópont hozzáadása.

![Adja hozzá a skálázási egység folyamat](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *e OEM hardvergyártójához ír elő a fizikai kiszolgáló rack elhelyezése és a belső vezérlőprogram frissítése a támogatási szerződés alapján változik.*

A művelet egy új csomópont hozzáadása is igénybe vehet, néhány óra vagy nap végrehajtásához.

> [!Note]  
> Ne kísérelje meg a következő műveletek bármelyike közben egy Hozzáadás skálázási egység csomópont művelet már folyamatban van:
>
>  - Az Azure Stack frissítése
>  - Tanúsítványok elforgatása
>  - Állítsa le az Azure Stack
>  - Javítás skálázási egység csomópont


## <a name="add-scale-unit-nodes"></a>Skálázási egység csomópontok hozzáadása

A következő lépéseket kell egy csomópont hozzáadása az magas szintű áttekintése. Ne kövesse az alábbi lépéseket az OEM által biztosított kapacitás bővítése dokumentáció első hivatkozó nélkül.

1. Az új fizikai kiszolgálót helyezze az állványra szerelt, és megfelelően kábelezése azt. 
2. Engedélyezze a fizikai kapcsolóhoz, és állítsa be a hozzáférés-vezérlési listák (ACL), ha van ilyen.
3. Konfigurálja a megfelelő IP-címet az alaplapi felügyeleti vezérlőnek (BMC), és az OEM által biztosított dokumentáció minden BIOS-beállítások alkalmazásához.
4. Az aktuális belső vezérlőprogram alapterv alkalmazási minden összetevő számára, amelyek a HLH futnak, a hardver gyártója által biztosított eszközök segítségével.
5. Futtassa az Add csomópont műveletet az Azure Stack felügyeleti portálon.
6. Ellenőrizze, hogy a Hozzáadás csomópont művelet sikeres. Ehhez ellenőrizze a [ **állapot** a skálázási egység](#monitor-add-node-operations). 

## <a name="add-the-node"></a>A csomópont hozzáadása

A felügyeleti portálon vagy a PowerShell használatával új csomópontokat. A Hozzáadás csomópont művelet először felveszi az új méretezési egység csomópont a rendelkezésre álló számítási kapacitást, és automatikusan kiterjeszti a tárolási kapacitást. A kapacitás automatikusan bontja ki, mert az Azure Stack egy hiperkonvergens rendszer ahol *számítási* és *tárolási* együtt méretezhető.

### <a name="use-the-admin-portal"></a>A felügyeleti portál

1. Jelentkezzen be az Azure Stack rendszergazdai portál az Azure Stack operátorait szerint.
2. Navigáljon a **+ erőforrás létrehozása** > **kapacitás** > **skálázási egység csomópont**.
   ![Skálázási egység csomópont](media/azure-stack-add-scale-node/select-node1.png)
3. Az a **csomópont hozzáadása** panelen válassza a *régió*, majd válassza ki a *skálázási egység* , hogy szeretné-e a csomópont hozzáadása. Is megadhat a *BMC IP-cím* a skálázási egység csomópont ad hozzá. Egyszerre csak egy csomópontot adhat hozzá.
   ![Adja hozzá a csomópont részletei](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>A PowerShell használata

Használja a **New-AzsScaleUnitNodeObject** parancsmag az egy csomóponton.  

Használja a következő PowerShell-példaszkriptekre egyikét, mielőtt cserélje le az értékeket *csomópontnevek* és *IP-címek* értékeit az Azure Stack környezettel.

  > [!Note]  
  > Egy csomópont elnevezésekor a név kell hagynia kevesebb mint 15 karakter hosszú lehet. Még nem használható egy neve szóközt tartalmaz, vagy a következő karakterek egyikét sem tartalmazza: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`, `}`, `_`.

**Csomópont hozzáadása:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>A figyelő csomópont műveletek hozzáadása 
A felügyeleti portálon vagy a PowerShell segítségével a Hozzáadás csomópont művelet állapotának beolvasása. Adja hozzá a csomópont műveletek végrehajtásához napra több óráig is tarthat.

### <a name="use-the-admin-portal"></a>A felügyeleti portál 
Egy új csomópont hozzáadása figyeléséhez, a felügyeleti portálon tekintse át a skálázási egység vagy egység csomópont objektumokat. Ehhez nyissa meg **régiók kezelése** > **egységig**. Ezután válassza ki a skálázási egység vagy meg szeretné tekinteni a skálázási egység csomópont. 

### <a name="use-powershell"></a>A PowerShell használata
A skálázási egység és a skálázási egység csomópontok állapota lehet beolvasni a következő PowerShell-lel:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>A Hozzáadás csomópont művelet állapota 
**A skálázási egység:**

|Állapot               |Leírás  |
|---------------------|---------|
|Futtatás              |A skálázási egység aktívan résztvevő összes csomópontot.|
|Leállítva              |A skálázási egység csomópont, lefelé vagy nem érhető el.|
|Kibontás            |Egy vagy több skálázási egység csomópont jelenleg jelennek meg a számítási kapacitást.|
|Tároló konfigurálása  |A számítási kapacitás ki lett terjesztve, és a tárolási konfiguráció fut-e.|
|Szervizelés szükséges |Hibát észlelt, amely megköveteli, hogy egy vagy több skálázási egység csomópont javítani kell.|


**Egy méretezési egység csomópont:**

|Állapot                |Leírás  |
|----------------------|---------|
|Futtatás               |A csomópont tevékenyen részt vesz a skálázási egység.|
|Leállítva               |A csomópont nem érhető el.|
|Hozzáadás                |A csomópont aktívan ad hozzá a skálázási egység.|
|Javítás             |A csomópont aktívan javítása folyamatban van.|
|Karbantartás           |A csomópont fel van függesztve, és nincsenek aktív felhasználói munkaterhelés fut-e. |
|Szervizelés szükséges  |Hibát észlelt, amely megköveteli, hogy a csomópont nem működik helyesen.|


## <a name="troubleshooting"></a>Hibaelhárítás
Az alábbiakban a leggyakoribb problémáinak csomópont hozzáadásakor. 

**1. forgatókönyv:**  A Hozzáadás skálázási egység csomópont művelet sikertelen lesz, de egy vagy több csomópont leállítva állapottal láthatók.  
- Szervizelési: A javítási művelet használatával javítsa ki egy vagy több csomópontot. Csak egyetlen javítási művelet is futtatható egyszerre.

**2. forgatókönyv:** Egy vagy több skálázási egység csomópont már hozzá lett adva, de a tárolási bővítése nem sikerült. Ebben a forgatókönyvben a skálázási egység csomópont objektum állapotjelentést fut, de a tárolás konfigurálása a feladat nincs elindítva.  
- Szervizelési: A kiemelt végponthoz segítségével áttekintheti az storage a következő PowerShell-parancsmag futtatásával:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**3. forgatókönyv:** Azt jelzi, hogy a horizontális felskálázás feladat nem sikerült riasztást kapott.  
- Szervizelési: Ebben az esetben a tároló konfigurációs feladat nem sikerült. Ez a probléma megköveteli, hogy forduljon az ügyfélszolgálathoz.


## <a name="next-steps"></a>További lépések 
[Nyilvános IP-címek hozzáadása](azure-stack-add-ips.md) 
