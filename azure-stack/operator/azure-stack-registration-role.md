---
title: Egy regisztrációs szerepkör létrehozása az Azure Stackhez
description: Hogyan lehet, kerülje a globális rendszergazda regisztrációs egyéni szerepkör létrehozása.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 09a75b7aad3d0a9a919883641d8dc901353a5048
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251910"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Azure stack-regisztráció szerepkör létrehozása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Forgatókönyvek, ahol nem szeretnénk az Azure-előfizetés tulajdonosa engedélyt létrehozhat egy egyéni szerepkör engedélyek hozzárendelése egy felhasználói fiókot az Azure Stack regisztrálni.

> [!WARNING]
> Ez nem egy biztonsági rendszer kialakításához funkció. Használhatja a forgatókönyvek, amelyre megkötések az Azure-előfizetéshez véletlen módosításainak elkerülés érdekében. Ha egy felhasználó az egyéni szerepkör delegált jogosultságokkal, a felhasználó rendelkezik szerkesztési jogosultságot, és a jogosultságok. Az egyéni szerepkör megbízható felhasználók csak hozzárendelése.

Regisztrálás az Azure Stack, a regisztrációs fiókhoz kell rendelkeznie a következő Azure Active Directory-engedélyek és az Azure-előfizetés engedélyeket:

* **Az Azure Active Directory-bérlőben alkalmazás a regisztrációs engedélyeket:** Rendszergazdák rendelkeznek az alkalmazás regisztrációs engedélyeket. A felhasználóknak engedélyt egy globális beállítás, a bérlő összes felhasználója. Megtekintéséhez, vagy módosítsa a beállítást, lásd: [hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    A *felhasználók regisztrálhatnak alkalmazásokat* értékűre kell állítani **Igen** , hogy regisztrálja az Azure Stack felhasználói fiók engedélyezése. Ha az alkalmazásregisztrációk beállítás értéke **nem**, egy felhasználói fiók nem használható, és regisztrálja az Azure Stack egy globális rendszergazdai fiókot kell használnia.

* **Azure-előfizetés szükséges engedélyek egy készletét:** A tulajdonosok csoport rendelkezik megfelelő engedélyekkel. Egyéb fiókok esetében a következő szakaszokban ismertetett módon egy egyéni szerepkör hozzárendelésével arra az engedélycsoportra rendelhet.

## <a name="create-a-custom-role-using-powershell"></a>Hozzon létre egy egyéni szerepkört, PowerShell-lel

Egyéni szerepkör létrehozása, rendelkeznie kell a `Microsoft.Authorization/roleDefinitions/write` engedély az összes `AssignableScopes`, mint például [tulajdonosa](/azure/role-based-access-control/built-in-roles#owner) vagy [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator). A következő JSON-sablon használatával egyszerűsíthető a definiálása az egyéni szerepkör. A sablon létrehoz egy egyéni biztonsági szerepkört, amely lehetővé teszi a szükséges olvasási és írási hozzáférés az Azure Stack-regisztráció.

1. Hozzon létre egy JSON-fájlt. Ha például  `C:\CustomRoles\registrationrole.json`
2. Adja hozzá az alábbi JSON-kódot a fájlhoz. Cserélje le a `<SubscriptionID>` értékét a saját Azure-előfizetése azonosítójára.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. A PowerShell-lel csatlakozzon az Azure-bA az Azure Resource Manager használatára. Amikor a rendszer kéri, hitelesítést egy olyan fiókkal megfelelő engedélyekkel rendelkező például [tulajdonosa](/azure/role-based-access-control/built-in-roles#owner) vagy [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Adja hozzá a szerepkört az előfizetés, használja a **New-AzureRmRoleDefinition** adja meg a sablon JSON-fájlt.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Felhasználó hozzárendelése regisztrációs szerepkör

A regisztrációs egyéni szerepkör létrehozása után rendelje hozzá a szerepkörhöz felhasználók regisztrálása az Azure Stack.

1. Jelentkezzen be az Azure-előfizetés elegendő engedéllyel rendelkező fiók rights – például delegálása [tulajdonosa](/azure/role-based-access-control/built-in-roles#owner) vagy [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator) .
2. A **előfizetések**válassza **hozzáférés-vezérlés (IAM) > szerepkör-hozzárendelés hozzáadása**.
3. A **szerepkör**, válassza ki a létrehozott egyéni szerepkört *Azure Stack-regisztráció szerepkör*.
4. Válassza ki a felhasználók, rendelje hozzá a szerepkörhöz kíván.
5. Válassza ki **mentése** hozzárendelése a kiválasztott felhasználók a szerepkörhöz.

    ![Szerepkör hozzárendelése a kiválasztott felhasználók](media/azure-stack-registration-role/assign-role.png)

Egyéni szerepkörök használatával kapcsolatos további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>További lépések

[Az Azure Stack regisztrálása az Azure-ban](azure-stack-registration.md)
