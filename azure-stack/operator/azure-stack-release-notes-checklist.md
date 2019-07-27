---
title: Azure Stack kibocsátási megjegyzések – frissítési tevékenység ellenőrzőlistája | Microsoft Docs
description: Gyors ellenőrzőlista a rendszer előkészítéséhez a legújabb Azure Stack frissítéshez.
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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 0c7840c723aedd38b51002e4a1f18f5a87d66ec4
ms.sourcegitcommit: c2690b2dd36918ff3e47e359cac926128bb83101
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68544069"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack frissítési tevékenység ellenőrzőlistája

Ez a cikk a Azure Stack operátorok frissítéssel kapcsolatos tevékenységeinek listáját tartalmazza. Ha arra készül, hogy Azure Stack frissítését alkalmazza, tekintse át ezeket az információkat.

## <a name="prepare-for-azure-stack-update"></a>Felkészülés Azure Stack frissítésre

| Tevékenység                     | Részletek                                                   |
|------------------------------|-----------------------------------------------------------|
| Ismert problémák áttekintése     | [Ismert problémák listája](azure-stack-release-notes-known-issues-1906.md).                |
| Biztonsági frissítések áttekintése | [A biztonsági frissítések listája](azure-stack-release-notes-security-updates-1906.md).      |
| A legújabb OEM-csomag alkalmazása | Lépjen kapcsolatba az OEM-vel, és győződjön meg arról, hogy a rendszer megfelel a rendszer frissítésének Azure Stack verziójának minimális OEM-csomagra vonatkozó követelményeinek. |
| Run Test-AzureStack     | Futtassa `Test-AzureStack -Group UpdateReadiness` a parancsot a működési problémák azonosításához.      |
| Problémák megoldása          | Oldja meg a **test-AzureStack**által azonosított működési problémákat.                |
| Legújabb gyorsjavítások alkalmazása   | Alkalmazza a jelenleg telepített kiadásra érvényes legújabb gyorsjavításokat.         |
| A Capacity Planner eszköz futtatása   | Ügyeljen arra, hogy az [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) eszköz legújabb verzióját használja a számítási feladatok tervezéséhez és méretezéséhez. A legújabb verzió hibajavításokat tartalmaz, és új funkciókat biztosít az egyes Azure Stack frissítésekhez. |
| Frissítés jelent meg        | Csak a csatlakoztatott forgatókönyvek esetében Azure Stack üzemelő példányok rendszeresen ellenőrzi a biztonságos végpontot, és automatikusan értesíti Önt, ha elérhető egy frissítés a felhőhöz. A leválasztott ügyfelek az [itt ismertetett eljárással](azure-stack-apply-updates.md)tölthetik le és importálhatók az új 1906-csomagot. |


## <a name="during-azure-stack-update"></a>Azure Stack frissítés során

| Tevékenység              | Részletek                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| A frissítés kezelése         | [Azure stack frissítéseinek kezelése az operátori portál használatával](azure-stack-updates.md). |
| A frissítés figyelése        | Ha az operátori portál nem érhető el, [a rendszerjogosultságú végpont használatával figyelheti Azure stack frissítéseit](azure-stack-monitor-update.md). |
| Frissítések folytatása            | A sikertelen frissítés szervizelését követően [folytassa a frissítéseket a Azure stack a privilegizált végpont használatával](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Ne futtasson **teszt-AzureStack** a frissítés során, mivel ez a frissítés elakad.

## <a name="after-azure-stack-update"></a>Azure Stack frissítés után

| Tevékenység              | Részletek                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Legújabb gyorsjavítások alkalmazása | Alkalmazza a frissített verzióra vonatkozó legújabb gyorsjavításokat.                          |
| Titkosítási kulcsok beolvasása | Az adatok lekérése a REST titkosítási kulcsokban, és biztonságosan tárolható a Azure Stack központi telepítésén kívül. Kövesse a [kulcsok](azure-stack-security-bitlocker.md)lekérésének utasításait. |
| Több-bérlő ismételt engedélyezése | Több-bérlős Azure Stack esetén [Győződjön meg arról, hogy sikeres frissítés után konfigurálja az összes vendég címtár](https://docs.microsoft.com/azure-stack/operator/azure-stack-enable-multitenancy#configure-guest-directory) -bérlőt. |

## <a name="next-steps"></a>További lépések

- [Ismert problémák listájának áttekintése](azure-stack-release-notes-known-issues-1907.md)
- [Biztonsági frissítések listájának áttekintése](azure-stack-release-notes-security-updates-1907.md)
