---
title: Azure Stack hub regisztrálása az Azure-ban
description: Ismerje meg, hogyan regisztrálhat Azure Stack hub-t az Azure-ban, hogy letöltse az Azure Marketplace-elemeket, és hogyan állíthatja be az adatjelentéskészítést.
author: sethmanheim
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 88f6c0e36fb8a101b963835fd7f0dcd69595e119
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329666"
---
# <a name="register-azure-stack-hub-with-azure"></a>Azure Stack hub regisztrálása az Azure-ban

A Marketplace Syndication beállításához regisztrálnia és aktiválni kell a moduláris adatközpontot (MDC) vagy Azure Stack hub-t Azure Stack hub alapján, az üzembe helyezés befejeződése után. A Marketplace Syndication szolgáltatásban a rendszergazda feltölti a helyi Azure Stack központ Piactért az Azure Marketplace-ről letöltött rendszerképekkel.

Az Azure-felhőhöz és a leválasztani kívánt rendszerekhez való kapcsolódáshoz regisztrálni kell a regisztrációt.

## <a name="overview"></a>Áttekintés

A regisztrációs folyamat a következő lépésekből áll:

1. Regisztrációs jogkivonat exportálása Azure Stack hub-ból.
2. Regisztrálja Azure Stack hub-t az Azure-ban.
3. Aktiválási kulcs exportálása az Azure-ból.
4. Azure Stack hub aktiválása.

Ha egy rendszer csatlakozik az Azure-felhőhöz, mind a négy lépést egyetlen parancsfájllal lehet végrehajtani. Ha a rendszer le van választva, a rendszergazdának külön kell végrehajtania a lépéseket.

> [!NOTE]
> A MDC esetében minden Azure Stack hub rendszernek külön regisztrálnia kell. Ezért az alábbi utasításokat három alkalommal kell végrehajtani.

## <a name="prerequisites"></a>Előfeltételek

A regisztráció előtt a következő előfeltételekre lesz szüksége:

- Ellenőrizze a hitelesítő adatokat.
- Állítsa be a PowerShell nyelvi módját.
- Telepítse a PowerShellt Azure Stack hubhoz.
- Az Azure Stack hub Tools modul telepítése
- Határozza meg a regisztrációs forgatókönyvet.

### <a name="verify-your-credentials"></a>Hitelesítő adatok ellenőrzése

Az Azure Stack hub Azure-ban való regisztrálása előtt a következőket kell tennie:

- Az Azure-előfizetéshez tartozó előfizetés-azonosító.  

    > [!Note]  
    > Az Azure-előfizetések Azure-beli felhőalapú környezetekhez (Azure kereskedelmi, Azure Government stb.) vannak társítva. Ez határozza meg, hogy melyik felhőhöz fog csatlakozni a piactér-tartalmak eléréséhez.
    > 
    > A regisztrációhoz használt előfizetést jóvá kell hagyni a JEDI szolgáltatásokhoz. Ez biztosítja, hogy az Ön által regisztrált eszköz korlátlan erőforrás-használatot biztosítson, és ne legyen jelentéskészítés az Azure-ban. Ha jóvá szeretné hagyni az előfizetését, küldjön egy e-mailt az azshregistration@microsoft.com előfizetés-azonosítóra, amelyet jóvá kell hagynia a regisztrálni kívánt Azure stack hub-vagy moduláris adatközponttal (MDC) együtt.

- Az előfizetés tulajdonosát képező fiók felhasználóneve és jelszava. 
- A felhasználói fióknak hozzáféréssel kell rendelkeznie az Azure-előfizetéshez, és engedélyekkel kell rendelkeznie az identitáshoz tartozó alkalmazások és egyszerű szolgáltatások létrehozásához az előfizetéshez társított könyvtárban. Javasoljuk, hogy a minimális jogosultságú felügyelettel regisztrálja Azure Stack hub-t az Azure-ban. További információ az előfizetés regisztrációját korlátozó egyéni szerepkör-definíció létrehozásáról: [Azure stack hub regisztrációs szerepkörének létrehozása](../../operator/azure-stack-registration-role.md).
- Regisztrálja az Azure Stack hub erőforrás-szolgáltatót (további részleteket a következő részekben talál).

