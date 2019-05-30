---
title: Hozzon létre egy előkészített adatelemzési megoldással az Azure és az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy előkészített adatelemzési megoldással az Azure és az Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 9359c1393229709fc77ee08216a80a26de9135dc
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252012"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Oktatóanyag: Hozzon létre egy előkészített adatelemzési megoldással az Azure és az Azure Stackben 

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ismerje meg, hogyan használhatja a helyszíni és a nyilvános felhő környezeteiben a videólejátszást több létesítményben vállalatok. Az Azure Stack gyűjtése, feldolgozása, tárolására és helyi és távoli adatok terjesztése, különösen akkor, ha a biztonsági, bizalmas, a vállalati házirenddel és szabályozási követelmények eltérhetnek helyek közötti gyors, biztonságos és rugalmas megoldást kínál. és a felhasználók.

Ebben a mintában az ügyfelek, hogy gyors döntéseket lehet tenni a gyűjtemény folyamatos elemzés igénylő adatait gyűjti. Általában adatgyűjteményben nincs Internet-hozzáféréssel rendelkező történik. Ha a kapcsolat létrejött, szükség lehet egy erőforrás-igényes elemzéseket végezhet az adatok további betekintést. Továbbra is elemezheti adatait, nyilvános felhő esetén túl késő vagy nem érhető el.

Ebben az oktatóanyagban egy mintául szolgáló környezet létrehozása:

> [!div class="checklist"]
> - A nyers adatok tárolási blob létrehozásához.
> - Hozzon létre egy új Azure Stack-függvény tiszta adatok áthelyezése az Azure Stack az Azure-bA.
> - Blobtároló által aktivált függvény létrehozása.
> - Hozzon létre egy Azure Stack tárfiókok a blob és üzenetsor tartalmazó.
> - Üzenetsor által aktivált függvény létrehozása.
> - Teszt az üzenetsor által aktivált függvény.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack hatékonyságával és innovációjával a felhőalapú számítástechnika a helyszíni környezetben, és a egyetlen olyan hibrid felhő, amely lehetővé teszi, hogy létrehozása és üzembe helyezése bárhol a hibrid alkalmazások engedélyezésével biztosítható.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) szoftverminőség alappillérei (elhelyezését, a méretezhetőség, rugalmasság, kezelhetőségi és biztonsági) felülvizsgálatai kialakítása, üzembe helyezése és működtetése hibrid alkalmazások. A kialakítási szempontokat segítséget nyújt a hibrid alkalmazások kialakítását, minimálisra csökkentik az éles környezetben kihívások optimalizálása.

## <a name="prerequisites"></a>Előfeltételek

Ez a megoldás felépítéséhez szükséges néhány előkészítése:

