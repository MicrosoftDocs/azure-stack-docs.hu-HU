---
title: Azure Stack Storage-fiókok kezelése | Microsoft Docs
description: Megtudhatja, hogyan keresheti meg, kezelheti, helyreállíthatja és visszaigényelheti Azure Stack Storage-fiókokat
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 4f9e9c4f79a06e0f1f74db8152047beb3af07b75
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417543"
---
# <a name="manage-azure-stack-storage-accounts"></a>Azure Stack Storage-fiókok kezelése

Ismerje meg, hogyan kezelheti Azure Stack Storage-fiókokat. Az üzleti igények alapján megkeresheti, helyreállíthatja és visszaigényelheti a tárolási kapacitást.

## <a name="find-a-storage-account"></a>Storage-fiók keresése

A régióban lévő Storage-fiókok listáját a Azure Stack a következőket tekintheti meg:

1. Jelentkezzen be a [felügyeleti portálra](https://adminportal.local.azurestack.external).

2. Válassza **a minden szolgáltatás** > **tároló** > **Storage-fiókok**lehetőséget.

   ![Azure Stack Storage-fiókok](media/azure-stack-manage-storage-accounts/image4.png)

Alapértelmezés szerint az első 10 fiók jelenik meg. A lista alján található **további terhelés** hivatkozásra kattintva további részleteket is megadhat.

VAGY

Ha érdekli egy adott Storage-fiók, akkor csak **a megfelelő fiókokat szűrheti és** kérheti le.


**A fiókok szűrése:**

1. Válassza a **szűrő** elemet a panel tetején.
2. A szűrő panelen megadhatja a **fiók nevét**, az **előfizetés azonosítóját**vagy az **állapotot** a megjelenítendő Storage-fiókok listájának finomhangolásához. Szükség szerint használja őket.
3. A gépelés során a lista automatikusan alkalmazza a szűrőt.  .
   
    ![Azure Stack Storage-fiókok szűrése](media/azure-stack-manage-storage-accounts/image5.png)

4. A szűrő alaphelyzetbe állításához: válassza a **szűrő**lehetőséget, törölje a beállításokat és a frissítést.

A keresési szövegmező (a Storage-fiókok listája ablaktábla tetején) lehetővé teszi a kijelölt szöveg kijelölését a fiókok listájában. Ezt akkor használhatja, ha a teljes név vagy az azonosító nem könnyen elérhető.

Az itt található ingyenes szöveg segítségével megtalálhatja az Önt érdeklő fiókot.

![Azure Stack Storage-fiókok keresése](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Tekintse meg a fiók részleteit
Miután megtalálta a megtekinteni kívánt fiókokat, kiválaszthatja az adott fiókot az egyes adatok megtekintéséhez. Megnyílik egy új ablaktábla a fiók részleteivel, például: a fiók típusa, létrehozási idő, hely stb.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Törölt fiók helyreállítása
Előfordulhat, hogy olyan helyzetben van, ahol egy törölt fiókot kell helyreállítani.

Azure Stack egy egyszerű módja van ennek:

1. Tallózással keresse meg a Storage-fiókok listáját. További információ: Storage-fiók keresése ebben a cikkben.
2. Keresse meg az adott fiókot a listában. Előfordulhat, hogy szűrnie kell.
3. A fiók *állapotának* bejelölése. A törlést is **el**kell mondani.
4. Válassza ki a fiókot, amely megnyitja a fiók adatai ablaktáblát.
5. A panel tetején keresse meg a **helyreállítás** gombot, és jelölje ki.
6. Válassza az **Igen** lehetőséget a megerősítéshez.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. A helyreállítás most folyamatban van *... Várjon, amíg* sikeres volt a jelzés.
   A "harang" ikont a portál tetején is kiválasztva megtekintheti a folyamatjelzőket.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Ha a helyreállított fiók szinkronizálása sikeresen megtörtént, újra felhasználható.

### <a name="some-gotchas"></a>Néhány megkerülő
* A Törölt fiók állapota az adatmegőrzési állapotnak megfelelően jelenik **meg**.
  
  Az adatmegőrzés nem jelenti azt, hogy a Törölt fiók túllépte a megőrzési időtartamot, és előfordulhat, hogy nem lehet helyreállítani.
* A Törölt fiók nem jelenik meg a fiókok listájában.
  
  Előfordulhat, hogy a fiók nem jelenik meg a fiók listájában, ha a Törölt fiók már be van gyűjtve. Ebben az esetben nem lehet helyreállítani. Lásd a [kapacitás](#reclaim) visszaigénylése című cikket.

## <a name="set-the-retention-period"></a>A megőrzési időtartam beállítása
A megőrzési időtartam beállítás lehetővé teszi, hogy a felhő operátora napokban határozzon meg egy időszakot (0 és 9999 nap között), amely alatt a törölt fiókok esetleg helyreállíthatók. Az alapértelmezett megőrzési időtartam 0 napra van állítva. Ha az értéket "0" értékre állítja, az azt jelenti, hogy a törölt fiókok azonnal nem maradnak meg, és meg vannak jelölve az időszakos szemetet tartalmazó gyűjtemény számára.

**A megőrzési időtartam módosítása:**

1. Jelentkezzen be a [felügyeleti portálra](https://adminportal.local.azurestack.external).
2. Válassza az **összes szolgáltatás** > **régió** felügyelete lehetőséget az **Adminisztráció**alatt.
3. Válassza **az erőforrás-szolgáltatók** > **tárolási** > **Beállítások**lehetőséget. Az elérési út a Home > *region* – Resource providers > Storage.
4. Válassza a **konfiguráció** lehetőséget, majd szerkessze a megőrzési időszak értékét.

   Állítsa be a napok számát, majd mentse.
   
   Ez az érték azonnal érvényes, és a teljes régióra van beállítva.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Kapacitás visszaigénylése
A megőrzési idő egyik mellékhatása, hogy a törölt fiókok továbbra is felhasználják a kapacitást, amíg nem kerül sor a megőrzési időszakra. A Felhőbeli operátornak szüksége lehet arra, hogy visszaigényelje a Törölt fiók területét annak ellenére, hogy a megőrzési időszak még nem járt le.

A kapacitást a portál vagy a PowerShell segítségével állíthatja vissza.

**Kapacitás visszaigénylése a portál használatával:**
1. Navigáljon a Storage-fiókok ablaktáblára. Lásd: Storage-fiók keresése.
2. Válassza ki a panel felső részén található visszaigénylési **terület** lehetőséget.
3. Olvassa el az üzenetet, majd kattintson **az OK gombra**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Várjon a sikeres értesítésre, és tekintse meg a harang ikont a portálon.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Frissítse a Storage-fiókok lapot. A törölt fiókok már nem jelennek meg a listában, mert törölve lettek.

A PowerShell használatával is felülbírálhatja a megőrzési időszakot, és azonnal visszaigényelheti a kapacitást.

**Kapacitás visszaigénylése a PowerShell használatával:**   

1. Győződjön meg arról, hogy van Azure PowerShell telepítve és konfigurálva. Ha nem, kövesse az alábbi utasításokat: 
   * A legújabb Azure PowerShell verziójának telepítéséhez és az Azure-előfizetéshez való hozzárendeléséhez tekintse meg a [Azure PowerShell telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)című témakört.
   Azure Resource Manager parancsmagokkal kapcsolatos további információkért lásd: [Azure PowerShell használata a Azure Resource Manager használatával](https://go.microsoft.com/fwlink/?LinkId=394767)
2. Futtassa a következő parancsmagokat:

> [!NOTE]  
> Ha ezeket a parancsmagokat futtatja, véglegesen törli a fiókot és annak tartalmát. Nem helyreállítható. Ezt körültekintően használhatja.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

További információ: [Azure stack PowerShell-dokumentáció](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>További lépések

 - Az engedélyek kezelésével kapcsolatos információkért lásd: [szerepköralapú Access Control kezelése](azure-stack-manage-permissions.md).
 - További információ a Azure Stack tárolási kapacitásának kezeléséről: a [Azure stack tárolási kapacitásának kezelése](azure-stack-manage-storage-shares.md).