A regisztrációt követően a Azure Active Directory (Azure AD) globális rendszergazdai jogosultsága nem szükséges. Bizonyos műveletek esetében azonban szükség lehet a globális rendszergazdai hitelesítő adatokra (például egy erőforrás-szolgáltatói telepítő parancsfájlra vagy egy olyan új szolgáltatásra, amely engedélyt kér). Ideiglenesen visszaállíthatja a fiók globális rendszergazdai engedélyeit, vagy használhat egy különálló globális rendszergazdai fiókot, amely az *alapértelmezett szolgáltatói előfizetés* tulajdonosa.

Az Azure Stack hub-t regisztráló felhasználó az Azure AD-ben az egyszerű szolgáltatás tulajdonosa. Csak az Azure Stack hub-ban regisztrált felhasználó módosíthatja az Azure Stack hub regisztrációját. Ha egy nem rendszergazda felhasználó, aki nem tulajdonosa a regisztrációs szolgáltatásnak, az Azure Stack hub regisztrációját vagy újbóli regisztrálását kísérli meg, a 403-es válasz jelenhet meg. Az 403-es válasz azt jelzi, hogy a felhasználó nem rendelkezik megfelelő engedélyekkel a művelet végrehajtásához.

Az Azure Stack hub regisztrálása nem jár költségekkel az Azure-előfizetésében.

### <a name="powershell-language-mode"></a>PowerShell nyelvi mód

Azure Stack hub sikeres regisztrálásához a PowerShell nyelvi módját **FullLanguageMode** értékre kell állítani. Annak ellenőrzéséhez, hogy az aktuális nyelvi mód a teljes értékre van-e állítva, nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsmagokat:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, hogy a kimenet **FullLanguageMode** ad vissza. Ha más nyelvi módot ad vissza, a regisztrációt egy másik gépen kell futtatni, vagy a nyelvi módot a folytatás előtt be kell állítani a **FullLanguageMode** értékre.

### <a name="install-powershell-for-azure-stack-hub"></a>A PowerShell telepítése Azure Stack hubhoz

Az Azure-ban való regisztráláshoz használja a Azure Stack hub legújabb PowerShell-szolgáltatását.

Ha még nem telepítette a legújabb verziót, tekintse [meg a PowerShell telepítése Azure stack hubhoz](../../operator/azure-stack-powershell-install.md)című témakört.

### <a name="install-the-azure-stack-hub-tools-module"></a>Az Azure Stack hub Tools modul telepítése

Az [Azure stack hub Tools modul](https://www.powershellgallery.com/packages/azs.tools.admin/0.1.0) olyan PowerShell-modulokat tartalmaz, amelyek támogatják a Azure stack hub funkciót, beleértve a regisztrációs funkciókat is. A regisztrációs folyamat során importálnia és használnia kell a **RegisterWithAzure. psm1** almodult az Azure-beli Azure stack hub-példány regisztrálásához.

A legújabb Azure Stack hubok eszköz telepítése:

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.
2. Futtassa a következő parancsmagot:

    ```powershell  
        Install-Module -Name Azs.Tools.Admin
    ```

### <a name="determine-your-registration-scenario"></a>A regisztrációs forgatókönyv meghatározása

Előfordulhat, hogy az Azure Stack hub üzemelő példánya *csatlakoztatva* van vagy *le van választva*.

- **Csatlakoztatva** : a csatlakoztatott eszköz Azure stack hub üzembe helyezését jelenti, hogy az internethez és az Azure-hoz is kapcsolódjon. Azure AD-t vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) is használhat az identitás-tárolóhoz.

- **Leválasztva** : az Azure-beli üzembe helyezési lehetőség leválasztásával az internethez való kapcsolódás nélkül telepítheti és használhatja Azure stack hubot.

### <a name="determine-a-unique-registration-name-to-use"></a>A használni kívánt egyedi regisztrációs név meghatározása

