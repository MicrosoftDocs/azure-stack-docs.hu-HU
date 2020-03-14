---
title: Azure Stack hub regisztrálása az Azure-ban
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan regisztrálhat Azure Stack hub integrált rendszereit az Azure-ban, hogy letöltse az Azure Marketplace-elemeket, és hogyan állíthatja be az adatjelentéskészítést.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 02/25/2020
ms.author: inhenkel
ms.reviewer: avishwan
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 6af18b519f32975643dea10d81dfa9ed1f0588c5
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294835"
---
# <a name="register-azure-stack-hub-with-azure"></a>Azure Stack hub regisztrálása az Azure-ban

Regisztrálja Azure Stack hubot az Azure-ban, hogy letöltse az Azure Marketplace-elemeket az Azure-ból, és állítsa be a kereskedelmi adatokat a Microsoftnak. Azure Stack hub regisztrálása után a rendszer a használatot az Azure Commerce szolgáltatásban fogja jelenteni, és a regisztrációhoz használt Azure számlázási előfizetés azonosítója alatt látja.

A cikkben található információk az Azure Stack hub integrált rendszerek Azure-ban való regisztrálását ismertetik. További információ a ASDK az Azure-ban való regisztrálásáról: [Azure stack hub regisztrációja](../asdk/asdk-register.md) a ASDK dokumentációjában.

