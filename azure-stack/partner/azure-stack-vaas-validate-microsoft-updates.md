---
title: A Microsoft frissítéseinek érvényesítése Azure Stack érvényesítés szolgáltatásként | Microsoft Docs
description: Ismerje meg, hogy miként érvényesítheti a Microsoft-szoftverfrissítéseket szolgáltatásként történő érvényesítéssel.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6fe2f8e7ab435cae3517890f79c26611a80c8a60
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167149"
---
# <a name="validate-software-updates-from-microsoft"></a>Szoftverfrissítések érvényesítése a Microsofttól

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft rendszeres időközönként frissítéseket küld a Azure Stack szoftverhez. Ezek a frissítések a Azure Stack Engineering-partnerek számára érhetők el. A frissítések nyilvánosan elérhetők. A frissítéseket megtekintheti a megoldáson, és visszajelzést küldhet a Microsoftnak.

A Azure Stackra vonatkozó Microsoft-szoftverfrissítések elnevezési konvencióval vannak megjelölve, például az 1803-es frissítés a következőre mutat: március 2018. További információ a Azure Stack frissítési szabályzatáról: a lépésszám és a kibocsátási megjegyzések elérhetők [Azure stack karbantartási házirendben](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezte a havi frissítési folyamatot az Varga-ban, ismernie kell a következő elemeket:

- [Érvényesítés szolgáltatási kulcs fogalmai szerint](azure-stack-vaas-key-concepts.md)
- [Interaktív funkció-ellenőrzési teszt](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Szükséges tesztek

A következő teszteket kell végrehajtani a havi szoftveres ellenőrzéshez a következő sorrendben:

- 1\. lépés – havi AzureStack-frissítés ellenőrzése
- 2\. lépés – OEM-bővítményi csomag ellenőrzése
- 3\. lépés – OEM – Cloud szimulációs motor

## <a name="validating-software-updates"></a>Szoftverfrissítések ellenőrzése

1. Hozzon létre egy új **csomag-ellenőrzési** munkafolyamatot.
1. A fenti tesztek végrehajtásához kövesse a [csomag-ellenőrzési tesztek futtatásának](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)utasításait. A **havi Azure stack Update ellenőrzési** teszttel kapcsolatos további információkért tekintse meg az alábbi szakaszt.

Ha kérdése vagy problémája van, lépjen kapcsolatba az "az [Varga súgóval](mailto:vaashelp@microsoft.com)".

## <a name="next-steps"></a>Következő lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)