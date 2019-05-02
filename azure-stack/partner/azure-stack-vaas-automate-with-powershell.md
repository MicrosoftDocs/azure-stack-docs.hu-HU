---
title: A PowerShell-lel az Azure Stack Kódérvényesítés automatizálása |} A Microsoft Docs
description: Az Azure Stack érvényesítési a PowerShell használatával automatizálható.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 39ed9ee9dab7f2ec97d2fb6a0148db333648b227
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297479"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>A PowerShell-lel az Azure Stack Kódérvényesítés automatizálása

Ellenőrzés (VaaS) szolgáltatás lehetővé teszi a indítása tesztek segítségével automatizálhatja a **LaunchVaaSTests.ps1** parancsfájlt.

> [!NOTE]  
> Automation csak a Tesztmenetek munkafolyamat érhető el. A csomag és a megoldás értékelési munkafolyamatokat csak támogatottak a VaaS portálon keresztül.

Ez a szkript segítségével:

> [!div class="checklist"]
> * Az előfeltételek telepítése
> * Telepítse és indítsa el a helyi ügynök
> * Indítsa el például egy kategóriájú tesztek, *integrációs*, *működési*, *megbízhatóság*
> * A jelentés teszt eredményei

## <a name="launch-the-test-pass-workflow"></a>Indítsa el a Tesztmenetek munkafolyamat

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssorban.

2. Futtassa a következő szkriptet az automation-szkript letöltése:

    ```powershell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Futtassa a következő parancsfájlt a megfelelő paraméter értékét:

    ```powershell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Paraméterek**

    | Paraméter | Leírás |
    | --- | --- |
    | VaaSUserId | VaaS felhasználói azonosítóját. |
    | VaaSUserPassword | A VaaS jelszavát. |
    | VaaSAccountTenantId | A VaaS bérlő GUID azonosítója. |
    | VaaSSolutionName | Neve a VaaS megoldás, amely alatt a teszt át fog futni. |
    | VaaSTestPassName | A VaaS teszt neve munkafolyamat létrehozásához adja át. |
    | VaaSTestCategories | `Integration`, `Functional`, vagy a. `Reliability`. Ha több értéket használja, külön az egyes értékek vesszővel elválasztva.  |
    | ServiceAdminUserName | Az Azure Stack szolgáltatás-rendszergazdai fiókot.  |
    | ServiceAdminPassword | Az Azure Stack szolgáltatás jelszava.  |
    | TenantAdminUserName | A rendszergazda az elsődleges bérlő számára.  |
    | TenantAdminPassword | Az elsődleges bérlő jelszava.  |
    | CloudAdminUserName | A felhő-rendszergazda felhasználóneve  |
    | CloudAdminPassword | A felhő rendszergazdájának jelszava.  |

4. Tekintse át a vizsgálat eredményeit. További beállításokért lásd: [figyelése és kezelése a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>További lépések

Az Azure Stack PowerShell kapcsolatos további információkért tekintse át a legújabb modulok.

> [!div class="nextstepaction"]
> [Az Azure Stack-modul](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
