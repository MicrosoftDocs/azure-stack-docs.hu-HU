---
title: Eredeti berendezésgyártó (OEM) csomagok ellenőrzése a Azure Stack hub érvényesítése szolgáltatásként
description: Megtudhatja, hogyan ellenőrizheti az eredeti berendezésgyártó (OEM) csomagjait szolgáltatásként történő érvényesítéssel.
author: mattbriggs
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 621bed34b4d5f633b1a104c03c0bca341ec21bdd
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294978"
---
# <a name="validate-oem-packages"></a>OEM-csomagok ellenőrzése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Tesztelheti az új OEM-csomagokat, ha módosult a belső vezérlőprogram vagy az illesztőprogramok egy befejezett megoldás ellenőrzéséhez. Ha a csomag megfelelt a tesztnek, a Microsoft aláírja azt. A tesztnek tartalmaznia kell a frissített OEM-bővítmény csomagot azon illesztőprogramok és belső vezérlőprogram esetében, amelyek átadták a Windows Server emblémáját és a számítógépek tesztelését.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Csomagok feltöltése vagy elküldése előtt tekintse át a következőt: [OEM-csomag létrehozása](azure-stack-vaas-create-oem-package.md) a várt csomag formátumához és tartalmához.

## <a name="managing-packages-for-validation"></a>Csomagok kezelése érvényesítéshez

