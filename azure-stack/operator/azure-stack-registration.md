---
title: Azure Stack hub regisztrálása az Azure-ban
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan regisztrálhat Azure Stack hub integrált rendszereit az Azure-ban, hogy letöltse az Azure Marketplace-elemeket, és hogyan állíthatja be az adatjelentéskészítést.
author: PatAltimore
ms.topic: how-to
ms.date: 11/19/2020
ms.author: patricka
ms.reviewer: avishwan
ms.lastreviewed: 11/19/2020
ms.custom: contperf-fy20q4
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 7888d23390706a098f814f4cc674d030662c9915
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869559"
---
# <a name="register-azure-stack-hub-with-azure"></a>Azure Stack hub regisztrálása az Azure-ban

Regisztrálja Azure Stack hubot az Azure-ban, hogy letöltse az Azure Marketplace-elemeket az Azure-ból, és állítsa be a kereskedelmi adatokat a Microsoftnak. Azure Stack hub regisztrálása után a rendszer a használatot az Azure Commerce szolgáltatásban fogja jelenteni, és a regisztrációhoz használt Azure számlázási előfizetés azonosítója alatt látja.

A cikkben található információk az Azure Stack hub integrált rendszerek Azure-ban való regisztrálását ismertetik. További információ a ASDK az Azure-ban való regisztrálásáról: [Azure stack hub regisztrációja](../asdk/asdk-register.md) a ASDK dokumentációjában.