Ha az Azure-ban regisztrálja Azure Stack hubot, egyedi regisztrációs nevet kell megadnia. Az Azure Stack hub-előfizetés Azure-regisztrációval való összekapcsolásának egyszerű módja, ha az Azure Stack hub **Cloud ID** -t használja.

Az Azure Stack hub központi telepítésének felhőalapú AZONOSÍTÓjának meghatározásához nyissa meg a PowerShellt rendszergazdaként egy olyan számítógépen, amely hozzáfér a Kiemelt végponthoz, futtassa a következő parancsokat, majd jegyezze fel a **CloudID** értéket:

```PowerShell
Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Get-AzureStackStampInformation
```

## <a name="registration-and-activation-for-systems-connected-to-the-azure-cloud"></a>Az Azure-felhőhöz kapcsolódó rendszerek regisztrálása és aktiválása

Az alábbi lépéseket követve regisztrálhat egy Azure Stack hub-rendszerkapcsolatot az Azure-hoz.

> [!NOTE]  
> Ezeket a lépéseket olyan számítógépről kell futtatni, amely hozzáfér a privilegizált végponthoz (PEP). A PEP részletes ismertetését lásd: [a privilegizált végpont használata Azure stack központban](../../operator/azure-stack-privileged-endpoint.md).

A csatlakoztatott környezetek hozzáférhetnek az internethez és az Azure-hoz. Ezekben a környezetekben regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-ban, majd konfigurálja a számlázási modellt.

1. Az Azure Stack hub erőforrás-szolgáltató az Azure-ban való regisztrálásához indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd a paramétereket az alábbiak szerint).

2. Adja hozzá a Azure Stack hub regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzureRmAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiók konfigurációja alapján kétfaktoros hitelesítést kell használnia.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud** vagy **AzureUSGovernment**.  |

   >[!NOTE]
   > Ha a munkamenet lejár, a jelszó módosult, vagy a fiókokat át szeretné váltani, futtassa a következő parancsmagot, mielőtt bejelentkezik a **Add-AzureRmAccount** : **Remove-AzureRmAccount-scope folyamat** használatával.

3. Ugyanebben a PowerShell-munkamenetben ellenőrizze, hogy be van-e jelentkezve a megfelelő Azure PowerShell környezetbe. Ez a környezet az Azure-fiók, amelyet korábban a Azure Stack hub erőforrás-szolgáltató regisztrálásához használt:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud** vagy **AzureUSGovernment**.  |

4. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

5. A következő parancs futtatásával regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

   Azt is megteheti, hogy az Azure Portal használatával regisztrálja az Azure Stack hub erőforrás-szolgáltatót az Azure-előfizetésében. Jelentkezzen be a Azure Portal az Azure-előfizetéshez társított fiók használatával. Keresse meg **Categories** az  >  **általános**  >  **előfizetések** kategóriáit, és válassza ki azt az előfizetés-azonosítót, amelyre regisztrálni kívánja az Azure stack hub erőforrás-szolgáltatót. A bal oldali ablaktáblán navigáljon a **Beállítások**  >  **erőforrás-szolgáltatók** elemre. Válassza ki a **Microsoft. AzureStack** erőforrás-szolgáltatót, és válassza a **regisztráció** lehetőséget.

   ![Azure Stack hub erőforrás-szolgáltató regisztrálása](./media/registration-tzl/register-azure-resource-provider-portal.png)


6. Ugyanebben a PowerShell-munkamenetben futtassa a **set-AzsRegistration** parancsmagot:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $msAssetTag = "Enter the value printed on the product"
   $RegistrationName = "<unique-registration-name>"

   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Custom `
      -RegistrationName $RegistrationName `
      -msAssetTag $msAssetTagName `
      -UsageReportingEnabled: $false
   ```
   Az MS Asset címkéje ( `msAssetTag` ) kötelező az egyéni számlázási modell regisztrálásához, és a termékre van kinyomtatva.
    
   A folyamat 10 – 15 percet vesz igénybe. Ha a parancs befejeződik, megjelenik az üzenet. **A környezet most már regisztrálva van és aktiválva van a megadott paraméterek használatával.**