> [!IMPORTANT]  
> Regisztráció szükséges a teljes Azure Stack hub-funkciók támogatásához, beleértve a piactéren található elemeket is. Ha nem regisztrálja az utólagos használatú számlázási modellt, az Azure Stack hub licencelési feltételeinek megsértését fogja megsérteni. Ha többet szeretne megtudni a Azure Stack hub licencelési modelljeiről, tekintse meg a [Hogyan vásárolhat lapot](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Előfeltételek

A regisztráció előtt a következő előfeltételek szükségesek:

- Ellenőrizze a hitelesítő adatokat.
- Állítsa be a PowerShell nyelvi módját.
- Telepítse a PowerShellt Azure Stack hubhoz.
- Töltse le az Azure Stack hub-eszközöket.
- Határozza meg a regisztrációs forgatókönyvet.

### <a name="verify-your-credentials"></a>Hitelesítő adatok ellenőrzése

Az Azure Stack hub Azure-ban való regisztrálása előtt a következőket kell tennie:

- Az Azure-előfizetéshez tartozó előfizetés-azonosító. A regisztráció csak az EA, a CSP vagy a CSP közös szolgáltatások előfizetéseit támogatja. A CSP-nek el kell döntenie, hogy [CSP-t vagy APSS-előfizetést használ](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)-e.<br><br>Az azonosító beszerzéséhez jelentkezzen be az Azure-ba, kattintson a **minden szolgáltatás**elemre. Ezt követően az **általános** kategóriában válassza az **előfizetések**elemet, kattintson a használni kívánt előfizetésre, és az **Essentials** területen találja az előfizetés-azonosítót. Ajánlott eljárásként használjon külön előfizetéseket üzemi és fejlesztési vagy tesztelési környezetekhez. 

  > [!Note]  
  > A németországi felhőalapú előfizetések jelenleg nem támogatottak.

- Az előfizetés tulajdonosát képező fiók felhasználóneve és jelszava.

- A felhasználói fióknak hozzáféréssel kell rendelkeznie az Azure-előfizetéshez, és engedélyekkel kell rendelkeznie az identitáshoz tartozó alkalmazások és egyszerű szolgáltatások létrehozásához az előfizetéshez társított könyvtárban. Javasoljuk, hogy a minimális jogosultságú felügyelettel regisztrálja Azure Stack hub-t az Azure-ban. Az előfizetéshez való regisztrációt korlátozó egyéni szerepkör-definíciók létrehozásával kapcsolatos további információkért lásd: [Azure stack hub regisztrációs szerepkörének létrehozása](azure-stack-registration-role.md).

- Regisztrálja az Azure Stack hub erőforrás-szolgáltatót (további részletekért lásd a Azure Stack hub erőforrás-szolgáltató következő regisztrációja című szakaszt).

A regisztrációt követően a Azure Active Directory (Azure AD) globális rendszergazdai jogosultsága nem szükséges. Egyes műveletek esetében azonban szükség lehet a globális rendszergazdai hitelesítő adatokra (például egy erőforrás-szolgáltatói telepítő parancsfájlra vagy egy olyan új szolgáltatásra, amely engedélyt kér). Ideiglenesen visszaállíthatja a fiók globális rendszergazdai engedélyeit, vagy használhat egy különálló globális rendszergazdai fiókot, amely az *alapértelmezett szolgáltatói előfizetés*tulajdonosa.

Az Azure Stack hub-t regisztráló felhasználó az Azure AD-ben az egyszerű szolgáltatás tulajdonosa. Csak az Azure Stack hub-ban regisztrált felhasználó módosíthatja az Azure Stack hub regisztrációját. Ha egy nem rendszergazda felhasználó, aki nem tulajdonosa a regisztrációs szolgáltatásnak Azure Stack hub regisztrációját vagy újbóli regisztrálását, akkor előfordulhat, hogy egy 403-válaszon keresztül érkeznek. A 403-válasz azt jelzi, hogy a felhasználó nem rendelkezik megfelelő engedélyekkel a művelet végrehajtásához.

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, [létrehozhat egy ingyenes Azure-fiókot itt](https://azure.microsoft.com/free/?b=17.06). Az Azure Stack hub regisztrálása nem jár költségekkel az Azure-előfizetésében.

> [!NOTE]
> Ha egynél több Azure Stack központtal rendelkezik, az ajánlott eljárás az egyes Azure Stack központoknak a saját előfizetéshez való regisztrálása. Így könnyebben nyomon követheti a használatot.

### <a name="powershell-language-mode"></a>PowerShell nyelvi mód

Azure Stack hub sikeres regisztrálásához a PowerShell nyelvi módját **FullLanguageMode**értékre kell állítani.  Annak ellenőrzéséhez, hogy az aktuális nyelvi mód a teljes értékre van-e állítva, nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsmagokat:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, hogy a kimenet **FullLanguageMode**ad vissza. Ha a rendszer bármilyen más nyelvi módot ad vissza, a regisztrációt egy másik gépen kell futtatni, vagy a nyelvi módot a folytatás előtt be kell állítani a **FullLanguageMode** értékre.

### <a name="install-powershell-for-azure-stack-hub"></a>A PowerShell telepítése Azure Stack hubhoz

Az Azure-ban való regisztráláshoz használja a Azure Stack hub legújabb PowerShell-szolgáltatását.

Ha még nem telepítette a legújabb verziót, tekintse [meg a PowerShell telepítése Azure stack hubhoz](azure-stack-powershell-install.md)című témakört.

### <a name="download-the-azure-stack-hub-tools"></a>Az Azure Stack hub-eszközök letöltése

Az Azure Stack hub Tools GitHub-tárháza olyan PowerShell-modulokat tartalmaz, amelyek támogatják a Azure Stack hub funkciót, beleértve a regisztrációs funkciókat is. A regisztrációs folyamat során importálnia és használnia kell a **RegisterWithAzure. psm1** PowerShell-modult (a Azure stack hub Tools repositoryban található), hogy regisztrálja az Azure stack hub-példányt az Azure-ban.

Annak érdekében, hogy a legújabb verziót használja, törölje az Azure Stack hub-eszközök meglévő verzióit, és [töltse le a legújabb verziót a githubról](azure-stack-powershell-download.md) az Azure-ban való regisztrálás előtt.

### <a name="determine-your-registration-scenario"></a>A regisztrációs forgatókönyv meghatározása

Előfordulhat, hogy az Azure Stack hub üzemelő példánya *csatlakoztatva* van vagy *le van választva*.

- **Csatlakoztatott**  
 A csatlakoztatott eszköz Azure Stack hub üzembe helyezését jelenti, hogy az internethez és az Azure-hoz is csatlakozhat. Azure AD-t vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) kell használnia az identitás-tárolóhoz. A csatlakoztatott üzemelő példányok esetében két számlázási modell közül választhat: utólagos használat vagy kapacitás alapján.
  - [Regisztráljon egy csatlakoztatott Azure stack hubot az Azure-ban az utólagos **használatú** számlázási modell használatával](#register-connected-with-pay-as-you-go-billing).
  - [Regisztráljon egy csatlakoztatott Azure stack hubot az Azure-ban a **kapacitás** számlázási modell használatával](#register-connected-with-capacity-billing).

- **Leválasztott**  
 Az Azure-beli üzembe helyezési lehetőség leválasztásával az internettel való kapcsolat nélkül telepítheti és használhatja Azure Stack hubot. A leválasztott központi telepítés azonban csak egy AD FS Identity Store-ra és a Capacity-alapú számlázási modellre korlátozódik.
  - [Regisztrálja a leválasztott Azure stack hubot a **kapacitás** számlázási modell használatával ](#register-disconnected-with-capacity-billing).

### <a name="determine-a-unique-registration-name-to-use"></a>A használni kívánt egyedi regisztrációs név meghatározása

Ha az Azure-ban regisztrálja Azure Stack hubot, egyedi regisztrációs nevet kell megadnia. Az Azure Stack hub-előfizetés Azure-regisztrációval való összekapcsolásának egyszerű módja, ha az Azure Stack hub **Cloud ID**-t használja.

> [!NOTE]
> A Capacity-alapú számlázási modell használatával Azure Stack hub-regisztrációk esetében módosítania kell az egyedi nevet, ha az éves előfizetések lejárta után az újbóli regisztráció lejár, kivéve, ha [törli a lejárt regisztrációt](azure-stack-registration.md#change-the-subscription-you-use) , és újra regisztrálja magát az Azure-ban.

Az Azure Stack hub központi telepítésének felhőalapú AZONOSÍTÓjának meghatározásához nyissa meg a PowerShellt rendszergazdaként egy olyan számítógépen, amely hozzáfér a Kiemelt végponthoz, futtassa a következő parancsokat, majd jegyezze fel a **CloudID** értéket:

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Az utólagos elszámolású számlázással kapcsolatos regisztráció

Ezekkel a lépésekkel regisztrálhat Azure Stack hubot az Azure-ban az utólagos használatú számlázási modell használatával.

> [!Note]  
> Ezeket a lépéseket olyan számítógépről kell futtatni, amely hozzáfér a privilegizált végponthoz (PEP). A PEP részletes ismertetését lásd: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

A csatlakoztatott környezetek hozzáférhetnek az internethez és az Azure-hoz. Ezekben a környezetekben regisztrálnia kell az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, majd konfigurálnia kell a számlázási modellt.

1. Ha regisztrálni szeretné az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd az alábbi paramétereket).

2. Adja hozzá a Azure Stack hub regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzureRmAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

   >[!Note]
   > Ha a munkamenet lejár, a jelszó módosult, vagy egyszerűen csak szeretné váltani a fiókokat, az Add-AzureRmAccount használatával történő bejelentkezés előtt futtassa a következő parancsmagot: `Remove-AzureRmAccount-Scope Process`

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. A következő parancs futtatásával regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon az Azure Stack hub-eszközök letöltésekor létrehozott **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához. Importálja a **RegisterWithAzure. psm1** modult a PowerShell használatával:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Ezután ugyanebben a PowerShell-munkamenetben ellenőrizze, hogy be van-e jelentkezve a megfelelő Azure PowerShell környezetbe. Ez a kontextus lenne az Azure-fiók, amelyet korábban a Azure Stack hub erőforrás-szolgáltató regisztrálásához használt. Futtatandó PowerShell:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

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
   A set-AzsRegistration parancsmaggal kapcsolatos további információkért lásd: [regisztrációs hivatkozás](#registration-reference).

   A folyamat 10 – 15 percet vesz igénybe. Ha a parancs befejeződik, megjelenik a **"saját környezet regisztrálása és aktiválása a megadott paraméterek használatával** " üzenet.

## <a name="register-connected-with-capacity-billing"></a>Kapcsolat regisztrálása a kapacitás számlázásával

Ezekkel a lépésekkel regisztrálhat Azure Stack hubot az Azure-ban az utólagos használatú számlázási modell használatával.

> [!Note]  
> Ezeket a lépéseket olyan számítógépről kell futtatni, amely hozzáfér a privilegizált végponthoz (PEP). A PEP részletes ismertetését lásd: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

A csatlakoztatott környezetek hozzáférhetnek az internethez és az Azure-hoz. Ezekben a környezetekben regisztrálnia kell az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, majd konfigurálnia kell a számlázási modellt.

1. Ha regisztrálni szeretné az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd az alábbi paramétereket).

2. Adja hozzá a Azure Stack hub regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzureRmAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. A következő parancs futtatásával regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
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
   
   A set-AzsRegistration parancsmaggal kapcsolatos további információkért lásd: [regisztrációs hivatkozás](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Regisztráció leválasztva a kapacitás számlázásával

Ha Azure Stack központot regisztrál egy leválasztott környezetben (internetkapcsolat nélkül), akkor a Azure Stack hub-környezetből kell beszereznie egy regisztrációs jogkivonatot. Ezt követően használja ezt a tokent egy olyan számítógépen, amely tud csatlakozni az Azure-hoz, és telepítve van a PowerShell Azure Stack hubhoz.  

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Regisztrációs jogkivonat beszerzése az Azure Stack hub-környezetből

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon az Azure Stack hub-eszközök letöltésekor létrehozott **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához. Importálja a **RegisterWithAzure. psm1** modult:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. A regisztrációs jogkivonat beszerzéséhez futtassa a következő PowerShell-parancsmagokat:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   A Get-AzsRegistrationToken parancsmaggal kapcsolatos további információkért lásd: [regisztrációs hivatkozás](#registration-reference).

   > [!Tip]  
   > A regisztrációs jogkivonatot a rendszer a *$FilePathForRegistrationToken*számára megadott fájlba menti. Saját belátása szerint módosíthatja a filepath vagy a fájlnevet.

3. Mentse ezt a regisztrációs jogkivonatot az Azure-ra csatlakoztatott gépen való használatra. A fájlt vagy a szöveget *$FilePathForRegistrationTokenból*másolhatja.

### <a name="connect-to-azure-and-register"></a>Kapcsolódjon az Azure-hoz, és regisztráljon

Az internethez csatlakozó számítógépen hajtsa végre ugyanezen lépéseket a RegisterWithAzure. psm1 modul importálásához, és jelentkezzen be a megfelelő Azure PowerShell-környezetbe. Ezután hívja meg a Register-AzsEnvironment. Az Azure-ban regisztrálni kívánt regisztrációs jogkivonat meghatározása. Ha a Azure Stack hub több példányát regisztrálja ugyanazzal az Azure-előfizetési AZONOSÍTÓval, adjon meg egy egyedi regisztrációs nevet.

A regisztrációs jogkivonat és egy egyedi jogkivonat neve szükséges.

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon az Azure Stack hub-eszközök letöltésekor létrehozott **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához. Importálja a **RegisterWithAzure. psm1** modult:  

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

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon az Azure Stack hub-eszközök letöltésekor létrehozott **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához. Importálja a **RegisterWithAzure. psm1** modult:  

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
  > Az aktiválási kulcsot a rendszer a *$KeyOutputFilePath*számára megadott fájlban menti. Saját belátása szerint módosíthatja a filepath vagy a fájlnevet.

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

## <a name="verify-azure-stack-hub-registration"></a>Azure Stack hub regisztrációjának ellenőrzése

A **régió kezelése** csempével ellenőrizheti, hogy az Azure stack hub regisztrációja sikeres volt-e. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján érhető el. Az állapot regisztrálható vagy nem regisztrálható. Ha regisztrálva van, akkor az Azure-előfizetés AZONOSÍTÓját is megjeleníti, amelyet az Azure Stack hub regisztrálásához használt a regisztrációs erőforráscsoport és a név használatával.

1. Jelentkezzen be az [Azure stack hub felügyeleti portálján](https://adminportal.local.azurestack.external).

2. Az irányítópulton válassza a **régió kezelése**lehetőséget.

3. Válassza ki a **Tulajdonságok** elemet. Ez a panel a környezet állapotát és részleteit jeleníti meg. Az állapot **regisztrálható**, **nem regisztrálható**vagy nem **járt le**.

    [![Régió-felügyeleti csempe Azure Stack hub felügyeleti portálon](media/azure-stack-registration/admin1sm.png "Régió-felügyeleti csempe")](media/azure-stack-registration/admin1.png#lightbox)

    Ha regisztrálva van, a tulajdonságok a következők:
    
    - **Regisztrációs előfizetés azonosítója**: az Azure-előfizetési azonosító regisztrálva van, és Azure stack hubhoz van társítva.
    - **Regisztrációs erőforráscsoport**: az Azure stack hub-erőforrásokat tartalmazó társított előfizetésben található Azure-erőforráscsoport.

4. A Azure Portal használatával megtekintheti Azure Stack hub regisztrációs erőforrásait, majd ellenőrizheti, hogy a regisztráció sikeres volt-e. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az Azure stack hub regisztrálásához használt előfizetéshez társított fiókkal. Válassza a **minden erőforrás**lehetőséget, engedélyezze a **rejtett típusok megjelenítése** jelölőnégyzetet, és válassza ki a regisztrációs nevet.

5. Ha a regisztráció nem sikerült, a probléma megoldásához kövesse az [itt ismertetett lépéseket](#change-the-subscription-you-use) .  

Másik lehetőségként ellenőrizheti, hogy a regisztráció sikeres volt-e a piactér-felügyeleti szolgáltatás használatával. Ha a piactér-kezelés panelen megjelenik a piactér-elemek listája, a regisztráció sikeres volt. A leválasztott környezetekben azonban nem láthatók a piactéren a Marketplace-elemek.

> [!NOTE]
> A regisztráció befejezése után a nem regisztrált aktív figyelmeztetés többé nem jelenik meg. Az Azure Stack hub 1904-as előtti kiadásaiban a leválasztott forgatókönyvekben egy üzenet jelenik meg, amely arra kéri, hogy regisztrálja és aktiválja a Azure Stack hub-t, még akkor is, ha a regisztráció sikeresen megtörtént. Ez az üzenet nem jelenik meg a 1904-es és újabb verziókban.

## <a name="renew-or-change-registration"></a>Regisztráció megújítása vagy módosítása

### <a name="renew-or-change-registration-in-connected-environments"></a>A regisztráció megújítása vagy módosítása csatlakoztatott környezetekben

A regisztrációt a következő esetekben kell frissítenie vagy megújítani:

- A kapacitás-alapú éves előfizetés megújítása után.
- A számlázási modell módosításakor.
- A változások méretezése (csomópontok hozzáadása/eltávolítása) a kapacitás alapú számlázáshoz.

#### <a name="change-the-subscription-you-use"></a>A használt előfizetés módosítása

Ha módosítani szeretné a használt előfizetést, először futtatnia kell a **Remove-AzsRegistration** parancsmagot, majd ellenőrizze, hogy be van-e jelentkezve a megfelelő Azure PowerShell környezetbe. Ezután futtassa a **set-AzsRegistration** parancsot a módosított paraméterekkel, beleértve a `<billing model>`:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>A számlázási modell vagy a szolgáltatások ajánlatának módosítása

Ha módosítani szeretné a számlázási modellt, vagy azt, hogy miként lehet szolgáltatásokat kínálni a telepítéshez, meghívhatja a regisztrációs függvényt az új értékek beállítására. Nem kell először eltávolítania az aktuális regisztrációt:

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Leválasztott környezetekben való regisztráció megújítása vagy módosítása

A regisztrációt a következő esetekben kell frissítenie vagy megújítani:

- A kapacitás-alapú éves előfizetés megújítása után.
- A számlázási modell módosításakor.
- A változások méretezése (csomópontok hozzáadása/eltávolítása) a kapacitás alapú számlázáshoz.

#### <a name="remove-the-activation-resource-from-azure-stack-hub"></a>Az aktiválási erőforrás eltávolítása Azure Stack hub-ból

Először el kell távolítania az aktiválási erőforrást Azure Stack hubhoz, majd a regisztrációs erőforrást az Azure-ban.  

Az Azure Stack hub aktiválási erőforrásának eltávolításához futtassa a következő PowerShell-parancsmagokat az Azure Stack hub-környezetben:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Ha a regisztrációs erőforrást el szeretné távolítani az Azure-ban, győződjön meg róla, hogy egy Azure-ra csatlakoztatott számítógépen van, jelentkezzen be a megfelelő Azure PowerShell környezetbe, és futtassa a megfelelő PowerShell-parancsmagokat az alább leírtak szerint.

Használhatja az erőforrás létrehozásához használt regisztrációs jogkivonatot:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Vagy használhatja a regisztrációs nevet is:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Újbóli regisztrálás a leválasztott lépések használatával

Mostantól teljesen megszüntette a regisztrációt egy leválasztott forgatókönyvben, és meg kell ismételnie a Azure Stack hub-környezet leválasztott forgatókönyvben való regisztrálásának lépéseit.

### <a name="disable-or-enable-usage-reporting"></a>Használati jelentéskészítés letiltása vagy engedélyezése

A kapacitás-számlázási modellt használó Azure Stack hub-környezetek esetében kapcsolja ki a használati jelentéskészítést a **UsageReportingEnabled** paraméterrel a **set-AzsRegistration** vagy a **Get-AzsRegistrationToken** parancsmagok használatával. Azure Stack hub alapértelmezés szerint a használati metrikákat jelenti. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést.

#### <a name="with-a-connected-azure-stack-hub"></a>Csatlakoztatott Azure Stack hubhoz

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack-hub"></a>Leválasztott Azure Stack hubhoz

1. A regisztrációs jogkivonat módosításához futtassa a következő PowerShell-parancsmagokat:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > A regisztrációs jogkivonatot a rendszer a *$FilePathForRegistrationToken*számára megadott fájlba menti. Saját belátása szerint módosíthatja a filepath vagy a fájlnevet.

2. Mentse ezt a regisztrációs jogkivonatot az Azure-beli csatlakoztatott gépen való használatra. A fájlt vagy a szöveget *$FilePathForRegistrationTokenból*másolhatja.

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
| MarketplaceSyndicationEnabled | Igaz/hamis | Meghatározza, hogy a piactér-felügyeleti funkció elérhető-e a portálon. Állítsa igaz értékre, ha az internetkapcsolattal van regisztrálva. Hamis értékre állítva, ha a regisztráció a leválasztott környezetekben megtörténjen. A leválasztott regisztrációk esetében az [Offline hírszolgáltatási eszköz](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) használható a Piactéri elemek letöltésére. |
| UsageReportingEnabled | Igaz/hamis | Azure Stack hub alapértelmezés szerint a használati metrikákat jelenti. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést. A paraméter megengedett értékei a következők: true, false. |
| AgreementNumber | Sztring | Azon EA-szerződés száma, amely alatt a Azure Stack kapacitásának SKU-jának megrendelése megvolt. |
| RegistrationName | Sztring | Adja meg a regisztráció egyedi nevét, ha a regisztrációs parancsfájlt a Azure Stack hub egynél több példányán futtatja ugyanazzal az Azure-előfizetés-AZONOSÍTÓval. A paraméter alapértelmezett értéke **AzureStackRegistration**. Ha azonban ugyanazt a nevet használja az Azure Stack hub több példányán, a parancsfájl meghiúsul. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

A Get-AzsRegistrationToken regisztrációs jogkivonatot hoz létre a bemeneti paraméterekből.

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
| MarketplaceSyndicationEnabled | Igaz/hamis |  |
| UsageReportingEnabled | Igaz/hamis | Azure Stack hub alapértelmezés szerint a használati metrikákat jelenti. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést. A paraméter megengedett értékei a következők: true, false. |
| AgreementNumber | Sztring |  |

## <a name="registration-failures"></a>Regisztrációs hibák

Az Azure Stack hub regisztrálására tett kísérlet során előfordulhat, hogy az alábbi hibák valamelyike látható:

- Nem sikerült beolvasni a `$hostName`kötelező hardver-információit. Tekintse át a fizikai gazdagépet és a kapcsolatot, majd próbálja meg újból futtatni a regisztrációt.

- Nem lehet csatlakozni a `$hostName`hoz a hardver adatainak beszerzéséhez. Tekintse át a fizikai gazdagépet és a kapcsolatot, majd próbálja meg újból futtatni a regisztrációt.

   Ok: ez általában azért van, mert a gazdagépekről próbáljuk ki a hardver részleteit, például az UUID-t, a BIOS-t és a CPU-t, hogy megkísérelje az aktiválást, és nem tudta elérni a fizikai gazdagéphez való kapcsolódás lehetőségét.

- A felhő-azonosító [`GUID`] már regisztrálva van. A Felhőbeli azonosítók újrafelhasználása nem engedélyezett.

   Ok: Ez akkor fordul elő, ha a Azure Stack-környezet már regisztrálva van. Ha újra regisztrálni szeretné a környezetet egy másik előfizetéssel vagy számlázási modellel, [tekintse meg ezeket az utasításokat](#change-the-subscription-you-use).

- Amikor megpróbál hozzáférni a piactér-felügyelethez, hiba történik a termékek szindikátusba való kipróbálásakor.

   Ok: ez általában akkor fordul elő, ha Azure Stack hub nem fér hozzá a regisztrációs erőforráshoz. Ennek egyik gyakori oka, hogy amikor egy Azure-előfizetés címtár-bérlője megváltozik, visszaállítja a regisztrációt. Ha módosította az előfizetés címtár-bérlőjét, nem férhet hozzá a Azure Stack hub piactérhez vagy a jelentéshez. A probléma megoldásához újra kell regisztrálnia a problémát.

- A piactér-felügyelet továbbra is kéri a Azure Stack hub regisztrálását és aktiválását akkor is, ha már regisztrálta a bélyegzőt a leválasztott folyamat használatával.

   Ok: ez egy ismert probléma a leválasztott környezetek esetében. A regisztráció állapotát a [következő lépésekkel](azure-stack-registration.md#verify-azure-stack-hub-registration)ellenőrizheti. A piactér-kezelés használatához használja [az offline eszközt](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario).

## <a name="next-steps"></a>Következő lépések

[Marketplace-elemek letöltése az Azure-ból](azure-stack-download-azure-marketplace-item.md)