> [!IMPORTANT]  
> Regisztráció szükséges a teljes Azure Stack hub-funkciók támogatásához, beleértve a piactéren található elemeket is. Ha nem regisztrálja az utólagos használatú számlázási modellt, az Azure Stack hub licencelési feltételeinek megsértését fogja megsérteni. Ha többet szeretne megtudni a Azure Stack hub licencelési modelljeiről, tekintse meg a [Hogyan vásárolhat lapot](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Előfeltételek

A regisztráció előtt végezze el a következő előfeltételeket:

- Ellenőrizze a hitelesítő adatokat.
- Állítsa be a PowerShell nyelvi módját.
- Telepítse a PowerShellt Azure Stack hubhoz.
- Töltse le az Azure Stack hub-eszközöket.
- Határozza meg a számlázási modellt.
- Határozza meg az egyedi regisztrációs nevet.

### <a name="verify-your-credentials"></a>Hitelesítő adatok ellenőrzése

Az Azure Stack hub Azure-ban való regisztrálása előtt a következőket kell tennie:

::: zone pivot="state-connected"
- Az Azure-előfizetéshez tartozó előfizetés-azonosító. A regisztráció csak az EA, a CSP vagy a CSP közös szolgáltatások előfizetéseit támogatja. A CSP-nek el kell döntenie, hogy [CSP-t vagy APSS-előfizetést használ](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)-e.<br><br>Az azonosító beszerzéséhez jelentkezzen be az Azure-ba, kattintson a **minden szolgáltatás** elemre. Ezt követően az **általános** kategóriában válassza az **előfizetések** elemet, kattintson a használni kívánt előfizetésre, és az **Essentials** területen találja az előfizetés-azonosítót. Ajánlott eljárásként használjon külön előfizetéseket üzemi és fejlesztési vagy tesztelési környezetekhez. 
::: zone-end
::: zone pivot="state-disconnected"
- Az Azure-előfizetéshez tartozó előfizetés-azonosító. Csak az EA-előfizetések támogatottak a regisztrációhoz. 

    Az azonosító beszerzéséhez jelentkezzen be az Azure-ba, kattintson a **minden szolgáltatás** elemre. Ezt követően az **általános** kategóriában válassza az **előfizetések** elemet, kattintson a használni kívánt előfizetésre, és az **Essentials** területen találja az előfizetés-azonosítót. Ajánlott eljárásként használjon külön előfizetéseket üzemi és fejlesztési vagy tesztelési környezetekhez. 
::: zone-end
   > [!Note]  
   > A németországi felhőalapú előfizetések jelenleg nem támogatottak.  

- Az előfizetés tulajdonosát képező fiók felhasználóneve és jelszava.

- A felhasználói fióknak hozzáféréssel kell rendelkeznie az Azure-előfizetéshez, és engedélyekkel kell rendelkeznie az identitáshoz tartozó alkalmazások és egyszerű szolgáltatások létrehozásához az előfizetéshez társított könyvtárban. Javasoljuk, hogy a minimális jogosultságú felügyelettel regisztrálja Azure Stack hub-t az Azure-ban. Az előfizetéshez való regisztrációt korlátozó egyéni szerepkör-definíciók létrehozásával kapcsolatos további információkért lásd: [Azure stack hub regisztrációs szerepkörének létrehozása](azure-stack-registration-role.md).

- Regisztrálja az Azure Stack hub erőforrás-szolgáltatót (további részletekért lásd a Azure Stack hub erőforrás-szolgáltató következő regisztrációja című szakaszt).

A regisztrációt követően a Azure Active Directory (Azure AD) globális rendszergazdai jogosultsága nem szükséges. Egyes műveletek esetében azonban szükség lehet a globális rendszergazdai hitelesítő adatokra (például egy erőforrás-szolgáltatói telepítő parancsfájlra vagy egy olyan új szolgáltatásra, amely engedélyt kér). Ideiglenesen visszaállíthatja a fiók globális rendszergazdai engedélyeit, vagy használhat egy különálló globális rendszergazdai fiókot, amely az *alapértelmezett szolgáltatói előfizetés* tulajdonosa.

Az Azure Stack hub-t regisztráló felhasználó az Azure AD-ben az egyszerű szolgáltatás tulajdonosa. Csak az Azure Stack hub-ban regisztrált felhasználó módosíthatja az Azure Stack hub regisztrációját. Ha egy nem rendszergazda felhasználó, aki nem tulajdonosa a regisztrációs szolgáltatásnak Azure Stack hub regisztrációját vagy újbóli regisztrálását, akkor előfordulhat, hogy egy 403-válaszon keresztül érkeznek. A 403-válasz azt jelzi, hogy a felhasználó nem rendelkezik megfelelő engedélyekkel a művelet végrehajtásához.

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, [létrehozhat egy ingyenes Azure-fiókot itt](https://azure.microsoft.com/free/?b=17.06). Az Azure Stack hub regisztrálása nem jár költségekkel az Azure-előfizetésében.

> [!NOTE]
> Ha egynél több Azure Stack központtal rendelkezik, az ajánlott eljárás az egyes Azure Stack központoknak a saját előfizetéshez való regisztrálása. Így könnyebben nyomon követheti a használatot.

### <a name="set-the-powershell-language-mode"></a>A PowerShell nyelvi módjának beállítása

Azure Stack hub sikeres regisztrálásához a PowerShell nyelvi módját **FullLanguageMode** értékre kell állítani.  Annak ellenőrzéséhez, hogy az aktuális nyelvi mód a teljes értékre van-e állítva, nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsmagokat:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, hogy a kimenet **FullLanguageMode** ad vissza. Ha a rendszer bármilyen más nyelvi módot ad vissza, a regisztrációt egy másik gépen kell futtatni, vagy a nyelvi módot a folytatás előtt be kell állítani a **FullLanguageMode** értékre.

### <a name="install-powershell-for-azure-stack-hub"></a>A PowerShell telepítése Azure Stack hubhoz

Az Azure-ban való regisztráláshoz használja a Azure Stack hub legújabb PowerShell-szolgáltatását.

Ha még nem telepítette a legújabb verziót, tekintse [meg a PowerShell telepítése Azure stack hubhoz](powershell-install-az-module.md)című témakört.

### <a name="download-the-azure-stack-hub-tools"></a>Az Azure Stack hub-eszközök letöltése

Az Azure Stack hub Tools GitHub-tárháza olyan PowerShell-modulokat tartalmaz, amelyek támogatják a Azure Stack hub funkciót, beleértve a regisztrációs funkciókat is. A regisztrációs folyamat során importálnia és használnia kell a **RegisterWithAzure. psm1** PowerShell-modult (a Azure stack hub Tools repositoryban található), hogy regisztrálja az Azure stack hub-példányt az Azure-ban.

Annak érdekében, hogy a legújabb verziót használja, törölje az Azure Stack hub-eszközök meglévő verzióit, és [töltse le a legújabb verziót a githubról](azure-stack-powershell-download.md) az Azure-ban való regisztrálás előtt.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

### <a name="determine-your-billing-model"></a>Számlázási modell meghatározása
::: zone pivot="state-connected"
 Egy csatlakoztatott üzemelő példány lehetővé teszi, hogy Azure Stack hub csatlakozhasson az internethez és az Azure-hoz. Az Azure AD-t vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) is használhatja Identity Store-ként, és két számlázási modell közül választhat: utólagos használat vagy kapacitás alapján. A számlázási modellt később, a regisztrációs parancsfájl futtatásakor kell megadnia.
::: zone-end

::: zone pivot="state-disconnected"
 A leválasztott központi telepítés lehetővé teszi az Azure Stack hub használatát az internettel való kapcsolat nélkül. A leválasztott központi telepítés esetében csak egy AD FS Identity Store-t és a Capacity-alapú számlázási modellt kell használnia. A számlázási modellt később, a regisztrációs parancsfájl futtatásakor kell megadnia.
::: zone-end

### <a name="determine-your-unique-registration-name"></a>Egyedi regisztrációs név meghatározása

A regisztrációs parancsfájl futtatásakor egyedi regisztrációs nevet kell megadnia. Az Azure Stack hub-előfizetés Azure-regisztrációval való összekapcsolásának egyszerű módja, ha az Azure Stack hub **Cloud ID**-t használja.

> [!NOTE]
> A Capacity-alapú számlázási modell használatával Azure Stack hub-regisztrációk esetében módosítania kell az egyedi nevet, ha az éves előfizetések lejárta után az újbóli regisztráció lejár, kivéve, ha [törli a lejárt regisztrációt](#renew-or-change-registration) , és újra regisztrálja magát az Azure-ban.

A Azure Stack hub központi telepítésének felhőalapú AZONOSÍTÓjának meghatározásához tekintse meg a [Felhőbeli azonosító megkeresése](azure-stack-find-cloud-id.md)című témakört.

::: zone pivot="state-connected"
## <a name="register-with-pay-as-you-use-billing"></a>Regisztráció a használaton kívüli számlázással

Ezekkel a lépésekkel regisztrálhat Azure Stack hubot az Azure-ban az utólagos használatú számlázási modell használatával.

> [!Note]  
> Ezeket a lépéseket olyan számítógépről kell futtatni, amely hozzáfér a privilegizált végponthoz (PEP). A PEP részletes ismertetését lásd: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

A csatlakoztatott környezetek hozzáférhetnek az internethez és az Azure-hoz. Ezekben a környezetekben regisztrálnia kell az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, majd konfigurálnia kell a számlázási modellt.

### <a name="az-modules"></a>[Az modulok](#tab/az1)

1. Ha regisztrálni szeretné az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd az alábbi paramétereket).

2. Adja hozzá a Azure Stack hub regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   ```powershell
   Add-AzAccount -EnvironmentName "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment** vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

   >[!Note]
   > Ha a munkamenet lejár, a jelszó módosult, vagy egyszerűen csak szeretné váltani a fiókokat, az Add-AzAccount használatával történő bejelentkezés előtt futtassa a következő parancsmagot: `Remove-AzAccount-Scope Process`

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:  

   ```powershell  
   Get-AzSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzSubscription
   ```

4. A következő parancs futtatásával regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools –** az könyvtár **regisztrációs** mappájához, amelyet az Azure stack hub-eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult a PowerShell használatával:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Ezután ugyanebben a PowerShell-munkamenetben ellenőrizze, hogy be van-e jelentkezve a megfelelő Azure PowerShell környezetbe. Ez a kontextus lenne az Azure-fiók, amelyet korábban a Azure Stack hub erőforrás-szolgáltató regisztrálásához használt. Futtatandó PowerShell:

   ```powershell  
   Connect-AzAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment** vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

7. Ugyanebben a PowerShell-munkamenetben futtassa a **set-AzsRegistration** parancsmagot. Futtatandó PowerShell:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   További információ a Set-AzsRegistration parancsmagról: [regisztrációs hivatkozás](#registration-reference).

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm1)

1. Ha regisztrálni szeretné az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd az alábbi paramétereket).

