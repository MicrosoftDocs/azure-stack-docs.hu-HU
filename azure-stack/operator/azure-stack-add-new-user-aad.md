---
title: Új Azure Stack hub felhasználói fiók hozzáadása a Azure Active Directory
description: Megtudhatja, hogyan hozhat létre felhasználói fiókot Azure Active Directoryban, így a felhasználói portálon is megismerheti.
author: PatAltimore
ms.topic: article
ms.date: 05/20/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: dcd2779bf1166b6418a0ecbec3b025e889ca048a
ms.sourcegitcommit: 5f3d37994b8cb63c76e54136c0cc05bc4f475950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99495567"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-azure-active-directory-azure-ad"></a>Új Azure Stack hub felhasználói fiók hozzáadása Azure Active Directory (Azure AD)

Az ajánlatok és csomagok teszteléséhez és az erőforrások létrehozásához felhasználói fiókra van szükség. Hozzon létre egy felhasználói fiókot az Azure AD-bérlőben a Azure Portal vagy a PowerShell használatával.

## <a name="create-user-account-using-the-azure-portal"></a>Felhasználói fiók létrehozása a Azure Portal használatával

Az Azure Portal használatához Azure-előfizetéssel kell rendelkeznie.

1. Jelentkezzen be az [Azure](https://portal.azure.com)-ba.
2. A bal oldali navigációs sávon válassza a **Active Directory** lehetőséget, majd váltson arra a könyvtárra, amelyet az Azure stack hub számára kíván használni (vagy hozzon létre újat).
3. Válassza **Azure Active Directory**  >  **felhasználók**  >  **új felhasználó** elemet.

    ![Felhasználók – minden felhasználó lap új felhasználóval kiemelve](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. A **felhasználó** lapon adja meg a szükséges adatokat.

    ![Új felhasználó, felhasználói oldal hozzáadása a felhasználói adatokhoz](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Név (kötelező)**: az új felhasználó vezetékneve és utóneve. Például: Mary Parker.
   - **Felhasználónév (kötelező)**: az új felhasználó felhasználóneve. Például: mary@contoso.com.
       A Felhasználónév tartomány részének a kezdeti alapértelmezett tartománynevet kell használnia, <_yourdomainname_>. onmicrosoft.com vagy egy egyéni tartománynevet, például contoso.com. Az Egyéni tartománynév létrehozásával kapcsolatos további információkért lásd: [Egyéni tartománynév hozzáadása az Azure ad](/azure/active-directory/fundamentals/add-custom-domain)-hez.
   - **Profil**: igény szerint további információkat is hozzáadhat a felhasználóról. Emellett később is hozzáadhat felhasználói adatokat. A felhasználói adatok hozzáadásával kapcsolatos további információkért lásd: [felhasználói profil adatainak hozzáadása vagy módosítása](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Címtárbeli szerepkör**: válassza a **felhasználó** lehetőséget.

5. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, és másolja ki a **jelszó** mezőben megadott automatikusan generált jelszót. Erre a jelszóra lesz szüksége a kezdeti bejelentkezési folyamathoz.

6. Válassza a **Létrehozás** lehetőséget.

    A rendszer létrehozza a felhasználót, és hozzáadja az Azure AD-bérlőhöz

7. Jelentkezzen be a Azure Portalba az új fiókkal. Ha a rendszer kéri, módosítsa a jelszót.
8. A `https://portal.local.azurestack.external` felhasználói portál megtekintéséhez jelentkezzen be az új fiókkal.

## <a name="create-a-user-account-using-powershell"></a>Felhasználói fiók létrehozása a PowerShell használatával

Ha nem rendelkezik Azure-előfizetéssel, nem használhatja a Azure Portal bérlői felhasználói fiók hozzáadásához. Ebben az esetben a Windows PowerShellhez készült Azure AD-modult használhatja helyette.

> [!NOTE]
> Ha Microsoft-fiókot használ a ASDK üzembe helyezéséhez, nem használhatja az Azure AD PowerShellt a bérlői fiók létrehozásához.

1. Telepítse a [Microsoft Online Services Bejelentkezési segédének](https://www.microsoft.com/download/details.aspx?id=28177) **64-bites** verzióját az informatikai szakemberek RTW-hez.

2. Telepítse a Windows PowerShell Microsoft Azure AD modulját a következő lépésekkel:

    - Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort (futtassa a Windows PowerShellt rendszergazdaként).
    - Futtassa az **install-Module MSOnline** parancsot.
    - Ha a rendszer felszólítja a NuGet-szolgáltató telepítésére, válassza az **Y** lehetőséget, és **adja meg** a következőt:.
    - Ha a rendszer kéri, hogy telepítse a modult a PSGallery-ből, válassza az **Y** lehetőséget, majd **adja meg a értéket**.

3. Futtassa a következő parancsmagokat:

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a user account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Jelentkezzen be az Azure-ba az új fiókkal. Ha a rendszer kéri, módosítsa a jelszót.
2. A `https://portal.local.azurestack.external` felhasználói portál megtekintéséhez jelentkezzen be az új fiókkal.

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub-felhasználók hozzáadása a AD FS](azure-stack-add-users-adfs.md)
