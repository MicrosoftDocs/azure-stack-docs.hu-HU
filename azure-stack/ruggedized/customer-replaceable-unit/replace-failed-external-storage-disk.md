---
title: Meghibásodott külső tároló lemezének cseréje
description: Megtudhatja, hogyan cserélhet le egy meghibásodott külső tárolóeszközt
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: e53d81e7606c6440ce535726199af31a1dbfd950
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910652"
---
# <a name="replacing-a-failed-external-storage-disk"></a>A meghibásodott külső tároló lemezének cseréje

A meghibásodott külső lemez cseréjéhez kövesse az alábbi eljárást.

## <a name="prerequisites"></a>Előfeltételek

1.  Az útmutató elején tekintse át a *megjegyzéseket, a figyelmeztetéseket és a figyelmeztetéseket*

2.  Tekintse át a kezelési óvintézkedéseket.

3.  Áttekintés

    -   Szükséges ismeretek a méretezési egység csomópontjainak használatához egy taktikai Felhőbeli berendezésben, ha a méretezési egység csomópontja működik

    -   Szükséges ismeretek a hardveres életciklus-gazdagép használatáról, ha a hardveres életciklus-gazdagépet dolgozik

4.  Kész

    -   A méretezési egység csomópont-hozzáférésének és állapotának ellenőrzése, ha a méretezési egység csomópontja működik

    -   A hardveres életciklus-gazdagép hozzáférésének és állapotának ellenőrzése, ha együttműködik a hardver életciklus-Gazdagépével

5.  Kész

    -   A skálázási egység csomópontjainak kikapcsolása, ha a méretezési egység csomópontja működik

    -   A hardveres életciklus-gazdagép kikapcsolása, ha a hardveres életciklus-gazdagépen dolgozik

## <a name="steps"></a>Lépések

1.  Keresse meg a fizikai csomópontot a rackben.

2.  Ellenőrizze a lemez modelljét.

    Ellenőrizze, hogy az új lemez modell támogatott-e a [Microsoft Azure stack hub 14G támogatási mátrix * integrált rendszerében](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D) 
     [](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D).
    Ha a lemez nem a támogatási mátrixban található, akkor másik helyettesítőt kell kérnie.
    
    > [!CAUTION]
    > A támogatási mátrixban nem szereplő lemez beszúrása a karanténba helyezett új lemezen történik.
        
    A modell ellenőrzéséhez keresse meg a címkét, és ellenőrizze, hogy az összetevő megtalálható-e a támogatási mátrixban.
    
3.  Cserélje le a meghibásodott külső tárolóeszköz lemezét.

    Kövesse a lemezmeghajtó-szolgáltató helyettesítési folyamatát a [Dell EMC PowerEdge R640 telepítési és szervizelési kézikönyvében](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) a méretezési egység csomópontjai vagy a hardveres életciklus-gazdagépek esetében.
    
    Emellett a lemezmeghajtók lemezről történő eltávolításához és a lemezmeghajtó-vagy lemezmeghajtó-adapterhez való telepítéséhez szükséges részekre is hivatkoznia kell.
    
4.  A lemez cseréje után ellenőrizze, hogy a belső vezérlőprogram támogatott-e a támogatási mátrix alapján. A lemez \' belső vezérlőprogram-verziójának lekéréséhez tegye a következőket:

    1.  Jelentkezzen be a iDRAC az irányítható IP-cím vagy közvetlenül az iDRAC felület közvetlen USB-kapcsolaton keresztüli elérésével.

    1.  A felső menüben válassza a **tárterület** elemet:

        ![Képernyőkép, amely megjeleníti a iDRAC lapot a kiválasztott "Storage" művelettel.](media/image-30.png)
    
    1.  Keresse meg a lecserélt lemezt, és bontsa ki az elemet. Hasonlítsa össze a lemez **változatát** a támogatási mátrixban található változattal. Ha a lemez nem a támogatási mátrixban található, akkor a probléma megoldásához támogassa a támogatását.

        ![Képernyőkép, amely a iDRAC "Speciális tulajdonságok" oldalát jeleníti meg a "gyártó", a "termék azonosítója" és a "változat" kiemelésével.](media/image-31.png)
        
## <a name="next-steps"></a>Következő lépések

Ha a méretezési egység csomópontot dolgozik:

1.  A skálázási egység állapotának ellenőrzésének befejezése

2.  Fejezze be a skálázási egység csomópontjának állapotát, ha a hardveres életciklus-gazdagépet használja:

    -   A hardver életciklus-gazdagép állapotának ellenőrzése
    
