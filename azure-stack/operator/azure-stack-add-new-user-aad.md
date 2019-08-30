---
title: Új Azure Stack bérlői fiók hozzáadása a Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre bérlői fiókot a ASDK, így megismerheti a bérlői portált.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: b9b18b62786c0720b531d0f74ed68c629034b8d7
ms.sourcegitcommit: 5efa09034a56eb2f3dc0c9da238fe60cff0c67ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143992"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Új Azure Stack bérlői fiók hozzáadása a Azure Active Directory


[A Azure stack Development Kit üzembe helyezése](../asdk/asdk-install.md)után szüksége lesz egy bérlői felhasználói fiókra, amellyel felfedezheti a bérlői portált, és tesztelheti ajánlatait és csomagjait. Bérlői fiókot a Azure Portal vagy a PowerShell használatával hozhat létre.



## <a name="create-an-azure-stack-tenant-account-by-using-the-azure-portal"></a>Azure Stack bérlői fiók létrehozása a Azure Portal használatával

Az Azure Portal használatához Azure-előfizetéssel kell rendelkeznie.

1. Jelentkezzen be az [Azure](https://portal.azure.com)-ba.
2. A bal oldali navigációs sávon válassza a **Active Directory** lehetőséget, majd váltson arra a könyvtárra, amelyet a Azure stack kíván használni (vagy hozzon létre újat).
3. Válassza **Azure Active Directory** > felhasználókúj > **felhasználó**elemet.

    ![Felhasználók – minden felhasználó oldalon kiemelve az új felhasználóval](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. A **felhasználó** lapon adja meg a szükséges adatokat.

    ![Új felhasználó, a felhasználó oldalon a felhasználói adatok hozzáadása](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Név (kötelező)** : Az első és utolsó az új felhasználó neve. Ha például Anna Parker.
   - **Felhasználónév (kötelező)** : Az új felhasználó felhasználóneve. Például: mary@contoso.com.
       A felhasználó nevét tartomány része kell használnia a vagy a kezdeti alapértelmezett tartománynévnek, <_saját_tartománynév_>. onmicrosoft.com, vagy egy egyéni tartománynevet, például contoso.com. Az Egyéni tartománynév létrehozásával kapcsolatos további információkért lásd: [Egyéni tartománynév hozzáadása az Azure ad](/azure/active-directory/fundamentals/add-custom-domain)-hez.
   - **Profil**: Igény szerint további információkat is hozzáadhat a felhasználóról. Emellett később is hozzáadhat felhasználói adatokat. A felhasználói adatok hozzáadásával kapcsolatos további információkért lásd: [felhasználói profil adatainak hozzáadása vagy módosítása](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Címtárbeli szerepkör**: válassza a **felhasználó**lehetőséget.

5. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, és másolja ki a **jelszó** mezőben megadott automatikusan generált jelszót. Erre a jelszóra lesz szüksége a kezdeti bejelentkezési folyamathoz.

6. Kattintson a **Létrehozás** gombra.

    A felhasználó létrehozása és az Azure AD-bérlőhöz hozzáadni.

7. Jelentkezzen be a Azure Portalba az új fiókkal. Ha a rendszer kéri, módosítsa a jelszót.
8. `https://portal.local.azurestack.external` A bérlői portál megtekintéséhez jelentkezzen be az új fiókkal.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Azure Stack felhasználói fiók létrehozása a PowerShell használatával

Ha nem rendelkezik Azure-előfizetéssel, nem használhatja a Azure Portal bérlői felhasználói fiók hozzáadásához. Ebben az esetben a Windows PowerShellhez készült Azure AD-modult használhatja helyette.

> [!NOTE]
> Ha Microsoft-fiókot használ a ASDK üzembe helyezéséhez, nem használhatja az Azure AD PowerShellt a bérlői fiók létrehozásához.

1. Telepítse a [Microsoft Online Services Bejelentkezési segédének](https://go.microsoft.com/fwlink/p/?LinkId=286152) **64-bites** verzióját az informatikai szakemberek RTW-hez.

2. Telepítse a Windows PowerShell Microsoft Azure AD modulját a következő lépésekkel:

    - Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort (futtassa a Windows PowerShellt rendszergazdaként).
    - Futtassa az **install-Module MSOnline** parancsot.
    - Ha a rendszer felszólítja a NuGet-szolgáltató telepítésére, válassza az **Y** lehetőséget, és **adja meg**a következőt:.
    - Ha a rendszer kéri, hogy telepítse a modult a PSGallery-ből, válassza az **Y** lehetőséget, majd **adja meg a értéket**.

3. Futtassa a következő parancsmagokat:

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Jelentkezzen be az Azure-ba az új fiókkal. Ha a rendszer kéri, módosítsa a jelszót.
2. `https://portal.local.azurestack.external` A bérlői portál megtekintéséhez jelentkezzen be az új fiókkal.

## <a name="next-steps"></a>További lépések

[Azure Stack-felhasználók hozzáadása az AD FS-ben](azure-stack-add-users-adfs.md)
