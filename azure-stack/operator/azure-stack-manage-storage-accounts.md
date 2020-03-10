---
title: Azure Stack hub Storage-fiókok kezelése
description: Ismerje meg, hogyan lehet megkeresni, felügyelni, helyreállítani és visszaigényelni Azure Stack hub Storage-fiókokat.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 995342f471ed7300d2fb9dd95a6623588a13d1a9
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368839"
---
# <a name="manage-azure-stack-hub-storage-accounts"></a>Azure Stack hub Storage-fiókok kezelése

Ismerje meg, hogyan kezelheti Azure Stack hub Storage-fiókokat. Az üzleti igények alapján megkeresheti, helyreállíthatja és visszaigényelheti a tárolási kapacitást.

## <a name="find-a-storage-account"></a>Storage-fiók keresése

A régióban található Storage-fiókok listáját Azure Stack központban tekintheti meg a következő lépésekkel:

1. Jelentkezzen be a [felügyeleti portálra](https://adminportal.local.azurestack.external).

2. Válassza **a minden szolgáltatás** > **Storage** - > **Storage-fiókok**lehetőséget.

   ![Azure Stack hub Storage-fiókok](media/azure-stack-manage-storage-accounts/image4.png)

Alapértelmezés szerint az első 10 fiók jelenik meg. A lista alján található **további terhelés** hivatkozásra kattintva további részleteket is megadhat.

VAGY

Ha érdekli egy adott Storage-fiók, akkor csak **a megfelelő fiókokat szűrheti és** kérheti le.

**A fiókok szűrése:**

1. Válassza a **szűrő** elemet a panel tetején.
2. A szűrő panelen megadhatja a **fiók nevét**, az **előfizetés azonosítóját**vagy az **állapotot** a megjelenítendő Storage-fiókok listájának finomhangolásához. Szükség szerint használja őket.
3. A gépelés során a lista automatikusan alkalmazza a szűrőt.

    ![Azure Stack hub Storage-fiókok szűrése](media/azure-stack-manage-storage-accounts/image5.png)

4. A szűrő alaphelyzetbe állításához: válassza a **szűrő**lehetőséget, törölje a beállításokat és a frissítést.

A keresési szövegmező (a Storage-fiókok listája ablaktábla tetején) lehetővé teszi a kijelölt szöveg kijelölését a fiókok listájában. Ezt akkor használhatja, ha a teljes név vagy az azonosító nem érhető el egyszerűen.

Az itt található ingyenes szöveg segítségével megtalálhatja a kívánt fiókot.

![Azure Stack hub Storage-fiókok keresése](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Tekintse meg a fiók részleteit
Miután megtalálta a megtekinteni kívánt fiókokat, kiválaszthatja az adott fiókot az egyes részletek megtekintéséhez. Megnyílik egy új ablaktábla a fiók részleteivel. Ezen adatok közé tartozik a fiók, a létrehozás időpontja, a hely stb.

![Storage-fiók adatai](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Törölt fiók helyreállítása
Előfordulhat, hogy olyan helyzetben van, ahol egy törölt fiókot kell helyreállítani.

Azure Stack központban van egy egyszerű módja:

1. Tallózással keresse meg a Storage-fiókok listáját. További információ: Storage- [fiók keresése](azure-stack-manage-storage-accounts.md) a cikk elején.
2. Keresse meg az adott fiókot a listában. Előfordulhat, hogy szűrnie kell.
3. A fiók *állapotának* bejelölése. A **törlést is el**kell mondani.
4. Válassza ki a fiókot, amely megnyitja a fiók adatai ablaktáblát.
5. A panel tetején keresse meg a **helyreállítás** gombot, és jelölje ki.
6. Válassza az **Igen** lehetőséget a megerősítéshez.

   ![A Storage-fiók megerősítésének helyreállítása](media/azure-stack-manage-storage-accounts/image8.png)

7. A helyreállítás már folyamatban van. Várjon, amíg sikeres volt a jelzés. A "harang" ikont a portál tetején is kiválasztva megtekintheti a folyamatjelzőket.

   ![A Storage-fiók helyreállítása sikerült](media/azure-stack-manage-storage-accounts/image9.png)

   Ha a helyreállított fiók szinkronizálása sikeresen megtörtént, újra felhasználható.

### <a name="some-gotchas"></a>Néhány megkerülő
* A Törölt fiók állapota az **adatmegőrzési**állapotnak megfelelően jelenik meg.
  
  Az adatmegőrzés nem jelenti azt, hogy a Törölt fiók túllépte a megőrzési időtartamot, és előfordulhat, hogy nem lehet helyreállítani.

* A Törölt fiók nem jelenik meg a fiókok listájában.
  
  Előfordulhat, hogy a fiók nem jelenik meg a fiók listájában, ha a Törölt fiók már be van gyűjtve. Ebben az esetben nem lehet helyreállítani. További információ: a [kapacitás visszaigénylése](#reclaim) ebben a cikkben.

## <a name="set-the-retention-period"></a>A megőrzési időtartam beállítása
Az adatmegőrzési időtartam beállítása lehetővé teszi, hogy a felhőüzemeltető napokban (0–9999 nap) meghatározza azt az időszakot, amely alatt a törölt fiókok esetleg helyreállíthatók. Az alapértelmezett adatmegőrzési időszak 0 nap. Az érték „0”-ra állításával a törölt fiók azonnal megszűnik, és a rendszer megjelöli az időszakos szemétgyűjtésre.

**A megőrzési időtartam módosítása:**

1. Jelentkezzen be a [felügyeleti portálra](https://adminportal.local.azurestack.external).
2. Válassza a **minden szolgáltatás** > **régió** felügyelete az **Adminisztráció**alatt lehetőséget.
3. Válassza az **erőforrás-szolgáltatók** > **Storage** > **Beállítások**lehetőséget. Az elérési út a Home > *region* – Resource providers > Storage.
4. Válassza a **konfiguráció** lehetőséget, majd szerkessze a megőrzési időszak értékét.

   Állítsa be a napok számát, majd mentse.

   Ez az érték azonnal érvényes, és a teljes régióra van beállítva.

   ![Megőrzési időszak szerkesztése a felügyeleti portálon](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Kapacitás visszaigénylése
A megőrzési idő egyik mellékhatása, hogy a törölt fiókok továbbra is felhasználják a kapacitást, amíg nem kerül sor a megőrzési időszakra. A Felhőbeli operátornak szüksége lehet arra, hogy visszaigényelje a Törölt fiók területét annak ellenére, hogy a megőrzési időszak még nem járt le.

A kapacitást a portál vagy a PowerShell segítségével állíthatja vissza.

**Kapacitás visszaigénylése a portál használatával:**
1. Navigáljon a Storage-fiókok ablaktáblára. Lásd: Storage-fiók keresése.
2. Válassza ki a panel felső részén található **visszaigénylési terület** lehetőséget.
3. Olvassa el az üzenetet, majd kattintson **az OK gombra**.

    ![Tárterület visszaigénylése a Storage-fiókokban](media/azure-stack-manage-storage-accounts/image11.png)

4. Várjon a sikeres értesítésre. Tekintse meg a harang ikont a portálon.

    ![A tárhely sikeresen visszaállítva](media/azure-stack-manage-storage-accounts/image12.png)

5. Frissítse a Storage-fiókok lapot. A törölt fiókok már nem jelennek meg a listában, mert törölve lettek.

A PowerShell használatával is felülbírálhatja a megőrzési időszakot, és azonnal visszaigényelheti a kapacitást.

**Kapacitás visszaigénylése a PowerShell használatával:**

1. Győződjön meg arról, hogy van Azure PowerShell telepítve és konfigurálva. Ha nem, kövesse az alábbi utasításokat: 
   * A legújabb Azure PowerShell verziójának telepítéséhez és az Azure-előfizetéshez való hozzárendeléséhez tekintse meg a [Azure PowerShell telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)című témakört.
   Azure Resource Manager parancsmagokkal kapcsolatos további információkért lásd: [Azure PowerShell használata a Azure Resource Manager használatával](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Futtassa a következő parancsmagokat:

> [!NOTE]  
> Ha ezeket a parancsmagokat futtatja, véglegesen törli a fiókot és annak tartalmát. Nem lehet helyreállítani. Ezt körültekintően használhatja.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

További információ: [Azure stack hub PowerShell dokumentációja](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Következő lépések

 - Az engedélyek kezelésével kapcsolatos információkért lásd: [hozzáférési engedélyek beállítása szerepköralapú hozzáférés-vezérlés használatával](azure-stack-manage-permissions.md).
 - Az Azure Stack hub tárolási kapacitásának kezelésével kapcsolatos információkért lásd: [Azure stack hub tárolókapacitásának kezelése](azure-stack-manage-storage-shares.md).
