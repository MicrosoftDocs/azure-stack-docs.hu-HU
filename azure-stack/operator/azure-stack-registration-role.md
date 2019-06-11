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
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 50c15403ec4bf41f7513af30d2dca53310d45298
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828259"
---
# <a name="create-a-custom-role-for-azure-stack-registration"></a>Azure Stack-regisztráció egyéni szerepkör létrehozása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!WARNING]
> Ez nem egy biztonsági rendszer kialakításához funkció. Használhatja a forgatókönyvek, amelyre megkötések az Azure-előfizetéshez véletlen módosításainak elkerülés érdekében. Ha egy felhasználó az egyéni szerepkör delegált jogosultságokkal, a felhasználó rendelkezik szerkesztési jogosultságot, és a jogosultságok. Az egyéni szerepkör megbízható felhasználók csak hozzárendelése.

Azure Stack-regisztráció során az Azure Active Directory-fiókkal kell bejelentkeznie. A fiókot a következő Azure Active Directory-engedélyek és az Azure-előfizetés engedélyek szükségesek:

* **Az Azure Active Directory-bérlőben alkalmazás a regisztrációs engedélyeket:** Rendszergazdák rendelkeznek az alkalmazás regisztrációs engedélyeket. A felhasználóknak engedélyt egy globális beállítás, a bérlő összes felhasználója. Megtekintéséhez, vagy módosítsa a beállítást, lásd: [hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    A *felhasználók regisztrálhatnak alkalmazásokat* értékűre kell állítani **Igen** , hogy regisztrálja az Azure Stack felhasználói fiók engedélyezése. Ha az alkalmazásregisztrációk beállítás értéke **nem**, egy felhasználói fiók nem használható, és regisztrálja az Azure Stack egy globális rendszergazdai fiókot kell használnia.

* **Azure-előfizetés szükséges engedélyek egy készletét:** A tulajdonosi szerepkörhöz tartozó felhasználók rendelkezik megfelelő engedélyekkel. Egyéb fiókok esetében a következő szakaszokban ismertetett módon egy egyéni szerepkör hozzárendelésével arra az engedélycsoportra rendelhet.

Ahelyett, hogy az Azure-előfizetésben tulajdonosi engedélyekkel rendelkező fiókot használ, hozzon létre egy egyéni szerepkör engedélyek hozzárendelése egy kevésbé kiemelt jogosultságú felhasználói fiókhoz. Ezt a fiókot ezután regisztrálja az Azure Stack használható.

## <a name="create-a-custom-role-using-powershell"></a>Hozzon létre egy egyéni szerepkört, PowerShell-lel

Egyéni szerepkör létrehozása, rendelkeznie kell a `Microsoft.Authorization/roleDefinitions/write` engedély az összes `AssignableScopes`, mint például [tulajdonosa](/azure/role-based-access-control/built-in-roles#owner) vagy [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator). A következő JSON-sablon segítségével egyszerűsítheti az egyéni szerepkör létrehozása. A sablon létrehoz egy egyéni biztonsági szerepkört, amely lehetővé teszi a szükséges olvasási és írási hozzáférés az Azure Stack-regisztráció.

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

4. Az egyéni szerepkör létrehozásához használja **New-AzureRmRoleDefinition** adja meg a sablon JSON-fájlt.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Felhasználó hozzárendelése regisztrációs szerepkör

A regisztrációs egyéni szerepkör létrehozása után rendelje hozzá a szerepkört az Azure Stack regisztrálásához használt felhasználói fiók.

1. Jelentkezzen be az Azure-előfizetés elegendő engedéllyel rendelkező fiók rights – például delegálása [tulajdonosa](/azure/role-based-access-control/built-in-roles#owner) vagy [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator) .
2. A **előfizetések**válassza **hozzáférés-vezérlés (IAM) > szerepkör-hozzárendelés hozzáadása**.
3. A **szerepkör**, válassza ki a létrehozott egyéni szerepkört *Azure Stack-regisztráció szerepkör*.
4. Válassza ki a felhasználók, rendelje hozzá a szerepkörhöz kíván.
5. Válassza ki **mentése** hozzárendelése a kiválasztott felhasználók a szerepkörhöz.

    ![Szerepkör hozzárendelése a kiválasztott felhasználók](media/azure-stack-registration-role/assign-role.png)

Egyéni szerepkörök használatával kapcsolatos további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>További lépések

[Az Azure Stack regisztrálása az Azure-ban](azure-stack-registration.md)
