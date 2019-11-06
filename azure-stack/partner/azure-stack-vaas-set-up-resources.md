---
title: Oktatóanyag – erőforrások beállítása szolgáltatásként történő érvényesítéshez | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthat be erőforrásokat az érvényesítéshez szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: e36235af4dea72ae6d8016085ee18aec819ae4dd
ms.sourcegitcommit: 20d1c0ab3892e9c4c71d5b039457f1e15b1c84c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73618242"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Oktatóanyag: erőforrások beállítása szolgáltatásként történő érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az érvényesítési szolgáltatásként (az Azure-ban) olyan Azure-szolgáltatás, amely a piacon Azure Stack megoldások ellenőrzéséhez és támogatásához használható. Kövesse ezt a cikket, mielőtt a szolgáltatást használja a megoldás ellenőrzéséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készüljön fel az Azure Active Directory (AD) beállításával való használatra.
> * Tárfiók létrehozása.

## <a name="configure-an-azure-ad-tenant"></a>Azure AD-bérlő konfigurálása

Az Azure AD-bérlő használatával regisztrálhat egy szervezetet, és hitelesítheti a felhasználókat az Azure-ban. A partner a bérlő szerepköralapú hozzáférés-vezérlési (RBAC) funkcióit fogja használni, hogy a partner szervezeten belül kik is használhatják az Varga-t. További információkért lásd: [Mi az az Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Bérlő létrehozása

Hozzon létre egy olyan bérlőt, amelyet a szervezete használni fog az Varga szolgáltatások eléréséhez. Adjon meg egy leíró nevet, például `ContosoVaaS@onmicrosoft.com`.

1. Hozzon létre egy Azure AD-bérlőt a [Azure Portalban](https://portal.azure.com), vagy használjon egy meglévő bérlőt. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Vegye fel a szervezet tagjait a bérlőbe. Ezeknek a felhasználóknak a feladata a szolgáltatás használata a tesztek megtekintéséhez vagy megadásához. A regisztráció befejezését követően meg kell határoznia a felhasználók hozzáférési szintjeit.

    A következő szerepkörök egyikének hozzárendelésével engedélyezheti a bérlőben lévő felhasználók számára a műveletek futtatását az Varga-ban:

    | Szerepkör neve | Leírás |
    |---------------------|------------------------------------------|
    | Tulajdonos | Teljes hozzáféréssel rendelkezik az összes erőforráshoz. |
    | Olvasó | Megtekintheti az összes erőforrást, de nem hozható létre és nem kezelhető. |
    | Tesztelési közreműködő | Létrehozhat és kezelhet tesztelési erőforrásokat. |

    Szerepkörök társítása a **Azure stack érvényesítési szolgáltatásalkalmazás** alkalmazásban:

   1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
   2. Válassza az **összes szolgáltatás** > **Azure Active Directory** az **identitás** szakaszban.
   3. Válassza a **vállalati alkalmazások** > **Azure stack érvényesítési szolgáltatásalkalmazás** elemet.
   4. Válassza a **Felhasználók és csoportok** elemet. A **Azure stack érvényesítési szolgáltatás – felhasználók és csoportok** panel felsorolja azokat a felhasználókat, akik jogosultak az alkalmazás használatára.
   5. Válassza a **+ felhasználó hozzáadása** lehetőséget egy felhasználó hozzáadásához a bérlőtől, és rendeljen hozzá egy szerepkört.

      Ha el szeretné különíteni az Azure-erőforrásokat és-műveleteket a szervezeten belüli különböző csoportok között, létrehozhat több Azure AD-bérlői könyvtárat is.

### <a name="register-your-tenant"></a>Bérlő regisztrálása

Ezzel a folyamattal engedélyezheti a bérlőnek az **Azure stack érvényesítési szolgáltatás** Azure ad-alkalmazását.

1. Küldje el az alábbi adatokat a bérlőről a Microsoftnak a [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)címen.

    | Adatok | Leírás |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Szervezet neve | A hivatalos szervezet neve. |
    | Azure AD-bérlői könyvtár neve | Az Azure AD-bérlő könyvtárának neve regisztrálva van. |
    | Azure AD-bérlői címtár azonosítója | A címtárhoz tartozó Azure AD-bérlői könyvtár GUID-azonosítója. További információ az Azure AD-bérlői címtár AZONOSÍTÓjának megkereséséről: a [bérlő azonosítójának beolvasása](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-values-for-signing-in). |

2. Várja meg, amíg a Azure Stack ellenőrző csapata megerősíti, hogy a bérlő használhatja-e az adatbázis-portált.

### <a name="consent-to-the-vaas-application"></a>Beleegyezett az alapprogram-alkalmazásba

Az Azure AD-rendszergazdaként adja meg az Azure AD-alkalmazás számára a bérlő nevében szükséges engedélyeket:

1. A bérlőhöz tartozó globális rendszergazdai hitelesítő adatokkal jelentkezzen be az [Varga portálra](https://azurestackvalidation.com/). 

2. Válassza **a saját fiók**lehetőséget.

3 fogadja el a feltételt, amikor a rendszer kéri, hogy adja meg az Azure AD-engedélyek listáját.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

A tesztelés végrehajtása során az Azure a diagnosztikai naplókat egy Azure Storage-fiókba exportálja. A naplók tesztelésén kívül a Storage-fiók is felhasználható a csomag-ellenőrzési munkafolyamat OEM-bővítmény csomagjainak feltöltésére.

Az Azure Storage-fiók az Azure nyilvános felhőben, nem pedig a Azure Stack-környezetben üzemel.

1. A Azure Portal válassza a **minden szolgáltatás** > **Storage** - > **Storage-fiókok**lehetőséget. A **Storage-fiókok** panelen válassza a **Hozzáadás**lehetőséget.

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.

3. Az **erőforráscsoport**területen válassza az **új létrehozása**lehetőséget. Adja meg az új erőforráscsoport nevét.

4. Tekintse át az Azure Storage-fiókok [elnevezési konvencióit](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#storage) . Adja meg a tárfiók nevét.

5. Válassza ki az **USA nyugati** régióját a Storage-fiókjához.

    Az Azure Storage-fiók csak az **USA nyugati** régiójának használatára konfigurálható, hogy a hálózati költségek ne legyenek a naplók tárolásához. Ezekhez az adatokhoz nem szükséges az adatreplikálás és a gyakori tárolási réteg funkció. Az egyik funkció engedélyezése jelentősen növeli a költségeket.

6. A beállításokat a **Fiók típusa**kivételével az alapértelmezett értékekre hagyja:

    - A **telepítési modell** mező alapértelmezett értéke a **Resource Manager** .
    - A **Teljesítmény** mező alapértelmezett értéke **Standard**.
    - Válassza ki a **Fiók típusa** mezőt **blob Storage**-ként.
    - Alapértelmezés szerint a **replikálási mező** a **helyileg REDUNDÁNS tárolás (LRS)** értékre van beállítva.
    - A **Hozzáférési szint** alapértelmezett beállítása **Gyakori elérésű**.

7. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

## <a name="next-steps"></a>Következő lépések

Ha a környezet nem engedélyezi a kötött kapcsolatokat, kövesse az oktatóanyagot a helyi ügynök üzembe helyezéséről, hogy tesztet futtasson a hardveren.

> [!div class="nextstepaction"]
> [A helyi ügynök üzembe helyezése](azure-stack-vaas-local-agent.md)