## <a name="registration-and-activation-for-systems-not-connected-to-the-azure-cloud"></a>Az Azure-felhőhöz nem csatlakoztatott rendszerek regisztrálása és aktiválása 

Ha Azure Stack hub-t egy leválasztott környezetben (internetkapcsolat nélkül) regisztrálja, hajtsa végre a következő lépéseket:

1. Szerezze be a regisztrációs jogkivonatot az Azure Stack hub-környezetből.

2. Kapcsolódjon az Azure-hoz, és regisztráljon az 1. lépésben szereplő regisztrációs jogkivonat használatával. Használjon olyan számítógépet, amely képes csatlakozni az Azure-hoz, és telepítve van a Azure Stack hub PowerShell-je.

3. Aktiválási kulcs beolvasása az Azure-regisztrációból.

4. Aktiválja Azure Stack hub-t az Azure aktiválási kulcsának használatával.

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Regisztrációs jogkivonat beszerzése az Azure Stack hub-környezetből

Szerezze be a regisztrációs jogkivonatot az Azure Stack hub-környezetből. Ezt követően használja ezt a tokent egy olyan számítógépen, amely tud csatlakozni az Azure-hoz, és rendelkezik PowerShell-lel a Azure Stack hub és a Azure Stack hub-eszközök telepítéséhez.

1. A regisztrációs jogkivonat beszerzéséhez futtassa a következő PowerShell-parancsmagokat egy emelt szintű parancssorból:

   ```PowerShell
    $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt" 
    
    $RegistrationToken = Get-AzsRegistrationToken `
      -PrivilegedEndpointCredential $YourCloudAdminCredential `
      -UsageReportingEnabled:$False `
      -PrivilegedEndpoint $YourPrivilegedEndpoint `
      -BillingModel Custom -msAssetTag '<MS Asset tag>' `
      -TokenOutputFilePath $FilePathForRegistrationToken 
   ```

   > [!TIP]  
   > A regisztrációs jogkivonat a által megadott fájlban lett mentve `$FilePathForRegistrationToken` . Igény szerint módosíthatja az elérési utat vagy a fájlnevet.

2. Mentse ezt a regisztrációs jogkivonatot az Azure-ra csatlakoztatott gépen való használatra. A fájlból vagy a szövegből is másolhatja a fájlt `$FilePathForRegistrationToken` .

### <a name="connect-to-azure-and-register"></a>Kapcsolódjon az Azure-hoz, és regisztráljon

Az internethez csatlakozó számítógépen jelentkezzen be a megfelelő Azure PowerShell környezetbe. Ezután hívja meg a **Register-AzsEnvironment**. Az Azure-ban regisztrálni kívánt regisztrációs jogkivonat meghatározása. Ha a Azure Stack hub több példányát regisztrálja ugyanazzal az Azure-előfizetési AZONOSÍTÓval, adjon meg egy egyedi regisztrációs nevet.

A regisztrációs jogkivonat és egy egyedi jogkivonat neve szükséges.

1. Egy rendszergazda jogú PowerShell-parancssorból futtassa a következő parancsmagokat, és győződjön meg róla, hogy a megfelelő előfizetést használja.

    ```powershell  
    Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
    ```

2. Ezután futtassa a következő PowerShell-parancsmagokat:

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

3. Igény szerint a **Get-Content** parancsmaggal egy olyan fájlra is rámutathat, amely tartalmazza a regisztrációs tokent.

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

> [!NOTE]  
> Mentse a regisztrációs erőforrás nevét és a regisztrációs jogkivonatot későbbi használatra.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Aktiválási kulcs beolvasása az Azure regisztrációs erőforrásból

Ezután az Azure-ban a **Register-AzsEnvironment** alatt létrehozott regisztrációs erőforrásból kérje le az aktiválási kulcsot.

Az aktiválási kulcs lekéréséhez futtassa a következő PowerShell-parancsmagokat:

```PowerShell
$RegistrationResourceName = "<unique-registration-name>"
$KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
$ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
```

> [!TIP]  
> Az aktiválási kulcsot a által megadott fájlban menti a rendszer `$KeyOutputFilePath` . Igény szerint módosíthatja az elérési utat vagy a fájlnevet.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Aktiválási erőforrás létrehozása Azure Stack hub-ban

Térjen vissza az Azure Stack hub-környezethez a **Get-AzsActivationKey** által létrehozott aktiválási kulcs fájljával vagy szövegével. Ezután hozzon létre egy aktiválási erőforrást Azure Stack hub-ban az adott aktiválási kulccsal. Az aktiválási erőforrás létrehozásához futtassa a következő PowerShell-parancsmagokat:

```PowerShell
$ActivationKey = "<activation key>"
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

