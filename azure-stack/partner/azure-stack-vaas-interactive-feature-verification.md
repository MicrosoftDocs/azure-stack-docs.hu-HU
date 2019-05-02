---
title: Interaktív szolgáltatás ellenőrző tesztelése az Azure Stack érvényesítési szolgáltatásként |} A Microsoft Docs
description: Ismerje meg, interaktív funkció ellenőrző tesztek létrehozása az Azure Stack-érvényesítéssel szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d3db8ea8639f73f3522ddaa358195e7c9ef2f9a9
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64303254"
---
# <a name="interactive-feature-verification-testing"></a>Interaktív funkció ellenőrzési tesztelés  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az interaktív funkció ellenőrzési tesztelés keretrendszer használatával tesztek kérése a rendszerhez. Amikor egy vizsgálatot igényel, a Microsoft a keretrendszer készíti elő az interaktív manuális lépéseket igénylő tesztek használja. A Microsoft a keretrendszer használatával lánc együttesen több különálló automatizált teszteket.

Ez a cikk ismerteti egy egyszerű manuális forgatókönyvet. A teszt ellenőrzi, hogy az Azure Stackben lemez cseréje. A keretrendszer összegyűjti az egyes lépéseknél a diagnosztikai naplók. Kapcsolatos problémák megoldásában is, ahogy látja őket. A keretrendszer is lehetővé teszi, hogy a más eszközök vagy a folyamatok által előállított naplók megosztása, és lehetővé teszi a forgatókönyvet a visszajelzést.

> [!Important]  
> Ez a cikk hivatkozik a lemez azonosítása végrehajtásához szükséges lépéseket. Ez a bemutató egyszerűen a Tesztmenetek munkafolyamat összeállításunkat eredményt nem lehet használni az új megoldás-ellenőrzéshez.

## <a name="overview-of-interactive-testing"></a>Interaktív vizsgálati áttekintése

Egy teszt lemezcsere egy gyakori forgatókönyvet. Ebben a példában a teszt öt lépésből áll:

1. Hozzon létre egy új **Tesztmenetek** munkafolyamat.
2. Válassza ki a **lemez azonosítása teszt**.
3. Végezze el a manuális lépés, amikor a rendszer kéri.
4. Ellenőrizze a forgatókönyv eredményét.
5. A teszt eredménye küldeni a Microsoftnak.

## <a name="create-a-new-test-pass"></a>Hozzon létre egy új tesztelési fázis

Ha nem rendelkezik egy meglévő tesztcélú adja át a rendelkezésre álló, kövesse a lépéseket a [ütemezés egy teszt](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>A vizsgálat ütemezése

1. Válassza ki **lemez azonosítása teszt**.

    > [!Note]  
    > A teszt verzióját, a teszt biztosítékot való fejlesztést növeli. A legújabb verziót mindig kell használni, kivéve, ha a Microsoft egyéb jelzi.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Adja meg a tartomány rendszergazdai felhasználónév és jelszó kiválasztásával **szerkesztése**.

1. Válassza ki a megfelelő vizsgálati végrehajtási ügynök/DVM meg a teszt elindításához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Válassza ki **küldés** a teszt elindításához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Az interaktív vizsgálati felhasználói felülete elérhető az ügynök az előző lépésben kiválasztott.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Kövesse a **dokumentáció** és **érvényesítési** hivatkozásokra kattintva útmutatást nyújt a Microsoft a forgatókönyv végrehajtásához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Kattintson a **Tovább** gombra.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Kövesse az utasításokat a precheck parancsfájl futtatásához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. A precheck szkript sikeres befejezése után futtassa a manuális forgatókönyv (lemezcsere) megfelelően a **dokumentáció** és **érvényesítési** származó hivatkozások a **információk**fülre.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > A manuális forgatókönyv működés során ne zárja be a párbeszédpanelt.

1. Amikor végzett, a manuális forgatókönyv végrehajtása, kövesse az utasításokat a jelölőnégyzet utólagos parancsfájl futtatásához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. A sikeres végrehajtása manuális forgatókönyv (lemezcsere), jelölje be **tovább**.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Ha bezárja az ablakot, a teszt leáll, mielőtt befejeződött.

1. Visszajelzés küldése a vizsgálati élmény érdekében. Ezeket a kérdéseket a segítségével a Microsoft értékeli a forgatókönyv sikerességi arányát és a kiadási minőségét.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Csatlakoztassa a Microsoftnak elküldeni kívánt naplófájlokat.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. A visszajelzés elküldése végfelhasználói licencszerződés elfogadásához.

1. Válassza ki **küldés** az eredményeket küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)
