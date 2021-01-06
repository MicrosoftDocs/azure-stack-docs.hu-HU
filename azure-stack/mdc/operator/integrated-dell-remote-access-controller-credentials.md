---
title: Integrált Dell-távelérési vezérlő hitelesítő adatai – MDC
description: Megtudhatja, hogyan frissítheti az integrált Dell távelérési vezérlő (iDRAC) hitelesítő adatait egy moduláris adatközpontban (MDC).
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/27/2020
ms.reviewer: justinha
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: d0d2f522038d0788d4942db68b36e5bb21884619
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910737"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller---modular-data-center-mdc"></a>Hitelesítő adatok frissítése az integrált Dell távelérési vezérlőhöz – moduláris adatközpont (MDC)

Ez a szakasz azt ismerteti, hogyan lehet módosítani az integrált Dell-távelérési vezérlő (iDRAC) hitelesítő adatait. 

## <a name="prerequisites"></a>Előfeltételek

Az eljárás futtatása előtt: 

- A Távoli asztal használatával csatlakozhat a MGMT virtuális géphez. 
- Győződjön meg arról, hogy rendelkezik a fiók vagy a fiókok új hitelesítő adataival. 
 
## <a name="update-the-idrac-credentials"></a>A iDRAC hitelesítő adatainak frissítése

A iDRAC hitelesítő adatainak frissítése az összes PowerEdge-kiszolgálón (HLH és skálázási egység csomópontjai) a környezetben:

1. A webböngészőben jelentkezzen be a https://<iDRAC_IP>. 
1. Lépjen a **iDRAC beállítások**  >  **felhasználók** menüpontra. 
1. Válassza ki a módosítani kívánt felhasználót, majd kattintson a **Szerkesztés** gombra. 
1. A **felhasználó szerkesztése** ablakban adja meg az új jelszót a **jelszóban** , és **erősítse meg a jelszót**, ahogy az a következő ábrán látható: 

   ![Felhasználói adatokat megjelenítő képernyőfelvétel](../operator/media/idrac-credentials/enter-user.png)

1. Kattintson a **Mentés**, majd **az OK** gombra. 

## <a name="next-steps"></a>Következő lépések

[Az Azure Stack Hub titkos kulcsainak rotálása](../../operator/azure-stack-rotate-secrets.md)