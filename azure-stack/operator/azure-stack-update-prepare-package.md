---
title: Azure Stack frissítési csomag előkészítése | Microsoft Docs
description: Útmutató Azure Stack frissítési csomag előkészítéséhez.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: justinha
ms.lastreviewed: 09/03/2019
ms.reviewer: ppacent
ms.openlocfilehash: 9b58b4911a575ef66c95594b6cb4cd1cc9e27a43
ms.sourcegitcommit: 314fd74caf356b157583d38d2b8b1dee30408b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234996"
---
# <a name="prepare-an-azure-stack-update-package"></a>Azure Stack frissítési csomag előkészítése

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk áttekintést nyújt Azure Stack frissítési csomagok előkészítéséről, hogy azok a Azure Stack-környezet frissítéséhez használhatók legyenek. Ez a folyamat a következőkből áll:

- [A frissítési csomag letöltése](#download-the-update-package)
- [A frissítési csomag importálása a Azure Stack-környezetbe a Azure Stack felügyeleti portálon keresztül](#import-and-install-updates)

Azokon a rendszereken, amelyek csatlakozhatnak az automatikus frissítési végpontokhoz, Azure Stack szoftverfrissítések és gyorsjavítások automatikusan letöltődnek és felkészültek. A kapcsolat nélküli rendszerek és az OEM-ből származó bármely frissítés esetén a frissítési csomagot a jelen témakörben ismertetett módon kell előkészíteni.  

A következő táblázat azt mutatja be, hogy a frissítési csomagok manuális előkészítést igényelnek, és automatikusan készüljön fel.

| Frissítés típusa | Kapcsolat | Beavatkozás szükséges |
| --- | --- | --- |
| Szoftverfrissítések Azure Stack | Csatlakozva | A rendszer automatikusan letölti és előkészíti a frissítést a frissítés alkalmazása után. |
| Azure Stack gyorsjavítások | Csatlakozva | A rendszer automatikusan letölti és előkészíti a frissítést a frissítés alkalmazása után. |
| OEM-csomagok frissítései | Csatlakozva | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| Szoftverfrissítések Azure Stack | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| Azure Stack gyorsjavítások | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| OEM-csomagok frissítései | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |

## <a name="download-the-update-package"></a>A frissítési csomag letöltése
A Azure Stack frissítések és gyorsjavítások frissítési csomagja a csatlakoztatott rendszerek frissítés paneljén keresztül érhető el. Le kell töltenie a csomagot, és át kell helyeznie a csomagot egy olyan helyre, amely elérhető a Azure Stack példánya számára, ha OEM-csomagot frissít, vagy ha egy leválasztott rendszer támogatását támogatja. Előfordulhat, hogy le kell töltenie a csomagot, majd fel kell töltenie egy elérhető helyre, ha a rendszer egy időszakos kapcsolatban álló rendszert futtat.

>[!NOTE]
>Maga a frissítési csomag és annak tartalma (például bináris fájlok, PowerShell-parancsfájlok stb.) a Microsoft tulajdonában lévő tanúsítványokkal van aláírva. A csomag módosításával az aláírás érvénytelenné válik.

Tekintse át a csomag tartalmát. A frissítési csomagok általában a következő fájlokból állnak:

-   **Egy önkicsomagoló \<PackageName >. zip fájl**. Ez a fájl tartalmazza a frissítés hasznos adatait.
- **Egy metaadat. xml fájl**. Ez a fájl lényeges információt tartalmaz a frissítésről, például a közzétevőről, a név, az előfeltétel, a méret és a támogatási útvonal URL-címéről.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Frissítési csomagok automatikus letöltése és előkészítése
Azure Stack szoftverfrissítések és gyorsjavítások automatikus előkészítése a **Azure stack automatikus frissítési végpontokkal**létesített kapcsolattal rendelkező rendszerekhez: https://*. azureedge. net és https://aka.ms/azurestackautomaticupdate. Az **Azure stack automatikus frissítési végpontokhoz**való kapcsolódás beállításával kapcsolatos további információkért tekintse meg a [Azure stack tűzfal-integráció](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound) című részben ismertetett **javítást és frissítési** végpontokat.

### <a name="where-to-download-azure-stack-update-packages"></a>Honnan tölthetők le Azure Stack frissítési csomagok

A [teljes és expressz frissítések](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) Azure stack frissítései egy biztonságos Azure-végponton futnak. Azure Stack csatlakoztatott példányokkal rendelkező operátorok [automatikusan megjelennek a felügyeleti portálon a Azure stack frissítései](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Az internetes leválasztott rendszerek vagy a gyenge internetkapcsolattal rendelkező rendszerek esetében a frissítési csomagok a [Azure stack Updates Downloader eszközzel](https://aka.ms/azurestackupdatedownload)tölthetők le. Azure Stack szoftverfrissítési csomagok tartalmazhatják Azure Stack szolgáltatások frissítéseit, valamint a Azure Stack skálázási egységei operációs rendszerének frissítéseit is.

### <a name="where-to-download-azure-stack-hotfix-packages"></a>Honnan tölthetők le Azure Stack gyorsjavítási csomagok

A [Azure stack gyorsjavítások](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) csomagja ugyanabban a biztonságos Azure-végponton fut, mint Azure stack frissítésekhez. Azure Stack csatlakoztatott példányokkal rendelkező operátorok [automatikusan megjelennek a felügyeleti portálon a Azure stack frissítései](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Ezeket a megfelelő gyorsjavítási TUDÁSBÁZIS cikkeiben található beágyazott hivatkozások használatával töltheti le. például [Azure stack gyorsjavítások 1.1906.11.52](https://support.microsoft.com/help/4515650). A gyorsjavításokat a Azure Stack verziójához tartozó kibocsátási megjegyzésekben találja. OEM hardver szállítója – megadott frissítések

### <a name="where-to-download-oem-update-packages"></a>Honnan tölthetők le az OEM-frissítési csomagok
Az OEM-gyártó a frissítéseket is kiadhatja, például az illesztőprogram-és a belső vezérlőprogram-frissítéseket. Habár ezek a frissítések különálló OEM- [csomagokként](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) jelennek meg a hardver gyártójától, azok továbbra is importálhatók, települnek és kezelhetők ugyanúgy, mint a Microsoft frissítési csomagjai. A szállítói kapcsolattartási hivatkozások listáját az [Apply Azure stack Original Equipment Manufacturer (OEM) frissítéseinél](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information)találja.

## <a name="import-and-install-updates"></a>Frissítések importálása és telepítése

Az alábbi eljárás bemutatja, hogyan importálhat és telepíthet frissítési csomagokat a felügyeleti portálon.

> [!Important]  
> Értesítse a felhasználókat a karbantartási műveletekről, és a szokásos karbantartási időszakokat a munkaidőn kívül, a lehető legnagyobb mértékben ütemezze. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

1.  Az adminisztrációs portálon válassza a **minden szolgáltatás**lehetőséget. Ezután az **adat + tárolás** kategóriában válassza a Storage- **fiókok**lehetőséget. (Vagy a szűrő mezőbe írja be a Storage- **fiókok**kifejezést, majd válassza ki.)

    ![Azure Stack frissítés](./media/azure-stack-update-prepare-package/image1.png) 

1.  A szűrő mezőbe írja be a **frissítés**értéket, majd válassza ki a **updateadminaccount** .

2.  A Storage-fiók adatai alatt, a **szolgáltatások**területen válassza a **Blobok**elemet.

    ![Azure Stack Update-blob](./media/azure-stack-update-prepare-package/image2.png)

1.  A tároló létrehozásához a **blob Service**alatt válassza a **+ tároló** elemet. Adjon meg egy nevet (például *Update-1811*), majd kattintson **az OK gombra**.

    ![Azure Stack Update-Container](./media/azure-stack-update-prepare-package/image3.png)

1.  A tároló létrehozása után kattintson a tároló nevére, majd kattintson a **feltöltés** gombra a csomagfájl tárolóba való feltöltéséhez.

    ![Azure Stack frissítés – feltöltés](./media/azure-stack-update-prepare-package/image4.png)

1.  A **blob feltöltése**területen kattintson a mappa ikonra, keresse meg a frissítési csomag. zip fájlját, majd kattintson a **Megnyitás** gombra a fájlkezelő ablakban.

2.  A **blob feltöltése**területen kattintson a **feltöltés**elemre.

    ![Azure Stack frissítés – blob feltöltése](./media/azure-stack-update-prepare-package/image5.png)

1.  Ismételje meg a 6. és 7. lépést a metaadat. xml fájlhoz és a frissítési csomagban található további. zip fájlokhoz. Ha belefoglalt, ne importálja a kiegészítő értesítés. txt fájlt.

2.  Ha elkészült, tekintse át az értesítéseket (harang ikon a portál jobb felső sarkában). Az értesítéseknek jelezniük kell, hogy a feltöltés befejeződött.

3.  Váltson vissza az irányítópult frissítés paneljére. A panelnek jeleznie kell, hogy van elérhető frissítés. Ez azt jelzi, hogy a frissítés sikeresen előkészített. Az újonnan hozzáadott frissítési csomag áttekintéséhez kattintson a panelre.

4.  A frissítés telepítéséhez jelölje ki a **készként** megjelölt csomagot, vagy kattintson a jobb gombbal a csomagra, majd válassza a **Frissítés most**lehetőséget, vagy kattintson a **Frissítés most** gombra a felső részen.

5.  A frissítési csomag telepítése lehetőségre kattintva megtekintheti az állapotot a **frissítési Futtatás részletei** területen. Innen az **Összefoglalás letöltése** lehetőségre kattintva letöltheti a naplófájlokat. A frissítési kísérletek naplói a kísérlet befejezése után 6 hónapig elérhetők.

6.  Ha a frissítés befejeződött, a frissítés panelen látható a frissített Azure Stack verziója.

Manuálisan is törölheti a frissítéseket a Storage-fiókból, miután telepítette azokat a Azure Stackra. Azure Stack rendszeresen ellenőrzi a régebbi frissítési csomagokat, és eltávolítja azokat a tárolóból. A régi csomagok eltávolítása Azure Stack két hétig is eltarthat.

## <a name="next-steps"></a>További lépések

[A frissítés alkalmazása](azure-stack-apply-updates.md)
