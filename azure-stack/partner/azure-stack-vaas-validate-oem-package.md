---
title: Az Azure Stack érvényesítési szolgáltatásként számítógépgyártó (OEM)-csomagok érvényesítése |} A Microsoft Docs
description: Ismerje meg, hogyan érvényesítheti az eredeti hardvergyártó (OEM) csomagok-érvényesítéssel szolgáltatásként.
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
ms.openlocfilehash: 8dca51f274d5bcb5e761f701963ed4bd62889b3f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297787"
---
# <a name="validate-oem-packages"></a>OEM-csomagok érvényesítése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ha a belső vezérlőprogram vagy illesztőprogramok egy befejezett megoldás érvényesítéshez változás történt tesztelheti egy új OEM-csomagot. Ha a csomag megfelelt a vizsgálatot, azt a Microsoft által aláírt. A tesztet tartalmaznia kell a frissített illesztőprogramokat és belső vezérlőprogramja, amelyek megfeleltek a Windows Server embléma és a számítógépek tesztek OEM bővítmény csomagot.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Feltöltése vagy a csomagok elküldése előtt tekintse át a [OEM csomag létrehozása](azure-stack-vaas-create-oem-package.md) a várt csomag formátum és a tartalom.

## <a name="managing-packages-for-validation"></a>Ellenőrzés-csomagok kezelése

