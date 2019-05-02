---
title: Az Azure stack-beli szolgáltatásnév létrehozása |} A Microsoft Docs
description: Ismerteti, hogyan lehet létrehozni egy szolgáltatásnevet, a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager-erőforrásokhoz való hozzáférés kezelésére használható.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 3fc50d068eb07d7da2d55d855b2ea8ea758ca6b1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64300614"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Alkalmazások hozzáférést biztosíthat az Azure Stack-erőforrások egyszerű szolgáltatások létrehozásával

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Egy alkalmazás-hozzáférés egy egyszerű szolgáltatásnév létrehozása az Azure Resource Managert használja, amely szerint adhat az Azure Stack-erőforrások. Egyszerű szolgáltatás lehetővé teszi a delegált engedélyeket használatával [szerepköralapú hozzáférés-vezérlés](azure-stack-manage-permissions.md).

Ajánlott eljárásként használjon szolgáltatásnevek alkalmazásai számára. Szolgáltatásnevek használata előnyösebb fut egy alkalmazást a saját hitelesítő adataival a következő okok miatt:

* Engedélyeket rendelhet a szolgáltatás egyszerű, amelyek eltérnek a saját fiók engedélyeit. A szolgáltatásnév engedélyeinek általában pontosan mit az alkalmazásnak szüksége van korlátozva.
* Nem kell módosítani az alkalmazás hitelesítő adatait, ha a szerepkör vagy felelősséget módosítása.
* A tanúsítvány segítségével automatizálhatja a hitelesítést egy felügyelet nélküli parancsfájl futtatásakor.

## <a name="example-scenario"></a>Példaforgatókönyv

Rendelkezünk egy konfigurációs felügyeleti alkalmazás, amelyet a szoftverleltár-Azure-erőforrások Azure Resource Manager használatával. A szolgáltatásnév létrehozásához, és rendelje hozzá az Olvasó szerepkörhöz. Ez a szerepkör Azure-erőforrások az alkalmazás csak olvasható hozzáférést biztosít.

## <a name="getting-started"></a>Első lépések

Kövesse a lépéseket ebben a cikkben egy útmutató, mint:

* Hozzon létre egy egyszerű szolgáltatást az alkalmazás számára.
* Az alkalmazás regisztrálásához és a hitelesítési kulcs létrehozásához.
* Az alkalmazás hozzárendelése szerepkörhöz.

Azure Stack konfigurált Active Directory módja határozza meg, hogyan hoz létre egy egyszerű szolgáltatást. Válasszon az alábbi lehetőségek közül:

