---
title: Azure Stack hub Azure-identitásának ellenőrzése | Microsoft Docs
description: Az Azure Identity ellenőrzéséhez használja a Azure Stack hub Readiness-ellenőrzőt.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: e38e0462bc9b30783ff0932a16e2e997f64df0fd
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812892"
---
# <a name="validate-azure-identity"></a>Azure-identitás ellenőrzése

Az Azure Stack hub Readiness-ellenőrző eszköz (**AzsReadinessChecker**) segítségével ellenőrizze, hogy az Azure Active Directory (Azure ad) készen áll-e a Azure stack hub használatára. Az Azure Stack hub üzembe helyezésének megkezdése előtt ellenőrizze az Azure Identity-megoldást.  

A készültség-ellenőrző ellenőrzi a következőket:

- Azure Active Directory (Azure AD) Azure Stack hub identitás-szolgáltatóként.
- A használni kívánt Azure AD-fiók bejelentkezhet a Azure Active Directory globális rendszergazdájaként.

Az érvényesítés biztosítja, hogy a környezet készen áll a Azure Stack hub számára az Azure AD-beli Azure Stack hub-beli felhasználókra, alkalmazásokra, csoportokra és egyszerű szolgáltatásokra vonatkozó információk tárolására.

## <a name="get-the-readiness-checker-tool"></a>A Readiness-ellenőrző eszköz beszerzése

