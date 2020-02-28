---
title: Interaktív funkció-ellenőrzési teszt
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan hozhat létre interaktív funkció-ellenőrzési teszteket Azure Stack hubhoz szolgáltatásként történő érvényesítéssel.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a1ef4a77bdc4f4dd83a7bced78c85d64ae7836c3
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704726"
---
# <a name="interactive-feature-verification-testing"></a>Interaktív funkció-ellenőrzési teszt  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az interaktív funkció-ellenőrzési tesztelési keretrendszer használatával teszteket kérhet a rendszer számára. Ha tesztet kér, a Microsoft a keretrendszer használatával készít elő manuális interaktív lépéseket igénylő teszteket. A Microsoft a keretrendszer használatával több önálló automatizált tesztet is felhasználhat.

Ez a cikk egy egyszerű manuális forgatókönyvet ismertet. A teszt ellenőrzi a lemez Azure Stack hub-ban való cseréjét. A keretrendszer minden egyes lépésnél összegyűjti a diagnosztikai naplókat. A problémák hibakeresése a keresés során. A keretrendszer lehetővé teszi a más eszközök vagy folyamatok által létrehozott naplók megosztását, és lehetővé teszi, hogy visszajelzést nyújtson a forgatókönyvről.

> [!Important]  
> Ez a cikk a lemez-azonosítási teszt végrehajtásának lépéseit ismerteti. Ez egyszerűen egy bemutató, és a test pass-munkafolyamatból összegyűjtött összes eredmény nem használható új megoldás-ellenőrzéshez.

## <a name="overview-of-interactive-testing"></a>Az interaktív tesztelés áttekintése

A lemezek cseréjének tesztelése gyakori forgatókönyv. Ebben a példában a teszt öt lépésből áll:

1. Hozzon létre egy új **tesztelési menet** -munkafolyamatot.
2. Válassza ki a **lemez azonosítására szolgáló tesztet**.
3. Ha a rendszer kéri, hajtsa végre a manuális lépést.
4. A forgatókönyv eredményének ellenőrzéséhez.
5. A teszt eredményének elküldése a Microsoftnak.

## <a name="create-a-new-test-pass"></a>Új tesztelési menet létrehozása

Ha nem érhető el meglévő teszt, kövesse a [tesztek ütemezésének](azure-stack-vaas-schedule-test-pass.md)utasításait.

## <a name="schedule-the-test"></a>A teszt beosztása

1. Válassza a **lemez azonosítása teszt**lehetőséget.

    > [!Note]  
    > A teszt verziója növeli a tesztelési biztosítékok fejlesztését. A legmagasabb verziót mindig csak akkor kell használni, ha a Microsoft másként nem jelez.

    ![Lemez-azonosító teszt – Azure Stack hub interaktív tesztelése](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2. Válassza a **Szerkesztés** lehetőséget, és adja meg a tartományi rendszergazda felhasználónevét és jelszavát.

3. Válassza ki a megfelelő tesztelési végrehajtási ügynököt/DVM a teszt elindításához.

    ![Teszt végrehajtási ügynök kiválasztása – interaktív tesztelés Azure Stack központban](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4. A teszt elindításához válassza a **Submit (Küldés** ) lehetőséget.

    ![Tesztek áttekintése és beküldése – interaktív tesztelés Azure Stack központban](media/azure-stack-vaas-interactive-feature-verification/image6.png)

5. Az interaktív teszt felhasználói felületének elérése az előző lépésben kiválasztott ügynöktől.

    ![Lemez-azonosító teszt – Azure Stack hub interaktív tesztelése](media/azure-stack-vaas-interactive-feature-verification/image8.png)

6. Ennek a forgatókönyvnek a végrehajtásához kövesse a **dokumentációt** és az **érvényesítési** hivatkozásokat, és tekintse át a Microsoft utasításait.

    ![Dokumentáció és érvényesítési hivatkozások a lemez-azonosítási tesztben](media/azure-stack-vaas-interactive-feature-verification/image9.png)

7. Kattintson a **Tovább** gombra.

    ![Válassza a tovább – interaktív tesztelés Azure Stack központban](media/azure-stack-vaas-interactive-feature-verification/image10.png)

8. Az elővizsgálati parancsfájl futtatásához kövesse az utasításokat.

    ![Az előellenőrzési parancsfájl futtatása – interaktív tesztelés Azure Stack központban](media/azure-stack-vaas-interactive-feature-verification/image11.png)

9. Ha az előzetes ellenőrzési parancsfájl sikeresen befejeződött, futtassa a manuális forgatókönyvet (lemez cseréje) az **információ** lapról a **dokumentáció** és **Érvényesítés** hivatkozásaiban leírtak szerint.

    ![A manuális forgatókönyv futtatása – interaktív tesztelés Azure Stack központban](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Ne zárjuk be a párbeszédpanelt a manuális forgatókönyv végrehajtása közben.

10. Ha végzett a manuális forgatókönyv végrehajtásával, kövesse az utasításokat az utólagos ellenőrzési szkript futtatásához.

    ![A postcheck parancsfájl futtatása – interaktív tesztelés Azure Stack központban](media/azure-stack-vaas-interactive-feature-verification/image13.png)

11. A kézi forgatókönyv (lemez cseréje) sikeres befejezésekor válassza a Submit ( **Küldés**) lehetőséget.

    ![A meghajtó-azonosító teszt elküldése – interaktív tesztelés Azure Stack központban](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Ha bezárta az ablakot, a teszt leáll, mielőtt elkészült.

12. Visszajelzés küldése a tesztelési élményről. Ezek a kérdések segítik a Microsoftot a forgatókönyv sikerességi arányának és kiadási minőségének felmérésében.

    ![Visszajelzés küldése Azure Stack hub interaktív tesztelési felhasználhatóságáról](media/azure-stack-vaas-interactive-feature-verification/image15.png)

13. Csatolja a Microsoftnak elküldeni kívánt naplófájlokat.

    ![Naplófájlok csatolása – interaktív tesztelés Azure Stack központban](media/azure-stack-vaas-interactive-feature-verification/image16.png)

14. Fogadja el a visszajelzés küldésével kapcsolatos LICENCSZERZŐDÉSt.

15. Válassza a **Submit (Küldés** ) lehetőséget az eredmények Microsoftnak való elküldéséhez.

## <a name="next-steps"></a>Következő lépések

- [Tesztek monitorozása és kezelése az Azure Stack hub ellenőrzési portálján](azure-stack-vaas-monitor-test.md)