Használatakor a **csomag** munkafolyamat érvényesítése a csomag, meg kell adnia az URL-címe egy **Azure Storage-blobot**. A blob, a teszt aláírt OEM-csomagot, amely a frissítési folyamat részeként telepíti. A telepítés során létrehozott Azure Storage-fiók használata a blob létrehozásához (lásd: [állítsa be a szolgáltatási erőforrások, az érvényesítési](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Előfeltétel: A storage-tároló üzembe helyezése

Hozzon létre egy tárolót a storage-fiókban csomag blobok számára. Ez a tároló használható összes futtatja a csomag érvényességének ellenőrzése.

1. Az a [az Azure portal](https://portal.azure.com), lépjen a létrehozott tárfiókban [állítsa be a szolgáltatási erőforrások, az érvényesítési](azure-stack-vaas-set-up-resources.md).

2. A bal oldali panel alatt **Blob Service**válassza **tárolók**.

3. Válassza ki **+ tároló** a menüsoron.
    1. Adja meg például a tároló nevét `vaaspackages`.
    1. Válassza ki a kívánt hozzáférési szintet VaaS például nem hitelesített ügyfelek számára. VaaS hozzáférést az egyes csomagok kapcsolatos részletekért lásd: [kezelése a tároló hozzáférési szintjét](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Csomag feltöltése a storage-fiókba

1. Készítse elő az érvényesíteni kívánt csomagot. Ez egy `.zip` fájl, amelynek a tartalmát meg kell egyeznie a leírt struktúra [OEM csomag létrehozása](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Győződjön meg arról, hogy a `.zip` tartalma gyökérmappájában kerülnek a `.zip` fájlt. A csomag nincs almappák kell lennie.

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a csomag tárolót, és töltse fel a csomagot, válassza a **feltöltése** a menüsávon.

1. Válassza ki a csomagot `.zip` fájlt a feltöltéshez. Hagyja változatlanul az alapértelmezett beállításokat **Blob típusát** (azaz **Blokkblob**) és **blokkméret**.

### <a name="generate-package-blob-url-for-vaas"></a>Csomag blob URL-címet generálni VaaS

Amikor létrehozza a **csomag** munkafolyamat a VaaS portálon, meg kell adnia a csomagot tartalmazó Azure Storage blob URL-címe. Néhány *interaktív* teszteket, beleértve a **havi AzureStack frissítés ellenőrzés** és **OEM kiterjesztésű csomag ellenőrzési**, is szükség van a csomag blobok URL-CÍMÉT.

#### <a name="handling-container-access-level"></a>Tároló hozzáférési szintjét kezelése

VaaS által megkövetelt minimális hozzáférési szintjét attól függ, hogy egy csomag érvényesítése munkafolyamat létrehozásakor és ütemezési egy *interaktív* teszteléséhez.

Abban az esetben, **privát** és **Blob** hozzáférési szintet, akkor ideiglenesen hozzáférést kell biztosítania a csomag blob VaaS, így egy [közös hozzáférésű jogosultságkód](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). A **tároló** hozzáférés szintje nem igényel, hogy hozzon létre SAS URL-címeit, de lehetővé teszi, hogy a nem hitelesített hozzáférést a tárolóhoz és annak blobjaihoz.

|Hozzáférési szint | Munkafolyamat-követelmény | Teszt követelmény |
|---|---------|---------|
|Privát | Hozzon létre egy SAS URL-cím csomag blobonkénti ([1. lehetőség](#option-1-generate-a-blob-sas-url)). | Hozzon létre egy SAS URL-címet a fiók szintjén, és adja hozzá manuálisan a blob nevét ([2. lehetőség](#option-2-construct-a-container-sas-url)). |
|Blob | Adja meg a blob URL-cím tulajdonsága ([beállítást 3](#option-3-grant-public-read-access)). | Hozzon létre egy SAS URL-címet a fiók szintjén, és adja hozzá manuálisan a blob nevét ([2. lehetőség](#option-2-construct-a-container-sas-url)). |
|Tároló | Adja meg a blob URL-cím tulajdonsága ([beállítást 3](#option-3-grant-public-read-access)). | Adja meg a blob URL-cím tulajdonsága ([beállítást 3](#option-3-grant-public-read-access)).

A csomagok való hozzáférést a lehetőségeket a legnagyobb hozzáférés legalább hozzáférés vannak rendezve.

#### <a name="option-1-generate-a-blob-sas-url"></a>Option 1: A blob SAS URL-cím létrehozása

Használja ezt a beállítást, ha a tároló hozzáférési szintjét **privát**, ahol a tároló nem engedélyezi a nyilvános olvasási hozzáférést a tárolóhoz és annak blobjaihoz.

> [!NOTE]
> Ez a módszer nem fog működni a *interaktív* teszteket. Lásd: [2. lehetőség: Hozza létre a tároló SAS URL-címet](#option-2-construct-a-container-sas-url).

1. Az a [az Azure portal](https://portal.azure.com/)nyissa meg a tárfiók, és keresse meg a .zip a csomagot tartalmazó.

2. Válassza ki **SAS létrehozása** a helyi menüből.

3. Válassza ki **olvasási** a **engedélyek**.

4. Állítsa be **kezdési idő** az aktuális idő és **befejezési idő** legalább 48 órát a **kezdési idő**. Ha a létrehozni kívánt egyéb munkafolyamatok a ugyanazon csomag, érdemes növelni **befejezési idő** hosszán át a tesztelést.

5. Válassza ki **készítése a blob SAS-jogkivonat és URL-cím**.

Használja a **a Blob SAS URL-cím** amikor csomagot biztosít a blob URL-címek a portálra.

#### <a name="option-2-construct-a-container-sas-url"></a>Option 2: Hozza létre a tároló SAS URL-címe

Használja ezt a beállítást, ha a tároló hozzáférési szintjét **privát** kell megadnia a csomagot a blob URL-CÍMÉT és a egy *interaktív* tesztelése. Az URL-címet a munkafolyamat szintjén is használható.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Válassza ki **Blob** a **engedélyezett szolgáltatások beállítások**. Kapcsolja ki a fennmaradó beállításokat.

1. Válassza ki **tároló** és **objektum** a **engedélyezett erőforrástípusok**.

1. Válassza ki **olvasási** és **lista** a **engedélyezett jogosultságok**. Kapcsolja ki a fennmaradó beállításokat.

1. Válassza ki **kezdési idő** aktuális idő szerint, és **befejezési idő** legalább 14 nappal a **kezdési idő**. Ha a rendszer más teszteket ugyanaz a csomag kell futtatja, érdemes növelni **befejezési idő** hosszán át a tesztelést. Bármely keresztül után VaaS ütemezett vizsgálatok **befejezési idő** sikertelen és a egy új SAS fog kell létrehozni.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Formátum a következőképpen jelenik meg: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. A létrehozott SAS URL-címe tartalmazza a csomag tároló módosítása `{containername}`, és a blob nevét, a csomagot, `{mypackage.zip}`, az alábbiak szerint:  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Használja ezt az értéket, ha a csomag biztosít a blob URL-címek a portálra.

#### <a name="option-3-grant-public-read-access"></a>3. lehetőség: Nyilvános olvasási hozzáférés engedélyezése

Használja ezt a beállítást, ha elfogadható, hogy a nem hitelesített ügyfelek és az azokhoz az egyes blobok, abban az esetben, *interaktív* teszteli, a tárolót.

> [!CAUTION]
> Ez a beállítás a névtelen olvasási hozzáférés a BLOB(ok) nyit meg.

1. Állítsa be a csomag tároló hozzáférési szintjét **Blob** vagy **tároló** szakasz utasításait követve [névtelen felhasználók engedélyeket tárolók és blobok](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > Ha meg van adva a csomag URL-CÍMÉT egy *interaktív* teszt, meg kell adnia **teljes nyilvános olvasási hozzáférés** tesztelési folytatásához a tárolóhoz.

1. A csomag tárolóban válassza ki a csomag blob a Tulajdonságok ablak megnyitásához.

1. Másolás a **URL-cím**. Használja ezt az értéket, ha a csomag biztosít a blob URL-címek a portálra.

## <a name="create-a-package-validation-workflow"></a>Csomag érvényesítése munkafolyamat létrehozása

1. Jelentkezzen be a [VaaS portál](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Válassza ki **Start** a a **csomag** csempére.

    ![Csomag ellenőrzések munkafolyamat csempe](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Adja meg az Azure Storage blob URL-címe, a tesztelési aláírt igénylő Microsoft aláírás OEM-csomagot. Útmutatásért lásd: [csomag blob URL-címet generálni VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Környezeti paraméter nem lehet módosítani egy munkafolyamat létrehozása után.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    A tesztek összefoglaló oldalra irányítja.

## <a name="required-tests"></a>Szükséges tesztek

A következő ellenőrzés szükség az OEM csomag érvényességének ellenőrzése:

- OEM-bővítmény csomag ellenőrzése
- Felhőbeli szimuláció motor

## <a name="run-package-validation-tests"></a>Csomag Érvényesítési tesztek futtatásához

1. Az a **csomag Érvényesítési tesztek összefoglalása** lapon fog futtatni a forgatókönyvnek megfelelő felsorolt tesztek egy részét.

    Az érvényesítés munkafolyamatokban **ütemezés** egy tesztet a munkafolyamat-szintű gyakori paramétereket, a munkafolyamat létrehozása során megadott használja (lásd: [az Azure Stack érvényesítésiszolgáltatásáltalánosmunkafolyamat-paraméterek](azure-stack-vaas-parameters.md)). Ha bármelyik teszt paraméterértékek érvénytelenné válik, meg kell resupply őket a utasításai [munkafolyamat-paraméterek módosítása](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Teszt ütemezés egy meglévő példányát keresztül fog létrehozni a régi példány helyett egy új példányt a portálon. A régi példány naplók megőrzi a rendszer, de nem érhetők el a portálon.  
    > Egy teszt sikeres végrehajtása után a **ütemezés** művelet le lesz tiltva.

2. Válassza ki az ügynök, amely a teszt fog futni. Információ hozzáadása helyi végrehajtási tesztügynökök, lásd: [üzembe helyezése a helyi ügynök](azure-stack-vaas-local-agent.md).

3. OEM kiterjesztésű csomag ellenőrzés végrehajtásához válassza **ütemezés** nyissa meg a tevékenységütemezést a test-példány ehhez a helyi menüből.

4. Tekintse át a teszt paramétereket, majd **küldés** ütemezése OEM kiterjesztésű csomag ellenőrzése a végrehajtáshoz.

    OEM kiterjesztésű csomag ellenőrzési van felosztva, amelyek két manuális lépéseket: Az Azure Stack-frissítést, és az OEM frissítése.

   1. **Válassza ki** "Futtatás" a felhasználói felületen, hajtsa végre a precheck parancsfájlt. Ez az egy automatizált tesztelés elvégzése körülbelül 5 percet vesz igénybe, és nincs szükség felhasználói beavatkozásra.

   1. A precheck parancsprogram befejezését követően hajtsa végre a manuális lépés: **telepítése** a legújabb elérhető az Azure Stack frissítés az Azure Stack portal használatával.

   1. **Futtatás** Test-AzureStack a blokkban. Hibák lépnek fel, ha nem folytatja a vizsgálati és kapcsolattartó [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

       A Test-AzureStack parancs futtatásával kapcsolatos további információkért lásd: [rendszerállapotának ellenőrzése az Azure Stack](../operator/azure-stack-diagnostic-test.md).

   1. **Válassza ki** hajtsa végre a postcheck parancsfájlt a "Tovább gombra". Ez egy automatizált tesztelés és az Azure Stack-frissítési folyamat végét jelöli.

   1. **Válassza ki** "Futtatás" OEM frissítése a precheck parancsfájl végrehajtására.

   1. Az előzetesen ellenőrizni befejezését követően hajtsa végre a manuális lépés: **telepítése** az OEM-kiterjesztés csomagjának a portálon keresztül.

   1. **Futtatás** Test-AzureStack a blokkban.

      > [!NOTE]
      > Mint korábban, ne folytassa a vizsgálati és kapcsolattartó [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) Ha sikertelen. Ez a lépés nem kritikus, mivel, menti egy újbóli üzembe helyezés.

   1. **Válassza ki** hajtsa végre a postcheck parancsfájlt a "Tovább gombra". Ez az OEM frissítése lépés végét jelöli.

   1. Bármely egyéb kérdésre fennmaradó a vizsgálat végén és **kiválasztása** "Küldés".

   1. Ez az interaktív vizsgálati végét jelöli.

5. Tekintse át az eredmény az OEM kiterjesztésű csomag ellenőrzése. Ha a teszt sikeres volt, ütemezése végrehajtási felhőalapú szimulációs összetevő.

Egy csomag aláírási kérelem elküldéséhez küldése [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) futtatni társított a megoldás és a csomag nevét.

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)
