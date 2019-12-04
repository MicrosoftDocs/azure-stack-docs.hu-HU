---
title: Azure Stack regisztrálása az Azure-ban
titleSuffix: Azure Stack
description: Ismerje meg, hogyan regisztrálhat Azure Stack integrált rendszereket az Azure-ban, hogy letöltse az Azure Marketplace-elemeket, és hogyan állíthatja be az adatjelentéskészítést.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: mabrigg
ms.reviewer: avishwan
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 1ab0e33d894b1856e1769a0f6780f5d0b9df3474
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780762"
---
# <a name="register-azure-stack-with-azure"></a>Azure Stack regisztrálása az Azure-ban

Regisztrálja Azure Stack az Azure-ban, így letöltheti az Azure Marketplace-elemeket az Azure-ból, és beállíthatja a kereskedelmi adatok jelentéseit a Microsoftnak. Azure Stack regisztrálása után a rendszer a használatot az Azure Commerce szolgáltatásnak jelenti, és a regisztrációhoz használt Azure számlázási előfizetés azonosítója alatt látja.

A cikkben található információk Azure Stack integrált rendszerek regisztrálását ismertetik az Azure-ban. További információ a ASDK az Azure-ban való regisztrálásáról: [Azure stack regisztráció](../asdk/asdk-register.md) a ASDK dokumentációjában.

