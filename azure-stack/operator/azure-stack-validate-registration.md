---
title: Azure-regisztráció ellenőrzése
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan érvényesítheti az Azure-regisztrációt az Azure Stack hub Readiness-ellenőrző eszközzel.
author: PatAltimore
ms.topic: how-to
ms.date: 10/19/2020
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: ffe992c4a2db39f5b2e29d80a002f1486099baaa
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868794"
---
# <a name="validate-azure-registration"></a>Azure-regisztráció ellenőrzése

Az Azure Stack hub Readiness-ellenőrző eszköz (**AzsReadinessChecker**) használatával ellenőrizze, hogy az Azure-előfizetése készen áll-e a Azure stack hub használatára, mielőtt megkezdené az Azure stack hub központi telepítését. A készültség-ellenőrző ellenőrzi, hogy:

- A használt Azure-előfizetés támogatott típusú. Az előfizetésnek felhőalapú megoldás-szolgáltatónak (CSP) vagy Nagyvállalati Szerződés (EA) kell lennie.
- Az előfizetés az Azure-ban való regisztrálásához használt fiók bejelentkezhet az Azure-ba, és az előfizetés tulajdonosa.

Az Azure Stack hub regisztrálásával kapcsolatos további információkért lásd: [Azure stack hub regisztrálása az Azure](azure-stack-registration.md)-ban.

## <a name="get-the-readiness-checker-tool"></a>A Readiness-ellenőrző eszköz beszerzése