Töltse le az Azure Stack hub Readiness-ellenőrző eszközének (AzsReadinessChecker) legújabb verzióját a [PowerShell-Galéria](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek szükségesek:

**Az a számítógép, amelyen az eszköz fut:**

- Windows 10 vagy Windows Server 2016, internetkapcsolattal.
- PowerShell 5,1 vagy újabb. A verzió ellenőrzéséhez futtassa a következő **PowerShell-parancsot** , majd tekintse át a főverziót **és az alverziókat** :  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Azure stack hubhoz konfigurált PowerShell](azure-stack-powershell-install.md).
- [Microsoft Azure stack hub Readiness-ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz legújabb verziója.

**Azure Active Directory környezet:**

- Azonosítsa az Azure Stack hub számára használandó Azure AD-fiókot, és gondoskodjon arról, hogy az Azure Active Directory globális rendszergazda legyen.
- Azonosítsa az Azure AD-bérlő nevét. A bérlő nevének a Azure Active Directory elsődleges tartománynevének kell lennie; például: **contoso.onmicrosoft.com**.
- Azonosítsa a használni kívánt Azure-környezetet. A környezeti név paraméter támogatott értékei a **AzureCloud**, a **AzureChinaCloud**vagy a **AzureUSGovernment**, attól függően, hogy melyik Azure-előfizetést használja.

## <a name="steps-to-validate-azure-identity"></a>Az Azure-identitás érvényesítésének lépései

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort egy olyan számítógépen, amely megfelel az előfeltételeknek, majd futtassa a következő parancsot a **AzsReadinessChecker**telepítéséhez:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. A PowerShell-parancssorból futtassa a következő parancsot a **$serviceAdminCredential** beállításaként az Azure ad-bérlő szolgáltatás-rendszergazdájaként.  Cserélje le a **serviceadmin\@contoso.onmicrosoft.com** a fiókjára és a bérlő nevére:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. A PowerShell-parancssorból futtassa a következő parancsot az Azure AD érvényesítésének megkezdéséhez:

   - Adja meg a környezet neve értéket a **AzureEnvironment**. A környezeti név paraméter támogatott értékei a **AzureCloud**, a **AzureChinaCloud**vagy a **AzureUSGovernment**, attól függően, hogy melyik Azure-előfizetést használja.
   - Cserélje le a **contoso.onmicrosoft.com** -t a Azure Active Directory bérlő nevére.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, hogy az **OK** érték van a telepítési követelményeknél. A sikeres ellenőrzés az alábbi képhez hasonlóan jelenik meg:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>Jelentés és naplófájl

A rendszer minden alkalommal futtatja az eredményeket a **AzsReadinessChecker. log** és a **AzsReadinessCheckerReport. JSON**fájlban. A fájlok helye a PowerShell érvényesítési eredményeivel jelenik meg.

Ezek a fájlok segíthetnek az érvényesítési állapot megosztásában az Azure Stack hub üzembe helyezése vagy az érvényesítési problémák vizsgálata előtt. Mindkét fájl megőrzi az összes további érvényesítési ellenőrzés eredményét. A jelentés az identitás konfigurációjának megerősítését biztosítja az üzembe helyezési csoport számára. A naplófájl segítséget nyújthat az üzembe helyezéshez vagy a támogatási csoporthoz az érvényesítési problémák kivizsgálásához.

Alapértelmezés szerint mindkét fájl a **C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.JSON**.  

- Egy másik jelentés helyének megadásához használja a Run parancssor végén található **-OutputPath** ***&lt;Path&gt;*** paramétert.
- A Run parancs végén a **-CleanReport** paraméterrel törölheti az eszköz előző futtatásával kapcsolatos információkat a **AzsReadinessCheckerReport. JSON**fájlból.

További információ: [Azure stack hub-ellenőrzési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy érvényesítési ellenőrzés sikertelen, a PowerShell-ablakban megjelenő hiba részletei láthatók. Az eszköz az AzsReadinessChecker. log fájlba is naplózza az adatokat.

Az alábbi példák útmutatást nyújtanak a gyakori ellenőrzési hibákról.

### <a name="expired-or-temporary-password"></a>Lejárt vagy ideiglenes jelszó

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**OK** – a fiók nem tud bejelentkezni, mert a jelszó vagy lejárt, vagy ideiglenes.

**Megoldás** – a PowerShellben futtassa a következő parancsot, majd kövesse az utasításokat a jelszó alaphelyzetbe állításához:

```powershell
Login-AzureRMAccount
```

Azt is megteheti, hogy a fiók tulajdonosaként bejelentkezik a [Azure Portalba](https://portal.azure.com) , és a felhasználó kénytelen lesz megváltoztatni a jelszót.

### <a name="unknown-user-type"></a>Ismeretlen felhasználói típus 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**OK** – a fiók nem tud bejelentkezni a megadott Azure Active Directoryba (**AADDirectoryTenantName**). Ebben a példában a **AzureChinaCloud** a **AzureEnvironment**adja meg.

**Megoldás** – ellenőrizze, hogy a fiók érvényes-e a megadott Azure-környezetben. A PowerShellben futtassa a következő parancsot annak ellenőrzéséhez, hogy a fiók érvényes-e egy adott környezetben:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>A fiók nem rendszergazda

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**OK** – bár a fiók sikeresen be tud jelentkezni, a fiók nem a Azure Active Directory (**AADDirectoryTenantName**) rendszergazdája.  

**Megoldás** – jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók tulajdonosaként, lépjen a **Azure Active Directory**, majd a **felhasználók**pontra, **válassza ki a felhasználót**, majd a **címtárbeli szerepkört**, és győződjön meg arról, hogy a felhasználó **globális rendszergazda**. Ha a fiók egy **felhasználó**, lépjen **Azure Active Directory** > **Egyéni tartománynevek**elemre, és győződjön meg arról, hogy a **AADDirectoryTenantName** megadott név a könyvtár elsődleges tartományneveként van megjelölve. Ebben a példában ez a **contoso.onmicrosoft.com**.

Azure Stack hub megköveteli, hogy a tartománynév legyen az elsődleges tartomány neve.

## <a name="next-steps"></a>Következő lépések

[Azure-regisztráció ellenőrzése](azure-stack-validate-registration.md)  
[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack hub integrációs szempontjai](azure-stack-datacenter-integration.md)  