2. Adja hozzá a Azure Stack hub regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzureRMAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   ```powershell
   Add-AzureRMAccount -EnvironmentName "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment** vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

   >[!Note]
   > Ha a munkamenet lejár, a jelszó módosult, vagy egyszerűen csak szeretné váltani a fiókokat, az Add-AzureRMAccount használatával történő bejelentkezés előtt futtassa a következő parancsmagot: `Remove-AzureRMAccount-Scope Process`

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:  

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRMSubscription
   ```

4. A következő parancs futtatásával regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools –** az könyvtár **regisztrációs** mappájához, amelyet az Azure stack hub-eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult a PowerShell használatával:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Ezután ugyanebben a PowerShell-munkamenetben ellenőrizze, hogy be van-e jelentkezve a megfelelő Azure PowerShell környezetbe. Ez a kontextus lenne az Azure-fiók, amelyet korábban a Azure Stack hub erőforrás-szolgáltató regisztrálásához használt. Futtatandó PowerShell:

   ```powershell  
   Connect-AzureRMAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment** vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

7. Ugyanebben a PowerShell-munkamenetben futtassa a **set-AzsRegistration** parancsmagot. Futtatandó PowerShell:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   További információ a Set-AzsRegistration parancsmagról: [regisztrációs hivatkozás](#registration-reference).

---

   A folyamat 10 – 15 percet vesz igénybe. Ha a parancs befejeződik, megjelenik a **"saját környezet regisztrálása és aktiválása a megadott paraméterek használatával** " üzenet.

## <a name="register-with-capacity-billing"></a>Regisztrálás kapacitás-számlázással

Az alábbi lépésekkel regisztrálhat Azure Stack hubot az Azure-ban a kapacitás számlázási modell használatával.

> [!Note]  
> Ezeket a lépéseket olyan számítógépről kell futtatni, amely hozzáfér a privilegizált végponthoz (PEP). A PEP részletes ismertetését lásd: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

A csatlakoztatott környezetek hozzáférhetnek az internethez és az Azure-hoz. Ezekben a környezetekben regisztrálnia kell az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, majd konfigurálnia kell a számlázási modellt.

### <a name="az-modules"></a>[Az modulok](#tab/az2)

1. Ha regisztrálni szeretné az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd az alábbi paramétereket).

2. Adja hozzá a Azure Stack hub regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   ```powershell  
   Connect-AzAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment** vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:  

   ```powershell  
   Get-AzSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzSubscription
   ```

