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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0a507b7488a34715e528b6bbf291fec9832ef027
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418283"
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

1. Havi Azure Stack frissítés ellenőrzése
2. Felhőalapú szimulációs motor

## <a name="validating-software-updates"></a>Szoftverfrissítések ellenőrzése

1. Hozzon létre egy új **csomag-ellenőrzési** munkafolyamatot.
1. A fenti tesztek végrehajtásához kövesse a [csomag-ellenőrzési tesztek futtatásának](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)utasításait. A **havi Azure stack Update ellenőrzési** teszttel kapcsolatos további információkért tekintse meg az alábbi szakaszt.

### <a name="apply-the-monthly-update"></a>A havi frissítés alkalmazása

1. Válasszon ki egy ügynököt a tesztek végrehajtásához.
1. A **havi Azure stack frissítés ellenőrzésének**ütemezett időpontja.
1. Adja meg a bélyegzőn jelenleg üzembe helyezett OEM kiterjesztési csomag helyét, valamint a frissítés során alkalmazni kívánt OEM-kiterjesztési csomag helyét. A csomagok URL-címeinek konfigurálásával kapcsolatban lásd: [csomagok kezelése ellenőrzés céljából](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Kövesse a kiválasztott ügynök felhasználói felületének lépéseit.

Ha kérdése vagy problémája van, lépjen kapcsolatba az "az [Varga súgóval](mailto:vaashelp@microsoft.com)".

## <a name="next-steps"></a>További lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)