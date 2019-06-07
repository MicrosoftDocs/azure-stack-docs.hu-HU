---
title: Oktatóanyag – szolgáltatásként érvényesítéshez beállítása erőforrások |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan erőforrások beállítása szolgáltatás ellenőrzés céljából.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 9ad15342a8c0116f928b416f26e64b08563a5c14
ms.sourcegitcommit: 75b13158347963063b7ee62b0ec57894b542c1be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66748954"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Oktatóanyag: A szolgáltatás érvényesítése erőforrások beállítása

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Érvényesítés szolgáltatásként (VaaS) egy Azure-szolgáltatás, amellyel ellenőrizheti, és támogatja az Azure Stack megoldásokat a piacon. Kövesse az ebben a cikkben ellenőrzése a megoldás a szolgáltatás használata előtt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felkészülés az VaaS használata az Azure Active Directory (AD) mentése beállításával.
> * Tárfiók létrehozása.

## <a name="configure-an-azure-ad-tenant"></a>Az Azure AD-bérlő konfigurálása

Az Azure AD-bérlő regisztrálásához a szervezet, és hitelesíti a felhasználókat az VaaS szolgál. A partner fogja használni a szerepköralapú hozzáférés-vezérlés (RBAC) funkcióját a bérlő kezelésére is, akik a fiókpartner-szervezet VaaS használni. További információkért lásd: [Mi az az Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Bérlő létrehozása

Hozzon létre egy bérlőt, amelyet a szervezet VaaS szolgáltatások eléréséhez fog használni. Egy leíró nevet, például használja `ContosoVaaS@onmicrosoft.com`.

1. Az Azure AD-bérlő létrehozása a [az Azure portal](https://portal.azure.com), vagy használjon egy meglévő bérlőt. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Adja hozzá a szervezet tagjai a bérlőhöz. Ezek a felhasználók felelőssége a szolgáltatás használatával megtekinthető és ütemezhető teszteket. Ha befejezte a regisztrációs, felhasználói hozzáférési szintet határozza meg.

    Engedélyezi a felhasználókat a bérlőben a VaaS műveletek futtatására a következő szerepkörök hozzárendelésével:

    | Szerepkör neve | Leírás |
    |---------------------|------------------------------------------|
    | Tulajdonos | Az összes erőforrás teljes hozzáféréssel rendelkezik. |
    | Olvasó | Megtekintheti összes erőforrást, de nem létrehozása és kezelése. |
    | Teszt Közreműködője | Létrehozhat és test-erőforrások kezeléséhez. |

    Ha a szerepköröket a **Azure Stack érvényesítési Service** alkalmazás:

   1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
   2. Válassza ki **minden szolgáltatás** > **Azure Active Directory** alatt a **identitás** szakaszban.
   3. Válassza ki **vállalati alkalmazások** > **Azure Stack érvényesítési Service** alkalmazás.
   4. Válassza a **Felhasználók és csoportok** elemet. A **Azure Stack érvényesítési Service – felhasználók és csoportok** panel felsorolja a felhasználók számára a engedélyt az alkalmazás használatára.
   5. Válassza ki **+ Hozzáadás felhasználó** felhasználó hozzáadása a bérlőről és rendelhet egy szerepkört.

      Ha szeretné elkülöníteni a VaaS erőforráshoz és művelethez egy szervezeten belül különböző csoportokkal, az Azure AD-bérlő több címtárat is létrehozhat.

### <a name="register-your-tenant"></a>A bérlő regisztrálásához

Ez a folyamat engedélyezi a bérlőben a **Azure Stack érvényesítési Service** Azure AD-alkalmazást.

1. A bérlő a következő információkat küldeni a Microsoftnak, [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

    | Adatok | Leírás |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Szervezet neve | A hivatalos szervezet nevét. |
    | Az Azure AD-bérlő könyvtár neve | Regisztrálja a címtár Azure AD-bérlő neve. |
    | Az Azure AD címtár azonosítója | Az Azure AD-bérlő Directory, a címtárhoz társított GUID. Információk megkereséséhez az Azure AD-bérlő címtár-azonosító: [Bérlőazonosító beszerzése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-values-for-signing-in). |

2. Várjon, amíg az Azure Stack érvényesítési csapat, ellenőrizze, hogy a bérlő használja az VaaS portal megerősítést.

### <a name="consent-to-the-vaas-application"></a>Hozzájárulás az a VaaS alkalmazáshoz

Az Azure AD-rendszergazdaként a szükséges engedélyek nevében a bérlő az Azure ad-ben VaaS alkalmazás megadása:

1. A bérlő globális rendszergazdai hitelesítő adatainak használatával jelentkezzen be a [VaaS portál](https://azurestackvalidation.com/). 

2. Válassza ki **fiókom**.

3 elfogadására folytatja, amikor a rendszer VaaS a felsorolt az Azure AD-engedélyek megadására kéri.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Teszt végrehajtása VaaS jelenít meg a diagnosztikai naplók egy Azure Storage-fiókhoz. Vizsgálati naplókat, valamint a tárfiók lehet, hogy is használható, a feltöltési az OEM bővítménycsomagok a csomag érvényesítése munkafolyamat.

Az Azure Storage-fiókot az Azure nyilvános felhő, nem pedig az Azure Stack-környezet üzemel.

1. Az Azure Portalon válassza ki a **minden szolgáltatás** > **tárolási** > **tárfiókok**. Az a **Tárfiókok** panelen válassza ki **Hozzáadás**.

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.

3. A **erőforráscsoport**válassza **új létrehozása**. Adja meg az új erőforráscsoport nevét.

4. Tekintse át a [elnevezési konvenciók](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#storage) Azure Storage-fiókok. Adja meg a tárfiók nevét.

5. Válassza ki a **USA nyugati régiójában** a tárfiók régióját.

    Győződjön meg arról, hogy hálózati nem számítunk fel díjat a naplók tárolásához, hogy csak az Azure Storage-fiók konfigurálható a **USA nyugati régiójában** régióban. Az adatreplikáció és a gyakori elérésű tárolási szinten a szolgáltatás az adatok nem szükségesek. Mindkét funkció engedélyezése jelentősen növeli a költségeket.

6. A beállítások hagyja az alapértelmezett értékeket, kivéve a **fióktípus**:

    - A **üzemi modell** mező értéke **Resource Manager** alapértelmezés szerint.
    - A **Teljesítmény** mező alapértelmezett értéke **Standard**.
    - Válassza ki **fióktípus** mezőt **a Blob storage-** .
    - A **replikációs mező** értékre van állítva **helyileg redundáns tárolás (LRS)** alapértelmezés szerint.
    - A **Hozzáférési szint** alapértelmezett beállítása **Gyakori elérésű**.

7. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

## <a name="next-steps"></a>További lépések

A környezet nem engedélyezi a bejövő kapcsolatok, kövesse az oktatóanyag a helyi ügynök egy teszt futtatásához hardverre történő üzembe helyezése.

> [!div class="nextstepaction"]
> [A helyi ügynök telepítése](azure-stack-vaas-local-agent.md)