-   Egy telepített és működő Azure Stack (további információt itt találhat: [Az Azure Stack áttekintése](azure-stack-storage-overview.md)

-   Azure-előfizetés. (Hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   A [Microsoft Azure Storage Explorer](https://storageexplorer.com/) letöltése és telepítése.

-   Adja meg a saját a functions által feldolgozandó adatokat kell. Adatok generált és elérhető az Azure Stack storage blob-tároló feltöltése kell lennie.

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

### <a name="scalability-considerations"></a>Méretezési szempontok

Az Azure functions és a tárolási megoldások adatmennyiség és feldolgozása a növekvő igények szerint méretezhető. Az Azure skálázhatósági adatait és célok: [Azure skálázhatóságával dokumentáció](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Storage szolgáltatás esetében ez a minta az elsődleges rendelkezésre állási szempont. Gyors kapcsolatokon keresztül nagy mennyiségű adat kötet feldolgozásában és terjesztésében szükség.

### <a name="manageability-considerations"></a>Felügyeleti szempontok

Vegye figyelembe, hogy a fejlesztői eszközöket és a verziókövetés lehetővé teszi kezelni a megoldást.

## <a name="create-the-raw-data-storage-blob"></a>A nyers adatok tárolási blob létrehozása

A storage-fiók és a blob-tárolót fog generálja a helyi tevékenységeket, beleértve a gép és az alkalmazottak tevékenységek, létesítmény adatok, éles mérőszámok és más reporting eredeti adatok tárolásához.

1.  Jelentkezzen be a [ *Azure Stack portálon*](https://portal.local.azurestack.external/).

2.  Az Azure Stack portálon bontsa ki a szolgáltatások a menü megnyitásához, majd válassza a bal oldali **minden szolgáltatás**. Görgessen le a **tárolási** válassza **tárfiókok**. A Storage-fiókok ablakában válassza **Hozzáadás**.

3.  A fiók használata a következő információkat:

    a.  Név: **A választása szerint**

    b.  Üzemi modell: **Resource Manager**

    c.  Fiók típusa: **Storage (általános célú V1)**

    d.  Hely: **USA nyugati régiója**

    e.  Replikáció: **Helyileg redundáns tárolás (LRS)**

    f.  Teljesítmény: **Standard**

    g.  Biztonságos átvitelre van szükség: **Letiltva**

    h.  Előfizetés: Válasszon

    i.  Erőforráscsoport: Adjon meg egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot

    j.  Virtuális hálózatok konfigurálása: **Letiltva**

4.  Válassza ki **Create a storage-fiók létrehozása**.

    ![Helyettesítő szöveg](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  Létrehozása után válassza ki a tárfiók nevét.

6.  A fiók panelen alatt a **BLOB SERVICE** szakaszban kattintson **tárolók**.

7.  Válassza a panel tetején lévő **+ tároló.** Válassza ki **tároló**.

    ![Helyettesítő szöveg](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  Név: **A választott**

9.  Nyilvános hozzáférés szintje: **Tároló** (névtelen olvasási hozzáférés tárolók és blobok)

10.  Kattintson az **OK** gombra.

## <a name="create-an-azure-stack-function"></a>Az Azure Stack-függvény létrehozása

Hozzon létre egy új Azure Stack-függvény tiszta adatok áthelyezése az Azure Stack az Azure-bA.

### <a name="create-the-azure-stack-function-app"></a>Az Azure Stack függvényalkalmazás létrehozása

1. Jelentkezzen be a [Azure Stack portálon](https://portal.local.azurestack.external).
2. Válassza az **Összes szolgáltatás** elemet.
3. Válassza ki **Függvényalkalmazások** a a **Web + mobil** csoport.

4.  A beállításokkal, mint az ábra alatti táblázatban megadott függvényalkalmazás létrehozásához.

    | Beállítás | Ajánlott érték | Leírás |
    | ---- | ---- | ---- |
    | Alkalmazásnév | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a` - `z`, `0``-9`, és `-`. |
    | Előfizetés | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **Erőforráscsoport** |  |  |
    | myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |  |
    | Operációs rendszer | Windows | A kiszolgáló nélküli üzemeltetés jelenleg csak Windows rendszeren történő futtatás esetében érhető el. |
    | **Szolgáltatási csomag** |  |  |
    | Használatalapú csomag | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az alapértelmezett Használatalapú csomagban, az erőforrások hozzáadása dinamikusan történik a függvények igényei szerint. Ez a kiszolgáló nélküli üzemeltetés, a csak kell fizetnie a függvények futási idejéért. |  |
    | Location egység | Önhöz legközelebbi régiót | Válassza ki egy régiót a közelben, vagy más szolgáltatások közelében a functions-hozzáférést. |
    | **Storage-fiók** |  |  |
    | \<fent létrehozott Storage-fiók > | A függvényalkalmazás által használt új tárfiók neve. Tárfiókok nevének 3 – 24 karakter hosszúságúnak kell lennie. A név csak felhasználhatja, számokat és kisbetűket tartalmazhat. Meglévő fiókot is használhat. |  |

    **Példa**

    ![Új függvényalkalmazás-beállítások megadása](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

6.  Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  Válassza ki **erőforrás megnyitása** új függvényalkalmazás megtekintéséhez.

![A függvényalkalmazás elkészült.](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Az Azure Stack függvényalkalmazáshoz függvény hozzáadása

1.  Hozzon létre egy új függvényt kattintva **funkciók**, akkor a **+ új funkció** gombra.

    ![Helyettesítő szöveg](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  Válassza ki **időzítő eseményindító**.

    ![Helyettesítő szöveg](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  Válassza ki **C\#**  , a nyelv és a függvény neve: `upload-to-azure`  Az ütemezés beállítása, `0 0 * * * *`, amely a CRON jelöléssel egyszer egy óra.

    ![Helyettesítő szöveg](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>A blobtároló által aktivált függvény létrehozása

1. Bontsa ki a függvényalkalmazást, és válassza ki a **+** megjelenítő gombra **funkciók**.

2. A keresőmezőbe írja be a `blob` majd válassza ki a kívánt nyelvet és a **Blob eseményindító** sablont.

   ![Válassza ki a blobtároló-eseményindító sablonját.](media/azure-stack-solution-staged-data-analytics/image10.png)

3. Az alábbi táblázatban megadott beállításokkal:

   | Beállítás | Ajánlott érték | Leírás |
   | ------- | ------- | ------- |
   | Name (Név) | Egyedi a függvényalkalmazásban | A blob által aktivált függvény neve. |
   | `Path` | \<a fenti tárolási hely elérési útja > | A figyelt blobtárolóban található hely. A blob fájlneve a kötésben a name paraméter át. |
   | Tárfiók kapcsolata | Függvény alkalmazás kapcsolat | A tárfiók kapcsolata a függvényalkalmazás által már használt használja, vagy hozzon létre egy újat. |

   **Példa**

   ![Hozza létre a blobtároló által aktivált függvényt.](media/azure-stack-solution-staged-data-analytics/image11.png)

4. Válassza ki **létrehozás** a függvény létrehozásához.

### <a name="test-the-function"></a>A függvény tesztelése

1.  Az Azure Portalon keresse meg a függvényt. Bontsa ki a **naplók** a lap alján, és győződjön meg arról, a naplózási adatfolyam nincs leállítva.

2.  Nyissa meg a Storage Explorert, és csatlakozzon, ez a szakasz elején létrehozott tárfiókot.

3.  Bontsa ki a tárfiókot, **Blobtárolók**, és a blob korábban létrehozott. Válassza ki **feltöltése** , majd **fájlok feltöltése**.

    ![Töltsön fel egy fájlt a blobtárolóba.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  A fájlok feltöltése párbeszédpanelen válassza ki a fájlokat a mező. Válasszon egy fájlt a helyi számítógépen, például egy képfájlt, jelölje ki, és válassza ki **nyílt** , majd **feltöltése**.

5.  Lépjen vissza a függvény naplóihoz, és ellenőrzése megtörtént a blob olvasása.

    **Példa**

    ![Tekintse meg a naplókban található üzeneteket.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Azure Stack-storage-fiók létrehozása

Hozzon létre egy Azure Stack tárfiókok a blob és üzenetsor tartalmazó.

### <a name="storage-blob--data-archiving"></a>Tárolási Blob adatok archiválása

Ez a tárfiók helyet biztosít két tárolót. Ezek a tárolók egy blob archív adatok tárolásához használja, és a egy üzenetsorba, a főiroda hozzárendelt adatok feldolgozásához használt.

A lépéseket és a egy másik tárolási fiók és a blob tároló létrehozásához az archive storage, a fent vázolt beállításait használja.

### <a name="storage-queue--filtered-data-holding"></a>Tárolási üzenetsor a szűrt adatokat tároló adatelem

1.  A lépéseket és az új tárfiók eléréséhez a fent vázolt beállításait használja.

2.  A Tárfiók áttekintésének területen válassza ki a **várólista.**

3.  Válassza ki a **+ üzenetsor** és kitöltési **neve** mezőt és a egy nevet az új várólista kitölthető.

4.  Válassza ki **OK.**

    ![Helyettesítő szöveg](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Helyettesítő szöveg](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>Üzenetsor által aktivált függvény létrehozása

1.  A függvény létrehozása fentebb a lépéseket követve hozzon létre egy további Azure Stack-függvényt egy üzenetsor eseményindító blob eseményindító helyett.

2.  Az alábbi táblázatban megadott beállításokkal:

    | Beállítás | Ajánlott érték | Leírás |
    | ------- | ------- | ------- |
    | Name (Név) | Egyedi a függvényalkalmazásban | Az üzenetsor által aktivált függvény neve. |
    | `Path` | \<a fenti tárolási hely elérési útja > | A figyelt tárolási helye. Az üzenetsor neve, mint a name paraméter a kötés átadott. |
    | Tárfiók kapcsolata | Függvény alkalmazás kapcsolat | A tárfiók kapcsolata a függvényalkalmazás által már használt használja, vagy hozzon létre egy újat. |

3.  Válassza ki **létrehozás** a függvény létrehozásához.

## <a name="test-the-queue-triggered-function"></a>Teszt az üzenetsor által aktivált függvény

1. Az Azure Stack portálon keresse meg a függvényt. Bontsa ki a **naplók** a lap alján, és győződjön meg arról, a naplózási adatfolyam nincs leállítva.

2. Nyissa meg a Storage Explorert, és csatlakozzon, ez a szakasz elején létrehozott tárfiókot.

3. Bontsa ki a tárfiókot, **Blobtárolók**, és a blob korábban létrehozott. Válassza ki **feltöltése** , majd **fájlok feltöltése.**

   ![Töltsön fel egy fájlt a blobtárolóba.](media/azure-stack-solution-staged-data-analytics/image12.png)

4. A fájlok feltöltése párbeszédpanelen válassza ki a fájlokat a mező. Válasszon egy fájlt a helyi számítógépen, például egy képfájlt, jelölje ki, és válassza ki **nyílt** , majd **feltöltése**.

5. Lépjen vissza a függvény naplóihoz, és ellenőrzése megtörtént a blob olvasása.

   **Példa**

   ![Tekintse meg a naplókban található üzeneteket.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Biztonságos tárolása és használt megfelelő adatok

Globális vállalati adatok biztonságos tárolt, feldolgozása, elosztott, és Azure és az Azure Stack előkészített Data Analytics és az egyéni végpont irányelvek változón keresztül érhetők el. Távoli iroda az alkalmazottak és a gép tevékenységek, létesítmény adatokat és üzleti metrikákat is folyamatosan lefordított, tárolt, tesztelését és céges szabályzat és a regionális rendelet elosztott.

## <a name="next-steps"></a>További lépések

- Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
