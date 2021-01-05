---
title: Azure AD-és tárolási erőforrások beállítása az Varga-hoz
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan állíthatja be az Azure AD-és tárolási erőforrásokat Azure Stack hub-ellenőrzéshez szolgáltatásként.
author: mattbriggs
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 19f1b9e33f0304d155dad070ef8cb10fc6a930fc
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869049"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Oktatóanyag: erőforrások beállítása szolgáltatásként történő érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az érvényesítési szolgáltatásként (adatbázis-ellenőrzés) olyan Azure-szolgáltatás, amely a piacon Azure Stack hub-megoldások ellenőrzéséhez és támogatásához használatos. Kövesse ezt a cikket, mielőtt a szolgáltatást használja a megoldás ellenőrzéséhez.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Készüljön fel az Azure Active Directory (AD) beállításával való használatra.
> * Tárfiók létrehozása.

## <a name="configure-an-azure-ad-tenant"></a>Azure AD-bérlő konfigurálása

Az Azure AD-bérlő használatával regisztrálhat egy szervezetet, és hitelesítheti a felhasználókat az Azure-ban. A partner a bérlő szerepköralapú hozzáférés-vezérlési (RBAC) funkcióit fogja használni, hogy a partner szervezeten belül kik is használhatják az Varga-t. További információkért lásd: [Mi az az Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Bérlő létrehozása

Hozzon létre egy olyan bérlőt, amelyet a szervezete használni fog az Varga szolgáltatások eléréséhez. Adjon meg egy leíró nevet (például: `ContosoVaaS@onmicrosoft.com` ).

1. Hozzon létre egy Azure AD-bérlőt a [Azure Portalban](https://portal.azure.com), vagy használjon egy meglévő bérlőt. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](/azure/active-directory/get-started-azure-ad). -->

2. Vegye fel a szervezet tagjait a bérlőbe. Ezeknek a felhasználóknak a feladata a szolgáltatás használata a tesztek megtekintéséhez vagy megadásához. Ha befejezte a regisztrációt, megadhatja a felhasználók hozzáférési szintjét.

    A következő szerepkörök egyikének hozzárendelésével engedélyezheti a bérlőben lévő felhasználók számára a műveletek futtatását az Varga-ban:

    | Szerepkör neve | Leírás |
    |---------------------|------------------------------------------|
    | Tulajdonos | Teljes hozzáféréssel rendelkezik az összes erőforráshoz. |
    | Olvasó | Megtekintheti az összes erőforrást, de nem hozható létre és nem kezelhető. |
    | Tesztelési közreműködő | Létrehozhat és kezelhet tesztelési erőforrásokat. |

    Szerepkörök hozzárendeléséhez az **Azure stack hub Validation Service** alkalmazásban:

   1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
   2. Válassza az **összes szolgáltatás**  >  **Azure Active Directory** az **Identity (identitás** ) szakaszban.
   3. Válassza a **vállalati alkalmazások**  >  **Azure stack hub Validation Service** -alkalmazás elemet.
   4. Válassza a **Felhasználók és csoportok** elemet. Az **Azure stack hub Validation Service – felhasználók és csoportok** panel felsorolja azokat a felhasználókat, akik jogosultak az alkalmazás használatára.
   5. Válassza a **+ felhasználó hozzáadása** lehetőséget egy felhasználó hozzáadásához a bérlőtől, és rendeljen hozzá egy szerepkört.

      Ha el szeretné különíteni az Azure-erőforrásokat és-műveleteket a szervezeten belüli különböző csoportok között, létrehozhat több Azure AD-bérlői könyvtárat is.

### <a name="register-your-tenant"></a>Bérlő regisztrálása

Ez a folyamat engedélyezi a bérlőnek az **Azure stack hub Validation Service** Azure ad-alkalmazást.

1. Küldje el az alábbi adatokat a bérlőről a Microsoftnak a következő címen: [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) .

    | Adatok | Leírás |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Szervezetnév | A hivatalos szervezet neve. |
    | Azure AD-bérlői könyvtár neve | Az Azure AD-bérlő könyvtárának neve regisztrálva van. |
    | Azure AD-bérlői címtár azonosítója | A címtárhoz tartozó Azure AD-bérlői könyvtár GUID-azonosítója. További információ az Azure AD-bérlői címtár AZONOSÍTÓjának megkereséséről: a [bérlő azonosítójának beolvasása](/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-values-for-signing-in). |

2. Várjon, amíg a Azure Stack hub ellenőrzési csapata megerősíti, hogy a bérlő használhatja-e a Azure Stack hub ellenőrzési portálját.

### <a name="consent-to-the-vaas-app"></a>Beleegyezett az alapalkalmazásba

Az Azure AD-rendszergazdaként adja meg az Azure AD-alkalmazás számára a bérlő nevében szükséges engedélyeket:

1. A bérlőhöz tartozó globális rendszergazdai hitelesítő adatokkal jelentkezzen be az [Azure stack hub ellenőrzési portálján](https://azurestackvalidation.com/).

2. Válassza **a saját fiók** lehetőséget.

3 a feltételek elfogadása a folytatáshoz, amikor a rendszer kéri, hogy adja meg az Azure AD-engedélyek listáját.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

A tesztelés végrehajtása során az Azure a diagnosztikai naplókat egy Azure Storage-fiókba exportálja. A naplók tesztelésén kívül a Storage-fiók is felhasználható a csomag-ellenőrzési munkafolyamat OEM-bővítmény csomagjainak feltöltésére.

Az Azure Storage-fiók az Azure nyilvános felhőben üzemel, nem az Azure Stack hub-környezetben.

1. A Azure Portal válassza a **minden szolgáltatás**  >  **tároló**  >  **Storage-fiókok** lehetőséget. A **Storage-fiókok** panelen válassza a **Hozzáadás** lehetőséget.

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.

3. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget. Adja meg az új erőforráscsoport nevét.

4. Tekintse át az Azure Storage-fiókok [elnevezési konvencióit](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#storage) . Adja meg a tárfiók nevét.

5. Válassza ki az **USA nyugati** régióját a Storage-fiókjához.

    Annak érdekében, hogy a hálózati költségek ne legyenek felszámítva a naplók tárolásához, az Azure Storage-fiók úgy is konfigurálható, hogy csak az **USA nyugati** régióját használja. Ezekhez az adatokhoz nem szükséges az adatreplikálás és a gyakori tárolási réteg funkció. Az egyik funkció engedélyezése jelentősen növeli a költségeket.

6. A beállításokat a **Fiók típusa** kivételével az alapértelmezett értékekre hagyja:

    - A **telepítési modell** mező alapértelmezett értéke a **Resource Manager** .
    - A **Teljesítmény** mező alapértelmezett értéke **Standard**.
    - Válassza ki a **Fiók típusa** mezőt **blob Storage**-ként.
    - Alapértelmezés szerint a **replikálási mező** a **helyileg REDUNDÁNS tárolás (LRS)** értékre van beállítva.
    - A **Hozzáférési szint** alapértelmezett beállítása **Gyakori elérésű**.

7. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

## <a name="next-steps"></a>További lépések

Ha a környezet nem engedélyezi a bejövő kapcsolatokat, kövesse a helyi ügynök üzembe helyezéséről szóló oktatóanyagot a hardveren futó teszt futtatásához.

> [!div class="nextstepaction"]
> [A helyi ügynök üzembe helyezése](azure-stack-vaas-local-agent.md)
