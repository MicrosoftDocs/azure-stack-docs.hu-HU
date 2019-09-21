---
title: Azure Stack Azure-regisztrációjának ellenőrzése | Microsoft Docs
description: Az Azure-regisztráció ellenőrzéséhez használja a Azure Stack Readiness-ellenőrzőt.
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
ms.date: 06/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 9c4ddec0606556290e55850a9081c6665f2524d1
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159585"
---
# <a name="validate-azure-registration"></a>Azure-regisztráció ellenőrzése

Használja az Azure Stack Readiness-ellenőrző eszközt (**AzsReadinessChecker**) annak ellenőrzéséhez, hogy az Azure-előfizetése készen áll-e a Azure stack használatára. A Azure Stack telepítésének megkezdése előtt ellenőrizze a regisztrációt. A készültség-ellenőrző ellenőrzi, hogy:

- A használt Azure-előfizetés támogatott típusú. Az előfizetésnek felhőalapú szolgáltatónak (CSP) vagy Nagyvállalati Szerződés (EA) kell lennie.
- Az előfizetés az Azure-ban való regisztrálásához használt fiók bejelentkezhet az Azure-ba, és az előfizetés tulajdonosa.

Azure Stack regisztrációval kapcsolatos további információkért lásd: [Azure stack regisztrálása az Azure](azure-stack-registration.md)-ban.

## <a name="get-the-readiness-checker-tool"></a>A Readiness-ellenőrző eszköz beszerzése

Töltse le a **AzsReadinessChecker** legújabb verzióját a [PowerShell-Galéria](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek szükségesek:

### <a name="the-computer-on-which-the-tool-runs"></a>Az a számítógép, amelyen az eszköz fut

- Windows 10 vagy Windows Server 2016, internetkapcsolattal.
- PowerShell 5,1 vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsmagot, majd tekintse át a **fő** -és alverziókat:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Azure Stackhoz konfigurált PowerShell](azure-stack-powershell-install.md).
- [Microsoft Azure stack Readiness-ellenőrző](https://aka.ms/AzsReadinessChecker)legújabb verziója.  

### <a name="azure-active-directory-environment"></a>Azure Active Directory környezet

- Azonosítsa egy olyan fiók felhasználónevét és jelszavát, amely a Azure Stack használatával használni kívánt Azure-előfizetés tulajdonosa.  
- Azonosítsa a használni kívánt Azure-előfizetés előfizetés-AZONOSÍTÓját.
- Azonosítsa a használni kívánt **AzureEnvironment** . A környezeti név paraméter támogatott értékei **AzureCloud**, **AzureChinaCloud**vagy **AzureUSGovernment**, attól függően, hogy melyik Azure-előfizetést használja.

## <a name="steps-to-validate-azure-registration"></a>Az Azure-regisztráció érvényesítésének lépései

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort egy olyan számítógépen, amely megfelel az előfeltételeknek, majd futtassa a következő parancsot a **AzsReadinessChecker**telepítéséhez:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. A PowerShell-parancssorból futtassa a következő parancsot az előfizetés `$registrationCredential` tulajdonosaként szolgáló fiók beállításához. Cserélje `subscriptionowner@contoso.onmicrosoft.com` le a fiókot és a bérlő nevét:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Ha megosztott szolgáltatásokat vagy – belső használatra-előfizetést használ, meg kell adnia egy felhasználó hitelesítő adatait az adott Azure AD-ből. Ez általában a `subscriptionowner@iurcontoso.onmicrosoft.com`következőhöz hasonló lesz:. A felhasználónak rendelkeznie kell a megfelelő hitelesítő adatokkal az előző lépésben leírtak szerint.

3. A PowerShell-parancssorból futtassa a következő parancsot a `$subscriptionID` használni kívánt Azure-előfizetés beállításához. Cserélje `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` le saját előfizetés-azonosítójával:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. A PowerShell-parancssorból futtassa a következő parancsot az előfizetés érvényesítésének megkezdéséhez:

   - Itt adhatja meg a `AzureEnvironment` **AzureCloud**, a **AzureGermanCloud**és a **AzureChinaCloud**értéket.  
   - Adja meg a Azure Active Directory rendszergazdáját és a Azure Active Directory bérlő nevét.

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Az eszköz futtatása után tekintse át a kimenetet. Ellenőrizze, hogy az állapot helyes-e a bejelentkezéshez és a regisztrációs követelményekhez. A sikeres ellenőrzési kimenet a következő példához hasonlóan jelenik meg:

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>Jelentés és naplófájl

A rendszer minden alkalommal futtatja az eredményeket a **AzsReadinessChecker. log** és a **AzsReadinessCheckerReport. JSON**fájlban. A fájlok helye a PowerShell érvényesítési eredményeivel együtt jelenik meg.

Ezek a fájlok segíthetnek az érvényesítési állapot megosztásában a Azure Stack üzembe helyezése vagy az érvényesítési problémák vizsgálata előtt. Mindkét fájl megőrzi az összes további érvényesítési ellenőrzés eredményét. A jelentés az identitás konfigurációjának megerősítését biztosítja az üzembe helyezési csoport számára. A naplófájl segítséget nyújthat az üzembe helyezéshez vagy a támogatási csoporthoz az érvényesítési problémák kivizsgálásához.

Alapértelmezés szerint mindkét fájl a **C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.JSON**-be van írva.  

- A Run parancssor végén a **-OutputPath** ***&lt;Path&gt;*** paraméterrel adhatja meg egy másik jelentés helyét.
- A Run parancs végén a **-CleanReport** paraméterrel törölheti az eszköz előző futtatásával kapcsolatos információkat a **AzsReadinessCheckerReport. JSON**fájlból.

További információ: [Azure stack ellenőrzési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy érvényesítési ellenőrzés sikertelen, a PowerShell-ablakban megjelenő hiba részletei láthatók. Az eszköz az **AzsReadinessChecker. log** fájlba is naplózza az adatokat.

Az alábbi példák útmutatást nyújtanak a gyakori ellenőrzési hibákhoz:

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

**Megoldás** – használjon olyan fiókot, amely az Azure-előfizetés rendszergazdája, amely a Azure stack üzemelő példány használatáról lesz kiszámlázva.

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

**OK** – a fiók nem tud bejelentkezni, mert a jelszó vagy lejárt, vagy ideiglenes.

**Megoldás** – a PowerShellben futtassa a következő parancsot, és kövesse az utasításokat a jelszó alaphelyzetbe állításához.

```powershell
Login-AzureRMAccount
```

Azt is megteheti, hogy a fiók tulajdonosaként bejelentkezik a [Azure Portalba](https://portal.azure.com) , és a felhasználó kénytelen lesz megváltoztatni a jelszót.

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

**OK** – a fiók nem tud bejelentkezni a megadott Azure Active Directory környezetbe. Ebben a példában a **AzureChinaCloud** a **AzureEnvironment**adja meg.  

**Megoldás** – ellenőrizze, hogy a fiók érvényes-e a megadott Azure-környezetben. A PowerShellben futtassa a következő parancsot annak ellenőrzéséhez, hogy a fiók érvényes-e egy adott környezetben:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>További lépések

- [Azure-identitás ellenőrzése](azure-stack-validate-identity.md)
- [A készültségi jelentés megtekintése](azure-stack-validation-report.md)
- [Általános Azure Stack integrációs megfontolások](azure-stack-datacenter-integration.md)
