---
title: Frissítések kezelése a Azure Stackban | Microsoft Docs
description: Tudnivalók a Azure Stack frissítéseinek kezeléséről
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
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 0cd83c7a16ef56e3432de7dcba39cc11ca20a379
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008473"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Frissítések kezelése Azure Stack – áttekintés

*Vonatkozik: Integrált rendszerek Azure Stack*

Azure Stack naprakészen kell tartania Azure Stack teljes és expressz frissítések, gyorsjavítások és az eredeti berendezésgyártó (OEM) csomagok frissítéseinek alkalmazásával. A legújabb frissítések Azure Stack naprakészen tartása a legújabb biztonsági javításokat, termékfrissítéseket és illesztőprogramokat, valamint a rendszer belső vezérlőprogram-frissítéseit alkalmazza. Ez a cikk áttekintést nyújt a különböző frissítési csomagokról, a kiadási csomagok ritmusáról, ahol megismerheti a jelenlegi kiadásokat és a teljes frissítési folyamatot.

> [!Note]  
> Azure Stack frissítési csomagokat nem lehet alkalmazni a Azure Stack Development Kitra (ASDK). A frissítési csomagok integrált rendszerekhez készültek. További információ: [a ASDK újratelepítése](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Frissítési csomagok típusai

Az integrált rendszerek három különböző típusú frissítési csomaggal rendelkeznek:

-   **Szoftverfrissítések Azure stack**. A Microsoft a Microsoft szoftverfrissítési csomagjainak teljes körű karbantartási életciklusát felelős. Ezek a csomagok magukban foglalhatják a Windows Server legújabb biztonsági frissítéseit, a nem biztonsági frissítéseket és a Azure Stack szolgáltatás frissítéseit. A tézisek frissítési csomagjait közvetlenül a Microsofttól töltheti le.

    Minden frissítési csomaghoz tartozik egy megfelelő típus, egy **teljes** vagy egy **expressz**. 
 
    A **teljes** frissítési csomagok frissítik a fizikai gazdagép operációs rendszereit a méretezési egységben, és nagyobb karbantartási időszakot igényelnek. 

    Az **expressz** frissítési csomagok hatóköre és a mögöttes fizikai gazda operációs rendszerek frissítése nem történik meg.

-   **Azure stack gyorsjavítások**. A Microsoft gyorsjavításokat biztosít Azure Stack, amelyek gyakran megelőző vagy időérzékeny problémát jelentenek. Az egyes gyorsjavítások a Microsoft Tudásbázis megfelelő cikkében jelennek meg, amely a probléma, az ok és a megoldás részleteit ismerteti. A gyorsjavításokat ugyanúgy letöltheti és telepítheti, mint a Azure Stackhoz tartozó normál teljes frissítési csomagokat. A gyorsjavítások kumulatívak, és percek alatt telepíthetők.

-   **OEM hardver – gyártó által megadott frissítések**. Azure Stack hardveres partnerek felelősek a hardveres belső vezérlőprogram és az illesztőprogram-frissítési csomagok végpontok közötti karbantartási életciklusához (beleértve az útmutatást is). Emellett Azure Stack a hardveres partnereknek is, és útmutatást nyújtanak a hardveres életciklus-gazdagépen található összes szoftverhez és hardverhez. Az OEM hardvergyártó a saját letöltési helyén tárolja ezeket a frissítési csomagokat.

## <a name="when-to-update"></a>Mikor kell frissíteni

A következő lépésszám jelenik meg a frissítések három típusában:

-   **Szoftverfrissítések Azure stack**. A Microsoft általában havonta frissíti a szoftverfrissítési csomagokat.

-   **Azure stack gyorsjavítások**. A gyorsjavítások időérzékeny kiadások, amelyeket bármikor fel lehet szabadítani.

-   **OEM hardver gyártó által megadott frissítések**. Az OEM-hardvergyártók a szükséges módon szabadítják fel a frissítéseiket.

Ha továbbra is támogatást szeretne kapni, a Azure Stack-környezetet egy támogatott Azure Stack szoftverrel kell megtartania. További információ: [Azure stack karbantartási szabályzat](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Honnan kaphat értesítést egy frissítésről

A frissítések figyelmeztetése néhány tényezőtől függ, például az internethez való kapcsolódással és a frissítés típusával.

- **Microsoft-szoftverfrissítések és-gyorsjavítások** 

    A Microsoft-szoftverfrissítések és-gyorsjavítások frissítési riasztása megjelenik az internethez csatlakozó Azure Stack példányok frissítés paneljén.

    Ha a példány nincs csatlakoztatva, és szeretne értesítést kapni az egyes gyorsjavításokról, fizessen elő az [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/32d322a8-acae-202d-e9a9-7371dccf381b/rss) -vagy [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/32d322a8-acae-202d-e9a9-7371dccf381b/atom) -hírcsatornára.

- **OEM hardver szállítója – megadott frissítések**

    Az OEM-frissítések a gyártótól függenek. Létre kell hoznia egy kommunikációs csatornát az OEM-mel, hogy tisztában legyen azzal, hogy mikor van szükség az OEM-mel való frissítésre. További információ a Számítógépgyártókról és az OEM-frissítési folyamatról: [Azure stack eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása](azure-stack-update-oem.md).

## <a name="update-processes"></a>Folyamatok frissítése

Ha már tudja, hogy rendelkezik frissítéssel, alkalmazza a frissítést a következő lépésekkel.

![Azure Stack frissítési folyamat](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Tervezze meg a frissítést**.

    Készítse elő a Azure Stack, hogy a frissítés folyamata a lehető legzökkenőmentesebb legyen, így a felhasználók számára minimális hatással lehet. Értesítse a felhasználókat a lehetséges szolgáltatások leállásáról, majd kövesse a példány frissítésre való előkészítésének lépéseit. A frissítés megtervezésének további lépéseiért tekintse meg a [Azure stack frissítésének](azure-stack-update-plan.md)megtervezése című témakört.

2. **Töltse fel és készítse elő a frissítési csomagot**.

    Internetkapcsolattal rendelkező Azure Stack környezetekben a rendszer automatikusan importálja a szoftverfrissítéseket és gyorsjavításokat a rendszerbe Azure Stack, és előkészíti azokat a frissítésre.

    Az internetről leválasztott Azure Stack környezetek és a gyenge vagy időszakos internetkapcsolattal rendelkező környezetek esetén a frissítési csomagok a Azure Stack felügyeleti portálon keresztül importálhatók a Azure Stack Storage-ba. A frissítési csomag feltöltésének és előkészítésének további lépéseiért lásd: [Azure stack frissítési csomag feltöltése és előkészítése](azure-stack-update-prepare-package.md).

    Az összes OEM frissítési csomagot manuálisan importálja a környezetbe, függetlenül a Azure Stack rendszer internetkapcsolatával. A frissítési csomag importálásával és előkészítésével kapcsolatos további lépésekért lásd: [Azure stack frissítési csomag feltöltése és előkészítése](azure-stack-update-prepare-package.md)).

3. **Alkalmazza a frissítést**.

    Alkalmazza a frissítést a Azure Stack **Update (frissítés** ) paneljének használatával. A frissítés során figyelje a frissítés állapotát, és ha szükséges, hárítsa el a frissítési folyamatot. A frissítés megtervezésének további lépéseiért tekintse meg a [Azure stack frissítésének alkalmazása](azure-stack-apply-updates.md)című témakört.

## <a name="the-update-resource-provider"></a>Az erőforrás-szolgáltató frissítése

Azure Stack tartalmaz egy frissítési erőforrás-szolgáltatót, amely a Microsoft-szoftverfrissítések alkalmazását kezeli. Ez a szolgáltató ellenőrzi, hogy a frissítések az összes fizikai gazdagépen, Service Fabric alkalmazásokon és futtatókörnyezeteken, valamint az összes infrastruktúra-virtuális gépen és a hozzájuk társított szolgáltatáson vannak-e alkalmazva.

A frissítések telepítésekor megtekintheti a magas szintű állapotot, mivel a frissítési folyamat a Azure Stack különböző alrendszereit (például a fizikai gazdagépeket és az infrastruktúra virtuális gépeket) célozza meg.

## <a name="next-steps"></a>További lépések

- A frissítési folyamat megkezdéséhez kövesse az [Azure stack frissítésének](azure-stack-update-plan.md)megtervezése című témakör lépéseit.
- Ha szeretné megtudni, hogy a Azure Stack mely verziói támogatottak, tekintse meg [Azure stack karbantartási szabályzatot](azure-stack-servicing-policy.md).  
- Ha többet szeretne megtudni az aktuális és a legutóbbi frissítésekről, tekintse meg a [Azure stack kibocsátási megjegyzéseit](azure-stack-release-notes-security-updates-1907.md).
