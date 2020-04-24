---
title: Skálázási egység csomópontjainak hozzáadása Azure Stack hub-ban
description: Megtudhatja, hogyan adhat hozzá méretezési egység csomópontjait Azure Stack hub egységéhez.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: c264e0abc0fdc5a382b83a23158f860a56aea260
ms.sourcegitcommit: a3ae6dd8670f8fb24224880df7eee256ebbcc4ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772585"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack-hub"></a>További skálázási egység csomópontjainak hozzáadása Azure Stack hub-ban

A Azure Stack hub operátorok egy további fizikai számítógép hozzáadásával növelhetik egy meglévő méretezési egység teljes kapacitását. A fizikai számítógépet a méretezési egység csomópontjának is nevezzük. Minden hozzáadott új méretezési egység csomópontnak homogénnek kell lennie a CPU-típus, a memória és a lemez száma és mérete között a méretezési egységben már meglévő csomópontok számára.

A méretezési egység csomópont hozzáadásához Azure Stack központban kell működnie, és futtatnia kell a hardveres berendezések gyártójának (OEM) eszközét. Az OEM-eszközök a hardveres életciklus-gazdagépen (HLH) futnak, így meggyőződhet róla, hogy az új fizikai számítógép megegyezik a meglévő csomópontokkal megegyező belső vezérlőprogram-szinttel.

A következő folyamatábra a méretezési egység csomópontjának általános folyamatát mutatja be:

![Skálázási egység folyamatának hozzáadása](media/azure-stack-add-scale-node/add-node-flow.svg)
<br> *Azt határozza meg, hogy az OEM hardver szállítója a fizikai kiszolgáló állványának elhelyezését és a belső vezérlőprogram frissítését a támogatási szerződés alapján változik-e.*

Az új csomópont hozzáadásának művelete több órát vagy napot is igénybe vehet. A rendszeren futó munkaterhelések egyike sem befolyásolja a további méretezési egység csomópont hozzáadását.

> [!Note]  
> Ne kísérelje meg a következő műveletek egyikét sem, amíg egy méretezési egység hozzáadása csomópont-művelet már folyamatban van:
>
>  - Azure Stack hub frissítése
>  - Tanúsítványok váltása
>  - Azure Stack hub leállítása
>  - Méretezési egység csomópontjának javítása


## <a name="add-scale-unit-nodes"></a>Skálázási egység csomópontjainak hozzáadása

A következő lépések áttekintést nyújtanak a csomópontok hozzáadásáról. Ne kövesse ezeket a lépéseket anélkül, hogy először a SZÁMÍTÓGÉPGYÁRTÓ által biztosított kapacitás-bővítési dokumentációra kellene hivatkoznia.