Töltse le a **AzsReadinessChecker** legújabb verzióját a [PowerShell-Galéria](https://aka.ms/AzsReadinessChecker).  

## <a name="install-and-configure"></a>Telepítés és konfigurálás

### <a name="az-powershell"></a>[Az PowerShell](#tab/az)

### <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek szükségesek:

#### <a name="az-powershell-modules"></a>Az PowerShell-modulok

Szüksége lesz az az PowerShell-modulok telepítésére. Útmutatásért lásd: a [PowerShell telepítése az előnézet modul](powershell-install-az-module.md).

#### <a name="azure-active-directory-aad-environment"></a>Azure Active Directory (HRE) környezet

- Azonosítsa egy olyan fiók felhasználónevét és jelszavát, amely az Azure Stack hub-ban használni kívánt Azure-előfizetéshez tartozó tulajdonos.  
- Azonosítsa a használni kívánt Azure-előfizetés előfizetési AZONOSÍTÓját.

### <a name="steps-to-validate-the-azure-registration"></a>Az Azure-regisztráció érvényesítésének lépései

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort, majd futtassa a következő parancsot a **AzsReadinessChecker** telepítéséhez:

   ```powershell
   Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
   Install-AzProfile -Profile 2019-03-01-hybrid -Force
   Install-Module -Name Microsoft.AzureStack.ReadinessChecker -AllowPrerelease
   ```

2. A PowerShell-parancssorból futtassa a következő parancsot a használni kívánt Azure-előfizetés beállításához `$subscriptionID` . Cserélje le `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` saját előfizetés-azonosítójával:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

3. A PowerShell-parancssorból futtassa a következő parancsot: 

   ```powershell
   Connect-AzAccount -subscription $subscriptionID
   ```

4. A PowerShell-parancssorból futtassa a következő parancsot az előfizetés érvényesítésének megkezdéséhez. Adja meg az Azure AD-rendszergazdát és az Azure AD-bérlő nevét:

   ```powershell
   Invoke-AzsRegistrationValidation  -RegistrationSubscriptionID $subscriptionID
   ```

5. Az eszköz futtatása után tekintse át a kimenetet. Ellenőrizze, hogy az állapot helyes-e a bejelentkezéshez és a regisztrációs követelményekhez. A sikeres ellenőrzési kimenet a következő példához hasonlóan jelenik meg:

   ```powershell
   Invoke-AzsRegistrationValidation v1.2005.1269 started.
   Checking Registration Requirements: OK

   Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

### <a name="azurerm-powershell"></a>[AzureRM PowerShell](#tab/rm)

### <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek szükségesek:

#### <a name="azurerm-powershell-modules"></a>AzureRM PowerShell-modulok

Szüksége lesz az az PowerShell-modulok telepítésére. Útmutatásért lásd: a [PowerShell AzureRM modul telepítése](powershell-install-az-module.md).

#### <a name="the-computer-on-which-the-tool-runs"></a>Az a számítógép, amelyen az eszköz fut

- Windows 10 vagy Windows Server 2016, internetkapcsolattal.
- PowerShell 5,1 vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsmagot, majd tekintse át a **fő** **-és** alverziókat:  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [Azure stack hubhoz konfigurált PowerShell](powershell-install-az-module.md).
- Az [Microsoft Azure stack hub Readiness-ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz legújabb verziója.  

#### <a name="azure-active-directory-azure-ad-environment"></a>Azure Active Directory (Azure AD-) környezet

- Azonosítsa egy olyan fiók felhasználónevét és jelszavát, amely az Azure Stack hub-ban használni kívánt Azure-előfizetéshez tartozó tulajdonos.  
- Azonosítsa a használni kívánt Azure-előfizetés előfizetési AZONOSÍTÓját.
- Azonosítsa a használni kívánt **AzureEnvironment** . A környezeti név paraméter támogatott értékei **AzureCloud**, **AzureChinaCloud** vagy **AzureUSGovernment**, attól függően, hogy melyik Azure-előfizetést használja.

### <a name="steps-to-validate-the-azure-registration"></a>Az Azure-regisztráció érvényesítésének lépései

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort egy olyan számítógépen, amely megfelel az előfeltételeknek, majd futtassa a következő parancsot a **AzsReadinessChecker** telepítéséhez:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
   ```

2. A PowerShell-parancssorból futtassa a következő parancsot az `$registrationCredential` előfizetés tulajdonosaként szolgáló fiók beállításához. Cserélje le a `subscriptionowner@contoso.onmicrosoft.com` fiókot és a bérlő nevét:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Ha megosztott szolgáltatásokat vagy – belső használatra-előfizetést használ, meg kell adnia egy felhasználó hitelesítő adatait az adott Azure AD-ből. Ez általában a következőhöz hasonló lesz: `subscriptionowner@iurcontoso.onmicrosoft.com` . A felhasználónak rendelkeznie kell a megfelelő hitelesítő adatokkal az előző lépésben leírtak szerint.

3. A PowerShell-parancssorból futtassa a következő parancsot a használni kívánt Azure-előfizetés beállításához `$subscriptionID` . Cserélje le `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` saját előfizetés-azonosítójával:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. A PowerShell-parancssorból futtassa a következő parancsot az előfizetés érvényesítésének megkezdéséhez:

   - Itt adhatja meg a `AzureEnvironment` **AzureCloud**, a **AzureGermanCloud** és a **AzureChinaCloud** értéket.  
   - Adja meg az Azure AD-rendszergazda és az Azure AD-bérlő nevét.
      ```powershell
      Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
      ```

5. Az eszköz futtatása után tekintse át a kimenetet. Ellenőrizze, hogy az állapot helyes-e a bejelentkezéshez és a regisztrációs követelményekhez. A sikeres ellenőrzési kimenet a következő példához hasonlóan jelenik meg:

   ```powershell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```
---

## <a name="report-and-log-file"></a>Jelentés és naplófájl

Minden alkalommal, amikor az érvényesítés lefut, az eredményeket a **AzsReadinessChecker. log** és **aAzsReadinessCheckerReport.js** bejegyzi. A fájlok helye a PowerShell érvényesítési eredményeivel együtt jelenik meg.

Ezek a fájlok segíthetnek az érvényesítési állapot megosztásában az Azure Stack hub üzembe helyezése vagy az érvényesítési problémák vizsgálata előtt. Mindkét fájl megőrzi az összes további érvényesítési ellenőrzés eredményét. A jelentés az identitás konfigurációjának megerősítését biztosítja az üzembe helyezési csoport számára. A naplófájl segítséget nyújthat az üzembe helyezéshez vagy a támogatási csoporthoz az érvényesítési problémák kivizsgálásához.

Alapértelmezés szerint mindkét fájl íródik a következőre: `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json` .  

- A `-OutputPath <path>` futtatási parancssor végén található paraméterrel adhatja meg egy másik jelentés helyét.
- A `-CleanReport` futtatási parancs végén található paraméterrel törölheti az eszköz előző futtatásával kapcsolatos információkat a **AzsReadinessCheckerReport.jsról**.

További információ: [Azure stack hub-ellenőrzési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy érvényesítési ellenőrzés sikertelen, a PowerShell-ablakban megjelenő hiba részletei láthatók. Az eszköz az **AzsReadinessChecker. log** fájlba is naplózza az adatokat.

Az alábbi példák a gyakori ellenőrzési hibákkal kapcsolatos további információkat tartalmazzák.

### <a name="user-must-be-an-owner-of-the-subscription"></a>A felhasználónak az előfizetés tulajdonosának kell lennie

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**OK** – a fiók nem az Azure-előfizetés rendszergazdája.

**Megoldás** – használjon olyan fiókot, amely az Azure-előfizetés rendszergazdája, amely az Azure stack hub üzembe helyezésével kapcsolatos díjat számít fel.

### <a name="expired-or-temporary-password"></a>Lejárt vagy ideiglenes jelszó

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**OK** – a fiók nem tud bejelentkezni, mert a jelszó lejárt vagy ideiglenes.

**Megoldás** – a PowerShellben futtassa a következő parancsot, és kövesse az utasításokat a jelszó alaphelyzetbe állításához.

```powershell
Login-AzureRMAccount
```

Egy másik lehetőség, hogy bejelentkezzen a [Azure Portalba](https://portal.azure.com) a fiók tulajdonosaként, és a felhasználó kénytelen lesz megváltoztatni a jelszót.

### <a name="unknown-user-type"></a>Ismeretlen felhasználói típus  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**OK** – a fiók nem tud bejelentkezni a megadott Azure ad-környezetbe. Ebben a példában a **AzureChinaCloud** a **AzureEnvironment** adja meg.  

**Megoldás** – ellenőrizze, hogy a fiók érvényes-e a megadott Azure-környezetben. A PowerShellben futtassa a következő parancsot annak ellenőrzéséhez, hogy a fiók érvényes-e egy adott környezetben:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>További lépések

- [Azure-identitás ellenőrzése](azure-stack-validate-identity.md)
- [A készültségi jelentés megtekintése](azure-stack-validation-report.md)
- [Általános Azure Stack hub integrációs szempontjai](azure-stack-datacenter-integration.md)
