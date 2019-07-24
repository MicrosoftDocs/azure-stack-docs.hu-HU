---
title: Azure Stack érvényesítésének automatizálása a PowerShell használatával | Microsoft Docs
description: Automatizálhatja Azure Stack érvényesítését a PowerShell használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5a49685da010fd7188fd04514a17b91dff7404f7
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418447"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Azure Stack érvényesítésének automatizálása a PowerShell használatával

A szolgáltatásként történő érvényesítés lehetővé teszi a tesztek automatizálását a **LaunchVaaSTests. ps1** parancsfájl használatával.

> [!NOTE]  
> Az Automation csak a test pass munkafolyamat esetében érhető el. A csomag-ellenőrzési és megoldás-ellenőrzési munkafolyamatok csak az alaprendszer-portálon keresztül támogatottak.

Ez a szkript a következőket tudja használni:

> [!div class="checklist"]
> * Előfeltételek telepítése
> * A helyi ügynök telepítése és elindítása
> * Elindíthat egy teszt kategóriát , például az integrációt, a *működést*, a *megbízhatóságot*
> * Vizsgálati eredmények jelentése

## <a name="launch-the-test-pass-workflow"></a>A test pass munkafolyamat elindítása

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.

2. Az Automation-szkript letöltéséhez futtassa a következő szkriptet:

    ```powershell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Futtassa a következő parancsfájlt a megfelelő paraméterek értékeivel:

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
    | VaaSUserId | Az alaprendszer-azonosító. |
    | VaaSUserPassword | Az Ön által használt Varga-jelszó. |
    | VaaSAccountTenantId | Az Ön által használt Varga-bérlő GUID-azonosítója. |
    | VaaSSolutionName | Annak az adatbázis-megoldásnak a neve, amely alatt a tesztelési fázis futni fog. |
    | VaaSTestPassName | A létrehozandó, az Varga test pass munkafolyamat neve. |
    | VaaSTestCategories | `Integration`, `Functional`, vagy. `Reliability`. Ha több értéket használ, az egyes értékeket vesszővel válassza el egymástól.  |
    | ServiceAdminUserName | A Azure Stack szolgáltatás-rendszergazdai fiókja.  |
    | ServiceAdminPassword | A Azure Stack szolgáltatás jelszava.  |
    | TenantAdminUserName | Az elsődleges bérlő rendszergazdája.  |
    | TenantAdminPassword | Az elsődleges bérlő jelszava.  |
    | CloudAdminUserName | A Felhőbeli rendszergazda felhasználóneve.  |
    | CloudAdminPassword | A felhő rendszergazdájának jelszava.  |

4. Tekintse át a teszt eredményét. További lehetőségekért lásd: [tesztek figyelése és kezelése az](azure-stack-vaas-monitor-test.md)alapvizsgálati portálon.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Stack PowerShell-ről, tekintse át a legújabb modulokat.

> [!div class="nextstepaction"]
> [Azure Stack modul](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