Ha a csomag- **ellenőrzési** munkafolyamattal ellenőrzi a csomagokat, meg kell adnia egy **Azure Storage-blob**URL-címét. Ez a blob az aláírt OEM-csomag, amelyet a rendszer a frissítési folyamat részeként fog telepíteni. Hozza létre a blobot a telepítés során létrehozott Azure Storage-fiók használatával (lásd: [az érvényesítés beállítása szolgáltatási erőforrásként](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Előfeltétel: Storage-tároló kiépítése

Hozzon létre egy tárolót a Storage-fiókban a csomagok Blobok számára. Ez a tároló használható a csomagok ellenőrzésének összes futtatásához.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az [Érvényesítés beállítása szolgáltatás-erőforrásokként beállításban](azure-stack-vaas-set-up-resources.md)létrehozott Storage-fiókot.

2. A bal oldali panelen a **blob szolgáltatás**területen válassza a **tárolók**lehetőséget.

3. Válassza a menüsorban a **+ tároló** elemet.
    1. Adja meg a tároló nevét, például `vaaspackages`.
    1. Válassza ki a nem hitelesített ügyfelek, például az Varga kívánt hozzáférési szintjét. A csomagok az egyes forgatókönyvekben való hozzáférésének biztosításáról további részleteket a [tárolók hozzáférési szintjének kezelésével](#handling-container-access-level)foglalkozó témakörben talál.

### <a name="upload-package-to-storage-account"></a>Csomag feltöltése a Storage-fiókba

1. Készítse elő az érvényesíteni kívánt csomagot. Ez egy `.zip` fájl, amelynek tartalmának meg kell egyeznie a [OEM-csomag létrehozása](azure-stack-vaas-create-oem-package.md)című témakörben leírt struktúrával.

    > [!NOTE]
    > Győződjön meg arról, hogy a `.zip` tartalma a `.zip` fájl gyökerébe kerül. A csomagban nem lehetnek almappák.

1. A [Azure Portal](https://portal.azure.com)válassza ki a csomag tárolót, és töltse fel a csomagot a menüsávon a **feltöltés** lehetőség kiválasztásával.

1. Válassza ki a feltölteni kívánt csomagot `.zip` fájlt. Tartsa meg az alapértelmezett **blob-típust** (azaz a **blob blokkolását**) és a **blokk méretét**.

### <a name="generate-package-blob-url-for-vaas"></a>Csomag blob URL-címének előállítása az Varga számára

Ha a **csomag-ellenőrzési** munkafolyamatot az Azure-portálon hozza létre, meg kell adnia egy URL-címet a csomagot tartalmazó Azure Storage-blobhoz. Egyes *interaktív* tesztek, például a **havi Azure stack hub Update ellenőrzése** és az **OEM-bővítmények ellenőrzése**is szükségesek a Blobok csomagjának URL-címéhez.

#### <a name="handling-container-access-level"></a>Tárolók hozzáférési szintjének feldolgozása

A minimálisan szükséges hozzáférési szint attól függ, hogy egy csomag-ellenőrzési munkafolyamatot hoz létre, vagy egy *interaktív* tesztet ütemez.

A **privát** és a **blob** -hozzáférési szintek esetében átmenetileg hozzáférést kell biztosítania a csomag blobjának a [közös hozzáférési aláírás](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS) megadásával. A **tároló** hozzáférési szintjének nem kell sas URL-címeket előállítania, de lehetővé teszi a nem hitelesített hozzáférést a tárolóhoz és a blobokhoz.

|Hozzáférési szint | Munkafolyamat-követelmény | Tesztelési követelmény |
|---|---------|---------|
|Privát | SAS URL-cím létrehozása csomag blobján ([1. lehetőség](#option-1-generate-a-blob-sas-url)). | Állítson elő SAS URL-címet a fiók szintjén, és manuálisan adja hozzá a csomag blobjának nevét ([2. lehetőség](#option-2-construct-a-container-sas-url)). |
|Blob | Adja meg a blob URL-tulajdonságát ([3. lehetőség](#option-3-grant-public-read-access)). | Állítson elő SAS URL-címet a fiók szintjén, és manuálisan adja hozzá a csomag blobjának nevét ([2. lehetőség](#option-2-construct-a-container-sas-url)). |
|Tároló | Adja meg a blob URL-tulajdonságát ([3. lehetőség](#option-3-grant-public-read-access)). | Adja meg a blob URL-tulajdonságát ([3. lehetőség](#option-3-grant-public-read-access)).

A csomagok hozzáférésének megadására vonatkozó beállítások a lehető legkevesebb hozzáféréshez vannak rendelve.

#### <a name="option-1-generate-a-blob-sas-url"></a>1\. lehetőség: blob SAS URL-cím létrehozása

Akkor használja ezt a beállítást, ha a tároló hozzáférési szintje **magán**értékre van állítva, ahol a tároló nem engedélyezi a nyilvános olvasási hozzáférést a tárolóhoz vagy a blobokhoz.

> [!NOTE]
> Ez a metódus nem fog működni az *interaktív* teszteknél. Lásd [a 2. lehetőséget: tároló sas URL-címének létrehozása](#option-2-construct-a-container-sas-url).

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a Storage-fiókját, és navigáljon a csomagot tartalmazó. zip fájlhoz.

2. Válassza a helyi menü **sas előállítása** elemét.

3. Válassza az **olvasás** az **engedélyekből**lehetőséget.

4. Állítsa be a **kezdési időt** az aktuális időpontra, a **befejezési időt** pedig legalább 48 óráig a **kezdési**időpontból. Ha más munkafolyamatokat is szeretne létrehozni ugyanazzal a csomaggal, érdemes lehet megnövelni a tesztelés hosszának **befejezési idejét** .

5. Válassza **a blob sas-token és URL-cím előállítása**lehetőséget.

A **blob sas URL-címének** használata a csomag blob URL-címeinek a portálra való megadásakor.

#### <a name="option-2-construct-a-container-sas-url"></a>2\. lehetőség: tároló SAS URL-címének létrehozása

Akkor használja ezt a beállítást, ha a Storage-tároló hozzáférési szintje **magánjellegűre** van állítva, és egy *interaktív* teszthez meg kell adnia egy csomag blob URL-címét. Ezt az URL-címet a munkafolyamat szintjén is használhatja.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Válassza a **blob** lehetőséget az **engedélyezett szolgáltatások beállításai**közül. Törölje a többi beállítást.

1. Válassza ki a **tárolót** és az **objektumot** az **engedélyezett erőforrástípusok**közül.

1. Válassza az **olvasás** és **Listázás** lehetőséget az **engedélyezett engedélyek**közül. Törölje a többi beállítást.

1. Válassza ki a **kezdési időt** az aktuális idő és a **Befejezés időpontja** szerint legalább 14 napig a **kezdési időpontból**. Ha más teszteket is futtat ugyanazzal a csomaggal, érdemes megfontolnia a tesztelési **idő** hosszának növelését. Az összes, a **befejezési időpont** utáni ütemezett teszt sikertelen lesz, és új sas-t kell létrehozni.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    A formátumnak a következőképpen kell megjelennie: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Módosítsa a generált SAS URL-címet, hogy tartalmazza a csomag tárolóját, a `{containername}`és a csomag blobjának nevét `{mypackage.zip}`a következőképpen: `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Akkor használja ezt az értéket, ha a csomag blob URL-címét adja meg a portálon.

#### <a name="option-3-grant-public-read-access"></a>3\. lehetőség: nyilvános olvasási hozzáférés megadása

Akkor használja ezt a beállítást, ha a nem hitelesített ügyfelek számára engedélyezi az egyes blobokhoz való hozzáférést, vagy *interaktív* tesztek esetén a tárolót.

> [!CAUTION]
> Ez a beállítás megnyitja a blob (oka) t a névtelen írásvédett hozzáféréshez.

1. Állítsa be a csomag tárolójának hozzáférési szintjét **blob** vagy **tároló** értékre a következő szakaszban található utasítások szerint: [névtelen felhasználók engedélyeinek megadása tárolók és Blobok](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)számára.

    > [!NOTE]
    > Ha a csomag URL-címét egy *interaktív* teszthez biztosítja, a tesztelés folytatásához **teljes nyilvános olvasási hozzáférést** kell adnia a tárolóhoz.

1. A csomag tárolóban válassza ki a csomag blobot a Tulajdonságok ablaktábla megnyitásához.

1. Másolja ki az **URL-címet**. Akkor használja ezt az értéket, ha a csomag blob URL-címét adja meg a portálon.

## <a name="create-a-package-validation-workflow"></a>Csomag-ellenőrzési munkafolyamat létrehozása

1. Jelentkezzen be az [Varga portálra](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Válassza a **Start** lehetőséget a **csomag érvényesítése** csempén.

    ![Csomag érvényességi munkafolyamatának csempe](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Adja meg az Azure Storage-blob URL-címét a Microsofttól származó aláírást igénylő teszt aláírt OEM-csomaghoz. Útmutatásért lásd: [csomag blob URL-címének előállítása az Varga számára](#generate-package-blob-url-for-vaas).

6. Másolja a Azure Stack hub Update Package mappát a DVM egy helyi könyvtárába. Adja meg annak a mappának az elérési útját, **amely a csomag zip-fájlját és metaadat-fájlját tartalmazza** a "AzureStack frissítési csomag elérési útja"

7. Másolja a fent létrehozott OEM-csomag mappát a DVM egy helyi könyvtárába. Adja meg annak a mappának az elérési útját, **amely a csomag zip-fájlját és metaadat-fájlját tartalmazza** az "OEM frissítési csomag mappa elérési útja

    > [!NOTE]
    > Másolja a Azure Stack hub Update és az OEM Update **két külön** könyvtárba.

8. "RequireDigitalSignature" – **igaz** értéket ad meg, ha a csomagnak a Microsoft által aláírtnak kell lennie (OEM-ellenőrzési munkafolyamat futtatása). Ha a legújabb Azure Stack hub Update szolgáltatásban érvényesít egy Microsoft által aláírt csomagot, adja meg ezt az értéket false (a havi Azure Stack hub Update ellenőrzésének futtatásakor).

9. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Munkafolyamat létrehozása után a környezeti paraméterek nem módosíthatók.

10. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

11. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    A rendszer átirányítja a tesztek összegzése lapra.

## <a name="required-tests"></a>Szükséges tesztek

Az OEM-csomagok érvényesítéséhez a következő tesztek szükségesek:

- OEM-ellenőrzési munkafolyamat

## <a name="run-package-validation-tests"></a>Csomag-ellenőrzési tesztek futtatása

1. A **csomag-ellenőrzési tesztek összegzése** lapon a forgatókönyvnek megfelelő, a felsorolt tesztek egy részhalmazát fogja futtatni.

    Az érvényesítési munkafolyamatokban a teszt **ütemezése** a munkafolyamat-létrehozás során megadott munkafolyamat-szintű általános paramétereket használja (lásd: a [Azure stack hub-hitelesítéshez használt munkafolyamat általános paraméterei szolgáltatásként](azure-stack-vaas-parameters.md)). Ha a teszt paramétereinek bármelyik értéke érvénytelenvé válik, a [munkafolyamat-paraméterek módosítása](azure-stack-vaas-monitor-test.md#change-workflow-parameters)elem utasításai szerint újra meg kell adnia őket.

    > [!NOTE]
    > Egy meglévő példányon egy érvényesítési teszt ütemezése egy új példányt hoz létre a régi példány helyett a portálon. A régi példány naplói megmaradnak, de nem érhetők el a portálról.  
    > Ha egy teszt sikeresen befejeződött, az **ütemezett** művelet le lesz tiltva.

2. Válassza ki azt az ügynököt, amely a tesztet futtatni fogja. A helyi tesztelési végrehajtó ügynökök hozzáadásával kapcsolatos információkért lásd: [a helyi ügynök üzembe helyezése](azure-stack-vaas-local-agent.md).

3. A teszt futtatásának ütemezéséhez válassza az **Ütemezés** lehetőséget a helyi menüben, és nyisson meg egy kérdést a tesztelési példány ütemezéséhez.

4. Tekintse át a tesztelési paramétereket, majd válassza a **Submit (Küldés** ) lehetőséget a teszt elvégzéséhez.

5. Tekintse át a **szükséges** tesztek eredményét.

A csomag-aláírási kérelem elküldéséhez küldje el [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) a futtatáshoz társított megoldás nevét és a csomag érvényesítési nevét.

## <a name="next-steps"></a>Következő lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)
