---
title: Azure Stack tárfiókok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan keresése, kezelése, helyreállítása és az Azure Stack tárfiókok VISSZAIGÉNYLÉSE
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 5d5fe68d9641b6ba7099268f631c1bfe89fb0674
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985153"
---
# <a name="manage-azure-stack-storage-accounts"></a>Azure Stack tárfiókok kezelése

Útmutató: Azure Stack tárfiókok kezelését. Keresse meg, helyreállítás és üzleti igényeinek megfelelően tárolási kapacitás visszaigényléséhez.

## <a name="find-a-storage-account"></a>Tárfiók keresése

A tárfiókok listája, a régióban az Azure Stackben által tekinthet meg:

1. Jelentkezzen be a [felügyeleti portál](https://adminportal.local.azurestack.external).

2. Válassza ki **minden szolgáltatás** > **tárolási** > **tárfiókok**.

   ![Az Azure Stack tárfiókok](media/azure-stack-manage-storage-accounts/image4.png)

Alapértelmezés szerint az első 10 fiókok jelennek meg. Ha szeretné beolvasni a több kattintva a **Továbbiak betöltése** hivatkozásra a lista alján.

VAGY

Ha egy adott tárfiók – érdekli **szűrése, és a megfelelő fiókok beolvasásához** csak.


**Fiókok szűrése:**

1. Válassza ki **szűrő** a panel tetején.
2. A szűrő panelen lehetővé teszi, hogy adja meg **fióknév**, **előfizetés-azonosító**, vagy **állapot** a tárfiókok listája, megjelenítendő finomhangolásához. Használja őket.
3. Írja be, mivel a lista automatikusan alkalmazza a szűrőt.  .
   
    ![Azure Stack tárfiókok szűréséhez](media/azure-stack-manage-storage-accounts/image5.png)

4. A szűrő alaphelyzetbe: válasszon **szűrő**, tisztítsa meg a beállításokat és frissíthet.

A keresőmezőbe (felső részén a lista ablaktáblájában storage fiókok) lehetővé teszi a kijelölt szöveg fiókok listájában jelölje ki. Ezzel amikor teljes neve vagy azonosítója nem érhető el egyszerűen.

Itt a szabad szöveges segítségével megkeresheti az Önt érdeklő fiók.

![Azure Stack tárfiókok keresése](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Tekintse meg fiók adatai
Ha megtalálta a számlák megtekintése érdekli, kiválaszthatja az adott fiók bizonyos részleteinek megtekintéséhez. Egy új panel nyílik meg a fiók adatainak például: a típus a fiók létrehozásának időpontja, helye, stb.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Egy törölt fiók helyreállítása
Olyan helyzetekben, ahol meg kell helyreállítani egy törölt fiók is.

Az Azure Stack egy egyszerű módja van:

1. Keresse meg a storage-fiókok listáján. Tekintse meg a Keresés ebben a cikkben további információkat a storage-fiók.
2. Keresse meg az adott fiók a listában. Szükség lehet szűrni.
3. Ellenőrizze a *állapot* fiók. Üzenetnek kell megjelennie **törölt**.
4. Válassza ki a fiókot, amely a fiók részletei ablaktábla megnyitása.
5. Felett ezen az ablaktáblán keresse meg a **helyreállítása** gombra, és válassza ki azt.
6. Válassza az **Igen** lehetőséget a megerősítéshez.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. A helyreállítási már az *folyamat... Várjon* az azt jelzi, hogy sikeres volt-e azt.
   Választhatja a folyamat jelzések megtekintéséhez a portál tetején a "harang" ikonra.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Ha a helyreállított fiók sikeresen szinkronizálta, használható újra.

### <a name="some-gotchas"></a>Néhány buktatóinak elkerülése
* A törölt fiók állapota látható **megőrzési kívül**.
  
  Adatmegőrzési azt jelenti, hogy ki, hogy a törölt fiók túllépte az adatmegőrzési időszak, és nem használható helyreállításhoz.
* A törölt fiók nem szerepelnek a fiókok listájában.
  
  Ha a törölt fiók már megtörtént a szemétgyűjtő fiók nem jelenik a fióklista. Ebben az esetben nem állíthatók vissza. Lásd: [kapacitás visszaigényléséhez](#reclaim) ebben a cikkben.

## <a name="set-the-retention-period"></a>A megőrzési időtartam
A megőrzési időszak beállítása lehetővé teszi, hogy a felhő üzemeltetője megadásához egy adott időszakban (0 és 9999 nap) közötti nap során, ami minden törölt fiók potenciálisan állíthatók helyre. Az alapértelmezett megőrzési időszak 0 nap van beállítva. Az érték "0" azt jelenti, hogy minden törölt fiók megőrzési azonnal esik, és rendszeres szemétgyűjtési megjelölve.

**A megőrzési idő módosítása:**

1. Jelentkezzen be a [felügyeleti portál](https://adminportal.local.azurestack.external).
2. Válassza ki **minden szolgáltatás** > **régiók kezelése** alatt **felügyeleti**.
3. Válassza ki **erőforrás-szolgáltatók** > **tárolási** > **beállítások**. Az elérési út Kezdőlap > *régió* – erőforrás-szolgáltatók > tároló.
4. Válassza ki **konfigurációs** majd szerkessze a megőrzési időtartam értéket.

   A napok számának megadása, és mentse azt.
   
   Ez az érték azonnal hatályba lép, és a teljes régió van beállítva.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Kapacitás visszaigényléséhez
Az, hogy olyan megőrzési időszakot hatásai egyik célja, hogy egy törölt fiók továbbra is kapacitás felhasználását, mindaddig, amíg a megőrzési időtartam azt éles. Felhőalapú felelősként szükség lehet olyan módon, a törölt fiók jusson, annak ellenére, hogy a megőrzési időszak még nem járt le.

A kapacitás a portál vagy a PowerShell használatával is visszaigényelhetők.

**A portál használatával kapacitás visszaigényléséhez:**
1. Keresse meg a storage-fiókok panelen. Tekintse meg a storage-fiók található.
2. Válassza ki **jusson** a panel tetején.
3. Olvassa el az üzenetet, majd **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Várjon, amíg a sikeres címtármódosítást jelző értesítés tekintse meg a portálon a harang ikonra.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. A Storage-fiókok lap frissítése. A törölt fiókok már nem jelennek meg a listában, mert azok kiürítése megtörtént.

Explicit módon a megőrzési időszak felülbírálására PowerShell is használható, és azonnal a kapacitás visszaigényléséhez.

**Kapacitás visszaigényléséhez PowerShell-lel:**   

1. Győződjön meg arról, hogy az Azure PowerShell telepítését és konfigurálását. Ha nem, kövesse az alábbi utasításokat: 
   * Telepítse a legújabb Azure PowerShell-verziót, és társíthatja azt az Azure-előfizetéséhez, tekintse meg a [telepítése és konfigurálása az Azure PowerShell-lel](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Azure Resource Manager-parancsmagokkal kapcsolatos további információkért lásd: [az Azure PowerShell az Azure Resource Managerrel](https://go.microsoft.com/fwlink/?LinkId=394767)
2. A következő parancsmagok futtatásához:

> [!NOTE]  
> Ha ezen parancsmagok futtatásához véglegesen törli a fiókot, és annak tartalmát. Esetén nem állítható helyre. Ez körültekintően szabad használni.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

További információkért lásd: [Azure Stack PowerShell-dokumentáció](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>További lépések

 - Engedélyek kezelése információ: [Manage Role-Based hozzáférés-vezérlés](azure-stack-manage-permissions.md).
 - Az Azure stack-kezelés tárolási kapacitás további információkért lásd: [kezelése az Azure Stack a tárolókapacitás](azure-stack-manage-storage-shares.md).
