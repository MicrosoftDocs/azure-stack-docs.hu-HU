---
title: Azure-identitás ellenőrzése az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack-készültségi ellenőrző használatával Azure-identitás ellenőrzése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 876ed83e06d7daa9d2f9b513b3cb398a8e69072b
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984259"
---
# <a name="validate-azure-identity"></a>Azure-identitás ellenőrzése

Az Azure Stack Readiness-ellenőrző eszközt (**AzsReadinessChecker**) ellenőrzése, hogy az Azure Stack használatra készen áll-e az Azure Active Directory (Azure AD). Ellenőrizze az Azure identitáskezelési megoldás, az Azure Stack központi telepítésének megkezdése előtt.  

A készenléti ellenőrző ellenőrzi:

- Az Azure Active Directory (Azure AD) identitás-szolgáltatóként az Azure Stackhez.
- Az Azure AD-fiókot, amelyet használni szeretne az Azure Active Directory globális rendszergazdájaként jelentkezhetnek be.

Az érvényesítés biztosítja, hogy a környezet készen áll a felhasználók, alkalmazások, csoportok és az Azure Stack szolgáltatásnevek kapcsolatos információk tárolására az Azure AD-ben az Azure Stack.

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) legújabb verziójának letöltése a [PowerShell-galériából](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek teljesülésére szükség:

**A számítógép, amelyen az eszköz fut:**

- Windows 10-es vagy Windows Server 2016, az internetkapcsolattal rendelkező.
- A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsot, és tekintse át a **fő** verzió és **kisebb** verziók:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Konfigurált az Azure Stack PowerShell](azure-stack-powershell-install.md).
- A legújabb [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszközt.

**Az Azure Active Directory-környezetet:**

- Azonosítsa az Azure AD-fiókot az Azure Stack használatának és az, hogy egy Azure Active Directory globális rendszergazdájaként.
- Azonosítsa az Azure AD-bérlő neve. A bérlő neve az Azure Active Directory; elsődleges tartomány nevének kell lennie. Ha például **contoso.onmicrosoft.com**.
- Azonosítsa az Azure-környezetet fogja használni. A környezet neve paraméter támogatott értékei a következők **AzureCloud**, **AzureChinaCloud**, vagy **AzureUSGovernment**, attól függően, melyik Azure-előfizetést használ.

## <a name="steps-to-validate-azure-identity"></a>Azure-identitás ellenőrzésének lépéseit

1. A számítógépen, amely megfelel az előfeltételeknek, nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő paranccsal telepíthető **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. A PowerShell használatával futtassa a következő parancsban **$serviceAdminCredential** a szolgáltatás-rendszergazdaként az Azure AD-bérlője számára.  Cserélje le **serviceadmin\@contoso.onmicrosoft.com** a fiók és a bérlő neve:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. A PowerShell használatával futtassa a következő parancsot az Azure AD-ellenőrzés:

   - Adja meg a környezet neve értéke **AzureEnvironment**. A környezet neve paraméter támogatott értékei a következők **AzureCloud**, **AzureChinaCloud**, vagy **AzureUSGovernment**, attól függően, melyik Azure-előfizetést használ.
   - Cserélje le **contoso.onmicrosoft.com** együtt az Azure Active Directory-bérlő neve.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Az eszköz futtatása után tekintse át a kimenetet. Az állapot megerősítéséhez **OK** a telepítési követelményeknek. Sikeres ellenőrzés a következő képhez hasonlóan jelenik meg:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>A jelentés és-naplófájl

Minden egyes alkalommal érvényesítési fut, az eredmények naplózza **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Ezek a fájlok helyét jeleníti meg az ellenőrzés eredményét a PowerShellben.

Ezek a fájlok segítségével megoszthatja érvényesítési állapot érvényesítési problémák vizsgálatához vagy az Azure Stack üzembe helyezése előtt. Mindkét fájlt megmarad, egyes további ellenőrzés eredményeit. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segíthet a telepítés vagy a támogatási csapat érvényesítési problémák kivizsgálásában.

Alapértelmezés szerint mindkét fájlt írt **C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Használja a **- OutputPath** ***&lt;elérési&gt;*** végén található egy másik jelentés helyét adja meg a futtatási parancssori paraméter.
- Használja a **- CleanReport** parancs futtatásával törölje az eszköz az előző futtatásokat információ végén paraméter **AzsReadinessCheckerReport.json**.

További információkért lásd: [Azure Stack érvényesítési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy ellenőrzés nem sikerül, a hibával kapcsolatos részleteket a PowerShell-ablakban jeleníti meg. Az eszköz is naplózza az információkat a AzsReadinessChecker.log fájl.

Az alábbi példák gyakori ellenőrzési hibákat ad útmutatást.

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

**Feloldási** – és a PowerShellben futtassa a következő parancsot, majd kövesse az utasításokat a jelszó alaphelyzetbe állítása:

```powershell
Login-AzureRMAccount
```

Azt is megteheti, hogy jelentkezzen be a [az Azure portal](https://portal.azure.com) , a fiók tulajdonosa, és a felhasználó kényszeríti a jelszó módosítására.

### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa 
 
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

**OK** – a fiók nem tud bejelentkezni a megadott Azure Active Directory (**AADDirectoryTenantName**). Ebben a példában **AzureChinaCloud** a következőként van megadva a **AzureEnvironment**.

**Feloldási** – győződjön meg arról, hogy a fiók érvényességét a megadott Azure-környezetében. A PowerShell, a következő parancs futtatásával ellenőrizze a fiók érvényességét, egy adott környezetben:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Fiók nem áll a rendszergazda

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

**OK** – Bár a fiók sikeresen jelentkezhetnek be, a fiók nem áll-e az Azure Active Directory-rendszergazda (**AADDirectoryTenantName**).  

**Feloldási** -jelentkezzen be a [az Azure portal](https://portal.azure.com) lépjen a fiók tulajdonosa **Azure Active Directory**, majd **felhasználók**, majd **válassza ki a Felhasználói**, majd **címtárbeli szerepkör**, és ezután ellenőrizze, hogy a felhasználó egy **globális rendszergazdai**. Ha a fiók egy **felhasználói**, lépjen a **Azure Active Directory** > **egyéni tartománynevek**, és győződjön meg arról, hogy a név, megadott  **AADDirectoryTenantName** van megjelölve, a címtárhoz tartozó elsődleges tartomány nevét. Ebben a példában ez **contoso.onmicrosoft.com**.

Az Azure Stack kell lennie, hogy a tartomány nevét az elsődleges tartomány nevét.

## <a name="next-steps"></a>További lépések

[Azure-regisztráció ellenőrzése](azure-stack-validate-registration.md)  
[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)  
