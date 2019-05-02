---
title: Kezelheti a frissítéseket az Azure Stack – áttekintés |} A Microsoft Docs
description: Ismerje meg az update management Azure Stack integrált rendszerek.
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
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: c0727ef3f55cba86bbb8c9953007256906852ccb
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984612"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Kezelheti a frissítéseket az Azure Stack áttekintése

*Vonatkozik: Az Azure Stack integrált rendszerek*

A Microsoft update csomagok az Azure Stack integrált rendszerek általában kiadás minden hónap negyedik keddjén körül. Kérdezze meg a számítógépgyártó (OEM) az adott értesítési folyamat frissítési értesítések eléréséhez a szervezet biztosításához. A jelen dokumentációs könyvtárban alapján is ellenőrizheti **áttekintése** > **kibocsátási megjegyzések** verziókról lévő aktív támogatási információkat. 

A Microsoft különböző kiadásai egyetlen frissítési csomag részét képezi. Az Azure Stack operátorait szerint importálhatja, telepítése és a figyelő ezeket a telepítési folyamat frissítési csomagok a felügyeleti portálról. 

Az OEM beszállítói is frissítéseket, például az illesztőprogram- és belsővezérlőprogram-frissítések. Ezeket a frissítéseket a szállító által lépnek különálló csomagként, amíg azok már importált, telepítve, és ugyanúgy, mint frissítési csomagokat a Microsoft által felügyelt.

Hogy a rendszer a támogatás keretében, Azure Stack egy adott verzió szintre frissíteni kell hagynia. Győződjön meg arról, hogy tekintse át a [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Azure Stack csomagok az Azure Stack Development Kit nem alkalmazhat. A frissítési csomagok integrált rendszerek lettek kialakítva. További információ: [ismételt üzembe helyezése a ASDK](/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Az erőforrás-szolgáltató frissítése

Az Azure Stack tartalmaz egy frissítési erőforrás-szolgáltató, amely kezeli a Microsoft szoftverfrissítések alkalmazására. Ez a szolgáltató ellenőrzi, hogy a frissítések alkalmazása minden fizikai gazdagép, Service Fabric-alkalmazásokat és futásidejének tekintetében, és az összes virtuális gépeket és a társított szolgáltatások között.

Telepíti a frissítéseket, mert a frissítési folyamat célként az Azure Stackben (például fizikai gazdagépeket és virtuális gépeket) a különböző alrendszereket megtekintheti általános állapotát.

## <a name="plan-for-updates"></a>Frissítések tervezése

Javasoljuk, hogy a karbantartási műveleteket felhasználók értesítése, és hogy úgy ütemezze a normál karbantartási időszakok során munkaidőn kívüli Ha lehetséges. Karbantartási műveletek hatással lehetnek a bérlői terhelések és a webportálos műveletek.

- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterekkel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>A frissítés csempe használata kezelheti a frissítéseket

A felügyeleti portálon kezelheti a frissítéseket. Az Azure Stack operátorait szerint használhatja a frissítés csempét az irányítópulton:

- fontos információkat, például a jelenlegi verzió megtekintéséhez.
- Telepítse a frissítéseket, és a folyamat állapotának monitorozásához.
- Tekintse át a korábban telepített frissítések frissítési előzményeket.
- Megtekintheti a felhő aktuális OEM csomag verziója
 
## <a name="determine-the-current-version"></a>Határozza meg az aktuális verzió

Azure Stack jelenlegi verziója a frissítés csempén tekintheti meg. A csempe megnyitása:

1. Nyissa meg az Azure Stack felügyeleti portálján.
2. Válassza ki **irányítópult**. Az a **frissítés** csempét, a jelenlegi verzió szerepel. 

    ![Frissítéseket alapértelmezett irányítópult csempéjén.](./media/azure-stack-updates/image1.png)

    Ha például a képernyőn a rendszer 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Telepítse a frissítéseket és az előrehaladásának figyeléséhez


1. Nyissa meg az Azure Stack felügyeleti portálján.
2. Válassza ki **irányítópult**. Kattintson a frissítés csempére.
3. Válassza ki **frissítés most**.

    ![Az Azure Stack a frissítési menetet részletei](media/azure-stack-updates/azure-stack-update-button.png)

4.  Megtekintheti az általános állapotát, a frissítési folyamat végighalad a különböző alrendszereket, az Azure Stackben. Példa alrendszerek közé tartoznak a fizikai gazdagépeket, a Service Fabric segítségével a virtuális gépeket és szolgáltatásokat, amelyek adja meg a rendszergazda és a felhasználói portálon. A frissítési folyamat során a frissítés erőforrás-szolgáltató, például a számát, hogy sikeres volt, valamint a folyamatban lévők száma a frissítéssel kapcsolatos további részletek jelentések.

5. Válassza ki a **teljes naplók letöltéséhez** részleteit tartalmazó panel teljes naplók letöltéséhez futtassa a frissítést.

    ![Az Azure Stack a frissítési menetet részletei](media/azure-stack-updates/update-run-details.png)

6. Ha befejeződött, a frissítés erőforrás-szolgáltató biztosít egy **sikeres** megerősítése, hogy tudja, hogy a frissítési folyamat befejeződött, és mennyi ideig tartottak. Itt megtekintheti az összes frissítések, az elérhető frissítések vagy a szűrő használatával telepített frissítések kapcsolatos információkat.

    ![Az Azure Stack a frissítési menetet részletek sikeres](media/azure-stack-updates/update-success.png)

   Ha a frissítés sikertelen, a frissítés csempére jelentések **figyelmet**. Használja a **teljes naplók letöltéséhez** ahol lehetett nem sikerült a frissítés magas szintű állapotának lekéréséhez. Az Azure Stack naplógyűjtés segít a diagnosztika és hibaelhárítás megkönnyítése.

## <a name="next-steps"></a>További lépések

- [Azure Stack-karbantartási szabályzat](azure-stack-servicing-policy.md) 
- [Régiók kezelése az Azure Stackben](azure-stack-region-management.md)
