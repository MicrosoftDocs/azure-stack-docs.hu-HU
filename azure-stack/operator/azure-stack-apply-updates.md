---
title: Alkalmazza a frissítéseket az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan importálhatja, és telepítse a Microsoft frissítési csomagokat egy Azure Stackkel integrált rendszereknél.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 147e6b7555d67b562b44102bfd022e954a87e0ae
ms.sourcegitcommit: 2b6a0b3b4dc63c26df3d0535d630d640ff232fb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521223"
---
# <a name="apply-updates-in-azure-stack"></a>Alkalmazza a frissítéseket az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek*

Használhatja a **frissítése** csempére a alkalmazni a Microsoft- vagy OEM csomagok az Azure Stack a felügyeleti portálon.

Ha egy integrált rendszerek 1807 verzióját használja, vagy a korábban, töltse le a csomag, a csomagfájlok importálása az Azure Stackhez, és a frissítési csomag telepítése. Útmutatásért lásd: [frissítés az Azure Stack úgy, hogy letölti a csomagot](#update-azure-stack-by-downloading-the-package)

Ezek frissítse az Azure Stack integrált rendszerek utasítások használata. Ha az Azure Stack fejlesztői rendszer használ, le kell töltenie az aktuális verzió esetében a telepítőcsomagot. Útmutatásért lásd: [az Azure Stack fejlesztői készletének telepítése](../asdk/asdk-install.md)

## <a name="update-azure-stack"></a>Az Azure Stack frissítése

### <a name="select-and-apply-an-update-package"></a>Válassza ki, és a egy frissítési csomag alkalmazása

1. Nyissa meg a felügyeleti portált.

2. Válassza ki **irányítópult**. Válassza ki a **frissítés** csempére.

    ![Az Azure Stack frissítés érhető el](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Jegyezze fel az Azure Stack jelenlegi verziójával. A következő teljes verzióra frissítheti. Például, ha Ön az Azure Stack 1811 fut, a következő elérhető verziója van-e 1901.

    ![Az Azure Stack szoftverfrissítést az alkalmazása](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. Jelölje ki a következő elérhető verzió a frissítések listájához. Választhat **nézet** , a kibocsátási megjegyzések oszlop nyissa meg a kibocsátási megjegyzések a témakör a verzió, ha szeretné tekintse át a verzió módosításokat.

5. Most jelölje ki a frissítést. A frissítés elindul.

### <a name="review-update-history"></a>Tekintse át a frissítési előzmények

1. Nyissa meg a felügyeleti portált.

2. Válassza ki **irányítópult**. Válassza ki a **frissítés** csempére.

3. Válassza ki **-előzmények frissítéséhez**.

![Az Azure Stack-frissítési előzményei](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Frissítés az Azure Stack úgy, hogy letölti a csomagot

Ha egy integrált rendszerek 1807 verzióját használja, vagy a korábban, töltse le a csomag, a csomagfájlok importálása az Azure Stackhez, és a frissítési csomag telepítése.

## <a name="download-the-update-package"></a>A frissítési csomag letöltése

Azure Stack egy Microsoft- vagy OEM frissítési csomag nem érhető el, letölti a csomagot olyan helyre, amely az Azure Stack-ból érhetők el, és tekintse át a csomag tartalma. A következő fájlok általában tartalmaz egy frissítési csomagot:

- Egy önkicsomagoló `<PackageName>.zip` fájlt. Ez a fájl tartalmazza a frissítés, például a legújabb kumulatív frissítés a Windows Server hasznos.

- Megfelelő `<PackageName>.bin` fájlokat. Ezeket a fájlokat, adja meg, amelyhez társítva van a tartalom tömörítése a *PackageName*.zip-fájlt.

- A `Metadata.xml` fájlt. Ez a fájl tartalmazza a frissítés, például a közzétevő, név, előfeltételként szükséges szoftvert, mérete és támogatási elérési út URL-CÍMÉT alapvető adatait.

> [!IMPORTANT]  
> Az Azure Stack 1901 frissítési csomag alkalmazása után az Azure Stack frissítési csomagokat a csomagolási formátumot áthelyezi a .zip, .bin(s), és a egy .zip(s) .xml formátumú és .xml formátumú. Stampek csatlakozott az Azure Stack-operátorok nem lesz hatással. Az Azure Stack-operátorok leválasztott egyszerűen importálni fogja az .xml és a .zip fájl az alább leírt eljárást.

## <a name="import-and-install-updates"></a>Importálhatja és a frissítések telepítése

Az alábbi eljárás bemutatja, hogyan importálhatja és frissítési csomagok telepítése a felügyeleti portálon.

> [!IMPORTANT]  
> Javasoljuk, hogy a karbantartási műveleteket felhasználók értesítése, és hogy úgy ütemezze a normál karbantartási időszakok során munkaidőn kívüli lehető legnagyobb mértékben. Karbantartási műveletek hatással lehet a felhasználó számítási feladatok és a webportálos műveletek.

1. A felügyeleti portálon, válassza ki a **minden szolgáltatás**. Ezt követően a **adatok + tárolás** kategória, jelölje be **tárfiókok**. (Vagy a szűrőmezőbe, kezdje el begépelni **tárfiókok**, és válassza ki azt.)

    ![Bemutatja, hogy hol található a storage-fiókok a portálon](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Írja be a Szűrő mezőbe **frissítése**, és válassza ki a **updateadminaccount** storage-fiókot.

3. A Storage-fiók részleteit, a **szolgáltatások**válassza **Blobok**.
 
    ![Bemutatja a blobok a storage-fiók beszerzése](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. A **Blob service**válassza **+ tároló** tároló létrehozásához. Adjon meg egy nevet (például *update-1811*), majd válassza ki **OK**.
 
     ![Bemutatja, hogyan tároló felvétele a storage-fiókban](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. A tároló létrehozása után kattintson a tároló nevére, és kattintson **feltöltése** a csomag fájlok feltöltése a tárolóba.
 
    ![Bemutatja, hogyan tölthet fel a csomagfájlok](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. A **blob feltöltése**, kattintson a mappa ikonra, és keresse meg a frissítési csomagot .zip-fájlt, majd kattintson a **nyílt** a fájl explorer-ablakban.
  
7. A **blob feltöltése**, kattintson a **feltöltése**.
  
    ![Látható minden alkalmazáscsomag-fájl feltöltése](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Ismételje meg a 6 és 7 a *PackageName*állapotára vonatkozó .bin és Metadata.xml fájlt. Ha tartalmazza, ne importálja a kiegészítő Notice.txt fájlt. Megjegyzés: a fájlok lesznek 1901 díjtól .zip .bin és a .zip - adatkötetekkel továbbra is a szokásos módon importálja az .xml.

9. Ha elkészült, az értesítések (Harang ikon a portál jobb felső sarokban) tekintheti meg. Az értesítés jelzi, hogy a feltöltés befejeződött.
10. Lépjen vissza a frissítés csempét az irányítópulton. A csempe kell azt jelzik, hogy a frissítés érhető el. Kattintson a csempére, és tekintse át az újonnan hozzáadott frissítési csomag.
11. Telepítheti a frissítést, válassza ki a csomagot, amely van megjelölve **készen áll** vagy kattintson a jobb gombbal a csomagot, és kattintson **frissítés most**, vagy kattintson a **frissítés most** tetejénél művelet .
12. Frissítési csomag telepítése gombra kattint, megtekintheti az állapotát a **frissítési menetet részletek** területen. Itt is kattinthat **teljes naplók letöltéséhez** letöltéséhez a rendszernapló fájljaiban.
13. A frissítés befejeződött, a frissítés csempét jelenít meg a frissített Azure Stack-verzió.

Manuálisan törölheti frissítések a storage-fiókból az Azure Stack telepítése után. Az Azure Stack rendszeresen keresi a korábbi frissítési csomagokat, és eltávolítja azokat a storage-ból. Azure Stack is igénybe vehet, távolítsa el a régi csomagot két hét.

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése az Azure Stackben – Áttekintés](azure-stack-updates.md)
- [Azure Stack-karbantartási szabályzat](azure-stack-servicing-policy.md)
