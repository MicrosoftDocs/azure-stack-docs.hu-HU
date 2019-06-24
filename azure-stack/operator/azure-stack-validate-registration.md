---
title: Azure-regisztráció ellenőrzése az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack-készültségi ellenőrző használatával Azure-regisztráció ellenőrzése.
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
ms.openlocfilehash: ca2ea89ab73c7a8e056e4b4700e92a872e628f34
ms.sourcegitcommit: 3f52cf06fb5b3208057cfdc07616cd76f11cdb38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316176"
---
# <a name="validate-azure-registration"></a>Azure-regisztráció ellenőrzése

Az Azure Stack Readiness-ellenőrző eszközt (**AzsReadinessChecker**) ellenőrzése, hogy az Azure Stack használatra készen áll-e az Azure-előfizetésében. Az Azure Stack központi telepítésének megkezdése előtt a regisztráció érvényesítése. A készenléti-ellenőrző a következőket ellenőrzi:

- Az Azure-előfizetést használ olyan támogatott. Előfizetések egy Felhőszolgáltató (CSP) vagy a nagyvállalati szerződés (EA) kell lennie.
- Regisztrálja az előfizetését az Azure-ral használt fióknak bejelentkezhet az Azure-ba, és a egy előfizetés tulajdonosa.

Azure Stack-regisztráció kapcsolatos további információkért lásd: [regisztrálása az Azure Stack az Azure-ral](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása

Töltse le a legújabb verzióját **AzsReadinessChecker** származó a [PowerShell-galériából](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek teljesülésére szükség:

### <a name="the-computer-on-which-the-tool-runs"></a>A számítógép, amelyen az eszköz fut.

- Windows 10-es vagy Windows Server 2016, az internetkapcsolattal rendelkező.
- A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsmagot, és tekintse át a **fő** és **kisebb** verziók:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Konfigurált az Azure Stack PowerShell](azure-stack-powershell-install.md).
- A legújabb [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker).  

### <a name="azure-active-directory-environment"></a>Az Azure Active Directory-környezetet

- Azonosítsa a felhasználónevet és jelszót, amely használhatja az Azure Stack Azure-előfizetés tulajdonosa.  
- Azonosítsa az előfizetés-azonosító az Azure-előfizetés fogja használni.
- Azonosítsa a **AzureEnvironment** fogja használni. A környezet neve paraméter támogatott értékei a következők **AzureCloud**, **AzureChinaCloud**, vagy **AzureUSGovernment**, attól függően, melyik Azure-előfizetés áll használatával.

## <a name="steps-to-validate-azure-registration"></a>Lépések az Azure-regisztráció ellenőrzése

1. Egy számítógépen, amely megfelel az előfeltételeknek, nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő paranccsal telepíthető **AzsReadinessChecker**:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. A PowerShell használatával futtassa a következő parancsban `$registrationCredential` a fiókot, amely az előfizetés tulajdonosa. Cserélje le `subscriptionowner@contoso.onmicrosoft.com` a fiók és a bérlő neve:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Egy CSP-hez, ha egy megosztott, szolgáltatások vagy IUR előfizetés esetén meg kell adnia egy felhasználó hitelesítő adatait, hogy saját AAD-ból. Ez általában a lesz hasonló `subscriptionowner@iurcontoso.onmicrosoft.com`. A felhasználó a megfelelő hitelesítő adatokkal, rendelkeznie kell az előző lépésben leírtak szerint.

3. A PowerShell parancssorában futtassa a következőt beállítása `$subscriptionID` mint az Azure-előfizetés használatára. Cserélje le `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` a saját előfizetés-Azonosítójára:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. A PowerShell használatával futtassa a következő parancsot az előfizetés érvényesítése elindításához:

   - Adja meg az értékét `AzureEnvironment` , **AzureCloud**, **AzureGermanCloud**, vagy **AzureChinaCloud**.  
   - Adja meg az Azure Active Directory-rendszergazda és az Azure Active Directory-bérlő neve.

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Az eszköz futtatása után tekintse át a kimenetet. Ellenőrizze az állapot be mind a eszközregisztráció követelményeinek. Sikeres ellenőrzés kimeneti az alábbi példához hasonlóan jelenik meg:

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>A jelentés és-naplófájl

Minden egyes alkalommal érvényesítési fut, az eredmények naplózza **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Ezek a fájlok helyét az ellenőrzés eredményét a PowerShellben együtt jelennek meg.

Ezek a fájlok segítségével megoszthatja érvényesítési állapot érvényesítési problémák vizsgálatához vagy az Azure Stack üzembe helyezése előtt. Mindkét fájlt megmarad, egyes további ellenőrzés eredményeit. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segíthet a telepítés vagy a támogatási csapat érvényesítési problémák kivizsgálásában.

Alapértelmezés szerint mindkét fájlt írt **C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Használja a **- OutputPath** ***&lt;elérési&gt;*** végén található egy másik jelentés helyét adja meg a futtatási parancssori paraméter.
- Használja a **- CleanReport** parancs futtatásával törölje az eszköz az előző futtatásokat információ végén paraméter **AzsReadinessCheckerReport.json**.

További információkért lásd: [Azure Stack érvényesítési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy ellenőrzés nem sikerül, a hibával kapcsolatos részleteket a PowerShell-ablakban jeleníti meg. Az eszköz is naplózza az adatokat a **AzsReadinessChecker.log** fájlt.

Az alábbi példák útmutatást nyújtanak a közös ellenőrzési hibák:

### <a name="user-must-be-an-owner-of-the-subscription"></a>Felhasználói előfizetés tulajdonosának kell lennie.

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

**OK** – a fiók nem áll az Azure-előfizetés rendszergazdája.

**Feloldási** -fiókkal, amely rendszergazda az Azure-előfizetés, amely az Azure Stack üzemelő példányához. a használat után kell fizetnie.

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

**Feloldási** – a PowerShellben futtassa a következő parancsot, és kövesse az utasításokat a jelszó alaphelyzetbe állítása.

```powershell
Login-AzureRMAccount
```

Azt is megteheti, hogy jelentkezzen be a [az Azure portal](https://portal.azure.com) , a fiók tulajdonosa, és a felhasználó kényszeríti a jelszó módosítására.

### <a name="unknown-user-type"></a>Ismeretlen felhasználó típusa  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**OK** – a fiók nem tud bejelentkezni a megadott Azure Active Directory-környezetet. Ebben a példában **AzureChinaCloud** a következőként van megadva a **AzureEnvironment**.  

**Feloldási** – győződjön meg arról, hogy a fiók érvényességét a megadott Azure-környezetében. A PowerShell, a következő parancs futtatásával ellenőrizze a fiók érvényességét, egy adott környezetben:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>További lépések

- [Azure-identitás ellenőrzése](azure-stack-validate-identity.md)
- [A készültségi jelentés megtekintése](azure-stack-validation-report.md)
- [Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)
