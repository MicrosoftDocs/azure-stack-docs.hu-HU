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
ms.date: 08/22/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 08/22/2019
ms.openlocfilehash: 29ab5b34fc669f69ee4a74e52559c7ad27b20af8
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008765"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack frissítési tevékenység ellenőrzőlistája

*Vonatkozik: Integrált rendszerek Azure Stack*

Tekintse át ezt a feladatlistát egy Azure Stack frissítés előkészítéséhez. Ez a cikk a Azure Stack operátorok frissítéssel kapcsolatos tevékenységeinek listáját tartalmazza.

## <a name="prepare-for-azure-stack-update"></a>Felkészülés Azure Stack frissítésre

| Tevékenység | Részletek |
| --- | --- |
| Ismert problémák áttekintése |[Ismert problémák listája](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-known-issues-1906). |
| Biztonsági frissítések áttekintése | [A biztonsági frissítések listája](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-security-updates-1906). |
| A legújabb OEM-csomag alkalmazása | Lépjen kapcsolatba az OEM-vel, és győződjön meg arról, hogy a rendszer megfelel a rendszer frissítésének Azure Stack verziójának minimális OEM-csomagra vonatkozó követelményeinek. |
| Run Test-AzureStack | Futtassa `Test-AzureStack -Group UpdateReadiness` a parancsot a működési problémák azonosításához. |
| Problémák megoldása | Oldja meg a által `Test-AzureStack`azonosított működési problémákat. |
| Legújabb gyorsjavítások alkalmazása | Alkalmazza a jelenleg telepített kiadásra érvényes legújabb gyorsjavításokat. |
| A Capacity Planner eszköz futtatása | Ügyeljen arra, hogy az Azure Stack Capacity Planner eszköz legújabb verzióját használja a számítási feladatok tervezéséhez és méretezéséhez. A legújabb verzió hibajavításokat tartalmaz, és új funkciókat biztosít az egyes Azure Stack frissítésekhez. |
| Frissítés jelent meg | Csak a csatlakoztatott forgatókönyvek esetében Azure Stack üzemelő példányok rendszeresen ellenőrzi a biztonságos végpontot, és automatikusan értesíti Önt, ha elérhető egy frissítés a felhőhöz. A leválasztott ügyfelek az [itt ismertetett eljárással](https://docs.microsoft.com/azure-stack/operator/azure-stack-apply-updates)tölthetik le és importálhatók új csomagokat. |


## <a name="during-azure-stack-update"></a>Azure Stack frissítés során

| Tevékenység | Részletek |
|--------------------|------------------------------------------------------------------------------------------------------|
| A frissítés kezelése |[Azure stack frissítéseinek kezelése az operátori portál használatával](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates). |
|  |  |
| A frissítés figyelése | Ha az operátori portál nem érhető el, [a rendszerjogosultságú végpont használatával figyelheti Azure stack frissítéseit](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-update). |
|  |  |
| Frissítések folytatása | A sikertelen frissítés szervizelését követően [folytassa a frissítéseket a Azure stack a privilegizált végpont használatával](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-update). |

> [!Important]  
> Ne futtasson **teszt-AzureStack** a frissítés során, mivel ez a frissítés elakad.

## <a name="after-azure-stack-update"></a>Azure Stack frissítés után

| Tevékenység | Részletek |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Legújabb gyorsjavítások alkalmazása | Alkalmazza a frissített verzióra vonatkozó legújabb gyorsjavításokat. |
| Titkosítási kulcsok beolvasása | Az adatok lekérése a REST titkosítási kulcsokban, és biztonságosan tárolható a Azure Stack központi telepítésén kívül. Kövesse a [kulcsok](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker)lekérésének utasításait. |
|  |  |
| Több-bérlő ismételt engedélyezése | Több-bérlős Azure Stack esetén [Győződjön meg arról, hogy sikeres frissítés után konfigurálja az összes vendég címtár](https://docs.microsoft.com/azure-stack/operator/azure-stack-enable-multitenancy#configure-guest-directory) -bérlőt. |

## <a name="next-steps"></a>További lépések

-   [Ismert problémák listájának áttekintése](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-known-issues-1907)  
-   [Biztonsági frissítések listájának áttekintése](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-security-updates-1907)