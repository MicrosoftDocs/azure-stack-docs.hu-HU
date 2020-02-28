---
title: Szoftverfrissítések érvényesítése a Microsofttól
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan érvényesítheti a Microsoft szoftverfrissítéseket a Azure Stack hub érvényesítésével szolgáltatásként.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7eade045461052b9d978910fdbcf06bd895e5b09
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704522"
---
# <a name="validate-software-updates-from-microsoft"></a>Szoftverfrissítések érvényesítése a Microsofttól

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft rendszeres időközönként frissítéseket szabadít fel az Azure Stack hub szoftverhez. Ezeket a frissítéseket a Azure Stack hub közös mérnöki partnerei számára biztosítjuk. A frissítések a nyilvánosan elérhető frissítések előtt vannak megadva. A frissítéseket megtekintheti a megoldáson, és visszajelzést küldhet a Microsoftnak.

Az Azure Stack hub-hoz tartozó Microsoft-szoftverfrissítések elnevezési konvenció alapján vannak kijelölve. Például a 1803-es név azt jelzi, hogy a frissítés a március 2018. További információ az Azure Stack hub karbantartási házirendjéről és a kibocsátási megjegyzésekről: [Azure stack hub karbantartási szabályzata](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt a havi frissítési folyamatot az érvényesítési szolgáltatásként (az "Varga") gyakorolja, ismerkedjen meg a következő elemekkel:

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