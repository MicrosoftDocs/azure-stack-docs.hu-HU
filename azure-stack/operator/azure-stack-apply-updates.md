---
title: Frissítések alkalmazása a Azure Stackban | Microsoft Docs
description: Ismerje meg, hogyan importálhat és telepíthet Microsoft Update-csomagokat egy Azure Stack integrált rendszerhez.
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
ms.openlocfilehash: 04494c3f394fb5a3b836c8fcf67cd02fb2900910
ms.sourcegitcommit: 4f3e161e7632c8a6e3d41946b09f22b5bdb08d36
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413184"
---
# <a name="apply-updates-in-azure-stack"></a>Frissítések alkalmazása Azure Stack

*Vonatkozik: Integrált rendszerek Azure Stack*

A felügyeleti portál **Update (frissítés** ) csempéje segítségével Microsoft-vagy OEM-frissítési csomagokat alkalmazhat a Azure stack.

Ha a 1807-es vagy korábbi verziójú integrált rendszerek valamelyikét használja, le kell töltenie a frissítési csomagot, importálnia kell a csomagokat a Azure Stackba, majd telepítenie kell a frissítési csomagot. Útmutatásért lásd: [Azure stack frissítése a csomag letöltésével](#update-azure-stack-by-downloading-the-package)

Ezek a frissítési utasítások Azure Stack integrált rendszerekkel működnek. Ha a Azure Stack fejlesztői rendszert használja, le kell töltenie a jelenlegi verzióra vonatkozó telepítőcsomagot. Útmutatásért lásd: [a Azure stack Development Kit telepítése](../asdk/asdk-install.md)

## <a name="update-azure-stack"></a>Azure Stack frissítése

### <a name="select-and-apply-an-update-package"></a>Frissítési csomag kiválasztása és alkalmazása

1. Nyissa meg a felügyeleti portált.

2. Válassza az **irányítópult**lehetőséget. Válassza a **frissítés** csempét.

    ![Azure Stack frissítés érhető el](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Jegyezze fel a Azure Stack aktuális verzióját. A következő teljes verzióra frissíthet. Ha például a Azure Stack 1811-es verzióját futtatja, a következő kiadott verzió a 1901.

    ![Azure Stack Update alkalmazás](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. A frissítések listájában válassza ki a következő elérhető verziót. A kiadási megjegyzések oszlopban a **nézet** lehetőségre kattintva megnyithatja a verzióhoz tartozó kibocsátási megjegyzések témakört, ha szeretné áttekinteni a verzió módosításait.

5. Válassza a frissítés most lehetőséget. Ekkor elindul a frissítés.

### <a name="review-update-history"></a>Frissítési előzmények áttekintése

1. Nyissa meg a felügyeleti portált.

2. Válassza az **irányítópult**lehetőséget. Válassza a **frissítés** csempét.

3. Válassza a **frissítési előzmények**lehetőséget.

![Azure Stack frissítési előzmények](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Azure Stack frissítése a csomag letöltésével

Ha a 1807-es vagy korábbi verziójú integrált rendszerek valamelyikét használja, le kell töltenie a frissítési csomagot, importálnia kell a csomagokat a Azure Stackba, majd telepítenie kell a frissítési csomagot.

## <a name="download-the-update-package"></a>A frissítési csomag letöltése

Ha elérhetővé válik egy Microsoft-vagy OEM-frissítési csomag a Azure Stackhoz, töltse le a csomagot egy Azure Stack elérhető helyre, és tekintse át a csomag tartalmát. A frissítési csomagok általában a következő fájlokból állnak:

- Egy önkicsomagoló `<PackageName>.zip` fájl. Ez a fájl tartalmazza a frissítés hasznos adatait, például a Windows Server legújabb összegző frissítését.

- Megfelelő `<PackageName>.bin` fájlok. Ezek a fájlok tömörítést biztosítanak a *PackageName*. zip fájlhoz társított hasznos adatokhoz.

- Egy `Metadata.xml` fájl. Ez a fájl lényeges információkat tartalmaz a frissítésről, például a közzétevőt, a nevet, az előfeltételt, a méretet és a támogatási útvonal URL-címét.

> [!IMPORTANT]  
> A Azure Stack 1901 frissítési csomag alkalmazása után a Azure Stack frissítési csomagok csomagolási formátuma. zip,. bin (s) és. XML formátumúról. zip (s) és. XML formátumúra kerül. Azure Stack csatlakoztatott bélyegzőket tartalmazó operátorok nem lesznek hatással. Azure Stack leválasztott operátorok egyszerűen csak az alább ismertetett eljárással importálják az. XML és a. zip fájlt (ka) t.

## <a name="import-and-install-updates"></a>Frissítések importálása és telepítése

Az alábbi eljárás bemutatja, hogyan importálhat és telepíthet frissítési csomagokat a felügyeleti portálon.

> [!IMPORTANT]  
> Javasoljuk, hogy minden karbantartási műveletről értesítse a felhasználókat, és a szokásos karbantartási időszakokat a lehető legnagyobb mértékben ütemezze a munkaidőn kívüli időpontokban. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

1. A felügyeleti portálon válassza a **minden szolgáltatás**lehetőséget. Ezután az **adat + tárolás** kategóriában válassza a Storage- **fiókok**lehetőséget. (Vagy a szűrő mezőbe írja be a Storage- **fiókok**kifejezést, majd válassza ki.)

    ![Itt látható, hogy hol található a Storage-fiókok a portálon](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. A szűrő mezőbe írja be a **frissítés**értéket, majd válassza ki a **updateadminaccount** .

3. A Storage-fiók adatai alatt, a **szolgáltatások**területen válassza a **Blobok**elemet.
 
    ![Bemutatja, hogyan érheti el a blobokat a Storage-fiókhoz](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. A tároló létrehozásához a **blob Service**alatt válassza a **+ tároló** elemet. Adjon meg egy nevet (például *Update-1811*), majd kattintson **az OK gombra**.
 
     ![Bemutatja, hogyan adhat hozzá tárolót a Storage-fiókhoz](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. A tároló létrehozása után kattintson a tároló nevére, majd kattintson a **feltöltés** gombra a csomagfájl tárolóba való feltöltéséhez.
 
    ![Bemutatja, hogyan tölthetők fel a csomagok fájljai](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. A **blob feltöltése**területen kattintson a mappa ikonra, keresse meg a frissítési csomag. zip fájlját, majd kattintson a **Megnyitás** gombra a fájlkezelő ablakban.
  
7. A **blob feltöltése**területen kattintson a **feltöltés**elemre.
  
    ![Megjeleníti az egyes csomagfájl feltöltésének helyét](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Ismételje meg a 6. és 7. lépést a *PackageName*. bin és a metadata. XML fájlok esetében. Ha belefoglalt, ne importálja a kiegészítő értesítés. txt fájlt. Vegye figyelembe, hogy a fájlok. zip a 1901-as, a. bin és a. zip kiterjesztéssel kezdődően. az. xml fájlt továbbra is a szokásos módon importálja.

9. Ha elkészült, tekintse át az értesítéseket (harang ikon a portál jobb felső sarkában). Az értesítéseknek jelezniük kell, hogy a feltöltés befejeződött.
10. Váltson vissza az irányítópult Update csempére. A csempének jeleznie kell, hogy van elérhető frissítés. Kattintson a csempére az újonnan hozzáadott frissítési csomag áttekintéséhez.
11. A frissítés telepítéséhez jelölje ki a készként megjelölt csomagot, vagy  kattintson a jobb gombbal a csomagra, majd válassza a **Frissítés most**lehetőséget, vagy kattintson a **Frissítés most** gombra a felső részen.
12. A frissítési csomag telepítése lehetőségre kattintva megtekintheti az állapotot a **frissítési Futtatás részletei** területen. Innen az **Összefoglalás letöltése** lehetőségre kattintva letöltheti a naplófájlokat. A frissítési kísérletek naplói a kísérlet befejezése után 6 hónapig elérhetők. 
13. Ha a frissítés befejeződött, a frissítés csempe megjeleníti a frissített Azure Stack verziót.

Manuálisan is törölheti a frissítéseket a Storage-fiókból, miután telepítette azokat a Azure Stackra. Azure Stack rendszeresen ellenőrzi a régebbi frissítési csomagokat, és eltávolítja azokat a tárolóból. A régi csomagok eltávolítása Azure Stack két hétig is eltarthat.

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése az Azure Stackben – Áttekintés](azure-stack-updates.md)
- [Azure Stack-karbantartási szabályzat](azure-stack-servicing-policy.md)