Igény szerint a **Get-Content** parancsmaggal egy olyan fájlra is rámutathat, amely tartalmazza a regisztrációs jogkivonatot:

```PowerShell
$ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

## <a name="verify-azure-stack-hub-registration"></a>Azure Stack hub regisztrációjának ellenőrzése

A **régió kezelése** csempével ellenőrizheti, hogy az Azure stack hub regisztrációja sikeres volt-e. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján érhető el. Az állapot regisztrálható vagy nem regisztrálható. Ha regisztrálva van, akkor az Azure-előfizetés AZONOSÍTÓját is megjeleníti, amelyet az Azure Stack hub regisztrálásához használt a regisztrációs erőforráscsoport és a név használatával.

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra ( `https://adminportal.local.azurestack.external` ).

2. Az irányítópulton válassza a **régió kezelése** lehetőséget.

3. Válassza ki a **Tulajdonságok** elemet. Ez a panel a környezet állapotát és részleteit jeleníti meg. Az állapot **regisztrálható** , **nem regisztrálható** vagy nem **járt le**.

   [![Régió-felügyeleti csempe Azure Stack hub felügyeleti portálon](media/registration-tzl/admin-1-sm.png "Régió-felügyeleti csempe")](media/registration-tzl/admin-1.png#lightbox)

   Ha regisztrálva van, a tulajdonságok a következők:

    - **Regisztrációs előfizetés azonosítója** : az Azure-előfizetési azonosító regisztrálva van, és Azure stack hubhoz van társítva.
    - **Regisztrációs erőforráscsoport** : az Azure stack hub-erőforrásokat tartalmazó társított előfizetésben található Azure-erőforráscsoport.

4. A Azure Portal használatával megtekintheti Azure Stack hub regisztrációs erőforrásait, majd ellenőrizheti, hogy a regisztráció sikeres volt-e. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) az Azure stack hub regisztrálásához használt előfizetéshez társított fiókkal. Válassza a **minden erőforrás** lehetőséget, engedélyezze a **rejtett típusok megjelenítése** jelölőnégyzetet, majd válassza ki a regisztrációs nevet.

5. Ha a regisztráció sikertelen volt, újra kell regisztrálnia a probléma megoldásához [használt előfizetés módosításával](../../operator/azure-stack-registration.md#change-the-subscription-you-use) .

Másik lehetőségként ellenőrizheti, hogy a regisztráció sikeres volt-e a piactér-felügyeleti szolgáltatás használatával. Ha a piactér- **kezelés** panelen megjelenik a piactér-elemek listája, a regisztráció sikeres volt. A leválasztott környezetekben azonban nem láthatók a piactéren a Marketplace-elemek.

> [!NOTE]
> A regisztráció befejezése után a nem regisztrált aktív figyelmeztetés többé nem jelenik meg.

> [!NOTE]
> Az **előfizetés nem támogatja a (z) [InvalidRegistrationToken]: BillingModel "Custom"** hibaüzenetet, amely azt jelzi, hogy a regisztrációhoz használt előfizetés nem lett jóváhagyva Azure stack hub ROBUSZTUS vagy MDC használata esetén. Kérjük, vegye fel a kapcsolatot azshregistration@microsoft.com , hogy jóváhagyja az előfizetést, valamint a termék típusát (Azure stack hub robusztus vagy MDC).

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub adminisztrációs alapjai](azure-stack-manage-basics-tzl.md)  
