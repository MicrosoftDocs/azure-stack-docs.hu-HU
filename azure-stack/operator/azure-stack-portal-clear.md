---
title: A portál felhasználói adatainak törlése Azure Stack hub által igény szerint.
description: Azure Stack hub-kezelőként megtudhatja, hogyan törölheti a portál felhasználói információit, ha Azure Stack hub-felhasználó kéri.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: bede8fc72cfcdd6f3376c49a87293fef3626c380
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698334"
---
# <a name="clear-portal-user-data-from-azure-stack-hub"></a>A portál felhasználói adatainak törlése Azure Stack hub-ból

Azure Stack hub-operátorok igény szerint törölhetik a portál felhasználói adatfájljait, amikor Azure Stack hub-felhasználó kéri azt. Azure Stack hub-felhasználóként a portál testreszabható a csempék rögzítésével és az irányítópult elrendezésének módosításával. A felhasználók emellett módosíthatják a témát, és a személyes beállításoknak megfelelően módosíthatják az alapértelmezett nyelvet.

A portál felhasználói információi közé tartoznak a kedvencek és a nemrégiben elért erőforrások a Azure Stack hub felhasználói portálon. Ez a cikk bemutatja, hogyan törölheti a portál felhasználói információit.

A portál felhasználói beállításainak eltávolítása csak a felhasználói előfizetés törlése után hajtható végre.

> [!NOTE]
> A jelen cikkben ismertetett útmutatást követve bizonyos felhasználói adatmennyiségek továbbra is megjelenhetnek az eseménynaplók rendszer szakaszában. Ezek az adat több napig is maradhat, amíg a naplók automatikusan át nem térnek.

## <a name="requirements"></a>Követelmények

- [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).
- [Töltse le a legújabb Azure stack hub-eszközöket](azure-stack-powershell-download.md) a githubról.
- A felhasználói fióknak továbbra is léteznie kell a címtárban.
- Azure Stack hub rendszergazdai hitelesítő adatai a rendszergazdai erőforrás-kezelő végpont eléréséhez.

> [!NOTE]
> Ha olyan felhasználótól kísérli meg törölni a portál felhasználói adatait, amely egy vendég címtárból érkezett (több-bérlős), akkor olvasási engedéllyel kell rendelkeznie a címtárban. További információ a [jelen cikk a CSP-forgatókönyvben című szakaszában található](#clear-portal-user-data-in-guest-directory).

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>A portál felhasználói adatai törlése egyszerű felhasználónév használatával

Ez a forgatókönyv feltételezi, hogy az alapértelmezett szolgáltatói előfizetés, a felhasználó pedig ugyanahhoz a címtárhoz tartozik, vagy ha olvasási jogosultsággal rendelkezik ahhoz a címtárhoz, amelyben a felhasználó található.

A folytatás előtt győződjön meg arról, hogy az [Azure stack hub Tools legújabb verzióját szeretné letölteni](azure-stack-powershell-download.md) a githubról.

Ehhez az eljáráshoz használjon olyan számítógépet, amely képes kommunikálni Azure Stack hub felügyeleti erőforrás-kezelői végpontjának használatával.

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), nyissa meg a **AzureStack-Tools-Master** könyvtár gyökérkönyvtárát, és importálja a szükséges PowerShell-modult:

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Futtassa az alábbi parancsokat. Ügyeljen arra, hogy a helyőrzőket a környezetnek megfelelő értékekkel helyettesítse:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $azureStackDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"

   ## Replace the following value with the user directory tenant ID.
   $userDirectoryTenantId = " 7ddf3648-9671-47fd-b63d-eecd82ed040e"

   ## Replace the following value with name of the user principal whose portal user data is to be cleared.
   $userPrincipalName = "myaccount@contoso.onmicrosoft.com"

   Clear-AzsUserDataWithUserPrincipalName -AzsAdminArmEndpoint $adminARMEndpoint `
    -AzsAdminDirectoryTenantId $azureStackDirectoryTenantId `
    -UserPrincipalName $userPrincipalName `
    -DirectoryTenantId $userDirectoryTenantId
   ```

   > [!NOTE]
   > A(z) `azureStackDirectoryTenantId` nem kötelező. Ha nem adja meg ezt az értéket, a parancsfájl megkeresi az egyszerű felhasználónevet az Azure Stack hub-ban regisztrált összes bérlői könyvtárban, majd törli a portálon tárolt összes felhasználót.

## <a name="clear-portal-user-data-in-guest-directory"></a>A portál felhasználói adatfájljainak törlése a vendég címtárban

Ebben az esetben az Azure Stack hub operátor nem fér hozzá a vendég címtárhoz, amelyben a felhasználó található. Ez egy gyakori forgatókönyv, ha egy felhőalapú megoldás-szolgáltató (CSP).

Ahhoz, hogy egy Azure Stack hub operátor el lehessen távolítani a portál felhasználói adatszolgáltatásait, legalább a felhasználói objektum AZONOSÍTÓját kötelező megadni.

A felhasználónak le kell kérdezni az objektumazonosítót, és meg kell adnia az Azure Stack hub-kezelőnek. Az operátor nem fér hozzá a címtárhoz, amelyben a felhasználó található.

### <a name="user-retrieves-the-user-object-id"></a>A felhasználó lekéri a felhasználói objektum AZONOSÍTÓját

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), keresse meg a gyökérkönyvtárat a **AzureStack-Tools-Master** könyvtárban, majd importálja a szükséges PowerShell-modult.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Futtassa az alábbi parancsokat. Ügyeljen arra, hogy a helyőrzőket a környezetének megfelelő értékekkel helyettesítse.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $userARMEndpoint = "https://management.local.azurestack.external"

   ## Replace the following value with the directory tenant ID, which contains the user account.
   $userDirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userPrincipleName = "myaccount@contoso.onmicrosoft.com"

   Get-UserObjectId -DirectoryTenantId $userDirectoryTenantId `
    -AzsArmEndpoint $userARMEndpoint `
    -UserPricinpalName $userPrincipleName
   ```

   > [!NOTE]
   > Felhasználóként meg kell adnia a felhasználói objektum AZONOSÍTÓját, amely az előző szkript kimenete az Azure Stack hub operátornak.

## <a name="azure-stack-hub-operator-removes-the-portal-user-data"></a>Azure Stack hub-kezelő eltávolítja a portál felhasználói adatfájljait

A felhasználói objektum AZONOSÍTÓjának Azure Stack hub-kezelőként való fogadása után futtassa a következő parancsokat a portál felhasználói adatértékének eltávolításához:

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), keresse meg a gyökérkönyvtárat a **AzureStack-Tools-Master** könyvtárban, majd importálja a szükséges PowerShell-modult.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Futtassa az alábbi parancsokat, és ügyeljen arra, hogy a paramétert a környezetnek megfelelően módosítsa:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $AzsAdminDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"
   
   ## Replace the following value with the directory tenant ID of the user to clear.
   $DirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userObjectID = "s-1-*******"
   Clear-AzsUserDataWithUserObject -AzsAdminArmEndpoint $AzsAdminARMEndpoint `
    -AzsAdminDirectoryTenantId $AzsAdminDirectoryTenantId `
    -DirectoryTenantID $DirectoryTenantId `
    -UserObjectID $userObjectID `
   ```

## <a name="next-steps"></a>További lépések

- [Regisztrálja Azure stack hubot az Azure](azure-stack-registration.md) -ban, és töltse fel az [Azure stack hub Marketplace](azure-stack-marketplace.md) -et a felhasználók számára elérhető elemek használatával.
