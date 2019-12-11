---
title: Azure Stack frissítési tevékenység ellenőrzőlista | Microsoft Docs
description: Ellenőrzőlista a rendszer előkészítéséhez a legújabb Azure Stack frissítéshez.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: a560b37051cf3b8b54e3cfec69dc6a9b28cc1cfb
ms.sourcegitcommit: d9430072dd96ae305101da6d8a47d6c23a0a64c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995569"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack frissítési tevékenység ellenőrzőlistája

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Tekintse át ezt a feladatlistát egy Azure Stack frissítés előkészítéséhez. Ez a cikk a Azure Stack operátorok frissítéssel kapcsolatos tevékenységeinek listáját tartalmazza.

## <a name="prepare-for-azure-stack-update"></a>Felkészülés Azure Stack frissítésre

| Tevékenység                     | Részletek                                                   |
|------------------------------|-----------------------------------------------------------|
| Ismert problémák áttekintése     | [Ismert problémák listája](known-issues.md).                |
| Biztonsági frissítések áttekintése | [A biztonsági frissítések listája](release-notes-security-updates.md).      |
| A legújabb OEM-csomag alkalmazása | Lépjen kapcsolatba az OEM-vel, és győződjön meg arról, hogy a rendszer megfelel a rendszer frissítésének Azure Stack verziójának minimális OEM-csomagra vonatkozó követelményeinek. Győződjön meg arról, hogy az OEM-csomag kompatibilis a frissíteni kívánt Azure Stack-verzióval. Ha az OEM-csomag nem kompatibilis a frissítendő Azure Stack verziójával, akkor Azure Stack frissítés futtatása előtt végre kell hajtania egy OEM-csomag frissítését. Útmutatásért tekintse meg a "Azure Stack eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása" című témakört. |
| Nem kötelező: automatikus naplózási gyűjtemény konfigurálása | Javasoljuk, hogy az automatikus naplózási gyűjteményt konfigurálja úgy az Azure Stack hub-környezetben, hogy leegyszerűsítse a rendszernaplók gyűjtésének folyamatát abban az esetben, ha támogatási jegyet kell megnyitnia. Az automatikus naplózási gyűjtemény konfigurálásához tekintse meg az [automatikus Azure stack diagnosztikai naplójának konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection.md)című témakör utasításait. |
| Legújabb gyorsjavítások alkalmazása | Alkalmazza a jelenleg telepített kiadásra érvényes legújabb gyorsjavításokat. A legújabb gyorsjavítások listáját a kibocsátási megjegyzések gyorsjavítása című szakaszban találja. |
| A Capacity Planner eszköz futtatása | Ügyeljen arra, hogy az [Azure Stack Capacity Planner eszköz](azure-stack-capacity-planning-overview.md) legújabb verzióját használja a számítási feladatok tervezéséhez és méretezéséhez. A legújabb verzió hibajavításokat tartalmaz, és új funkciókat biztosít az egyes Azure Stack frissítésekhez. |
| Teszt futtatása – AzureStack | `Test-AzureStack -Group UpdateReadiness` futtatása a működési problémák azonosításához. A parancsmag az emelt szintű végponti munkameneten (PEP) keresztül érhető el. További információ: [Azure stack rendszer állapotának ellenőrzése](azure-stack-diagnostic-test.md). |
| Problémák megoldása | Javítsa ki az `Test-AzureStack`által azonosított működési problémákat. |
| Frissítés érhető el | Csak a csatlakoztatott forgatókönyvek esetében Azure Stack üzemelő példányok rendszeresen ellenőrzi a biztonságos végpontot, és automatikusan értesíti Önt, ha elérhető egy frissítés a felhőhöz. A leválasztott ügyfelek az [itt ismertetett eljárással](azure-stack-apply-updates.md)tölthetik le és importálhatók új csomagokat. |
| A felhasználók értesítése | Minden karbantartási műveletről értesítenie kell a felhasználókat, és ha lehetséges, a szokásos karbantartási időszakokat a munkaidőn kívüli időben kell ütemezni. A karbantartási műveletek befolyásolhatják a bérlői munkaterheléseket és a portál műveleteit is. |

## <a name="during-azure-stack-update"></a>Azure Stack frissítés során

| Tevékenység | Részletek |
|--------------------|------------------------------------------------------------------------------------------------------|
| A frissítés kezelése |[Azure stack frissítéseinek kezelése az operátori portál használatával](azure-stack-updates.md). |
|  |  |
| A frissítés figyelése | Ha az operátori portál nem érhető el, [a rendszerjogosultságú végpont használatával figyelheti Azure stack frissítéseit](azure-stack-monitor-update.md). |
|  |  |
| Frissítések folytatása | A sikertelen frissítés szervizelését követően [folytassa a frissítéseket a Azure stack a privilegizált végpont használatával](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Ne futtasson **teszt-AzureStack** a frissítés során, mivel ez a frissítés elakad.

## <a name="after-azure-stack-update"></a>Azure Stack frissítés után

| Tevékenység | Részletek |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Legújabb gyorsjavítások alkalmazása | Alkalmazza a frissített verzióra vonatkozó legújabb gyorsjavításokat. |
| Titkosítási kulcsok beolvasása | Az adatok lekérése a REST titkosítási kulcsokban, és biztonságosan tárolható a Azure Stack központi telepítésén kívül. Kövesse a [kulcsok lekérésének utasításait](azure-stack-security-bitlocker.md). |
|  |  |
| Több-bérlő ismételt engedélyezése | Több-bérlős Azure Stack esetén [Győződjön meg arról, hogy sikeres frissítés után konfigurálja az összes vendég címtár-bérlőt](azure-stack-enable-multitenancy.md#configure-guest-directory) . |

## <a name="next-steps"></a>Következő lépések

- [Ismert problémák listájának áttekintése](known-issues.md)
- [Biztonsági frissítések listájának áttekintése](release-notes-security-updates.md)
