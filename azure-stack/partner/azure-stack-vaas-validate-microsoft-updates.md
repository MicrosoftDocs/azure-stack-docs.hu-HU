---
title: A Microsoft frissítéseinek érvényesítése Azure Stack hub érvényesítése szolgáltatásként
description: Ismerje meg, hogy miként érvényesítheti a Microsoft-szoftverfrissítéseket szolgáltatásként történő érvényesítéssel.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8e09160245551ee83f631360931c8e70bac4318e
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143907"
---
# <a name="validate-software-updates-from-microsoft"></a>Szoftverfrissítések érvényesítése a Microsofttól

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft rendszeres időközönként frissítéseket szabadít fel az Azure Stack hub szoftverhez. Ezeket a frissítéseket a Azure Stack hub közös mérnöki partnerei számára biztosítjuk. A frissítések nyilvánosan elérhetők. A frissítéseket megtekintheti a megoldáson, és visszajelzést küldhet a Microsoftnak.

Az Azure Stack hub-hoz tartozó Microsoft-szoftverfrissítések elnevezési konvenció alapján vannak kijelölve, például a 1803-es frissítés a következőt jelenti: március 2018. További információ az Azure Stack hub frissítési házirendjéről, a lépésszám és a kibocsátási megjegyzésekről: [Azure stack hub karbantartási szabályzata](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezte a havi frissítési folyamatot az Varga-ban, ismernie kell a következő elemeket:

- [Érvényesítés szolgáltatási kulcs fogalmai szerint](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Szükséges tesztek

A következő teszteket kell végrehajtani a havi szoftveres ellenőrzéshez a következő sorrendben:

- OEM-ellenőrzési munkafolyamat

## <a name="validating-software-updates"></a>Szoftverfrissítések ellenőrzése

1. Hozzon létre egy új **csomag-ellenőrzési** munkafolyamatot.
1. A fenti tesztek végrehajtásához kövesse a [csomag-ellenőrzési tesztek futtatásának](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)utasításait. A **havi Azure stack hub Update ellenőrzési** teszttel kapcsolatos további útmutatásért tekintse meg az alábbi szakaszt.

Ha kérdése vagy problémája van, lépjen kapcsolatba az "az [Varga súgóval](mailto:vaashelp@microsoft.com)".

## <a name="next-steps"></a>Következő lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)