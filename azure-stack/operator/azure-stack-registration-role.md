---
title: Egyéni szerepkör létrehozása Azure Stack hub-regisztrációhoz
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan hozhat létre egyéni szerepkört a globális rendszergazda Azure Stack hub-regisztrációhoz való használatának elkerüléséhez.
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
ms.openlocfilehash: dff7f2dd043a7df3749ec3cdc4b7560e6cd7bd06
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882028"
---
# <a name="create-a-custom-role-for-azure-stack-hub-registration"></a>Egyéni szerepkör létrehozása Azure Stack hub-regisztrációhoz

> [!WARNING]
> Ez nem egy biztonsági testtartási funkció. Olyan helyzetekben érdemes használni, ahol meg szeretné akadályozni az Azure-előfizetés véletlen módosításait. Ha a felhasználó jogosultságokat delegál az egyéni szerepkörhöz, a felhasználónak jogosultsága van az engedélyek szerkesztésére és a jogosultságszint-emelésre. Csak a megbízhatónak ítélt felhasználókat rendelje hozzá az egyéni szerepkörhöz.

Azure Stack hub-regisztráció során Azure Active Directory (Azure AD-) fiókkal kell bejelentkeznie. A fiókhoz a következő Azure AD-engedélyek és Azure-előfizetési engedélyek szükségesek:

* **Alkalmazás-regisztrációs engedélyek az Azure ad-bérlőben:** A rendszergazdák az alkalmazás regisztrációs engedélyeivel rendelkeznek. A felhasználókra vonatkozó engedély a bérlő összes felhasználója számára globális beállítás. A beállítás megtekintéséhez vagy módosításához tekintse meg az [erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatás létrehozása](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)című témakört.

    A *felhasználó regisztrálhatja az alkalmazások* beállítást **Igen** értékre kell állítani ahhoz, hogy egy felhasználói fiók regisztrálja Azure stack hubot. Ha az alkalmazás regisztrációja **nem**értékre van állítva, nem használhat felhasználói fiókot Azure stack hub regisztrálásához – globális rendszergazdai fiókot kell használnia.

* **Megfelelő Azure-előfizetési engedélyek készlete:** A tulajdonosi szerepkörhöz tartozó felhasználók rendelkeznek a megfelelő engedélyekkel. Más fiókok esetében az engedélyeket a következő részekben leírtak szerint rendelheti hozzá egy egyéni szerepkör hozzárendelésével.

Az Azure-előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiók használata helyett létrehozhat egy egyéni szerepkört, amellyel engedélyeket rendelhet egy kevésbé Kiemelt felhasználói fiókhoz. Ezt a fiókot ezután a Azure Stack hub regisztrálására használhatja.

## <a name="create-a-custom-role-using-powershell"></a>Egyéni szerepkör létrehozása a PowerShell használatával

Egyéni szerepkör létrehozásához a `Microsoft.Authorization/roleDefinitions/write` engedéllyel kell rendelkeznie minden `AssignableScopes`, például a [tulajdonos](/azure/role-based-access-control/built-in-roles#owner) vagy a [felhasználói hozzáférés rendszergazdájának](/azure/role-based-access-control/built-in-roles#user-access-administrator). Az alábbi JSON-sablon használatával egyszerűsítheti az egyéni szerepkör létrehozását. A sablon létrehoz egy egyéni szerepkört, amely lehetővé teszi a szükséges olvasási és írási hozzáférést Azure Stack hub-regisztrációhoz.

1. Hozzon létre egy JSON-fájlt. Például:  `C:\CustomRoles\registrationrole.json`.
2. Adja hozzá az alábbi JSON-kódot a fájlhoz. Cserélje le a `<SubscriptionID>` értékét a saját Azure-előfizetése azonosítójára.

    ```json
    {
      "Name": "Azure Stack Hub registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack Hub",
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

3. A PowerShellben kapcsolódjon az Azure-hoz a Azure Resource Manager használatához. Ha a rendszer kéri, végezzen hitelesítést olyan fiókkal, amely rendelkezik megfelelő engedélyekkel, például a [tulajdonos](/azure/role-based-access-control/built-in-roles#owner) vagy a [felhasználói hozzáférés rendszergazdájával](/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Az egyéni szerepkör létrehozásához használja a JSON-sablonfájl megadására szolgáló **New-AzureRmRoleDefinition** .

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Felhasználó társítása a regisztrációs szerepkörhöz

A regisztráció egyéni szerepkör létrehozása után rendelje hozzá a szerepkört az Azure Stack hub regisztrálásához használt felhasználói fiókhoz.

1. Jelentkezzen be az Azure-előfizetéshez megfelelő jogosultsággal rendelkező fiókkal a jogosultságok delegálásához – például a [tulajdonos](/azure/role-based-access-control/built-in-roles#owner) vagy a [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator)számára.
2. Az **előfizetések**területen válassza a **hozzáférés-vezérlés (iam) > szerepkör-hozzárendelés hozzáadása**elemet.
3. A **szerepkör**területen válassza ki a létrehozott egyéni szerepkört: *Azure stack hub regisztrációs szerepkört*.
4. Válassza ki a szerepkörhöz hozzárendelni kívánt felhasználókat.
5. Válassza a **Mentés** lehetőséget a kijelölt felhasználók szerepkörhöz való hozzárendeléséhez.

    ![Válassza ki az egyéni szerepkörhöz hozzárendelni kívánt felhasználókat Azure Portal](media/azure-stack-registration-role/assign-role.png)

További információ az egyéni szerepkörök használatáról: [a hozzáférés kezelése a RBAC és a Azure Portal használatával](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub regisztrálása az Azure-ban](azure-stack-registration.md)
