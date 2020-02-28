---
title: Frissítések kezelése
description: Ismerje meg, hogyan kezelheti a frissítéseket Azure Stack hub-ban
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/01/2019
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 69deade8a7084f751fda6d9f71ad3e7f3f5fc0f8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696585"
---
# <a name="manage-updates-in-azure-stack-hub"></a>Frissítések kezelése Azure Stack központban

A teljes és expressz frissítések, gyorsjavítások, valamint az eredeti berendezésgyártó (OEM) illesztőprogram-és belső vezérlőprogram-frissítései a Azure Stack hub naprakészen tartanak. Ez a cikk ismerteti a frissítések különböző típusait, a megjelenésük időpontját, valamint azt, hogy hol talál további információkat a jelenlegi kiadásról.

> [!Note]  
> Azure Stack hub-frissítési csomagokat nem alkalmazhat a Azure Stack Development Kitra (ASDK). A frissítési csomagok integrált rendszerekhez készültek. További információ: [a ASDK újratelepítése](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Frissítési csomagok típusai

Az integrált rendszerek három különböző típusú frissítési csomaggal rendelkeznek:

- **Azure stack hub-szoftverfrissítések**. A Microsoft a Microsoft szoftverfrissítési csomagjainak teljes körű karbantartási életciklusát felelős. Ezek a csomagok magukban foglalhatják a Windows Server legújabb biztonsági frissítéseit, a nem biztonsági frissítéseket és a Azure Stack hub szolgáltatás frissítéseit. A tézisek frissítési csomagjait közvetlenül a Microsofttól töltheti le.

    Minden frissítési csomaghoz tartozik egy **teljes** vagy **expressz**típus.

    A **teljes** frissítési csomagok frissítik a fizikai gazdagép operációs rendszereit a méretezési egységben, és nagyobb karbantartási időszakot igényelnek.

    Az **expressz** frissítési csomagok hatóköre, és nem frissíti a mögöttes fizikai gazdagép operációs rendszereit.

- **Azure stack hub-gyorsjavítások**. A Microsoft gyorsjavításokat biztosít Azure Stack hubhoz, amely egy gyakran megelőző vagy időérzékeny probléma megoldására szolgál. Az egyes gyorsjavítások a Microsoft Tudásbázis megfelelő cikkében jelennek meg, amely a probléma, az ok és a megoldás részleteit ismerteti. A gyorsjavításokat ugyanúgy letöltheti és telepítheti, mint a Azure Stack hub normál teljes frissítési csomagjait. A gyorsjavítások kumulatívak, és percek alatt telepíthetők.

- **OEM hardver – gyártó által megadott frissítések**. Azure Stack hub hardveres partnerei felelősek a hardveres belső vezérlőprogram és az illesztőprogram-frissítési csomagok végpontok közötti karbantartási életciklusának (beleértve az útmutatást is). Emellett a Azure Stack hub Hardware-partnerek saját és útmutatást nyújtanak a hardveres életciklus-gazdagépen található összes szoftverhez és hardverhez. Az OEM hardvergyártó a saját letöltési helyén tárolja ezeket a frissítési csomagokat.

## <a name="when-to-update"></a>Mikor kell frissíteni

A frissítések három típusát a következő lépésszám bocsátja ki:

- **Azure stack hub-szoftverfrissítések**. A Microsoft általában havonta frissíti a szoftverfrissítési csomagokat.

- **Azure stack hub-gyorsjavítások**. A gyorsjavítások időérzékeny kiadások, amelyeket bármikor fel lehet szabadítani.

- **OEM hardver gyártó által megadott frissítések**. Az OEM-hardvergyártók a szükséges módon szabadítják fel a frissítéseiket.

Ha továbbra is támogatást szeretne kapni, a Azure Stack hub-környezetet egy támogatott Azure Stack hub-szoftverrel kell megtartania. További információ: [Azure stack hub karbantartási szabályzata](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Honnan kaphat értesítést egy frissítésről

A frissítések figyelmeztetése néhány tényezőtől függ, például az internethez való kapcsolódással és a frissítés típusával.

- **Microsoft-szoftverfrissítések és-gyorsjavítások**

    A Microsoft-szoftverfrissítések és-gyorsjavítások frissítési riasztása megjelenik az internethez csatlakozó Azure Stack hub-példányok **frissítés** paneljén. Ha a **frissítés** panel nem jelenik meg, indítsa újra az infrastruktúra-kezelő vezérlő virtuális gépet.

    Ha a példány nincs csatlakoztatva, és szeretne értesítést kapni az egyes gyorsjavításokról, fizessen elő az [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) -vagy [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) -hírcsatornára.

- **OEM hardver szállítója – megadott frissítések**

    Az OEM-frissítések a gyártótól függenek. Létre kell hoznia egy kommunikációs csatornát az OEM-mel, hogy tisztában legyen az OEM-mel kapcsolatos frissítésekkel, amelyeket alkalmazni kell. További információ a Számítógépgyártókról és az OEM-frissítési folyamatról: [Azure stack hub eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása](azure-stack-update-oem.md).

## <a name="update-processes"></a>Folyamatok frissítése

Ha már tudja, hogy rendelkezik frissítéssel, alkalmazza a következő lépésekkel.

![Azure Stack hub frissítési folyamata](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Tervezze meg a frissítést**.

    Készítse elő a Azure Stack hub-t, hogy a lehető legzökkenőmentesebb legyen a frissítési folyamat, hogy minimális hatással legyen a felhasználókra. Értesítse a felhasználókat a lehetséges szolgáltatások leállásáról, majd kövesse a példány frissítésre való előkészítésének lépéseit. Győződjön meg arról, hogy az [Azure stack hub frissítés előtti ellenőrzőlistájának](release-notes-checklist.md) **összes** lépését követve ellenőrizze, hogy elvégezte-e a frissítés alkalmazásához szükséges lépéseket. Ügyeljen arra is, hogy a megfelelő karbantartási időszakot ütemezze az alkalmazott frissítési típushoz.

2. **Töltse fel és készítse elő a frissítési csomagot**.

    Az internethez csatlakoztatott Azure Stack hub-környezetek esetében a rendszer automatikusan importálja a központi szoftverfrissítéseket és gyorsjavításokat a rendszerbe Azure Stack, és előkészíti azokat a frissítésre.

    Az internetről leválasztott Azure Stack hub-környezetek és a gyenge vagy időszakos internetkapcsolattal rendelkező környezetek esetén a frissítési csomagok a Azure Stack hub felügyeleti portálján keresztül importálhatók Azure Stack hub-tárolóba. A frissítési csomag feltöltésének és előkészítésének további lépéseiért lásd: [Azure stack hub-frissítési csomag feltöltése és előkészítése](azure-stack-update-prepare-package.md).

    Az összes OEM-frissítési csomagot manuálisan importálja a környezetbe, függetlenül az Azure Stack hub rendszer internetkapcsolatával. A frissítési csomag importálásával és előkészítésével kapcsolatos további lépésekért lásd: [Azure stack hub-frissítési csomag feltöltése és előkészítése](azure-stack-update-prepare-package.md).

3. **Alkalmazza a frissítést**.

    Alkalmazza a frissítést a Azure Stack hub **Update (frissítés** ) paneljén. A frissítés során figyelje meg és hárítsa el a frissítési folyamatot. További információ: [Azure stack hub frissítésének alkalmazása](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Az erőforrás-szolgáltató frissítése

Azure Stack hub tartalmaz egy frissítési erőforrás-szolgáltatót, amely kezeli a Microsoft-szoftverfrissítések alkalmazását. Ez a szolgáltató ellenőrzi, hogy a frissítések az összes fizikai gazdagépen, Service Fabric alkalmazásokon és futtatókörnyezeteken, valamint az összes infrastruktúra-virtuális gépen és a hozzájuk társított szolgáltatáson vannak-e alkalmazva.

A frissítések telepítésekor megtekintheti a magas szintű állapotot, mivel a frissítési folyamat a Azure Stack hub különböző alrendszereit (például a fizikai gazdagépeket és az infrastruktúra virtuális gépeket) célozza meg.

## <a name="next-steps"></a>Következő lépések

- A frissítési folyamat megkezdéséhez kövesse az [Azure stack hub Update Activity ellenőrzőlista](release-notes-checklist.md)című témakör lépéseit.
- Az Azure Stack hub támogatott verzióinak megismeréséhez lásd: [Azure stack hub karbantartási szabályzata](azure-stack-servicing-policy.md).  
- Ha többet szeretne megtudni az aktuális és a legutóbbi frissítésekről, tekintse meg a [Azure stack hub kibocsátási megjegyzéseit](release-notes.md).