4. A következő parancs futtatásával regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools –** az könyvtár **regisztrációs** mappájához, amelyet az Azure stack hub-eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult a PowerShell használatával:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > A **set-AzsRegistration** parancsmag UsageReportingEnabled paraméterével letilthatja a használati jelentéskészítést úgy, hogy a paraméter hamis értékre van állítva. 
   
   További információ a Set-AzsRegistration parancsmagról: [regisztrációs hivatkozás](#registration-reference).

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm2)

1. Ha regisztrálni szeretné az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd az alábbi paramétereket).

2. Adja hozzá a AzureRMure stack hub regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzureRMAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   ```powershell  
   Connect-AzureRMAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment** vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:  

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRMSubscription
   ```

4. A következő parancs futtatásával regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon az Azure Stack hub-eszközök letöltésekor létrehozott **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához. Importálja a **RegisterWithAzure. psm1** modult a PowerShell használatával:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > A **set-AzsRegistration** parancsmag UsageReportingEnabled paraméterével letilthatja a használati jelentéskészítést úgy, hogy a paraméter hamis értékre van állítva. 
   
   További információ a Set-AzsRegistration parancsmagról: [regisztrációs hivatkozás](#registration-reference).

---

::: zone-end

::: zone pivot="state-disconnected"
## <a name="register-with-capacity-billing"></a>Regisztrálás kapacitás-számlázással

Ha Azure Stack központot regisztrál egy leválasztott környezetben (internetkapcsolat nélkül), akkor a Azure Stack hub-környezetből kell beszereznie egy regisztrációs jogkivonatot. Ezt követően használja ezt a tokent egy olyan számítógépen, amely tud csatlakozni az Azure-hoz, és telepítve van a PowerShell Azure Stack hubhoz.  

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Regisztrációs jogkivonat beszerzése az Azure Stack hub-környezetből

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools –** az könyvtár **regisztrációs** mappájához, amelyet az Azure stack hub-eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. A regisztrációs jogkivonat beszerzéséhez futtassa a következő PowerShell-parancsmagokat:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   További információ a Get-AzsRegistrationToken parancsmagról: [regisztrációs hivatkozás](#registration-reference).

   > [!Tip]  
   > A regisztrációs jogkivonatot a rendszer a *$FilePathForRegistrationToken* számára megadott fájlba menti. Saját belátása szerint módosíthatja a filepath vagy a fájlnevet.

3. Mentse ezt a regisztrációs jogkivonatot az Azure-ra csatlakoztatott gépen való használatra. A fájlt vagy a szöveget *$FilePathForRegistrationTokenból* másolhatja.

### <a name="connect-to-azure-and-register"></a>Kapcsolódjon az Azure-hoz, és regisztráljon

Az internethez csatlakozó számítógépen hajtsa végre ugyanezen lépéseket a RegisterWithAzure. psm1 modul importálásához, és jelentkezzen be a megfelelő Azure PowerShell környezetbe. Ezután hívja meg a Register-AzsEnvironment. Az Azure-ban regisztrálni kívánt regisztrációs jogkivonat meghatározása. Ha a Azure Stack hub több példányát regisztrálja ugyanazzal az Azure-előfizetési AZONOSÍTÓval, adjon meg egy egyedi regisztrációs nevet.

A regisztrációs jogkivonat és egy egyedi jogkivonat neve szükséges.

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools –** az könyvtár **regisztrációs** mappájához, amelyet az Azure stack hub-eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Ezután futtassa a következő PowerShell-parancsmagokat:  

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

Igény szerint a Get-Content parancsmaggal egy olyan fájlra is rámutathat, amely tartalmazza a regisztrációs tokent.

A regisztrációs jogkivonat és egy egyedi jogkivonat neve szükséges.

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools –** az könyvtár **regisztrációs** mappájához, amelyet az Azure stack hub-eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult:  

    ```powershell  
    Import-Module .\RegisterWithAzure.psm1
    ```

2. Ezután futtassa a következő PowerShell-parancsmagokat:  

    ```powershell  
    $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

  > [!Note]  
  > Mentse a regisztrációs erőforrás nevét és a regisztrációs jogkivonatot későbbi használatra.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Aktiválási kulcs beolvasása az Azure regisztrációs erőforrásból

Ezután be kell szereznie egy aktiválási kulcsot az Azure-ban a Register-AzsEnvironment-ben létrehozott regisztrációs erőforrásból.

Az aktiválási kulcs lekéréséhez futtassa a következő PowerShell-parancsmagokat:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Az aktiválási kulcsot a rendszer a *$KeyOutputFilePath* számára megadott fájlban menti. Saját belátása szerint módosíthatja a filepath vagy a fájlnevet.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Aktiválási erőforrás létrehozása Azure Stack hub-ban

Térjen vissza az Azure Stack hub-környezethez a Get-AzsActivationKey által létrehozott aktiválási kulcsból származó fájllal vagy szöveggel. Ezután hozzon létre egy aktiválási erőforrást Azure Stack hub-ban az adott aktiválási kulccsal. Az aktiválási erőforrás létrehozásához futtassa a következő PowerShell-parancsmagokat:

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Igény szerint a Get-Content parancsmaggal egy olyan fájlra is rámutathat, amely tartalmazza a regisztrációs jogkivonatot:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```
::: zone-end

## <a name="verify-azure-stack-hub-registration"></a>Azure Stack hub regisztrációjának ellenőrzése

A **régió kezelése** csempével ellenőrizheti, hogy az Azure stack hub regisztrációja sikeres volt-e. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján érhető el. Az állapot regisztrálható vagy nem regisztrálható. Ha regisztrálva van, akkor az Azure-előfizetés AZONOSÍTÓját is megjeleníti, amelyet az Azure Stack hub regisztrálásához használt a regisztrációs erőforráscsoport és a név használatával.

1. Jelentkezzen be az Azure Stack hub felügyeleti portálján `https://adminportal.local.azurestack.external` .

2. Az irányítópulton válassza a **régió kezelése** lehetőséget.

3. Válassza ki a **Tulajdonságok** elemet. Ez a panel a környezet állapotát és részleteit jeleníti meg. Az állapot **regisztrálható**, **nem regisztrálható** vagy nem **járt le**.

    [![Régió-felügyeleti csempe Azure Stack hub felügyeleti portálon](media/azure-stack-registration/admin1sm.png "Régió-felügyeleti csempe")](media/azure-stack-registration/admin1.png#lightbox)

    Ha regisztrálva van, a tulajdonságok a következők:
    
    - **Regisztrációs előfizetés azonosítója**: az Azure-előfizetési azonosító regisztrálva van, és Azure stack hubhoz van társítva.
    - **Regisztrációs erőforráscsoport**: az Azure stack hub-erőforrásokat tartalmazó társított előfizetésben található Azure-erőforráscsoport.

4. A Azure Portal használatával megtekintheti Azure Stack hub regisztrációs erőforrásait, majd ellenőrizheti, hogy a regisztráció sikeres volt-e. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az Azure stack hub regisztrálásához használt előfizetéshez társított fiókkal. Válassza a **minden erőforrás** lehetőséget, engedélyezze a **rejtett típusok megjelenítése** jelölőnégyzetet, és válassza ki a regisztrációs nevet.

5. Ha a regisztráció nem sikerült, a probléma megoldásához kövesse az [itt ismertetett lépéseket](#change-the-subscription-you-use) .  

Másik lehetőségként ellenőrizheti, hogy a regisztráció sikeres volt-e a piactér-felügyeleti szolgáltatás használatával. Ha a piactér-kezelés panelen megjelenik a piactér-elemek listája, a regisztráció sikeres volt. A leválasztott környezetekben azonban nem láthatók a piactéren a Marketplace-elemek.

> [!NOTE]
> A regisztráció befejezése után a nem regisztrált aktív figyelmeztetés többé nem jelenik meg. Az Azure Stack hub 1904-as előtti kiadásaiban a leválasztott forgatókönyvekben egy üzenet jelenik meg, amely arra kéri, hogy regisztrálja és aktiválja a Azure Stack hub-t, még akkor is, ha a regisztráció sikeresen megtörtént. Ez az üzenet nem jelenik meg a 1904-es és újabb verziókban.

## <a name="renew-or-change-registration"></a>Regisztráció megújítása vagy módosítása

::: zone pivot="state-connected"
A regisztrációt a következő esetekben kell frissítenie:

- A kapacitás-alapú éves előfizetés megújítása után.
- A számlázási modell módosításakor.
- A változások méretezése (csomópontok hozzáadása/eltávolítása) a kapacitás alapú számlázáshoz.

### <a name="prerequisites"></a>Előfeltételek

A következő információk szükségesek a [felügyeleti portálról](#verify-azure-stack-hub-registration) a regisztráció megújításához vagy módosításához:

| Felügyeleti portál | Parancsmag paraméter | Jegyzetek | 
|-----|-----|-----|
| REGISZTRÁCIÓS ELŐFIZETÉS AZONOSÍTÓJA | Előfizetés | Az előző regisztráció során használt előfizetés-azonosító |
| REGISZTRÁCIÓS ERŐFORRÁSCSOPORT | ResourceGroupName | Az az erőforráscsoport, amelyben az előző regisztrációs erőforrás létezik |
| REGISZTRÁCIÓ NEVE | RegistrationName | A korábbi regisztráció során használt regisztrációs név |

### <a name="change-the-subscription-you-use"></a>A használt előfizetés módosítása

Ha módosítani szeretné a használt előfizetést, először futtatnia kell a **Remove-AzsRegistration** parancsmagot, majd ellenőrizze, hogy be van-e jelentkezve a megfelelő Azure PowerShell környezetbe. Ezután futtassa a **set-AzsRegistration** parancsot bármilyen módosított paraméterrel, beleértve a következőt: `<billing model>` . A **Remove-AzsRegistration** futtatása közben be kell jelentkeznie a regisztráció során használt előfizetésbe, és a és a paraméterek használati értékeit a `RegistrationName` `ResourceGroupName` [felügyeleti portálon](#verify-azure-stack-hub-registration)látható módon kell megadnia:

### <a name="az-modules"></a>[Az modulok](#tab/az3)

```powershell  
# select the subscription used during the registration (shown in portal)
Select-AzSubscription -Subscription '<Registration subscription ID from portal>'
# unregister using the parameter values from portal
Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
# switch to new subscription id
Select-AzSubscription -Subscription '<New subscription ID>'
# register 
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<Billing model>' -RegistrationName '<Registration name>' -ResourceGroupName '<Registration resource group name>'
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm3)

```powershell  
# select the subscription used during the registration (shown in portal)
Select-AzureRMSubscription -Subscription '<Registration subscription ID from portal>'
# unregister using the parameter values from portal
Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
# switch to new subscription id
Select-AzureRMSubscription -Subscription '<New subscription ID>'
# register 
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<Billing model>' -RegistrationName '<Registration name>' -ResourceGroupName '<Registration resource group name>'
```

---

### <a name="change-billing-model-how-features-are-offered-or-re-register-your-instance"></a>A számlázási modell módosítása, a szolgáltatások felkínálása vagy a példány újbóli regisztrálása

Ez a szakasz akkor érvényes, ha módosítani szeretné a számlázási modellt, a szolgáltatások kínálatát, vagy ha újra szeretné regisztrálni a példányt. Ebben az esetben a regisztrációs függvényt kell meghívnia az új értékek beállítására. Nem kell először eltávolítania az aktuális regisztrációt. Jelentkezzen be a [felügyeleti portálon](#verify-azure-stack-hub-registration)megjelenő előfizetés-azonosítóba, majd futtassa újra a regisztrációt egy új `BillingModel` értékkel, miközben a `RegistrationName` és a `ResourceGroupName` Paraméterek értékei megegyeznek a [felügyeleti portálon](#verify-azure-stack-hub-registration)látható értékekkel:

### <a name="az-modules"></a>[Az modulok](#tab/az4)

```powershell  
# select the subscription used during the registration
Select-AzSubscription -Subscription '<Registration subscription ID from portal>'
# rerun registration with new BillingModel (or same billing model in case of re-registration) but using other parameters values from portal
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<New billing model>' -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm4)

```powershell  
# select the subscription used during the registration
Select-AzureRMSubscription -Subscription '<Registration subscription ID from portal>'
# rerun registration with new BillingModel (or same billing model in case of re-registration) but using other parameters values from portal
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<New billing model>' -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
```

---

::: zone-end

::: zone pivot="state-disconnected"
A regisztrációt a következő esetekben kell frissítenie vagy megújítani:

- A kapacitás-alapú éves előfizetés megújítása után.
- A számlázási modell módosításakor.
- A változások méretezése (csomópontok hozzáadása/eltávolítása) a kapacitás alapú számlázáshoz.

### <a name="remove-the-activation-resource-from-azure-stack-hub"></a>Az aktiválási erőforrás eltávolítása Azure Stack hub-ból

Először el kell távolítania az aktiválási erőforrást Azure Stack hubhoz, majd a regisztrációs erőforrást az Azure-ban.  

Az Azure Stack hub aktiválási erőforrásának eltávolításához futtassa a következő PowerShell-parancsmagokat az Azure Stack hub-környezetben:  

  ```powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Ha a regisztrációs erőforrást el szeretné távolítani az Azure-ban, győződjön meg róla, hogy egy Azure-ra csatlakoztatott számítógépen van, jelentkezzen be a megfelelő Azure PowerShell környezetbe, és futtassa a megfelelő PowerShell-parancsmagokat az alább leírtak szerint.

Használhatja az erőforrás létrehozásához használt regisztrációs jogkivonatot:  

  ```powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Vagy használhatja a regisztrációs név és a regisztrációs erőforráscsoport nevét a [felügyeleti portálról](#verify-azure-stack-hub-registration):

  ```powershell
  Unregister-AzsEnvironment -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
  ```

### <a name="re-register-using-connected-steps"></a>Ismételt regisztrálás a csatlakoztatott lépések használatával

Ha a számlázási modellt egy csatlakoztatott állapotban lévő, leválasztott állapotból a kapacitás számlázására módosítja, akkor a csatlakoztatott [modell lépései](azure-stack-registration.md?pivots=state-connected#change-billing-model-how-features-are-offered-or-re-register-your-instance)után újra regisztrálnia kell. 

>[!Note] 
>Ez nem változtatja meg az identitási modellt, csak a számlázási mechanizmust, és továbbra is a AD FS fogja használni az identitás forrásaként.

### <a name="re-register-using-disconnected-steps"></a>Újbóli regisztrálás a leválasztott lépések használatával

Mostantól teljesen megszüntette a regisztrációt egy leválasztott forgatókönyvben, és meg kell ismételnie a Azure Stack hub-környezet leválasztott forgatókönyvben való regisztrálásának lépéseit.
::: zone-end

### <a name="disable-or-enable-usage-reporting"></a>Használati jelentéskészítés letiltása vagy engedélyezése

A kapacitás-számlázási modellt használó Azure Stack hub-környezetek esetében kapcsolja ki a használati jelentéskészítést a **UsageReportingEnabled** paraméterrel a **set-AzsRegistration** vagy a **Get-AzsRegistrationToken** parancsmagok használatával. Azure Stack hub alapértelmezés szerint a használati metrikákat jelenti. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést.

::: zone pivot="state-connected"
Futtassa a következő PowerShell-parancsmagokat:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
      -UsageReportingEnabled:$false
   ```
::: zone-end
::: zone pivot="state-disconnected"
1. A regisztrációs jogkivonat módosításához futtassa a következő PowerShell-parancsmagokat:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > A regisztrációs jogkivonatot a rendszer a *$FilePathForRegistrationToken* számára megadott fájlba menti. Saját belátása szerint módosíthatja a filepath vagy a fájlnevet.

2. Mentse ezt a regisztrációs jogkivonatot az Azure-beli csatlakoztatott gépen való használatra. A fájlt vagy a szöveget *$FilePathForRegistrationTokenból* másolhatja.
::: zone-end

## <a name="move-a-registration-resource"></a>Regisztrációs erőforrás áthelyezése

Ha egy regisztrációs erőforrást ugyanahhoz az előfizetéshez tartozó erőforráscsoportok között helyez át **,** az összes környezet esetében támogatott. Azonban az előfizetések közötti regisztrációs erőforrás áthelyezése csak akkor támogatott, ha a két előfizetés ugyanahhoz a partner-AZONOSÍTÓhoz van feloldva. További információ az erőforrások új erőforráscsoporthoz való áthelyezéséről: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](/azure/azure-resource-manager/resource-group-move-resources).

> [!IMPORTANT]
> Ha meg szeretné akadályozni a regisztrációs erőforrások véletlen törlését a portálon, a regisztrációs parancsfájl automatikusan zárolást helyez el az erőforráshoz. Az áthelyezés vagy a törlés előtt el kell távolítania ezt a zárolást. Javasoljuk, hogy a véletlen törlés megakadályozása érdekében adjon hozzá egy zárolást a regisztrációs erőforráshoz.

## <a name="registration-reference"></a>Regisztrációs hivatkozás

### <a name="set-azsregistration"></a>Set-AzsRegistration

A **set-AzsRegistration** használatával regisztrálhat Azure stack hubot az Azure-ban, és engedélyezheti vagy letilthatja az elemek ajánlatát a piactéren és a használati jelentésekben.

A parancsmag futtatásához a következők szükségesek:

- Bármilyen típusú globális Azure-előfizetés.
- Ahhoz, hogy bejelentkezzen a Azure PowerShellba egy olyan fiókkal, amely az adott előfizetéshez tartozó tulajdonos vagy közreműködő.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Paraméter | Típus | Leírás |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | A [rendszerjogosultságú végpont eléréséhez](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)használt hitelesítő adatok. A Felhasználónév formátuma **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Sztring | Előre konfigurált távoli PowerShell-konzol, amely olyan képességeket biztosít, mint a naplók gyűjtése és az egyéb utólagos üzembe helyezési feladatok. További tudnivalókat a [privilegizált végpont használata](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) című cikkben találhat. |
| AzureContext | PSObject |  |
| ResourceGroupName | Sztring |  |
| ResourceGroupLocation | Sztring |  |
| BillingModel | Sztring | Az előfizetés által használt számlázási modell. A paraméter megengedett értékei a következők: kapacitás, PayAsYouUse és fejlesztés. |
| MarketplaceSyndicationEnabled | Igaz/Hamis | Meghatározza, hogy a piactér-felügyeleti funkció elérhető-e a portálon. Állítsa igaz értékre, ha az internetkapcsolattal van regisztrálva. Hamis értékre állítva, ha a regisztráció a leválasztott környezetekben megtörténjen. A leválasztott regisztrációk esetében az [Offline hírszolgáltatási eszköz](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) használható a Piactéri elemek letöltésére. |
| UsageReportingEnabled | Igaz/Hamis | Azure Stack hub alapértelmezés szerint a használati metrikákat jelenti. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést. A paraméter megengedett értékei a következők: true, false. |
| AgreementNumber | Sztring | Azon EA-szerződés száma, amely alatt a Azure Stack kapacitásának SKU-jának megrendelése megvolt. |
| RegistrationName | Sztring | Adja meg a regisztráció egyedi nevét, ha a regisztrációs parancsfájlt a Azure Stack hub egynél több példányán futtatja ugyanazzal az Azure-előfizetés-AZONOSÍTÓval. A paraméter alapértelmezett értéke **AzureStackRegistration**. Ha azonban ugyanazt a nevet használja az Azure Stack hub több példányán, a parancsfájl meghiúsul. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken regisztrációs tokent hoz létre a bemeneti paraméterekből.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Paraméter | Típus | Leírás |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | A [rendszerjogosultságú végpont eléréséhez](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)használt hitelesítő adatok. A Felhasználónév formátuma **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Sztring |  Előre konfigurált távoli PowerShell-konzol, amely olyan képességeket biztosít, mint a naplók gyűjtése és az egyéb utólagos üzembe helyezési feladatok. További tudnivalókat a [privilegizált végpont használata](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) című cikkben találhat. |
| AzureContext | PSObject |  |
| ResourceGroupName | Sztring |  |
| ResourceGroupLocation | Sztring |  |
| BillingModel | Sztring | Az előfizetés által használt számlázási modell. A paraméter megengedett értékei a következők: kapacitás, PayAsYouUse és fejlesztés. |
| MarketplaceSyndicationEnabled | Igaz/Hamis |  |
| UsageReportingEnabled | Igaz/Hamis | Azure Stack hub alapértelmezés szerint a használati metrikákat jelenti. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést. A paraméter megengedett értékei a következők: true, false. |
| AgreementNumber | Sztring |  |

## <a name="registration-failures"></a>Regisztrációs hibák

Az Azure Stack hub regisztrálására tett kísérlet során előfordulhat, hogy az alábbi hibák valamelyike látható:

- Nem sikerült beolvasni a kötelező hardver-információkat `$hostName` . Tekintse át a fizikai gazdagépet és a kapcsolatot, majd próbálja meg újra a regisztrációt.

- Nem lehet csatlakozni a következőhöz: `$hostName` a hardver információinak beolvasása. Tekintse át a fizikai gazdagépet és a kapcsolatot, majd próbálja meg újra a regisztrációt.

   Ok: ez általában azért van, mert a gazdagépekről próbáljuk ki a hardver részleteit, például az UUID-t, a BIOS-t és a CPU-t, hogy megkísérelje az aktiválást, és nem tudta elérni a fizikai gazdagéphez való kapcsolódás lehetőségét.

- A Felhőbeli azonosító [ `GUID` ] már regisztrálva van. A Felhőbeli azonosítók újrafelhasználása nem engedélyezett.

   Ok: Ez akkor fordul elő, ha a Azure Stack-környezet már regisztrálva van. Ha újra regisztrálni szeretné a környezetet egy másik előfizetéssel vagy számlázási modellel, kövesse a [regisztráció megújítása vagy módosítása lépéseit](#renew-or-change-registration).

- Amikor megpróbál hozzáférni a piactér-felügyelethez, hiba történik a termékek szindikátusba való kipróbálásakor.

   Ok: ez általában akkor fordul elő, ha Azure Stack hub nem fér hozzá a regisztrációs erőforráshoz. Ennek egyik gyakori oka, hogy amikor egy Azure-előfizetés címtár-bérlője megváltozik, visszaállítja a regisztrációt. Ha módosította az előfizetés címtár-bérlőjét, nem férhet hozzá a Azure Stack hub piactérhez vagy a jelentéshez. A probléma megoldásához újra kell regisztrálnia a problémát.
::: zone pivot="state-disconnected"
- A piactér-felügyelet továbbra is kéri a Azure Stack hub regisztrálását és aktiválását akkor is, ha már regisztrálta a bélyegzőt a leválasztott folyamat használatával.

   Ok: ez egy ismert probléma a leválasztott környezetek számára, és a [regisztráció állapotának ellenőrzéséhez](#verify-azure-stack-hub-registration)szükséges. A piactér-kezelés használatához használja [az offline eszközt](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected).
::: zone-end

## <a name="next-steps"></a>További lépések

[Marketplace-elemek letöltése az Azure-ból](azure-stack-download-azure-marketplace-item.md)
