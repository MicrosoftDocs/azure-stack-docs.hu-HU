---
title: Interaktív funkció-ellenőrzési teszt a Azure Stack érvényesítés szolgáltatásban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre interaktív funkció-ellenőrzési teszteket Azure Stackhoz szolgáltatásként történő érvényesítéssel.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5da64bbd13e2bfde5b469c1ae0de89d85a054e3c
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167249"
---
# <a name="interactive-feature-verification-testing"></a>Interaktív funkció-ellenőrzési teszt  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az interaktív funkció-ellenőrzési tesztelési keretrendszer használatával teszteket kérhet a rendszer számára. Ha tesztet kér, a Microsoft a keretrendszer használatával készít elő manuális interaktív lépéseket igénylő teszteket. A Microsoft a keretrendszer használatával több önálló automatizált tesztet is felhasználhat.

Ez a cikk egy egyszerű manuális forgatókönyvet ismertet. A teszt ellenőrzi a Azure Stack lemez cseréjét. A keretrendszer minden egyes lépésnél összegyűjti a diagnosztikai naplókat. A problémák hibakeresése a keresés során. A keretrendszer lehetővé teszi a más eszközök vagy folyamatok által létrehozott naplók megosztását is, és lehetővé teszi, hogy visszajelzést nyújtson a forgatókönyvről.

> [!Important]  
> Ez a cikk a lemez-azonosítás végrehajtásának lépéseit ismerteti. Ez egyszerűen egy bemutató, mivel a test pass-munkafolyamatból összegyűjtött összes eredmény nem használható új megoldás-ellenőrzéshez.

## <a name="overview-of-interactive-testing"></a>Az interaktív tesztelés áttekintése

A lemezek cseréjének tesztelése gyakori forgatókönyv. Ebben a példában a teszt öt lépésből áll:

1. Hozzon létre egy új **tesztelési menet** -munkafolyamatot.
2. Válassza ki a **lemez azonosítására szolgáló tesztet**.
3. Ha a rendszer kéri, hajtsa végre a manuális lépést.
4. A forgatókönyv eredményének ellenőrzéséhez.
5. A teszt eredményének elküldése a Microsoftnak.

## <a name="create-a-new-test-pass"></a>Új tesztelési menet létrehozása

Ha nem érhető el meglévő tesztelési fázis, kövesse a [tesztek ütemezésének](azure-stack-vaas-schedule-test-pass.md)utasításait.

## <a name="schedule-the-test"></a>A teszt beosztása

1. Válassza a **lemez azonosítása teszt**lehetőséget.

    > [!Note]  
    > A teszt verziója növeli a tesztelési biztosítékok fejlesztését. A legmagasabb verziót mindig csak akkor kell használni, ha a Microsoft másként nem jelez.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. A **Szerkesztés**lehetőség kiválasztásával adja meg a tartományi rendszergazda felhasználónevét és jelszavát.

1. Válassza ki a megfelelő tesztelési végrehajtási ügynököt/DVM a teszt elindításához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. A teszt elindításához válassza a **Submit (Küldés** ) lehetőséget.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Az interaktív teszt felhasználói felületének elérése az előző lépésben kiválasztott ügynöktől.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Ennek a forgatókönyvnek a végrehajtásához kövesse a **dokumentációt** és az **érvényesítési** hivatkozásokat, és tekintse át a Microsoft utasításait.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Kattintson a **Tovább** gombra.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Az elővizsgálati parancsfájl futtatásához kövesse az utasításokat.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Ha az előzetes ellenőrzési parancsfájl sikeresen befejeződött, futtassa a manuális forgatókönyv (lemez cseréje) elemet a **dokumentáció** és az **érvényesítési** hivatkozások alapján az **információ** lapról.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Ne zárjuk be a párbeszédpanelt a manuális forgatókönyv végrehajtása közben.

1. Ha végzett a manuális forgatókönyv végrehajtásával, kövesse az utasításokat az utólagos ellenőrzési szkript futtatásához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. A kézi forgatókönyv (lemez cseréje) sikeres befejezésekor válassza a **tovább**lehetőséget.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Ha bezárta az ablakot, a teszt leáll, mielőtt elkészült.

1. Visszajelzés küldése a tesztelési élményről. Ezek a kérdések segítik a Microsoftot a forgatókönyv sikerességi arányának és kiadási minőségének felmérésében.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Csatolja a Microsoftnak elküldeni kívánt naplófájlokat.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Fogadja el a visszajelzés küldésével kapcsolatos LICENCSZERZŐDÉSt.

1. Válassza a **Submit (Küldés** ) lehetőséget az eredmények Microsoftnak való elküldéséhez.

## <a name="next-steps"></a>Következő lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)
