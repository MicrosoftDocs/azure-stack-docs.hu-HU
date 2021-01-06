---
title: Belső vezérlőprogram telepítése a iDRAC használatával
description: Ismerje meg, hogyan telepítheti a belső vezérlőprogramot a iDRAC használatával
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3b6a257d5f5f42c951313c2220559306577de15d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910244"
---
# <a name="installing-firmware-using-the-idrac-interface"></a>A belső vezérlőprogram telepítése a iDRAC felület használatával

Az integrált Dell távelérési kártya (iDRAC) lehetővé teszi a belső vezérlőprogram távoli frissítését (egy-egy) a **frissítés és a visszaállítás** funkció használatával. Akkor is működik, ha a kiszolgáló fut.

Nyissa meg a [Microsoft Azure stack hub 14G dokumentációs oldalának integrált rendszerét](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs) , ahol letöltheti a legújabb támogatási mátrixot, amely tartalmazza a támogatott belső vezérlőprogram-verziók listáját.

Az alábbi eljárással frissítheti az egyetlen eszköz belső vezérlőprogramot a iDRAC web interface használatával.

**Lépések**

1.  Nyissa meg a **karbantartási** \* * rendszerfrissítés * * programot. Megjelenik a **belső vezérlőprogram frissítése** oldal.

    ![Képernyőkép a "karbantartás" oldaláról egy "integrált Dell távelérési vezérlő 9 | Enterprise ".](media/image-85.png)

2.  A **frissítés** lapon válassza a **helyi** lehetőséget a fájl helyeként.

3.  Válassza a **Tallózás** lehetőséget, válassza ki a szükséges összetevő belső vezérlőprogram-rendszerképét, majd válassza a **feltöltés** lehetőséget.

4.  A feltöltés befejezését követően a **frissítés részletei** szakasz megjeleníti a iDRAC feltöltött összes belső vezérlőprogram-fájlt és annak állapotát. Ha a belső vezérlőprogram lemezképfájlja érvényes és sikeresen fel lett töltve, a **tartalom** oszlop a belső vezérlőprogram képfájljának neve melletti (+) ikont jeleníti meg. A név kibontásával megtekintheti az **eszköz nevét**, az **aktuális** és a **rendelkezésre álló belső vezérlőprogram-verzió** információit.

5.  Válassza ki a szükséges belső vezérlőprogram-fájlt, és tegye a következők egyikét:

    -   A gazdagéprendszer újraindítását nem igénylő belső vezérlőprogram-lemezképek esetében válassza a **telepítés** lehetőséget.

    -   A gazda rendszer újraindítását igénylő belső vezérlőprogram-lemezképek esetében válassza a **telepítés és újraindítás** vagy a **következő újraindítás telepítése** lehetőséget.

    -   A belső vezérlőprogram frissítésének megszakításához válassza a **Mégse** lehetőséget.

6.  A **feladatok várólistájának** megjelenítéséhez válassza a **feladatok várólistája** lehetőséget. Ezen a lapon megtekintheti és kezelheti az előkészített belső vezérlőprogram frissítéseit, vagy kiválaszthatja a

    **Az aktuális** lap frissítéséhez és a belső vezérlőprogram frissítési állapotának megtekintéséhez.
    
