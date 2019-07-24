---
title: Frissítések kezelése Azure Stack – áttekintés | Microsoft Docs
description: További információ az Azure Stack integrált rendszerek frissítési felügyeletéről.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: a5fb41e63a8cd9e5f57e4476b1b7c23738f116bb
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418022"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Frissítések kezelése Azure Stack – áttekintés

*Vonatkozik: Integrált rendszerek Azure Stack*

A Azure Stack integrált rendszerek Microsoft Update csomagjai általában minden hónapban kiadásra kerülnek. Kérje meg az eredeti berendezésgyártó (OEM) adatait az adott értesítési folyamatról, hogy biztosítsa a frissítési értesítések elérését a szervezet számára. Az aktív támogatásban lévő kiadásokkal kapcsolatos információkért tekintse meg ezt a dokumentációs könyvtárat is az **Áttekintés** > **kibocsátási megjegyzései** alatt.

A Microsoft-szoftverfrissítések minden kiadása egyetlen frissítési csomagként van csomagolva. Azure Stack kezelőként importálhatja, telepítheti és figyelheti a frissítési csomagok telepítési folyamatát a Azure Stack felügyeleti portálról.

Az OEM-gyártó a frissítéseket is kiadhatja, például az illesztőprogram-és a belső vezérlőprogram-frissítéseket. Habár ezeket a frissítéseket a szállító külön csomagként továbbítja, néhányat importálnak, telepítenek és felügyelnek, ugyanúgy, mint a Microsoft frissítési csomagjai.

Annak érdekében, hogy a rendszer a támogatás alatt maradjon, Azure Stack frissítenie kell egy adott verzió szintjén. Győződjön meg arról, hogy áttekinti a [Azure stack karbantartási házirendet](azure-stack-servicing-policy.md).

> [!NOTE]
> Azure Stack frissítési csomagokat nem lehet alkalmazni a Azure Stack Development Kitra (ASDK). A frissítési csomagok integrált rendszerekhez készültek. További információ: [a ASDK újratelepítése](../asdk/asdk-redeploy.md).

## <a name="the-update-resource-provider"></a>Az erőforrás-szolgáltató frissítése

Azure Stack tartalmaz egy frissítési erőforrás-szolgáltatót, amely a Microsoft-szoftverfrissítések alkalmazását kezeli. Ez a szolgáltató ellenőrzi, hogy a frissítések az összes fizikai gazdagépen, Service Fabric alkalmazásokon és futtatókörnyezeteken, valamint az összes infrastruktúra-virtuális gépen és a hozzájuk társított szolgáltatáson vannak-e alkalmazva.

A frissítések telepítésekor megtekintheti a magas szintű állapotot, mivel a frissítési folyamat a Azure Stack különböző alrendszereit (például a fizikai gazdagépeket és az infrastruktúra virtuális gépeket) célozza meg.

## <a name="plan-for-updates"></a>A frissítések megtervezése

Javasoljuk, hogy minden karbantartási műveletről értesítse a felhasználókat, és ha lehetséges, a szokásos karbantartási időszakokat a munkaidőn kívüli időpontokban ütemezze. A karbantartási műveletek befolyásolhatják a bérlői munkaterheléseket és a portál műveleteit is.

A karbantartási időszak megtervezésekor fontos áttekinteni a Microsoft által kiadott frissítési csomag adott típusát a vonatkozó kibocsátási megjegyzésben leírtak szerint. Az esetenkénti Gyorsjavítástól eltekintve minden frissítési csomag megfelelő típusú, teljes vagy expressz **értékű** lesz . A teljes frissítési csomagok frissítik a fizikai gazdagép operációs rendszereit a méretezési egységben, és nagyobb karbantartási időszakot igényelnek. Az expressz frissítési csomagok hatóköre, és nem frissíti a mögöttes fizikai gazdagép operációs rendszereit.

A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterekkel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő.  

```powershell
Test-AzureStack -Group UpdateReadiness
```

## <a name="using-the-update-tile-to-manage-updates"></a>A frissítés csempe használata a frissítések kezeléséhez

A frissítéseket a felügyeleti portálról kezelheti. Azure Stack operátorként használhatja az irányítópulton található **Update** csempét a következőre:

- Tekintse meg a fontos információkat, például az aktuális verziót.
- Frissítések telepítése és a figyelési folyamat.
- Tekintse át a korábban telepített frissítések korábbi frissítéseit.
- Tekintse meg a felhő jelenlegi OEM-csomagjának verzióját.

## <a name="determine-the-current-version"></a>Az aktuális verzió meghatározása

A Azure Stack aktuális verzióját az **Update (frissítés** ) csempén tekintheti meg. A csempe megnyitása:

1. Nyissa meg a Azure Stack felügyeleti portált.
2. Válassza az **irányítópult**lehetőséget. A **frissítés** csempén az aktuális verzió szerepel a felsorolásban.

    ![Az alapértelmezett irányítópulton lévő frissítések csempe](./media/azure-stack-updates/image1.png)

    Ebben a képen például a verzió a 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Frissítések telepítése és a figyelési folyamat

1. Nyissa meg a Azure Stack felügyeleti portált.
2. Válassza az **irányítópult**lehetőséget. Válassza a **frissítés** csempét.
3. Válassza a **Frissítés most**lehetőséget.

    ![Azure Stack frissítés futtatásának részletei](media/azure-stack-updates/azure-stack-update-button.png)

4. Megtekintheti a magas szintű állapotot, mivel a frissítési folyamat megismétli a Azure Stack különböző alrendszerein keresztül. Az alrendszerek például olyan fizikai gazdagépek, Service Fabric, infrastruktúra-virtuális gépek és szolgáltatások, amelyek mind a rendszergazda, mind a felhasználói portált biztosítják. A frissítési folyamat során az erőforrás-szolgáltató további részleteket jelent a frissítésről, például a sikeres lépések számáról, valamint a folyamatban lévő számról.

5. A teljes naplók letöltéséhez válassza a **teljes naplók letöltése** lehetőséget a frissítés futtatása részletek panelen.

    ![Azure Stack frissítés futtatásának részletei](media/azure-stack-updates/update-run-details.png)

6. Ha elkészült, a frissítési erőforrás-szolgáltató **sikeres** megerősítést biztosít annak jelzésére, hogy a frissítési folyamat befejeződött, és mennyi ideig tartott. A szűrő használatával megtekintheti az összes frissítéssel, elérhető frissítéssel vagy telepített frissítéssel kapcsolatos információt.

    ![Azure Stack frissítés futtatásának részletei sikeresek](media/azure-stack-updates/update-success.png)

   Ha a frissítés sikertelen, a **frissítési** csempe jelentései **figyelmet igényelnek**. A **teljes naplók letöltése** lehetőséggel olyan magas szintű állapotot érhet el, ahol a frissítés nem sikerült. A Azure Stack log gyűjtemény segít a diagnosztika és a hibaelhárítás megkönnyítésében.

## <a name="next-steps"></a>További lépések

- [Azure Stack-karbantartási szabályzat](azure-stack-servicing-policy.md) 
- [Területi felügyelet Azure Stack](azure-stack-region-management.md)