> [!IMPORTANT]  
> A teljes Azure Stack funkció támogatásához regisztrációra van szükség, beleértve a piactéren található elemeket is. Ha nem regisztrálja magát az utólagos használatú számlázási modell használatakor, Azure Stack licencelési feltételek megsértését fogja használni. Ha többet szeretne megtudni a Azure Stack licencelési modellekről, tekintse meg a [Hogyan vásárolhat lapot](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Előfeltételek

A regisztráció előtt a következő előfeltételek szükségesek:

- Ellenőrizze a hitelesítő adatokat.
- Állítsa be a PowerShell nyelvi módját.
- Telepítse a PowerShellt Azure Stackhoz.
- Töltse le a Azure Stack eszközöket.
- Határozza meg a regisztrációs forgatókönyvet.

### <a name="verify-your-credentials"></a>Hitelesítő adatok ellenőrzése

A Azure Stack az Azure-ban való regisztrálása előtt a következőket kell tennie:

- Az Azure-előfizetéshez tartozó előfizetés-azonosító. A regisztráció csak az EA, a CSP vagy a CSP közös szolgáltatások előfizetéseit támogatja. A CSP-nek el kell döntenie, hogy [CSP-t vagy APSS-előfizetést használ](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)-e.<br><br>Az azonosító beszerzéséhez jelentkezzen be az Azure-ba, kattintson a **minden szolgáltatás**elemre. Ezt követően az **általános** kategóriában válassza az **előfizetések**elemet, kattintson a használni kívánt előfizetésre, és az **Essentials** területen találja az előfizetés-azonosítót. Ajánlott eljárásként használjon külön előfizetéseket üzemi és fejlesztési vagy tesztelési környezetekhez. 

  > [!Note]  
  > A németországi felhőalapú előfizetések jelenleg nem támogatottak.

- Az előfizetés tulajdonosát képező fiók felhasználóneve és jelszava.

- A felhasználói fióknak hozzáféréssel kell rendelkeznie az Azure-előfizetéshez, és engedélyekkel kell rendelkeznie az identitáshoz tartozó alkalmazások és egyszerű szolgáltatások létrehozásához az előfizetéshez társított könyvtárban. Javasoljuk, hogy a minimális jogosultságú felügyelettel regisztrálja Azure Stack az Azure-ban. A regisztrációhoz való hozzáférést korlátozó egyéni szerepkör-definíciók létrehozásával kapcsolatos további információkért lásd: [regisztrációs szerepkör létrehozása a Azure Stackhoz](azure-stack-registration-role.md).

- Regisztrálja a Azure Stack erőforrás-szolgáltatót (a részletekért tekintse meg a következő Azure Stack erőforrás-szolgáltató regisztrálása című szakaszt).

A regisztrációt követően a Azure Active Directory (Azure AD) globális rendszergazdai jogosultsága nem szükséges. Egyes műveletek esetében azonban szükség lehet a globális rendszergazdai hitelesítő adatokra (például egy erőforrás-szolgáltatói telepítő parancsfájlra vagy egy olyan új szolgáltatásra, amely engedélyt kér). Ideiglenesen visszaállíthatja a fiók globális rendszergazdai engedélyeit, vagy használhat egy különálló globális rendszergazdai fiókot, amely az *alapértelmezett szolgáltatói előfizetés*tulajdonosa.

A Azure Stackt regisztráló felhasználó az Azure AD-ben az egyszerű szolgáltatásnév tulajdonosa. Csak a Azure Stack regisztrált felhasználó módosíthatja a Azure Stack regisztrációját. Ha egy nem rendszergazda felhasználó, aki nem tulajdonosa a regisztrációs szolgáltatásnak a Azure Stack regisztrációját vagy újbóli regisztrálását, akkor előfordulhat, hogy egy 403-válaszon keresztül érkeznek. A 403-válasz azt jelzi, hogy a felhasználó nem rendelkezik megfelelő engedélyekkel a művelet végrehajtásához.

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, [létrehozhat egy ingyenes Azure-fiókot itt](https://azure.microsoft.com/free/?b=17.06). Az Azure-előfizetéshez tartozó Azure Stack regisztrációját nem kell fizetnie.

> [!NOTE]
> Ha egynél több Azure Stack van, az ajánlott eljárás az egyes Azure Stackek regisztrálása a saját előfizetésében. Így könnyebben nyomon követheti a használatot.

### <a name="powershell-language-mode"></a>PowerShell nyelvi mód

Azure Stack sikeres regisztrálásához a PowerShell nyelvi módját **FullLanguageMode**értékre kell állítani.  Annak ellenőrzéséhez, hogy az aktuális nyelvi mód a teljes értékre van-e állítva, nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsmagokat:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, hogy a kimenet **FullLanguageMode**ad vissza. Ha a rendszer bármilyen más nyelvi módot ad vissza, a regisztrációt egy másik gépen kell futtatni, vagy a nyelvi módot a folytatás előtt be kell állítani a **FullLanguageMode** értékre.

### <a name="install-powershell-for-azure-stack"></a>A PowerShell telepítése az Azure Stackhez

Az Azure-ban való regisztráláshoz használja a Azure Stack legújabb PowerShell-eszközét.

Ha a legújabb verzió még nincs telepítve, olvassa el a [PowerShell telepítése Azure Stackhoz](azure-stack-powershell-install.md)című témakört.

### <a name="download-the-azure-stack-tools"></a>A Azure Stack-eszközök letöltése

A Azure Stack Tools GitHub-adattár olyan PowerShell-modulokat tartalmaz, amelyek támogatják a Azure Stack funkciókat, beleértve a regisztrációs funkciókat is. A regisztrációs folyamat során importálnia és használnia kell a **RegisterWithAzure. psm1** PowerShell-modult (a Azure stack Tools adattárban található), hogy regisztrálja Azure stack példányát az Azure-ban.

Annak érdekében, hogy a legújabb verziót használja, törölje a Azure Stack eszközök meglévő verzióit, és [töltse le a legújabb verziót a githubról](azure-stack-powershell-download.md) az Azure-ban való regisztrálás előtt.

### <a name="determine-your-registration-scenario"></a>A regisztrációs forgatókönyv meghatározása

Előfordulhat, hogy a Azure Stack üzemelő példánya *csatlakoztatva* van vagy *le van választva*.

- **Csatlakoztatott**  
 A csatlakoztatott eszköz a Azure Stack üzembe helyezését jelenti, hogy az internethez és az Azure-hoz is kapcsolódjon. Azure AD-t vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) kell használnia az identitás-tárolóhoz. A csatlakoztatott üzemelő példányok esetében két számlázási modell közül választhat: utólagos használat vagy kapacitás alapján.
  - [Regisztráljon egy csatlakoztatott Azure stack az Azure-ban az utólagos **használatú** számlázási modell használatával](#register-connected-with-pay-as-you-go-billing).
  - [Egy csatlakoztatott Azure stack regisztrálása az Azure-ban a **kapacitás** számlázási modell használatával](#register-connected-with-capacity-billing).

- **Leválasztott**  
 Az Azure-beli üzembe helyezési lehetőség leválasztásával az internettel való kapcsolat nélkül telepítheti és használhatja Azure Stack. A leválasztott központi telepítés azonban csak egy AD FS Identity Store-ra és a Capacity-alapú számlázási modellre korlátozódik.
  - [Egy leválasztott Azure stack regisztrálása a **Capacity** számlázási modell használatával ](#register-disconnected-with-capacity-billing).

### <a name="determine-a-unique-registration-name-to-use"></a>A használni kívánt egyedi regisztrációs név meghatározása

Azure Stack az Azure-ban való regisztrálásakor egyedi regisztrációs nevet kell megadnia. A Azure Stack-előfizetés Azure-regisztrációval való összekapcsolásának egyszerű módja, ha a Azure Stack **Felhőbeli azonosítóját**használja.

> [!NOTE]
> A Capacity-alapú számlázási modell használatával Azure Stack regisztrációkat módosítani kell az egyedi nevet, ha az éves előfizetések lejárta után újra regisztrálni kell, hacsak nem [törli a lejárt regisztrációt](azure-stack-registration.md#change-the-subscription-you-use) , és újra regisztrálja magát az Azure-ban.

A Azure Stack üzemelő példány Felhőbeli AZONOSÍTÓjának meghatározásához nyissa meg a PowerShellt rendszergazdaként egy olyan számítógépen, amely hozzáfér a Kiemelt végponthoz, futtassa a következő parancsokat, majd jegyezze fel a **CloudID** értéket:

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Az utólagos elszámolású számlázással kapcsolatos regisztráció

Ezekkel a lépésekkel regisztrálhat Azure Stack az Azure-ban az utólagos használatú számlázási modell használatával.

> [!Note]  
> Ezeket a lépéseket olyan számítógépről kell futtatni, amely hozzáfér a privilegizált végponthoz (PEP). A PEP-vel kapcsolatos további információkért lásd: [a privilegizált végpont használata Azure Stackban](azure-stack-privileged-endpoint.md).

A csatlakoztatott környezetek hozzáférhetnek az internethez és az Azure-hoz. Ezekben a környezetekben regisztrálnia kell a Azure Stack erőforrás-szolgáltatót az Azure-ban, majd konfigurálnia kell a számlázási modellt.

1. Ha regisztrálni szeretné a Azure Stack erőforrás-szolgáltatót az Azure-ban, indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd az alábbi paramétereket).

2. Adja hozzá a Azure Stack regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzureRmAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | environmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

   >[!Note]
   > Ha a munkamenet lejár, a jelszó módosult, vagy egyszerűen csak szeretné váltani a fiókokat, az Add-AzureRmAccount használatával történő bejelentkezés előtt futtassa a következő parancsmagot: `Remove-AzureRmAccount-Scope Process`

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. A következő parancs futtatásával regisztrálja az Azure Stack erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához, amelyet a Azure stack eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult a PowerShell használatával:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Ezután ugyanebben a PowerShell-munkamenetben ellenőrizze, hogy be van-e jelentkezve a megfelelő Azure PowerShell környezetbe. Ez a kontextus lenne az Azure-fiók, amelyet korábban a Azure Stack erőforrás-szolgáltató regisztrálásához használt. Futtatandó PowerShell:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | environmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

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

Ezekkel a lépésekkel regisztrálhat Azure Stack az Azure-ban az utólagos használatú számlázási modell használatával.

> [!Note]  
> Ezeket a lépéseket olyan számítógépről kell futtatni, amely hozzáfér a privilegizált végponthoz (PEP). A PEP-vel kapcsolatos további információkért lásd: [a privilegizált végpont használata Azure Stackban](azure-stack-privileged-endpoint.md).

A csatlakoztatott környezetek hozzáférhetnek az internethez és az Azure-hoz. Ezekben a környezetekben regisztrálnia kell a Azure Stack erőforrás-szolgáltatót az Azure-ban, majd konfigurálnia kell a számlázási modellt.

1. Ha regisztrálni szeretné a Azure Stack erőforrás-szolgáltatót az Azure-ban, indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsmagokat a megfelelő Azure-előfizetési típushoz beállított **EnvironmentName** paraméterrel (lásd az alábbi paramétereket).

2. Adja hozzá a Azure Stack regisztrálásához használt Azure-fiókot. A fiók hozzáadásához futtassa az **Add-AzureRmAccount** parancsmagot. A rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására, és előfordulhat, hogy a fiókja konfigurációjától függően kétfaktoros hitelesítést kell használnia.

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | environmentName | Az Azure felhőalapú előfizetési környezet neve. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**vagy kínai Azure-előfizetés használata, **AzureChinaCloud**.  |

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt elem kiválasztásához:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. A következő parancs futtatásával regisztrálja az Azure Stack erőforrás-szolgáltatót az Azure-előfizetésében:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához, amelyet a Azure stack eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult a PowerShell használatával:

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

Ha Azure Stackt regisztrál egy leválasztott környezetben (internetkapcsolat nélkül), akkor a Azure Stack-környezetből kell beszereznie egy regisztrációs jogkivonatot. Ezután használja ezt a tokent egy olyan számítógépen, amely képes csatlakozni az Azure-hoz, és telepítve van a PowerShell Azure Stack.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Regisztrációs jogkivonat beszerzése a Azure Stack-környezetből

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához, amelyet a Azure stack eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult:  

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

Az internethez csatlakozó számítógépen hajtsa végre ugyanezen lépéseket a RegisterWithAzure. psm1 modul importálásához, és jelentkezzen be a megfelelő Azure PowerShell-környezetbe. Ezután hívja meg a Register-AzsEnvironment. Az Azure-ban regisztrálni kívánt regisztrációs jogkivonat meghatározása. Ha egynél több példányt regisztrál Azure Stack ugyanazzal az Azure-előfizetési AZONOSÍTÓval, adjon meg egy egyedi regisztrációs nevet.

A regisztrációs jogkivonat és egy egyedi jogkivonat neve szükséges.

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához, amelyet a Azure stack eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult:  

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

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és navigáljon a **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához, amelyet a Azure stack eszközök letöltésekor hozott létre. Importálja a **RegisterWithAzure. psm1** modult:  

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

### <a name="create-an-activation-resource-in-azure-stack"></a>Aktiválási erőforrás létrehozása Azure Stack

Térjen vissza a Azure Stack-környezetbe a Get-AzsActivationKey által létrehozott aktiválási kulcsból származó fájllal vagy szöveggel. A következő lépésben hozzon létre egy aktiválási erőforrást Azure Stack az aktiválási kulcsot használva. Az aktiválási erőforrás létrehozásához futtassa a következő PowerShell-parancsmagokat:

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Igény szerint a Get-Content parancsmaggal egy olyan fájlra is rámutathat, amely tartalmazza a regisztrációs jogkivonatot:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Azure Stack regisztrációjának ellenőrzése

A **régió kezelése** csempével ellenőrizheti, hogy a Azure stack regisztrációja sikeres volt-e. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján érhető el. Az állapot regisztrálható vagy nem regisztrálható. Ha regisztrálva van, akkor a Azure Stack regisztrálásához használt Azure-előfizetés AZONOSÍTÓját is megjeleníti a regisztrációs erőforráscsoport és a név használatával.

1. Jelentkezzen be a [Azure stack felügyeleti portálra](https://adminportal.local.azurestack.external).

2. Az irányítópulton válassza a **régió kezelése**lehetőséget.

3. Válassza ki a **Tulajdonságok** elemet. Ez a panel a környezet állapotát és részleteit jeleníti meg. Az állapot **regisztrálható**, **nem regisztrálható**vagy nem **járt le**.

    [![Régió-felügyeleti csempe Azure Stack felügyeleti portálon](media/azure-stack-registration/admin1sm.png "Régió-felügyeleti csempe")](media/azure-stack-registration/admin1.png#lightbox)

    Ha regisztrálva van, a tulajdonságok a következők:
    
    - **Regisztrációs előfizetés azonosítója**: az Azure-előfizetési azonosító regisztrálva van, és a Azure Stackhoz van társítva.
    - **Regisztrációs erőforráscsoport**: az Azure-erőforráscsoport a Azure stack erőforrásokat tartalmazó társított előfizetésben.

4. A Azure Portal használatával megtekintheti Azure Stack regisztrációs erőforrásait, majd ellenőrizheti, hogy a regisztráció sikeres volt-e. Jelentkezzen be a [Azure Portal](https://portal.azure.com) a Azure stack regisztrálásához használt előfizetéshez társított fiókkal. Válassza a **minden erőforrás**lehetőséget, engedélyezze a **rejtett típusok megjelenítése** jelölőnégyzetet, és válassza ki a regisztrációs nevet.

5. Ha a regisztráció nem sikerült, a probléma megoldásához kövesse az [itt ismertetett lépéseket](#change-the-subscription-you-use) .  

Másik lehetőségként ellenőrizheti, hogy a regisztráció sikeres volt-e a piactér-felügyeleti szolgáltatás használatával. Ha a piactér-kezelés panelen megjelenik a piactér-elemek listája, a regisztráció sikeres volt. A leválasztott környezetekben azonban nem láthatók a piactéren a Marketplace-elemek.

> [!NOTE]
> A regisztráció befejezése után a nem regisztrált aktív figyelmeztetés többé nem jelenik meg. Azure Stack a 1904 előtti kiadásokban a leválasztott forgatókönyvekben egy üzenet jelenik meg a piactér felügyeletében, amely arra kéri, hogy regisztrálja és aktiválja a Azure Stack, még akkor is, ha sikeresen regisztrálta magát. Ez az üzenet nem jelenik meg a 1904-es és újabb verziókban.

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

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Az aktiválási erőforrás eltávolítása Azure Stack

Először el kell távolítania az aktiválási erőforrást Azure Stack, majd a regisztrációs erőforrást az Azure-ban.  

Az aktiválási erőforrás Azure Stackban való eltávolításához futtassa a következő PowerShell-parancsmagokat a Azure Stack környezetben:  

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

Most teljesen megszüntette a regisztrációt egy leválasztott forgatókönyvben, és meg kell ismételnie a Azure Stack-környezet leválasztott forgatókönyvben való regisztrálásának lépéseit.

### <a name="disable-or-enable-usage-reporting"></a>Használati jelentéskészítés letiltása vagy engedélyezése

A kapacitás-számlázási modellt használó Azure Stack környezetekben a **set-AzsRegistration** vagy a **Get-AzsRegistrationToken** parancsmagok használatával kapcsolja ki a használati jelentéskészítést a **UsageReportingEnabled** paraméterrel. Alapértelmezés szerint Azure Stack a jelentések használati metrikáit. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést.

#### <a name="with-a-connected-azure-stack"></a>Csatlakoztatott Azure Stack

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Leválasztott Azure Stack

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

A **set-AzsRegistration** használatával regisztrálhat Azure stack az Azure-ban, és engedélyezheti vagy letilthatja az elemek ajánlatát a piactéren és a használati jelentésekben.

A parancsmag futtatásához a következők szükségesek:

- Bármilyen típusú globális Azure-előfizetés.
- Ahhoz, hogy bejelentkezzen a Azure PowerShellba egy olyan fiókkal, amely az adott előfizetéshez tartozó tulajdonos vagy közreműködő.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Paraméter | Type (Típus) | Leírás |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | A [rendszerjogosultságú végpont eléréséhez](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)használt hitelesítő adatok. A Felhasználónév formátuma **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Sztring | Előre konfigurált távoli PowerShell-konzol, amely olyan képességeket biztosít, mint a naplók gyűjtése és az egyéb utólagos üzembe helyezési feladatok. További tudnivalókat a [privilegizált végpont használata](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) című cikkben találhat. |
| AzureContext | PSObject |  |
| ResourceGroupName | Sztring |  |
| ResourceGroupLocation | Sztring |  |
| BillingModel | Sztring | Az előfizetés által használt számlázási modell. A paraméter megengedett értékei a következők: kapacitás, PayAsYouUse és fejlesztés. |
| MarketplaceSyndicationEnabled | Igaz/hamis | Meghatározza, hogy a piactér-felügyeleti funkció elérhető-e a portálon. Állítsa igaz értékre, ha az internetkapcsolattal van regisztrálva. Hamis értékre állítva, ha a regisztráció a leválasztott környezetekben megtörténjen. A leválasztott regisztrációk esetében az [Offline hírszolgáltatási eszköz](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) használható a Piactéri elemek letöltésére. |
| UsageReportingEnabled | Igaz/hamis | Alapértelmezés szerint Azure Stack a jelentések használati metrikáit. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést. A paraméter megengedett értékei a következők: true, false. |
| AgreementNumber | Sztring |  |
| RegistrationName | Sztring | Adja meg a regisztráció egyedi nevét, ha a regisztrációs parancsfájlt több példányon futtatja Azure Stack ugyanazzal az Azure-előfizetési AZONOSÍTÓval. A paraméter alapértelmezett értéke **AzureStackRegistration**. Ha azonban ugyanazt a nevet használja a Azure Stack több példányán, a parancsfájl meghiúsul. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

A Get-AzsRegistrationToken regisztrációs jogkivonatot hoz létre a bemeneti paraméterekből.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Paraméter | Type (Típus) | Leírás |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | A [rendszerjogosultságú végpont eléréséhez](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)használt hitelesítő adatok. A Felhasználónév formátuma **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Sztring |  Előre konfigurált távoli PowerShell-konzol, amely olyan képességeket biztosít, mint a naplók gyűjtése és az egyéb utólagos üzembe helyezési feladatok. További tudnivalókat a [privilegizált végpont használata](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) című cikkben találhat. |
| AzureContext | PSObject |  |
| ResourceGroupName | Sztring |  |
| ResourceGroupLocation | Sztring |  |
| BillingModel | Sztring | Az előfizetés által használt számlázási modell. A paraméter megengedett értékei a következők: kapacitás, PayAsYouUse és fejlesztés. |
| MarketplaceSyndicationEnabled | Igaz/hamis |  |
| UsageReportingEnabled | Igaz/hamis | Alapértelmezés szerint Azure Stack a jelentések használati metrikáit. A kapacitást használó vagy leválasztott környezetet támogató operátoroknak ki kell kapcsolniuk a használati jelentéskészítést. A paraméter megengedett értékei a következők: true, false. |
| AgreementNumber | Sztring |  |

## <a name="registration-failures"></a>Regisztrációs hibák

Előfordulhat, hogy az alábbi hibák egyike jelenik meg a Azure Stack regisztrálására tett kísérlet során:

- Nem sikerült beolvasni a $hostName kötelező hardver-információit. Tekintse át a fizikai gazdagépet és a kapcsolatot, majd próbálja meg újra a regisztrációt.

- Nem lehet csatlakozni a $hostNamehoz a hardver adatainak beszerzéséhez – ellenőrizze a fizikai gazdagépet és a kapcsolatot, majd próbálkozzon újra a regisztrációval.

> Ok: ez általában azért van, mert a gazdagépekről próbáljuk ki a hardver részleteit, például az UUID-t, a BIOS-t és a CPU-t, hogy megkísérelje az aktiválást, és nem tudta elérni a fizikai gazdagéphez való kapcsolódás lehetőségét.

Amikor megpróbál hozzáférni a piactér-felügyelethez, hiba történik a termékek szindikátusba való kipróbálásakor. 
> Ok: ez általában akkor fordul elő, ha Azure Stack nem fér hozzá a regisztrációs erőforráshoz. Ennek egyik gyakori oka, hogy amikor egy Azure-előfizetés címtár-bérlője megváltozik, visszaállítja a regisztrációt. Ha módosította az előfizetés címtár-bérlőjét, nem férhet hozzá a Azure Stack Marketplace-hez vagy a jelentésekhez. A probléma megoldásához újra kell regisztrálnia a problémát.

A piactér-felügyelet továbbra is kéri a Azure Stack regisztrálását és aktiválását akkor is, ha már regisztrálta a bélyegzőt a leválasztott folyamat használatával.
> Ok: ez egy ismert probléma a leválasztott környezetek esetében. A regisztráció állapotát a következő [lépésekkel](azure-stack-registration.md#verify-azure-stack-registration)ellenőrizheti. A piactér-kezelés használatához [az offline eszközt](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)kell használnia.

## <a name="next-steps"></a>Következő lépések

[Marketplace-elemek letöltése az Azure-ból](azure-stack-download-azure-marketplace-item.md)
