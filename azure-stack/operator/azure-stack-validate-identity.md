---
title: Azure-identitás ellenőrzése
titleSuffix: Azure Stack Hub
description: Az Azure Identity ellenőrzéséhez használja a Azure Stack hub Readiness-ellenőrzőt.
author: BryanLa
ms.topic: how-to
ms.date: 03/04/2020
ms.author: bryanla
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 0852c977494dcd91545b1ea13430811b83eeeddb
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82173829"
---
# <a name="validate-azure-identity"></a>Azure-identitás ellenőrzése

Az Azure Stack hub Readiness-ellenőrző eszköz (**AzsReadinessChecker**) segítségével ellenőrizze, hogy az Azure Active Directory (Azure ad) készen áll-e a Azure stack hub használatára. Az Azure Stack hub üzembe helyezésének megkezdése előtt ellenőrizze az Azure Identity-megoldást.  

A készültség-ellenőrző ellenőrzi a következőket:

- Az Azure AD-t Azure Stack hub identitás-szolgáltatója.
- A használni kívánt Azure AD-fiók bejelentkezhet az Azure AD globális rendszergazdájaként.

Az érvényesítés biztosítja, hogy a környezet készen áll a Azure Stack hub számára az Azure AD-beli Azure Stack hub-beli felhasználókra, alkalmazásokra, csoportokra és egyszerű szolgáltatásokra vonatkozó információk tárolására.

## <a name="get-the-readiness-checker-tool"></a>A Readiness-ellenőrző eszköz beszerzése

Töltse le az Azure Stack hub Readiness-ellenőrző eszközének (AzsReadinessChecker) legújabb verzióját a [PowerShell-Galéria](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek szükségesek:

**Az a számítógép, amelyen az eszköz fut:**

- Windows 10 vagy Windows Server 2016 internetkapcsolattal.
- PowerShell 5,1 vagy újabb. A verzió ellenőrzéséhez futtassa a következő **PowerShell-parancsot** , majd tekintse át a főverziót **és az alverziókat** :  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [Azure stack hubhoz konfigurált PowerShell](azure-stack-powershell-install.md).
- [Microsoft Azure stack hub Readiness-ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz legújabb verziója.

**Azure AD-környezet:**

- Azonosítsa az Azure Stack hub számára használandó Azure AD-fiókot, és gondoskodjon arról, hogy az Azure AD globális rendszergazdája legyen.
- Azonosítsa az Azure AD-bérlő nevét. A bérlő nevének az Azure AD elsődleges tartománynevének kell lennie. Például: **contoso.onmicrosoft.com**.
- Azonosítsa a használni kívánt Azure-környezetet. A környezeti név paraméter támogatott értékei a **AzureCloud**, a **AzureChinaCloud**vagy a **AzureUSGovernment**, attól függően, hogy melyik Azure-előfizetést használja.

## <a name="steps-to-validate-azure-identity"></a>Az Azure-identitás érvényesítésének lépései

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort egy olyan számítógépen, amely megfelel az előfeltételeknek, majd futtassa a következő parancsot a **AzsReadinessChecker**telepítéséhez:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. A PowerShell-parancssorból futtassa a következő parancsot az Azure `$serviceAdminCredential` ad-bérlő szolgáltatás-rendszergazdájaként való beállításához.  Cserélje `serviceadmin\@contoso.onmicrosoft.com` le a fiókot és a bérlő nevét:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. A PowerShell-parancssorból futtassa a következő parancsot az Azure AD érvényesítésének megkezdéséhez:

   - Adja meg a környezet neve értéket a **AzureEnvironment**. A környezeti név paraméter támogatott értékei a **AzureCloud**, a **AzureChinaCloud**vagy a **AzureUSGovernment**, attól függően, hogy melyik Azure-előfizetést használja.
   - Cserélje `contoso.onmicrosoft.com` le az helyére az Azure ad-bérlő nevét.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, hogy az **OK** érték van a telepítési követelményeknél. A sikeres ellenőrzés az alábbi példához hasonlóan jelenik meg:

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

Alapértelmezés szerint mindkét fájl íródik a `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`következőre:.  

- A futtatási parancssor végén található `-OutputPath <path>` paraméterrel adhatja meg egy másik jelentés helyét.
- A futtatási parancs végén található `-CleanReport` paraméterrel törölheti az eszköz előző futtatásával kapcsolatos információkat a **AzsReadinessCheckerReport. JSON**fájlból.

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

**OK** – a fiók nem tud bejelentkezni, mert a jelszó lejárt vagy ideiglenes.

**Megoldás** – a PowerShellben futtassa a következő parancsot, majd kövesse az utasításokat a jelszó alaphelyzetbe állításához:

```powershell
Login-AzureRMAccount
```

Egy másik módszer, ha a fiók tulajdonosaként bejelentkezik a [Azure Portalba](https://portal.azure.com) , és a felhasználó kénytelen lesz megváltoztatni a jelszót.

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

**OK** – a fiók nem tud bejelentkezni a megadott Azure ad-be (**AADDirectoryTenantName**). Ebben a példában a **AzureChinaCloud** a **AzureEnvironment**adja meg.

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

**OK** – bár a fiók sikeresen be tud jelentkezni, a fiók nem az Azure ad (**AADDirectoryTenantName**) rendszergazdája.  

**Megoldás** – jelentkezzen be a [Azure Portalba](https://portal.azure.com) a fiók tulajdonosaként, lépjen a **Azure Active Directory**, majd a **felhasználók**pontra, majd **válassza ki a felhasználót**. Ezután válassza ki a **címtárbeli szerepkört** , és győződjön meg arról, hogy a felhasználó **globális rendszergazda**. Ha a fiók egy **felhasználó**, lépjen **Azure Active Directory** > **Egyéni tartománynevek** elemre, és ellenőrizze, hogy a **AADDirectoryTenantName** megadott név meg van-e jelölve a könyvtár elsődleges tartománynevéként. Ebben a példában ez a **contoso.onmicrosoft.com**.

Azure Stack hub megköveteli, hogy a tartománynév legyen az elsődleges tartomány neve.

## <a name="next-steps"></a>Következő lépések

[Azure-regisztráció ellenőrzése](azure-stack-validate-registration.md)  
[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack hub integrációs szempontjai](azure-stack-datacenter-integration.md)  
