---
title: Frissítési csomag előkészítése Azure Stack központban
description: Megtudhatja, hogyan készítheti elő a frissítési csomagot Azure Stack hub-ban.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 4e6ce21088a95677907c978abf5e89612488381f
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374674"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Azure Stack hub-frissítési csomag előkészítése

Ez a cikk áttekintést nyújt Azure Stack hub frissítési csomagjainak előkészítéséről, hogy azok a Azure Stack hub-környezet frissítéséhez használhatók legyenek. Ez a folyamat a következőkből áll:

- [A frissítési csomag letöltése](#download-the-update-package).
- [A frissítési csomag importálása a Azure stack hub-környezetbe az Azure stack hub felügyeleti portálján keresztül](#import-and-install-updates).

Azokon a rendszereken, amelyek képesek az automatikus frissítési végpontokhoz való kapcsolódásra, az Azure Stack hub-szoftverfrissítések és-gyorsjavítások automatikusan letöltődnek és felkészültek. A kapcsolat nélküli rendszereken és az eredeti berendezésgyártó (OEM) minden frissítéséhez a frissítési csomagot a jelen témakörben ismertetett módon kell előkészíteni.  

A következő táblázat azt mutatja be, hogy a frissítési csomagok manuális előkészítést és automatikus felkészülést igényelnek.

| Frissítés típusa | Kapcsolatok | Beavatkozás szükséges |
| --- | --- | --- |
| Azure Stack hub-szoftverfrissítések | Csatlakozva | A rendszer automatikusan letölti és előkészíti a frissítést a frissítés alkalmazása után. |
| Azure Stack hub-gyorsjavítások | Csatlakozva | A rendszer automatikusan letölti és előkészíti a frissítést a frissítés alkalmazása után. |
| OEM-csomagok frissítései | Csatlakozva | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| Azure Stack hub-szoftverfrissítések | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| Azure Stack hub-gyorsjavítások | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |
| OEM-csomagok frissítései | Leválasztott vagy gyenge kapcsolat | A frissítési csomagot elő kell készíteni. Kövesse a cikkben ismertetett lépéseket. |

## <a name="download-the-update-package"></a>A frissítési csomag letöltése

A Azure Stack hub frissítéseinek és gyorsjavításának frissítési csomagja a csatlakoztatott rendszerek frissítés paneljén keresztül érhető el. Töltse le a csomagot, és helyezze át a csomagot olyan helyre, amely elérhető az Azure Stack hub-példány számára, ha OEM-csomagot frissít, vagy ha egy leválasztott rendszer támogatását végzi. Előfordulhat, hogy le kell töltenie a csomagot, majd fel kell töltenie egy elérhető helyre, ha időszakos kapcsolatban álló rendszert futtat.

Tekintse át a csomag tartalmát. A frissítési csomagok általában a következő fájlokból állnak:

- **Egy önkicsomagoló \< PackageName>. zip fájl**. Ez a fájl tartalmazza a frissítés hasznos adatait.
- **Egy metaadat. xml fájl**. Ez a fájl a frissítéssel kapcsolatos alapvető információkat tartalmaz. Fr példa: a közzétevő, a név, az előfeltétel, a méret és a támogatási útvonal URL-címe.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Frissítési csomagok automatikus letöltése és előkészítése

A Azure Stack hub-szoftverfrissítések és-gyorsjavítások automatikusan előkészítve vannak a **Azure stack hub automatikus frissítési végpontokkal**létesített kapcsolattal rendelkező rendszerekhez: <https://*.azureedge.net> és <https://aka.ms/azurestackautomaticupdate> . Az **Azure stack hub automatikus frissítési végpontokhoz**való kapcsolódás beállításával kapcsolatos további információkért tekintse meg a [Azure stack hub tűzfal-integráció](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound)című részben ismertetett **javítást és frissítési** végpontokat.

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Honnan tölthetők le Azure Stack hub frissítési csomagjai

A [teljes és expressz frissítések](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) Azure stack hub-frissítései egy biztonságos Azure-végponton futnak. A csatlakoztatott példányokkal rendelkező Azure Stack hub-operátorok [automatikusan megjelennek az Azure stack hub frissítései a felügyeleti portálon](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Az internetes leválasztott rendszerek vagy a gyenge internetkapcsolattal rendelkező rendszerek esetében a frissítési csomagok a [Azure stack hub Updates Downloader eszköz](https://aka.ms/azurestackupdatedownload)használatával tölthetők le. Azure Stack hub szoftverfrissítési csomagjai tartalmazhatnak Azure Stack hub-szolgáltatások frissítéseit és az Azure Stack hub méretezési egységeinek operációs rendszerének frissítéseit.

>[!NOTE]
>Maga a frissítési csomag és annak tartalma (például bináris fájlok, PowerShell-parancsfájlok stb.) a Microsoft tulajdonában lévő tanúsítványokkal van aláírva. A csomag módosításával az aláírás érvénytelenné válik.

### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Honnan tölthetők le Azure Stack hub gyorsjavítási csomagjai

[Azure stack hub-gyorsjavítások](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) csomagjai ugyanabban a biztonságos Azure-végponton futnak, mint Azure stack hub-frissítésekhez. A csatlakoztatott példányokkal rendelkező Azure Stack hub-operátorok [automatikusan megjelennek az Azure stack hub frissítései a felügyeleti portálon](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Ezeket a megfelelő gyorsjavítási TUDÁSBÁZIS cikkeiben található beágyazott hivatkozásokkal, például az [Azure stack hub gyorsjavítási 1.1906.11.52](https://support.microsoft.com/help/4515650)töltheti le. A gyorsjavításokat az Azure Stack hub verziójának megfelelő kibocsátási megjegyzésekben találja.

### <a name="where-to-download-oem-update-packages"></a>Honnan tölthetők le az OEM-frissítési csomagok

Az OEM-gyártó a frissítéseket, például az illesztőprogramok és a belső vezérlőprogram frissítéseit is kiadja. Habár ezek a frissítések különálló OEM- [csomagokként](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) jelennek meg a hardver gyártójától, azok továbbra is importálhatók, települnek és kezelhetők ugyanúgy, mint a Microsoft frissítési csomagjai. A szállítói kapcsolattartási hivatkozások listáját az [Apply Azure stack hub OEM-frissítések alkalmazásban](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information)találja.

## <a name="import-and-install-updates"></a>Frissítések importálása és telepítése

Az alábbi eljárás bemutatja, hogyan importálhat és telepíthet frissítési csomagokat a felügyeleti portálon.

> [!Important]  
> Értesítse a felhasználókat a karbantartási műveletekről, és gondoskodjon arról, hogy a szokásos karbantartási időszakok a munkaidőn kívüli órákban legyenek beütemezhetve. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

1. A felügyeleti portálon válassza a **minden szolgáltatás**lehetőséget. Ezután az **adat + tárolás** kategóriában válassza a Storage- **fiókok**lehetőséget. (Vagy a szűrő mezőbe írja be a Storage- **fiókok**kifejezést, majd válassza ki.)

    ![Azure Stack hub frissítése](./media/azure-stack-update-prepare-package/image1.png) 

2. A szűrő mezőbe írja be a **frissítés**értéket, majd válassza ki a **updateadminaccount** .

3. A Storage-fiók adatai alatt, a **szolgáltatások**területen válassza a **Blobok**elemet.

    ![Azure Stack hub frissítése – blob](./media/azure-stack-update-prepare-package/image2.png)

4. A tároló létrehozásához a **blob Service**alatt válassza a **+ tároló** elemet. Adjon meg egy nevet (például *Update-1811*), majd kattintson **az OK gombra**.

    ![Azure Stack hub frissítése – tároló](./media/azure-stack-update-prepare-package/image3.png)

5. A tároló létrehozása után válassza ki a tároló nevét, majd válassza a **feltöltés** lehetőséget a csomag fájljainak a tárolóba való feltöltéséhez.

    ![Azure Stack hub frissítése – feltöltés](./media/azure-stack-update-prepare-package/image4.png)

6. A **blob feltöltése**területen válassza a mappa ikont, keresse meg a frissítési csomag. zip fájlját, majd válassza a **Megnyitás** lehetőséget a fájlkezelő ablakban.

7. A **blob feltöltése**területen válassza a **feltöltés**lehetőséget.

    ![Azure Stack hub frissítése – blob feltöltése](./media/azure-stack-update-prepare-package/image5.png)

8. Ismételje meg a 6. és 7. lépést a `Metadata.xml` fájl és a frissítési csomagban található további. zip fájlok esetében. Ne importálja a `Supplemental Notice.txt ` fájlt, ha szerepel benne.

9. Ha elkészült, tekintse át az értesítéseket (harang ikon a portál jobb felső sarkában). Az értesítéseknek jelezniük kell, hogy a feltöltés befejeződött.

10. Lépjen vissza az irányítópult frissítés paneljére. A panelnek jeleznie kell, hogy van elérhető frissítés. Ez azt jelzi, hogy a frissítés sikeresen előkészített. Válassza ki a panelt az újonnan hozzáadott frissítési csomag áttekintéséhez.

11. A frissítés telepítéséhez jelölje ki a **készként** megjelölt csomagot, vagy kattintson a jobb gombbal a csomagra, majd válassza a **Frissítés most**lehetőséget, vagy válassza a **Frissítés most** a felül műveletet.

12. Amikor kiválasztja a frissítési csomag telepítését, megtekintheti az állapotot a **frissítési Futtatás részletei** területen. Innen kiválaszthatja az **Összefoglalás letöltése** lehetőséget is a naplófájlok letöltéséhez. A frissítési folyamatokból származó naplók a kísérlet befejezése után hat hónapig elérhetők.

13. A frissítés befejeződése után a frissítés panelen látható a frissített Azure Stack hub verziója.

Manuálisan is törölheti a frissítéseket a Storage-fiókból, miután telepítette Azure Stack központra. Azure Stack hub rendszeresen ellenőrzi a régebbi frissítési csomagokat, és eltávolítja azokat a tárolóból. A régi csomagok eltávolításához a Azure Stack hub két hetet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

[A frissítés alkalmazása](azure-stack-apply-updates.md)
