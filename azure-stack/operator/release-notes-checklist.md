---
title: Azure Stack hub frissítési tevékenységének ellenőrzőlista
description: Ellenőrzőlista a rendszer előkészítéséhez a legújabb Azure Stack hub-frissítéshez.
author: sethmanheim
ms.topic: article
ms.date: 02/18/2020
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 1df84dc5c6294bbdfc5d448dbf2f2a07a477a400
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "79512167"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Azure Stack hub frissítési tevékenységének ellenőrzőlista

A Azure Stack hub frissítésének előkészítéséhez tekintse át ezt a feladatlistát. Ez a cikk a Azure Stack hub-operátorok frissítéssel kapcsolatos tevékenységeinek ellenőrzőlista-listáját tartalmazza.

## <a name="prepare-for-azure-stack-hub-update"></a>Azure Stack hub frissítésének előkészítése

| &nbsp;&nbsp; Tevékenység &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;                   | Részletek                                                   |
|------------------------------|-----------------------------------------------------------|
| Az ismert problémák áttekintése     | [Ismert problémák listája](known-issues.md).                |
| Biztonsági frissítések áttekintése | [A biztonsági frissítések listája](release-notes-security-updates.md).      |
| A legújabb OEM-csomag alkalmazása | Lépjen kapcsolatba az OEM-vel, és győződjön meg arról, hogy a rendszer megfelel a rendszer frissítésének Azure Stack hub-verziójának minimális OEM-csomagra vonatkozó követelményeinek. Győződjön meg arról, hogy az OEM-csomag kompatibilis a Azure Stack hub azon verziójával, amelyet frissíteni kíván. Ha az OEM-csomag nem kompatibilis a frissítendő Azure Stack hub verziójával, akkor Azure Stack hub-frissítés futtatása előtt végre kell hajtania egy OEM-csomag frissítését. Útmutatásért lásd: "az Azure Stack hub eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása". |
| Nem kötelező: automatikus naplózási gyűjtemény konfigurálása | Javasoljuk, hogy az automatikus naplózási gyűjteményt konfigurálja úgy az Azure Stack hub-környezetben, hogy leegyszerűsítse a rendszernaplók gyűjtésének folyamatát abban az esetben, ha támogatási jegyet kell megnyitnia. Az automatikus naplózási gyűjtemény konfigurálásához tekintse meg az [automatikus Azure stack hub diagnosztikai naplók konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)című témakör utasításait. |
| Legújabb gyorsjavítások alkalmazása | Alkalmazza a jelenleg telepített kiadásra érvényes legújabb gyorsjavításokat. A legújabb gyorsjavítások listáját a [kibocsátási megjegyzések gyorsjavításai](release-notes.md) című szakaszban találja. |
| A Capacity Planner eszköz futtatása | Ügyeljen arra, hogy az [Azure stack Hub Capacity Planner eszköz](azure-stack-capacity-planning-overview.md) legújabb verzióját használja a számítási feladatok tervezéséhez és méretezéséhez. A legújabb verzió hibajavításokat tartalmaz, és az egyes Azure Stack hub-frissítésekhez kiadott új szolgáltatásokat nyújt. |
| **Teszt futtatása – AzureStack** | Futtassa `Test-AzureStack -Group UpdateReadiness` a parancsot a működési problémák azonosításához. A parancsmag az emelt szintű végponti munkameneten (PEP) keresztül érhető el. További információ: [Azure stack hub rendszer állapotának ellenőrzése](azure-stack-diagnostic-test.md). |
| Problémák megoldása | Oldja meg a által `Test-AzureStack`azonosított működési problémákat. |
| Frissítés érhető el | Csak a csatlakoztatott forgatókönyvekben Azure Stack hub üzemelő példányai rendszeresen ellenőrzi a biztonságos végpontot, és automatikusan értesíti Önt, ha elérhető egy frissítés a felhőhöz. A leválasztott ügyfelek az [itt ismertetett eljárással](azure-stack-apply-updates.md)tölthetik le és importálhatók új csomagokat. |
| Karbantartási időszak ütemezett és a felhasználók értesítése | Minden karbantartási műveletről értesítenie kell a felhasználókat, és ha lehetséges, a normál karbantartási időszakokat a munkaidőn kívül kell ütemezni. A karbantartási műveletek befolyásolhatják a meglévő bérlői munkaterheléseket, és az új bérlői műveleteket (például virtuális gépek létrehozását, újrakonfigurálását vagy törlését) eredményezhetik, hogy a műveletet a portálról kezdeményezték, vagy programozott módon a Azure Resource Manager API-ból. Más műveletek, például a biztonsági mentés is elérhetetlenné válhatnak, amíg a frissítés be nem fejeződik. A Azure Stack hub Express és a teljes frissítések esetében megtekintheti a [kibocsátási megjegyzéseket](release-notes.md) annak előrejelzéséhez, hogy a frissítés várhatóan mennyi ideig tart az alkalmazott verziónál. |

## <a name="during-azure-stack-hub-update"></a>Azure Stack hub frissítése közben

| Tevékenység | Részletek |
|--------------------|------------------------------------------------------------------------------------------------------|
| A frissítés kezelése |[Azure stack hub frissítéseinek kezelése az operátori portál használatával](azure-stack-updates.md). |
|  |  |
| A frissítés figyelése | Ha az operátori portál nem érhető el, [figyelje a Azure stack hub frissítéseit az emelt szintű végpont használatával](azure-stack-monitor-update.md). |
|  |  |
| Frissítések folytatása | A sikertelen frissítés szervizelését követően [folytassa a frissítéseket a Azure stack hub-ban a privilegizált végpont használatával](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Ne futtasson **teszt-AzureStack** a frissítés során, mivel ez a frissítés elakad.

## <a name="after-azure-stack-hub-update"></a>Azure Stack hub frissítését követően

| Tevékenység | Részletek |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Legújabb gyorsjavítások alkalmazása | Alkalmazza a frissített verzióra vonatkozó legújabb gyorsjavításokat. |
| Titkosítási kulcsok beolvasása | Az adatok lekérése a REST titkosítási kulcsokban, és biztonságosan tárolható a Azure Stack hub központi telepítésén kívül. Kövesse a [kulcsok lekérésének utasításait](azure-stack-security-bitlocker.md). |
|  |  |
| Több-bérlő ismételt engedélyezése | Több-bérlős Azure Stack hub esetén [Győződjön meg arról, hogy sikeres frissítés után konfigurálja az összes vendég címtár-bérlőt](azure-stack-enable-multitenancy.md#configure-guest-directory) . |

## <a name="next-steps"></a>További lépések

- [Ismert problémák listájának áttekintése](known-issues.md)
- [Biztonsági frissítések listájának áttekintése](release-notes-security-updates.md)