1. Helyezze az új fizikai kiszolgálót az állványba, és csatlakoztassa megfelelően. 
2. Engedélyezze a fizikai kapcsoló portjait, és módosítsa a hozzáférés-vezérlési listákat (ACL), ha vannak ilyenek.
3. Konfigurálja a megfelelő IP-címet a alaplapi felügyeleti vezérlőben (BMC), és alkalmazza az összes BIOS-beállítást a SZÁMÍTÓGÉPGYÁRTÓ által biztosított dokumentációban.
4. Alkalmazza a jelenlegi belső vezérlőprogram-alapkonfigurációt az összes összetevőre a HLH futó hardvergyártó által biztosított eszközök használatával.
5. Futtassa a csomópont hozzáadása műveletet a Azure Stack hub felügyeleti portálján.
6. Ellenőrizze, hogy a csomópont hozzáadása művelet sikeres-e. Ehhez ellenőriznie kell a [skálázási egység **állapotát** ](#monitor-add-node-operations). 

## <a name="add-the-node"></a>A csomópont hozzáadása

Új csomópontok hozzáadásához a felügyeleti portált vagy a PowerShellt használhatja. A csomópont hozzáadása művelet először hozzáadja az új méretezési egység csomópontot elérhető számítási kapacitásként, majd automatikusan kiterjeszti a tárolókapacitást. A kapacitás kibontása automatikusan megtörténik, mivel Azure Stack hub egy hiperkonvergens rendszer, ahol a *számítási* és a *tárolási* skálázás együttesen történik.

### <a name="use-the-administrator-portal"></a>A felügyeleti portál használata

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra Azure Stack hub-operátorként.
2. Navigáljon a **+ erőforrás** > -**kapacitás** > **skálázási egység csomópontjának**létrehozására.
   ![Méretezési egység csomópontja](media/azure-stack-add-scale-node/select-node1.png)
3. A **Csomópont hozzáadása** panelen válassza ki a *régiót*, majd válassza ki azt a *méretezési egységet* , amelyhez hozzá szeretné adni a csomópontot. Adja meg a hozzá tartozó méretezési egység csomópontjának *bmc IP-címét* is. Egyszerre csak egy csomópontot lehet hozzáadni.
   ![Csomópont hozzáadása – részletek](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>A PowerShell használata

Csomópont hozzáadásához használja a **New-AzsScaleUnitNodeObject** parancsmagot.  

Az alábbi PowerShell-parancsfájlok valamelyikének használata előtt cserélje le az értékeket a *csomópontok neveire* és az *IP-címekre* az Azure stack hub-környezet értékeivel.

  > [!Note]  
  > Egy csomópont elnevezése esetén a nevet 15 karakternél rövidebb ideig kell megtartani. Olyan nevet sem használhat, amely szóközt tartalmaz, vagy tartalmazza a következő karaktereket: `\` `/` `:` `*` `?` `"` `<` `>`,,,,,,,, `|`, `\` `~`,, `!`, `@` `#`,, `$`, `%`, `^`, `&` `(` `)`,,, `{`, `}`,. `_`

**Csomópont hozzáadása:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Csomópont-hozzáadási műveletek figyelése 
A csomópont hozzáadása művelet állapotának beolvasásához használja a felügyeleti portált vagy a PowerShellt. A csomópont-műveletek hozzáadása több órát is igénybe vehet.

### <a name="use-the-administrator-portal"></a>A felügyeleti portál használata 
Új csomópont hozzáadásának figyeléséhez tekintse át a méretezési egység vagy a skálázási egység csomópont objektumait a felügyeleti portálon. Ehhez nyissa meg a **régió-felügyeleti** > **méretezési egységeket**. Ezután válassza ki az áttekinteni kívánt méretezési egységet vagy méretezési egység csomópontot. 

### <a name="use-powershell"></a>A PowerShell használata
A skálázási egység és a skálázási egység csomópontjainak állapota a PowerShell használatával kérhető le a következő módon:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>A csomópont hozzáadása művelet állapota 
**Méretezési egység esetén:**

|status               |Leírás  |
|---------------------|---------|
|Fut              |Az összes csomópont aktívan részt vesz a skálázási egységben.|
|Leállítva              |A skálázási egység csomópontja vagy le van zárva, vagy nem érhető el.|
|Bővülő            |Egy vagy több méretezési egység csomópontja jelenleg számítási kapacitásként van hozzáadva.|
|Tároló konfigurálása  |A számítási kapacitás ki lett bontva, és a tárolási konfiguráció fut.|
|Szervizelést igényel |A rendszer olyan hibát észlelt, amely egy vagy több skálázási egység csomópontjának kijavítását igényli.|


**Méretezési egység csomópont esetén:**

|status                |Leírás  |
|----------------------|---------|
|Fut               |A csomópont aktívan részt vesz a skálázási egységben.|
|Leállítva               |A csomópont nem érhető el.|
|Hozzáadása                |A csomópontot aktívan felveszik a méretezési egységbe.|
|Javítása             |A csomópont aktívan javítás alatt áll.|
|Karbantartás           |A csomópont szüneteltetve van, és nem fut aktív felhasználói munkaterhelés. |
|Szervizelést igényel  |Hiba észlelhető, amely megköveteli a csomópont javítását.|


## <a name="troubleshooting"></a>Hibaelhárítás
A csomópontok hozzáadásakor a következő gyakori problémák észlelhetők. 

**1. forgatókönyv:**  A skálázási egység csomópont hozzáadása művelet meghiúsul, de egy vagy több csomópont leállított állapottal van felsorolva.  
- Szervizelés: egy vagy több csomópont kijavításához használja a javítási műveletet. Egyszerre csak egyetlen javítási művelet futhat.

**2. forgatókönyv:** Egy vagy több méretezési egység csomópontja hozzá lett adva, de a tárterület bővítése nem sikerült. Ebben a forgatókönyvben a skálázási egység csomópont-objektuma fut állapotot jelent, de a tárolási beállítások konfigurálása feladat nincs elindítva.  
- Szervizelés: a privilegizált végpont használatával tekintse át a tárolási állapotot a következő PowerShell-parancsmag futtatásával:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**3. forgatókönyv:** Olyan riasztást kapott, amely jelzi, hogy a tárolási Felskálázási feladata sikertelen volt.  
- Szervizelés: ebben az esetben a tárolási konfigurációs feladat meghiúsult. Ehhez a problémához kapcsolatba kell lépnie a támogatási szolgálattal.


## <a name="next-steps"></a>További lépések 
[Nyilvános IP-címek hozzáadása](azure-stack-add-ips.md) 
