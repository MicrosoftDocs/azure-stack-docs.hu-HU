---
title: Integrált Dell távelérési vezérlő hitelesítő adatai
description: Ismerteti, hogyan lehet frissíteni az integrált Dell távelérési vezérlő hitelesítő adatait
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/27/2020
ms.reviewer: justinha
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: 1525872e3c59ab7e5b0cd436ba5ab8e2e3836594
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329093"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller"></a>A beépített Dell távelérési vezérlő hitelesítő adatainak frissítése

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
1. A **felhasználó szerkesztése** ablakban adja meg az új jelszót a **jelszóban** , és **erősítse meg a jelszót** , ahogy az a következő ábrán látható: 

   ![Felhasználói adatokat megjelenítő képernyőfelvétel](../operator/media/idrac-credentials/enter-user.png)

1. Kattintson a **Mentés** , majd **az OK** gombra. 

## <a name="next-steps"></a>Következő lépések

[Titkok elforgatása Azure Stack központban](../../operator/azure-stack-rotate-secrets.md)