---
title: Azure Stack hub-frissítési csomag előkészítése
description: Útmutató Azure Stack hub-frissítési csomag előkészítéséhez.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: fd31aa7b6d45243a71ec4ba690a39497b386ef8e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696736"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Azure Stack hub-frissítési csomag előkészítése

Ez a cikk áttekintést nyújt Azure Stack hub frissítési csomagjainak előkészítéséről, hogy azok a Azure Stack hub-környezet frissítéséhez is használhatók legyenek. Ez a folyamat a következőkből áll:

- [A frissítési csomag letöltése](#download-the-update-package)
- [A frissítési csomag importálása a Azure Stack hub-környezetbe az Azure Stack hub felügyeleti portálján keresztül](#import-and-install-updates)

Azokon a rendszereken, amelyek képesek az automatikus frissítési végpontokhoz való kapcsolódásra, az Azure Stack hub-szoftverfrissítések és-gyorsjavítások automatikusan letöltődnek és felkészültek. A kapcsolat nélküli rendszerek és az OEM-ből származó bármely frissítés esetén a frissítési csomagot a jelen témakörben ismertetett módon kell előkészíteni.  

A következő táblázat azt mutatja be, hogy a frissítési csomagok manuális előkészítést igényelnek, és automatikusan készüljön fel.

| Frissítés típusa | Kapcsolatok | Beavatkozás szükséges |
| --- | --- | --- |
| Azure Stack hub-szoftverfrissítések | Csatlakozva | A rendszer automatikusan letölti és előkészíti a frissítést a frissítés alkalmazása után. |
| Azure Stack hub-gyorsjavítások | Csatlakozva | A rendszer automatikusan letölti és előkészíti a frissítést a frissítés alkalmazása után. |
| OEM-csomagok frissítései | Csatlakozva | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| Azure Stack hub-szoftverfrissítések | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| Azure Stack hub-gyorsjavítások | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| OEM-csomagok frissítései | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |

## <a name="download-the-update-package"></a>A frissítési csomag letöltése
A Azure Stack hub frissítéseinek és gyorsjavításának frissítési csomagja a csatlakoztatott rendszerek frissítés paneljén keresztül érhető el. A csomagot le kell töltenie, és át kell helyeznie egy olyan helyre, amely elérhető az Azure Stack hub-példány számára, ha OEM-csomagot frissít, vagy ha egy leválasztott rendszer támogatását támogatja. Előfordulhat, hogy le kell töltenie a csomagot, majd fel kell töltenie egy elérhető helyre, ha a rendszer egy időszakos kapcsolatban álló rendszert futtat.

Tekintse át a csomag tartalmát. A frissítési csomagok általában a következő fájlokból állnak:

-   **A önkicsomagoló \<PackageName >. zip fájl**. Ez a fájl tartalmazza a frissítés hasznos adatait.
- **Egy metaadat. xml fájl**. Ez a fájl lényeges információt tartalmaz a frissítésről, például a közzétevőről, a név, az előfeltétel, a méret és a támogatási útvonal URL-címéről.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Frissítési csomagok automatikus letöltése és előkészítése
A Azure Stack hub-szoftverfrissítések és-gyorsjavítások automatikusan készülnek a **Azure stack hub automatikus frissítési végpontokkal**létesített kapcsolattal rendelkező rendszerekhez: https://*. azureedge. net és https://aka.ms/azurestackautomaticupdate. Az **Azure stack hub automatikus frissítési végpontokhoz**való kapcsolódás beállításával kapcsolatos további információkért tekintse meg a [Azure stack hub tűzfal-integráció](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound) című témakörben ismertetett **javítást és frissítési** végpontokat.

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Honnan tölthetők le Azure Stack hub frissítési csomagjai

A [teljes és expressz frissítések](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) Azure stack hub-frissítései egy biztonságos Azure-végponton futnak. A csatlakoztatott példányokkal rendelkező Azure Stack hub-operátorok [automatikusan megjelennek a felügyeleti portálon a Azure stack hub frissítései](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Az internetes leválasztott rendszerek vagy a gyenge internetkapcsolattal rendelkező rendszerek esetében a frissítési csomagok a [Azure stack hub Updates Downloader eszköz](https://aka.ms/azurestackupdatedownload)használatával tölthetők le. Az Azure Stack hub szoftverfrissítési csomagjai tartalmazhatják Azure Stack hub-szolgáltatások frissítéseit, valamint az Azure Stack hub méretezési egységei operációs rendszerének frissítéseit is.

>[!NOTE]
>Maga a frissítési csomag és annak tartalma (például bináris fájlok, PowerShell-parancsfájlok stb.) a Microsoft tulajdonában lévő tanúsítványokkal van aláírva. A csomag módosításával az aláírás érvénytelenné válik.


### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Honnan tölthetők le Azure Stack hub gyorsjavítási csomagjai

Az [Azure stack hub-gyorsjavítások](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) csomagja ugyanazon a biztonságos Azure-végponton fut, mint Azure stack hub-frissítésekhez. A csatlakoztatott példányokkal rendelkező Azure Stack hub-operátorok [automatikusan megjelennek a felügyeleti portálon a Azure stack hub frissítései](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Ezeket a megfelelő gyorsjavítási TUDÁSBÁZIS cikkeiben található beágyazott hivatkozásokkal töltheti le, például [Azure stack hub gyorsjavítási 1.1906.11.52](https://support.microsoft.com/help/4515650). A gyorsjavításokat az Azure Stack hub verziójának megfelelő kibocsátási megjegyzésekben találja.

### <a name="where-to-download-oem-update-packages"></a>Honnan tölthetők le az OEM-frissítési csomagok
Az OEM-gyártó a frissítéseket is kiadhatja, például az illesztőprogram-és a belső vezérlőprogram-frissítéseket. Habár ezek a frissítések különálló OEM- [csomagokként](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) jelennek meg a hardver gyártójától, azok továbbra is importálhatók, települnek és kezelhetők ugyanúgy, mint a Microsoft frissítési csomagjai. A szállítói kapcsolattartási hivatkozások listáját az [Apply Azure stack hub eredeti berendezésgyártó (OEM) frissítéseinél](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information)tekintheti meg.

## <a name="import-and-install-updates"></a>Frissítések importálása és telepítése

Az alábbi eljárás bemutatja, hogyan importálhat és telepíthet frissítési csomagokat a felügyeleti portálon.

> [!Important]  
> Értesítse a felhasználókat a karbantartási műveletekről, és a szokásos karbantartási időszakokat a munkaidőn kívül, a lehető legnagyobb mértékben ütemezze. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

1.  Az adminisztrációs portálon válassza a **minden szolgáltatás**lehetőséget. Ezután az **adat + tárolás** kategóriában válassza a Storage- **fiókok**lehetőséget. (Vagy a szűrő mezőbe írja be a Storage- **fiókok**kifejezést, majd válassza ki.)

    ![Azure Stack hub frissítése](./media/azure-stack-update-prepare-package/image1.png) 

1.  A szűrő mezőbe írja be a **frissítés**értéket, majd válassza ki a **updateadminaccount** .

2.  A Storage-fiók adatai alatt, a **szolgáltatások**területen válassza a **Blobok**elemet.

    ![Azure Stack hub frissítése – blob](./media/azure-stack-update-prepare-package/image2.png)

1.  A tároló létrehozásához a **blob Service**alatt válassza a **+ tároló** elemet. Adjon meg egy nevet (például *Update-1811*), majd kattintson **az OK gombra**.

    ![Azure Stack hub frissítése – tároló](./media/azure-stack-update-prepare-package/image3.png)

1.  A tároló létrehozása után kattintson a tároló nevére, majd kattintson a **feltöltés** gombra a csomagfájl tárolóba való feltöltéséhez.

    ![Azure Stack hub frissítése – feltöltés](./media/azure-stack-update-prepare-package/image4.png)

1.  A **blob feltöltése**területen kattintson a mappa ikonra, keresse meg a frissítési csomag. zip fájlját, majd kattintson a **Megnyitás** gombra a fájlkezelő ablakban.

2.  A **blob feltöltése**területen kattintson a **feltöltés**elemre.

    ![Azure Stack hub frissítése – blob feltöltése](./media/azure-stack-update-prepare-package/image5.png)

1.  Ismételje meg a 6. és 7. lépést a metaadat. xml fájlhoz és a frissítési csomagban található további. zip fájlokhoz. Ha belefoglalt, ne importálja a kiegészítő értesítés. txt fájlt.

2.  Ha elkészült, tekintse át az értesítéseket (harang ikon a portál jobb felső sarkában). Az értesítéseknek jelezniük kell, hogy a feltöltés befejeződött.

3.  Váltson vissza az irányítópult frissítés paneljére. A panelnek jeleznie kell, hogy van elérhető frissítés. Ez azt jelzi, hogy a frissítés sikeresen előkészített. Az újonnan hozzáadott frissítési csomag áttekintéséhez kattintson a panelre.

4.  A frissítés telepítéséhez jelölje ki a **készként** megjelölt csomagot, vagy kattintson a jobb gombbal a csomagra, majd válassza a **Frissítés most**lehetőséget, vagy kattintson a **Frissítés most** gombra a felső részen.

5.  A frissítési csomag telepítése lehetőségre kattintva megtekintheti az állapotot a **frissítési Futtatás részletei** területen. Innen az **Összefoglalás letöltése** lehetőségre kattintva letöltheti a naplófájlokat. A frissítési kísérletek naplói a kísérlet befejezése után 6 hónapig elérhetők.

6.  Ha a frissítés befejeződött, a frissítés panelen látható a frissített Azure Stack hub verziója.

Manuálisan törölheti a frissítéseket a Storage-fiókból, miután telepítette azokat Azure Stack hubhoz. Azure Stack hub rendszeresen ellenőrzi a régebbi frissítési csomagokat, és eltávolítja azokat a tárolóból. A régi csomagok eltávolításához a Azure Stack hub két hetet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

[A frissítés alkalmazása](azure-stack-apply-updates.md)
