---
title: Frissítési csomag előkészítése Azure Stack központban
description: Megtudhatja, hogyan készítheti elő a frissítési csomagot Azure Stack hub-ban.
author: sethmanheim
ms.topic: how-to
ms.date: 03/08/2021
ms.author: sethm
ms.lastreviewed: 03/10/2021
ms.reviewer: sranthar
ms.openlocfilehash: cdc0968519dc5ac50153fb5a14faaafd3b048843
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515816"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Azure Stack hub-frissítési csomag előkészítése

Ez a cikk áttekintést nyújt Azure Stack hub frissítési csomagjainak előkészítéséről, hogy azok a Azure Stack hub-környezet frissítéséhez használhatók legyenek. Ez a folyamat a következő lépésekből áll:

- [Töltse le a frissítési csomagot](#download-the-update-package).
- [Importálja a frissítési csomagot a Azure stack hub-környezetbe az Azure stack hub felügyeleti portál használatával](#import-and-install-updates).

Azokon a rendszereken, amelyek képesek az automatikus frissítési végpontokhoz való kapcsolódásra, az Azure Stack hub-szoftverfrissítések és-gyorsjavítások automatikusan letöltődnek és felkészültek. A kapcsolat nélküli rendszereken és az eredeti berendezésgyártó (OEM) minden frissítéséhez a frissítési csomagot a jelen cikkben leírtak szerint kell előkészíteni.  

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

A Azure Stack hub frissítéseinek és gyorsjavításának frissítési csomagja a portál frissítés paneljén keresztül érhető el a csatlakoztatott rendszerekhez. Töltse le a csomagot, és helyezze át a csomagot olyan helyre, amely elérhető az Azure Stack hub-példány számára, ha OEM-csomagot frissít, vagy ha egy leválasztott rendszer támogatását végzi. Előfordulhat, hogy le kell töltenie a csomagot, majd fel kell töltenie egy elérhető helyre, ha időszakos kapcsolatban álló rendszert futtat.

Tekintse át a csomag tartalmát. A frissítési csomagok általában a következő fájlokból állnak:

- **Egy önkicsomagoló \<PackageName> . zip fájl**. Ez a fájl tartalmazza a frissítés hasznos adatait.
- **Metadata.xml-fájl**. Ez a fájl lényeges információkat tartalmaz a frissítésről; például a közzétevő, a név, az előfeltétel, a méret és a támogatási útvonal URL-címe.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Frissítési csomagok automatikus letöltése és előkészítése

A Azure Stack hub-szoftverfrissítések és-gyorsjavítások automatikusan előkészítve vannak a **Azure stack hub automatikus frissítési végpontokkal** létesített kapcsolattal rendelkező rendszerekhez: `https://*.azureedge.net` és `https://aka.ms/azurestackautomaticupdate` . Az **Azure stack hub automatikus frissítési végpontokhoz** való kapcsolódás beállításával kapcsolatos további információkért tekintse meg a [Azure stack hub tűzfal-integráció](./azure-stack-integrate-endpoints.md#ports-and-urls-outbound)című témakörben leírt **javítási és frissítési** végpontokat.

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Honnan tölthetők le Azure Stack hub frissítési csomagjai

A [teljes és expressz frissítések](./azure-stack-updates.md#update-package-types) Azure stack hub-frissítései egy biztonságos Azure-végponton futnak. Ha a frissítések elérhetővé válnak, a csatlakoztatott példányokkal rendelkező Azure Stack hub [-operátorok automatikusan megjelennek a felügyeleti portálon a Azure stack hub frissítései](#automatic-download-and-preparation-for-update-packages). A leválasztott rendszerek vagy a gyenge internetkapcsolattal rendelkező rendszerek esetében a frissítési csomagok a [Azure stack hub Updates Downloader eszköz](https://aka.ms/azurestackupdatedownload)használatával tölthetők le. Azure Stack hub szoftverfrissítési csomagjai tartalmazhatnak Azure Stack hub-szolgáltatások frissítéseit és az Azure Stack hub méretezési egységeinek operációs rendszerének frissítéseit.

>[!NOTE]
>Maga a frissítési csomag és annak tartalma (például bináris fájlok, PowerShell-parancsfájlok stb.) a Microsoft tulajdonában lévő tanúsítványokkal van aláírva. A csomag módosításával az aláírás érvénytelenné válik.

### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Honnan tölthetők le Azure Stack hub gyorsjavítási csomagjai

[Azure stack hub-gyorsjavítások](./azure-stack-updates.md#update-package-types) csomagjai ugyanabban a biztonságos Azure-végponton futnak, mint Azure stack hub-frissítés. A csatlakoztatott példányokkal rendelkező Azure Stack hub-operátorok [automatikusan megjelennek a felügyeleti portálon,](#automatic-download-and-preparation-for-update-packages) amikor elérhetővé válnak a Azure stack hub frissítései. Ezeket a megfelelő gyorsjavítási TUDÁSBÁZIS cikkeiben található beágyazott hivatkozások használatával töltheti le. A gyorsjavítási TUDÁSBÁZIS cikkeire mutató hivatkozásokat az Azure Stack hub verziójának megfelelő kibocsátási megjegyzésekben talál.

### <a name="where-to-download-oem-update-packages"></a>Honnan tölthetők le az OEM-frissítési csomagok

Előfordulhat, hogy az OEM-gyártó frissítéseket is kiad, például az illesztőprogram-és a belső vezérlőprogram-frissítéseket. Habár ezek a frissítések különálló OEM- [csomagokként](./azure-stack-updates.md#update-package-types) jelennek meg a hardver gyártójától, azok továbbra is importálhatók, települnek és kezelhetők ugyanúgy, mint a Microsoft frissítési csomagjai. A szállítói kapcsolattartási hivatkozások listáját a cikk [Azure stack hub OEM-frissítések alkalmazása](./azure-stack-update-oem.md#oem-contact-information)című cikkben találja.

## <a name="import-and-install-updates"></a>Frissítések importálása és telepítése

Az alábbi eljárás bemutatja, hogyan importálhat és telepíthet frissítési csomagokat a felügyeleti portálon.

> [!IMPORTANT]  
> Értesítse a felhasználókat minden karbantartási műveletről, és gondoskodjon arról, hogy a szokásos karbantartási időszakok a munkaidőn kívüli órákban legyenek beütemezhetve. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

1. A felügyeleti portálon válassza a **minden szolgáltatás** lehetőséget. Ezután a **tárolási** kategória alatt válassza a **Storage-fiókok** lehetőséget. Vagy a szűrő mezőbe írja be a Storage- **fiókok** kifejezést, majd válassza ki.

    [![Azure Stack hub frissítése](./media/azure-stack-update-prepare-package/select-storage-small.png)](./media/azure-stack-update-prepare-package/select-storage.png#lightbox)

2. A szűrő mezőbe írja be a **frissítés** értéket, majd válassza ki a **updateadminaccount** .

3. Az **összes szolgáltatásban** az **Essentials** vagy a **blob Service** területen válassza a **tárolók** lehetőséget.

    [![Azure Stack hub frissítése – blob](./media/azure-stack-update-prepare-package/select-containers-small.png)](./media/azure-stack-update-prepare-package/select-containers.png#lightbox)

4. A **tárolók területen válassza** a **+ tároló** elemet a tároló létrehozásához. Adjon meg egy nevet (például **Update-2102**), majd válassza a **Létrehozás** lehetőséget.

    [![Azure Stack hub frissítése – tároló](./media/azure-stack-update-prepare-package/new-container-small.png)](./media/azure-stack-update-prepare-package/new-container.png#lightbox)

5. A tároló létrehozása után válassza ki a tároló nevét, majd válassza a **feltöltés** lehetőséget a csomag fájljainak a tárolóba való feltöltéséhez.

    [![Azure Stack hub frissítése – feltöltés](./media/azure-stack-update-prepare-package/upload-package-small.png)](./media/azure-stack-update-prepare-package/upload-package.png#lightbox)

6. A **blob feltöltése** területen válassza a mappa ikont, keresse meg a frissítési csomag. zip fájlt, majd kattintson a **Megnyitás** gombra a fájlkezelő ablakban.

7. A **blob feltöltése** területen válassza a **feltöltés** lehetőséget.

    ![Azure Stack hub frissítése – blob feltöltése](./media/azure-stack-update-prepare-package/upload-blob.png)

8. Ismételje meg a 6. és 7. lépést a **Metadata.xml** fájlhoz és a frissítési csomagban található további. zip fájlokhoz. Ha szerepel, ne importálja a **kiegészítő Notice.txt** fájlt.

9. Ha elkészült, tekintse át az értesítéseket (a portál jobb felső sarkában válassza a harang ikont). Egy értesítésnek jeleznie kell, hogy a feltöltés befejeződött.

10. Lépjen vissza az irányítópult **frissítés** paneljére. A panelen látható, hogy a frissítés elérhető. Ez azt jelzi, hogy a frissítés sikeresen előkészített. Válassza ki a panelt az újonnan hozzáadott frissítési csomag áttekintéséhez.

11. A frissítés telepítéséhez jelölje ki a **készként** megjelölt csomagot, majd válassza a **Frissítés most** lehetőséget.

12. Amikor kiválasztja a frissítési csomag telepítését, megtekintheti az állapotot a **frissítési Futtatás részletei** területen. Innen kiválaszthatja az **Összefoglalás letöltése** lehetőséget is a naplófájlok letöltéséhez. A frissítési folyamatokból származó naplók a kísérlet befejezése után hat hónapig elérhetők.

13. A frissítés befejeződése után a **frissítés** panelen látható a frissített Azure stack hub verziója.

Manuálisan is törölheti a frissítéseket a Storage-fiókból, miután telepítette Azure Stack központra. Azure Stack hub rendszeresen ellenőrzi a régebbi frissítési csomagokat, és eltávolítja azokat a tárolóból. A régi csomagok eltávolításához a Azure Stack hub akár két hétig is eltarthat.

## <a name="next-steps"></a>Következő lépések

[A frissítés alkalmazása](azure-stack-apply-updates.md)
