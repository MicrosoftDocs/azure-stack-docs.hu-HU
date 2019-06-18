---
title: Az Azure Stack kibocsátási megjegyzések – tevékenység ellenőrzőlista |} A Microsoft Docs
description: A rendszer előkészítése a legújabb Azure Stack gyors ellenőrzőlista frissíteni.
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
ms.date: 06/05/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: e079121fda268e9892648fca99da34de04f08101
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152487"
---
# <a name="azure-stack-update-activity-checklist"></a>Az Azure Stack tevékenység ellenőrzőlista

Ez a cikk az Azure Stack-operátorok ellenőrzőlistája, frissítéssel kapcsolatos tevékenységeket tartalmazza. Ha tervezi az Azure Stackhez alkalmazná a frissítést, áttekintheti ezeket az információkat.

## <a name="prepare-for-azure-stack-update"></a>Azure Stack-frissítés előkészítése

| Tevékenység              | Részletek                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Tekintse át az ismert problémák   | [Ismert problémák listája](azure-stack-release-notes-known-issues-1905.md).                |
| Tekintse át a biztonsági frissítések | [Biztonsági frissítések listája](azure-stack-release-notes-security-updates-1905.md).      |
| Run Test-AzureStack   | Futtatás `Test-AzureStack -Group UpdateReadiness` működési problémák azonosításához.      |
| Problémák megoldása        | Oldja meg a működési problémákat által azonosított **Test-AzureStack**.                |
| Alkalmazza a legújabb gyorsjavítások | A alkalmazni a legújabb gyorsjavításokat, amelyek érvényesek a jelenleg telepített kiadása.         |
| Capacity planner eszközt futtatása | Ügyeljen arra, hogy a legújabb verzióját használja a [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) végrehajtására a számítási feladatok tervezésekor és méretezése. A legújabb verzió hibajavításokat tartalmaz, és olyan megjelent új funkciókat biztosít minden egyes Azure Stack a frissítéssel. |
| Frissítés érhető el       | Csak csatlakoztatott forgatókönyvekben Azure Stack üzemelő példányok rendszeres időnként ellenőrzik egy biztonságos végpontot, és automatikusan értesíti, ha egy frissítés érhető el a felhőben. Kapcsolat nélküli ügyfelek letöltése, és az új 1905 csomag használatával importálja a [folyamatot az itt leírtak szerint](azure-stack-apply-updates.md).               |

## <a name="during-azure-stack-update"></a>Azure Stack frissítése közben.

| Tevékenység              | Részletek                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| A frissítés kezelése         | [Kezelheti a frissítéseket az Azure Stack segítségével az operátor portal](azure-stack-updates.md). |
| A frissítés figyelése        | Az operátor portálon nem érhető el, ha [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md). |
| Frissítések folytatása            | Sikertelen frissítés esetén, szervizelés után [frissítéseket az Azure Stack használatával a privilegizált végpont folytatása](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Ne futtassa **Test-AzureStack** egy frissítés során, ennek eredményeként a frissítést leáll.

## <a name="after-azure-stack-update"></a>Azure Stack-frissítés után

| Tevékenység              | Részletek                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Alkalmazza a legújabb gyorsjavítások | A alkalmazni a legújabb gyorsjavítások frissített verzió érvényes.                          |
| Titkosítási kulcsok lekéréséhez | Az adatok rest titkosítási kulcsok lekéréséhez és tárolja azokat biztonságosan kívül az Azure Stack üzemelő példányához. Kövesse a [útmutatást nyújt a kulcsok lekéréséhez](azure-stack-security-bitlocker.md). |

## <a name="next-steps"></a>További lépések

- [Tekintse át a kapcsolatos ismert problémák listája](azure-stack-release-notes-known-issues-1905.md)
- [Tekintse át a biztonsági frissítések listája](azure-stack-release-notes-security-updates-1905.md)
