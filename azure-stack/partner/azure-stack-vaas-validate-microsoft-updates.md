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
ms.openlocfilehash: 3399288b801114f076afe3b68d3f9790bfe2af29
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954485"
---
# <a name="validate-software-updates-from-microsoft"></a>Szoftverfrissítések érvényesítése a Microsofttól

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft rendszeres időközönként frissítéseket küld a Azure Stack szoftverhez. Ezeket a frissítéseket Azure Stack közös mérnöki partnereknek biztosítjuk. A frissítések nyilvánosan elérhetők. A frissítéseket megtekintheti a megoldáson, és visszajelzést küldhet a Microsoftnak.

A Azure Stackra vonatkozó Microsoft-szoftverfrissítések elnevezési konvencióval vannak megjelölve, például az 1803-es frissítés a következőre mutat: március 2018. További információ a Azure Stack frissítési szabályzatáról: a lépésszám és a kibocsátási megjegyzések elérhetők [Azure stack karbantartási házirendben](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezte a havi frissítési folyamatot az Varga-ban, ismernie kell a következő elemeket:

- [Érvényesítés szolgáltatási kulcs fogalmai szerint](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Szükséges tesztek

A következő teszteket kell végrehajtani a havi szoftveres ellenőrzéshez a következő sorrendben:

- OEM-ellenőrzési munkafolyamat

## <a name="validating-software-updates"></a>Szoftverfrissítések ellenőrzése

1. Hozzon létre egy új **csomag-ellenőrzési** munkafolyamatot.
1. A fenti tesztek végrehajtásához kövesse a [csomag-ellenőrzési tesztek futtatásának](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)utasításait. A **havi Azure stack Update ellenőrzési** teszttel kapcsolatos további információkért tekintse meg az alábbi szakaszt.

Ha kérdése vagy problémája van, lépjen kapcsolatba az "az [Varga súgóval](mailto:vaashelp@microsoft.com)".

## <a name="next-steps"></a>Következő lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)