* Az egyszerű szolgáltatás létrehozása [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Az egyszerű szolgáltatás létrehozása [Active Directory összevonási szolgáltatások (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

A lépéseket a hozzárendelése egy egyszerű szolgáltatást a szerepkör ugyanazt az Azure AD és az AD FS. Miután létrehozta az egyszerű szolgáltatás, [engedélyeket delegálhatnak](azure-stack-create-service-principals.md) , ha hozzárendeli egy szerepkörhöz.

## <a name="create-service-principal-for-azure-ad"></a>Az Azure ad egyszerű szolgáltatás létrehozása

Ha az Azure Stack az Azure AD használja, mint az ügyfélidentitás-tárolóval, létrehozhat egy szolgáltatás egyszerű használja ugyanazokat a lépéseket, mint az Azure-ban, az Azure portal használatával.

> [!NOTE]
> Ellenőrizze, hogy rendelkezik a [szükséges Azure AD-engedélyekről](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) egyszerű szolgáltatás létrehozása előtt.

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az alkalmazás egyszerű szolgáltatás létrehozása:

1. Jelentkezzen be az Azure-fiók révén a [az Azure portal](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk** > **új regisztrációs**.
3. Adjon meg egy nevet.
4. Válassza ki a **támogatott fióktípusok**.
5.  Adjon hozzá egy URI-t az alkalmazáshoz. Válassza ki **webes** szeretne létrehozni az alkalmazás számára. Miután beállította az értékeket, válassza ki a **regisztrálása**.

### <a name="get-credentials"></a>Hitelesítő adatok beolvasása

Ha programozott módon jelentkezik be, az azonosító használata az alkalmazás és a egy hitelesítési kulcsra. Ezek az értékek lekéréséhez:

1. A **alkalmazásregisztrációk** az Active Directoryban, válassza ki az alkalmazását.

2. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában. Az alkalmazások a mintaalkalmazások használatához **ügyfél-azonosító** kontextusban való megnevezésekor a **Alkalmazásazonosító**.

     ![Az alkalmazás alkalmazás azonosítója](./media/azure-stack-create-service-principals/image12.png)
3. Hitelesítési kulcs létrehozásához válassza **tanúsítványok és titkos kulcsok**.

4. Válassza az **Új titkos ügyfélkód** lehetőséget.

5. Adjon meg egy leírást a kulcshoz, válasszon ki egy időtartamot a kulcshoz, majd válassza **Hozzáadás**. 

6. Ha elkészült, a titkos kód jelenik meg. Jegyezze fel ezt az értéket, mivel később nem tudja lekérni a kulcsot. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

![Mentett kulcs figyelmeztetés kulcs értékét.](./media/azure-stack-create-service-principals/image15.png)

Az utolsó lépés [az alkalmazás-szerepkör hozzárendelése](azure-stack-create-service-principals.md).

## <a name="create-service-principal-for-ad-fs"></a>Az AD FS egyszerű szolgáltatás létrehozása

Ha telepítette az Azure Stack az ügyfélidentitás-tárolóval, mint az AD FS használatával, használhatja a PowerShell a következő feladatokhoz:

* Hozzon létre egy egyszerű szolgáltatást.
* Egyszerű szolgáltatás hozzárendelése szerepkörhöz.
* Jelentkezzen be az egyszerű szolgáltatás identitás használatával.

Az egyszerű szolgáltatás létrehozása a részletekért lásd: [az AD FS egyszerű szolgáltatás létrehozása](../operator/azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Az egyszerű szolgáltatás hozzárendelése szerepkörhöz

Az előfizetésben lévő erőforrások eléréséhez, hozzá kell rendelnie az alkalmazás egy szerepkörhöz. Döntse el, melyik szerepkör jelöli az alkalmazást a megfelelő engedélyekkel. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: Beépített szerepkörök](/azure/role-based-access-control/built-in-roles).

> [!NOTE]
> A szerepkör hatóköre egy előfizetés, erőforráscsoport vagy erőforrás szinten állíthatja be. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Egy alkalmazást egy erőforráscsoportot az Olvasó szerepkör például azt jelenti, hogy az alkalmazás tudja olvasni a az erőforrások az erőforráscsoportban.

Kövesse az alábbi lépéseket segítségképp szerepkört rendel egy egyszerű szolgáltatás számára.

1. Az Azure Stack portálon lépjen a hatókör az alkalmazást hozzárendelni kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **előfizetések**.

2. Válassza ki az előfizetést, az alkalmazás hozzárendelése. Ebben a példában az előfizetés, a Visual Studio Enterprise.

     ![Válassza ki a Visual Studio Enterprise előfizetéssel hozzárendelés](./media/azure-stack-create-service-principals/image16.png)

3. Válassza ki **hozzáférés-vezérlés (IAM)** az előfizetéshez.

4. Válassza ki **szerepkör-hozzárendelés hozzáadása**.

5. Válassza ki a az alkalmazáshoz hozzárendelni kívánt szerepkört.

6. Keresse meg az alkalmazást, és válassza ki azt.

7. Válassza ki **OK** befejeződik, a szerepkör hozzárendelése. Láthatja, hogy a felhasználók az adott hatókörnél szerepköre az alkalmazásra a listában.

Most, hogy már létrehozott egy egyszerű szolgáltatást és szerepkört rendel hozzá, az alkalmazás hozzáférhet az Azure Stack-erőforrások.

## <a name="next-steps"></a>További lépések

[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)
