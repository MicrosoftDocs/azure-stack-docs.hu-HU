---
title: Azure-regisztráció az Azure Stack integrált rendszerek |} A Microsoft Docs
description: A több csomópontos, az Azure Stack Azure-kapcsolattal rendelkező üzemelő példányok az Azure-regisztráció folyamatát ismerteti.
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
ms.date: 04/16/2019
ms.author: mabrigg
ms.reviewer: brbartle
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: b70cd30653b8b324ae4d11a4a3e8aafe47d9a179
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618017"
---
# <a name="register-azure-stack-with-azure"></a>Regisztráljon az Azure Stack az Azure-ral

Az Azure Stack az Azure-ral való regisztrálásával letölthet Azure Marketplace-termékeket az Azure-ból, valamint beállíthatja a Commerce-adatok a Microsoftnak való visszaküldését. Miután regisztrálta Azure Stack, az Azure kereskedelmi jelentett használati, és láthatja az Azure számlázási használt előfizetés-Azonosítót a regisztráció alatt.

Ez a cikk ismerteti az Azure-ban való regisztrálásáról Azure Stack integrált rendszerek. A ASDK regisztrálása az Azure-ral kapcsolatos információkért lásd: [Azure Stack-regisztráció](../asdk/asdk-register.md) ASDK dokumentációjában.

> [!IMPORTANT]  
> Regisztráció szükséges teljes körű Azure Stack funkciót, beleértve az ajánlatot a piactéren elemek. Emellett fogja szabályzatsértéséről, a licencelési feltételeket, ha nem regisztrálja a használatalapú mint-akkor-használható számlázási modell használata esetén az Azure Stack. Azure Stack-licencelési modell kapcsolatos további információkért lásd: a [lap megvásárlása](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt regisztrálnia kell a következő helyen:

 - A hitelesítő adatok ellenőrzéséhez
 - A PowerShell nyelvmód beállítása
 - Az Azure Stack PowerShell telepítése
 - Az Azure Stack-eszközök letöltése
 - A regisztrációs forgatókönyv meghatározása

### <a name="verify-your-credentials"></a>A hitelesítő adatok ellenőrzéséhez

Mielőtt regisztrálná az Azure Stack az Azure-ral, a következőkkel kell rendelkeznie:

- Azure-előfizetés előfizetés-azonosítója. Csak az EA, CSP vagy a CSP megosztott szolgáltatások előfizetéseket támogatja a regisztrációhoz. CSP-k kell eldöntenie, hogy e [használja egy CSP vagy APSS előfizetését](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Az azonosító lekéréséhez jelentkezzen be az Azure-ba, kattintson a **minden szolgáltatás**. Ezt követően a **általános** kategória, válassza **előfizetések**, kattintson arra az előfizetésre szeretné használni, majd a **Essentials** is megtalálhatja, hogy az előfizetés-azonosító.

  > [!Note]  
  > A Németországi felhőalapú előfizetések jelenleg nem támogatottak.

- A felhasználónév és jelszó, amely az előfizetés tulajdonosa.

- A felhasználói fiók rendelkezik hozzáféréssel az Azure-előfizetés, és rendelkezik a címtárban, az adott előfizetéshez tartozó identitást használó alkalmazások és az egyszerű szolgáltatások létrehozásához szükséges engedélyek kell. Azt javasoljuk, hogy regisztrálja az Azure Stack az Azure-ral jogosultságon alapuló felügyeleti. Egy egyéni szerepkör-definíció, amely korlátozza a hozzáférést az előfizetés regisztrációs létrehozásáról további információkért lásd: [regisztrációs szerepkör létrehozása az Azure Stack](azure-stack-registration-role.md).

- Az Azure Stack erőforrás-szolgáltató regisztrálva (lásd a következő regisztrálása az Azure Stack erőforrás-szolgáltató részletekért).

A regisztrációt követően az Azure Active Directory globális rendszergazdának, nem szükséges. Egyes műveletek azonban szükség lehet a globális rendszergazdai hitelesítő adatok. Például egy erőforrás-szolgáltató szoftvertelepítő parancsfájl vagy egy új szolgáltatást igénylő engedélyt kell adni. Ideiglenesen érvényességének visszaállítása a fiók globális rendszergazdai jogosultságokkal, vagy használjon egy külön globális rendszergazdai fiókkal, amely a tulajdonosa a *szolgáltatói előfizetés alapértelmezett*.

A felhasználó, aki regisztrál az Azure Stack az Azure Active Directory egyszerű szolgáltatás tulajdonosa. Csak a felhasználó, aki regisztrált az Azure Stack az Azure Stack-regisztráció módosíthatja. Ha egy nem rendszergazdai felhasználó, aki nem tulajdonosa, a regisztrációs szolgáltatás egyszerű próbál meg regisztrálni, illetve regisztrálja újra az Azure Stack, azok tapasztalhat a 403-as választ. A 403-as válasz azt jelzi, hogy a felhasználó nem rendelkezik megfelelő engedélyekkel a művelet végrehajtásához.

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Az Azure-előfizetése ingyenes regisztrálása az Azure Stack tekintetében.

> [!NOTE]
> Ha egynél több Azure Stack, ajánlott eljárás az egyes Azure Stack saját előfizetését regisztrálja. Ez megkönnyíti, hogy a használat nyomon követése.

### <a name="powershell-language-mode"></a>PowerShell nyelvmód

Sikerült regisztrálni az Azure Stack, a PowerShell nyelv módba kell állítani **FullLanguageMode**.  Annak ellenőrzéséhez, hogy a jelenlegi nyelvmód beállítása teljes nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsmagokat:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, a kimenetet visszaadja **FullLanguageMode**. Ha bármilyen más nyelvmód adja vissza, regisztrációs szükség van egy másik gépen futtatására, vagy a nyelvmód kell beállítani **FullLanguageMode** a folytatás előtt.

### <a name="install-powershell-for-azure-stack"></a>Az Azure Stack PowerShell telepítése

A PowerShell legújabb verzióját az Azure Stack segítségével regisztrálja az Azure-ral.

Ha még nincs telepítve a legújabb verziót, tekintse meg [PowerShell telepítése az Azure Stack](azure-stack-powershell-install.md).

### <a name="download-the-azure-stack-tools"></a>Az Azure Stack-eszközök letöltése

Az Azure Stack eszközök GitHub-adattár tartalmaz, amelyek támogatják az Azure Stack funkció; PowerShell-modulok beleértve a regisztrációs funkciót. A regisztráció során kell importálni és használni a **RegisterWithAzure.psm1** PowerShell-modul az Azure Stack eszközök regisztrálása az Azure Stack-példány az Azure-ral adattárában található.

Annak érdekében, hogy a legújabb verzióját használja, törölnie kell minden meglévő verziói az Azure Stack-eszközök és [legújabb verziójának letöltése a Githubról](azure-stack-powershell-download.md) mielőtt regisztrálná az Azure-ral.

### <a name="determine-your-registration-scenario"></a>A regisztrációs forgatókönyv meghatározása

Lehet, hogy az Azure Stack üzemelő példányához *csatlakoztatott* vagy *leválasztott*.

 - **Csatlakoztatva**  
 A kapcsolat azt jelenti, hogy telepítette az Azure Stack, hogy képes csatlakozni az internethez, és az Azure-bA. Az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) számára az ügyfélidentitás-tárolóval, vagy rendelkezik. Egy csatlakoztatott üzembe helyezéssel két számlázási modellek közül választhat: használatalapú mint-akkor-használható, vagy kapacitás-alapú.
    - [A csatlakoztatott Azure Stack regisztrálása az Azure-ban a **használatalapú mint-akkor-használható** számlázási modell](#register-connected-with-pay-as-you-go-billing)
    - [A csatlakoztatott Azure Stack regisztrálása az Azure-ban a **kapacitás** számlázási modell](#register-connected-with-capacity-billing)

 - **Leválasztva**  
 A leválasztott az Azure üzembe helyezési lehetőséget, üzembe helyezése és használata az Azure Stack az internethez való csatlakozás nélkül. Azonban egy kapcsolat nélküli telepítés pedig csak az AD FS ügyfélidentitás-tárolóval, és a kapacitás-alapú számlázási modell.
    - [A leválasztott Azure Stack használatával regisztrálja a **kapacitás** számlázási modell ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Határozza meg egy egyedi regisztrációs neve 
Ha az Azure Stack regisztrálja az Azure-ral, meg kell adnia egy egyedi regisztrációs nevét. Az Azure Stack-előfizetés társítása egy Azure-regisztráció egyszerűen az, hogy az Azure Stack használata **Felhőazonosító**. 

> [!NOTE]
> A kapacitás-alapú számlázási modell segítségével az Azure Stack-regisztrációk kell módosítani az egyedi nevet, ha a regisztrálása után e éves előfizetés lejár, kivéve, ha Ön [törli a lejárt regisztrációs](azure-stack-registration.md#change-the-subscription-you-use) , és regisztrálja újra Azure-t.

Annak megállapításához, a felhő-azonosító az Azure Stack üzembe helyezéshez, nyissa meg a Powershellt olyan számítógépre, amelyen a kiemelt végponthoz hozzáférhetnek, futtassa a következő parancsokat rendszergazdaként, és jegyezze fel a **CloudID** érték: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Regisztráció a használatalapú számlázás csatlakoztatva

Ezeket a lépéseket segítségével regisztrálja az Azure Stack az Azure-ral a használatalapú mint-akkor-használható számlázási modell használatával.

> [!Note]  
> Ezek a lépések egy számítógépről, amely hozzáfér a kiemelt végponthoz (EGP) kell futnia. További információk az EGP: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).

Csatlakoztatott környezetek hozzáférhet az interneten és az Azure. Ilyen környezetben kell az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-ral, és állítsa be a számlázási modellt.

1. Az Azure-ral az Azure Stack erőforrás-szolgáltató regisztrálásához indítsa el a PowerShell ISE-t rendszergazdaként, és a következő PowerShell-parancsmag használatával a **EnvironmentName** (lásd a megfelelő Azure előfizetés-típus paraméter beállítása Paraméterek).

2. Adja hozzá az Azure-fiók, amely az Azure Stack regisztrálhat. A fiók hozzáadásához futtassa a **Add-AzureRmAccount** parancsmagot. Az Azure-fiók hitelesítő adatainak megadását kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2 többtényezős hitelesítés használatára.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure-felhő előfizetés környezet neve. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy a China Azure-előfizetéssel, használatakor **AzureChinaCloud**.  |

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza ki a:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Futtassa a következő parancsot az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-előfizetésben:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** jön létre, amikor az Azure Stack eszközök letöltött könyvtár. Importálás a **RegisterWithAzure.psm1** a modul PowerShell-lel:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Ezután ugyanazon PowerShell-munkamenetben, győződjön meg arról, a megfelelő Azure PowerShell-környezet van bejelentkezve. Ez az Azure-fiók, amellyel korábban az Azure Stack erőforrás-szolgáltató regisztrálásához. PowerShell futtatásához:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure-felhő előfizetés környezet neve. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy a China Azure-előfizetéssel, használatakor **AzureChinaCloud**.  |

7. Az ugyanazon PowerShell-munkamenetben futtassa a **Set-AzsRegistration** parancsmagot. PowerShell futtatásához:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   A Set-AzsRegistration parancsmagról további információkért lásd: [Alkalmazásregisztráció-referencia](#registration-reference).

   A folyamat között 10 – 15 percet vesz igénybe. Az üzenet jelenik meg a parancs befejeződésekor **"a környezet már regisztrálva van, és aktiválta a megadott paraméterek használatával."**

## <a name="register-connected-with-capacity-billing"></a>A kapacitás számlázással csatlakoztatott regisztrálása

Ezeket a lépéseket segítségével regisztrálja az Azure Stack az Azure-ral a használatalapú mint-akkor-használható számlázási modell használatával.

> [!Note]  
> Ezek a lépések egy számítógépről, amely hozzáfér a kiemelt végponthoz (EGP) kell futnia. További információk az EGP: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).

Csatlakoztatott környezetek hozzáférhet az interneten és az Azure. Ilyen környezetben kell az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-ral, és állítsa be a számlázási modellt.

1. Az Azure-ral az Azure Stack erőforrás-szolgáltató regisztrálásához indítsa el a PowerShell ISE-t rendszergazdaként, és a következő PowerShell-parancsmag használatával a **EnvironmentName** (lásd a megfelelő Azure előfizetés-típus paraméter beállítása Paraméterek).

2. Adja hozzá az Azure-fiók, amely az Azure Stack regisztrálhat. A fiók hozzáadásához futtassa a **Add-AzureRmAccount** parancsmagot. Az Azure-fiók hitelesítő adatainak megadását kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2 többtényezős hitelesítés használatára.

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure-felhő előfizetés környezet neve. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy a China Azure-előfizetéssel, használatakor **AzureChinaCloud**.  |

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza ki a:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Futtassa a következő parancsot az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-előfizetésben:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** jön létre, amikor az Azure Stack eszközök letöltött könyvtár. Importálás a **RegisterWithAzure.psm1** a modul PowerShell-lel:

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
   > Használatra vonatkozó jelentések készítéséhez a UsageReportingEnabled paraméterrel letilthatja a **Set-AzsRegistration** parancsmag a paraméter false értékre állításával. 
   
   A Set-AzsRegistration parancsmagról további információkért lásd: [Alkalmazásregisztráció-referencia](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>A kapacitás számlázással leválasztott regisztrálása

Ha regisztrál az Azure Stack kapcsolat nélküli környezetben (nincs internetkapcsolat), szeretné-e szerezze be egy regisztrációs tokent az Azure Stack-környezet, majd ezt a jogkivonatot egy számítógépen, amely képes csatlakozni az Azure és az Azure Stack PowerShell rendelkezik telepítve van.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Szerezze be egy regisztrációs tokent az Azure Stack-környezet

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** jön létre, amikor az Azure Stack eszközök letöltött könyvtár. Importálás a **RegisterWithAzure.psm1** modul:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. A regisztrációs jogkivonatot, futtassa a következő PowerShell-parancsmagokat:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   A Get-AzsRegistrationToken parancsmagról további információkért lásd: [Alkalmazásregisztráció-referencia](#registration-reference).

   > [!Tip]  
   > A regisztrációs jogkivonatot a rendszer menti a fájlt a megadott *$FilePathForRegistrationToken*. A fájl elérési útja vagy fájlnév szabadon módosíthatja.

3. A regisztrációs jogkivonatot az Azure-beli használatra mentse géphez csatlakoztatva. $FilePathForRegistrationToken átmásolhatja a fájlt vagy a szöveget.

### <a name="connect-to-azure-and-register"></a>Csatlakozás az Azure és a regisztráció

Azon a számítógépen, amely az internethez csatlakozik hajtsa végre a RegisterWithAzure.psm1 modul importálása, és jelentkezzen be a megfelelő Azure Powershell környezetben ugyanazokat a lépéseket. Majd hívja a Register-AzsEnvironment. Adja meg a regisztrációs jogkivonatot, regisztrálni az Azure-ral. Ha regisztrál az Azure Stack segítségével az ugyanazon Azure-előfizetési azonosító egynél több példányát, adjon meg egy egyedi regisztrációs nevet. Futtassa a következő parancsmagot:

  ```powershell  
  $RegistrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

A Get-tartalom parancsmag segítségével igény szerint, a regisztrációs jogkivonatot tartalmazó fájlra mutat:

  ```powershell  
  $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > A regisztrációs erőforrás neve és a regisztrációs jogkivonatot, a későbbiekben takaríthat meg.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Azure-regisztráció erőforrás egy aktiválási kulcs lekérése

Ezt követően kell az aktiválási kulcs lekérése a Register-AzsEnvironment során az Azure-ban létrehozott regisztrációs erőforrás.

Az aktiválási kulcs lekéréséhez futtassa a következő PowerShell-parancsmagokat:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Az aktiválási kulcsot a rendszer menti a megadott fájl *$KeyOutputFilePath*. A fájl elérési útja vagy fájlnév szabadon módosíthatja.

### <a name="create-an-activation-resource-in-azure-stack"></a>Hozzon létre egy aktiválási erőforrást az Azure Stackben

Térjen vissza az Azure Stack-környezet a fájl vagy a szöveget a Get-AzsActivationKey alapján létrehozott aktiválási kulcs. Ezután hozzon létre egy aktiválási erőforrás aktiválási kulcsot használ az Azure Stack. Hozzon létre egy aktiválási erőforrást, futtassa a következő PowerShell-parancsmagokat: 

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

A Get-tartalom parancsmag segítségével igény szerint, a regisztrációs jogkivonatot tartalmazó fájlra mutat:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Azure Stack-regisztráció ellenőrzése

Használhatja a **régiók kezelése** csempére kattintva ellenőrizze, hogy az Azure Stack-regisztráció sikeres volt-e. Ez a csempe nem érhető el az alapértelmezett irányítópult, a felügyeleti portálon. Az állapot regisztrálva, vagy nincs regisztrálva. Ha regisztrált, azt is bemutatja az Azure előfizetés-Azonosítót regisztrálni az Azure Stack neve mellett a regisztrációs erőforráscsoport használt.

1. Jelentkezzen be a [Azure Stack felügyeleti portálon](https://adminportal.local.azurestack.external).

2. Az irányítópulton, válassza ki a **régiók kezelése**.

3. Válassza ki **tulajdonságok**. Ezen a panelen látható, az állapot és a környezet sajátosságait. Az állapot lehet **regisztrált**, **nincs regisztrálva**, vagy **lejárt**.

    [![Régió felügyeleti csempe](media/azure-stack-registration/admin1sm.png "régió felügyeleti csempe")](media/azure-stack-registration/admin1.png#lightbox)

    Ha regisztrált, a tulajdonságok a következők:
    
    - **Előfizetés-azonosító regisztrációs**: A regisztrált és az Azure Stackhez kapcsolódó Azure-előfizetés azonosítója
    - **Regisztrációs erőforráscsoport**: Az Azure-erőforráscsoportot az Azure Stack erőforrásokat tartalmazó tartozó előfizetésben.

4. Az Azure portal segítségével megtekintheti az Azure Stack alkalmazásregisztrációk. Jelentkezzen be az Azure Portalra egy fiók az előfizetéshez tartozó regisztráció az Azure Stack használatával. Váltson Azure Stack társított tenant.
5. Navigáljon a **Azure Active Directory > alkalmazásregisztrációk > minden alkalmazás megtekintése**.

    ![Alkalmazásregisztrációk](media/azure-stack-registration/app-registrations.png)

    Az Azure Stack alkalmazásregisztrációk van fűzve előtagként **Azure Stack**.

Azt is megteheti ellenőrizheti, ha a regisztráció sikeres volt a Marketplace-en a felügyeleti szolgáltatás. Ha a Marketplace-en felügyeleti panelen a marketplace-elemek listáját látja, a regisztráció sikeres volt. Azonban a leválasztott környezetben lévő nem láthatja a marketplace-elemek a Marketplace-kezelés.

> [!NOTE]
> Regisztráció befejezése után az aktív figyelmeztetés nem regisztrálásához már nem jelenik meg. A kapcsolat nélküli esetekben használható akkor megjelenik egy üzenet, a Marketplace-kezelés kéri, hogy regisztráljon, és aktiválja az Azure Stack, még akkor is, ha sikeresen regisztrálta.

## <a name="renew-or-change-registration"></a>Újítsa meg vagy módosítsa a regisztráció

### <a name="renew-or-change-registration-in-connected-environments"></a>Újítsa meg vagy módosítsa a csatlakoztatott környezetekben regisztrációs

Frissítés vagy újítania regisztrációját a következő körülmények között szüksége:

- Miután kapacitásalapú éves előfizetés megújítása.
- Ha módosítja a számlázási modellt.
- Módosítások (csomópont hozzáadása/eltávolítása) Ha a kapacitás-alapú számlázáshoz méretezése.

#### <a name="change-the-subscription-you-use"></a>Az előfizetést használ

Ha szeretné az előfizetést használ, előbb futtatnia kell a **Remove-AzsRegistration** parancsmagot, majd győződjön meg arról, a megfelelő Azure PowerShell-környezet van bejelentkezve, és végül futtassa **Set-AzsRegistration**  minden módosított paraméterekkel, többek között `<billing model>`:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Módosítsa a számlázási modellt vagy a szolgáltatásokkal

Ha szeretné módosítani a számlázási modellt vagy a megfelelő telepítési szolgáltatásokkal, meghívhatja a regisztrációs függvény, amely az új értékeinek megadására. Nem kell először távolítsa el a jelenlegi regisztrációs:

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Újítsa meg vagy módosítsa a kapcsolat nélküli környezetekben regisztrációs

Frissítés vagy újítania regisztrációját a következő körülmények között szüksége:

- Miután kapacitásalapú éves előfizetés megújítása.
- Ha módosítja a számlázási modellt.
- Módosítások (csomópont hozzáadása/eltávolítása) Ha a kapacitás-alapú számlázáshoz méretezése.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Az aktiválási erőforrás eltávolítása az Azure Stackben

Először távolítsa el az aktiválási erőforrást az Azure Stack, és a regisztrációs erőforrás az Azure-ban.  

Az Azure Stack az aktiválási erőforrás eltávolításához futtassa a következő PowerShell-parancsmagok az Azure Stack környezettel:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Ezután távolítsa el a regisztrációs erőforrást az Azure-ban, hogy ellenőrizze, hogy a csatlakoztatott Azure-beli számítógép, jelentkezzen be a megfelelő Azure PowerShell-környezetben, és a megfelelő PowerShell-parancsmagok futtatásához az alább ismertetett.

A regisztrációs jogkivonatot, használja az erőforrás létrehozásához is használhatja:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Vagy használhatja a regisztrációs név:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Regisztrálja újra a leválasztott lépésekkel

A leválasztott forgatókönyvek esetében már rendelkezik teljesen regisztrációját, és meg kell ismételnie a lépéseket az Azure Stack-környezet regisztrálásához a leválasztott forgatókönyvek esetében.

### <a name="disable-or-enable-usage-reporting"></a>Használati jelentések engedélyezése vagy letiltása

Azure Stack-környezetekben, amelyek egy kapacitás-számlázási modell, kapcsolja ki a jelentéskészítés a használati a **UsageReportingEnabled** paraméter használatával a **Set-AzsRegistration** vagy a  **Get-AzsRegistrationToken** parancsmagok. Az Azure Stack használati metrikai jelentések alapértelmezés szerint. Kapacitás használt vagy leválasztott környezetben támogató kezelők kell kikapcsolni a használati jelentések készítése.

#### <a name="with-a-connected-azure-stack"></a>A csatlakoztatott Azure Stack segítségével

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Egy kapcsolat nélküli Azure Stack segítségével

1. Ha módosítani szeretné a regisztrációs jogkivonatot, futtassa a következő PowerShell-parancsmagokat:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > A regisztrációs jogkivonatot a rendszer menti a fájlt a megadott *$FilePathForRegistrationToken*. A fájl elérési útja vagy fájlnév szabadon módosíthatja.

2. A regisztrációs jogkivonatot az Azure-beli használatra mentse géphez csatlakoztatva. $FilePathForRegistrationToken átmásolhatja a fájlt vagy a szöveget.

## <a name="move-a-registration-resource"></a>Regisztrációs erőforrás áthelyezése
Regisztrációs erőforrás ugyanahhoz az előfizetéshez tartozó erőforráscsoportok közötti áthelyezése **van** minden környezet esetében támogatott. Azonban regisztrációs erőforrások áthelyezése előfizetések között csak a támogatott CSP-k amikor mindkét előfizetés oldja fel a ugyanezzel az azonosítóval Partner. Erőforrások áthelyezése új erőforráscsoport kapcsolatos további információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Alkalmazásregisztráció-referencia

### <a name="set-azsregistration"></a>Set-AzsRegistration

Set-AzsRegistration segítségével regisztrálja az Azure Stack az Azure-ral és engedélyezése vagy letiltása az ajánlatot a piactéren, és használati jelentések készítése elemek.

A parancsmag futtatásához szüksége:
- Globális Azure-előfizetés bármilyen típusú.
- Akkor is kell bejelentkeznie az Azure PowerShell egy olyan fiókkal, amely tulajdonosi vagy közreműködői előfizetéshez.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Paraméter | Típus | Leírás |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | A használt hitelesítő adatok [az emelt szintű végpontot](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). A felhasználónevet a következő formátumban kell **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String | Egy előre konfigurált távoli PowerShell-konzolt, amely biztosít, mint például a naplógyűjtés és egyéb post telepítési feladatokat. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) cikk. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | A számlázási modell, amely az előfizetés használja. Ez a paraméter megengedett értékei a következők: Kapacitás, PayAsYouUse és fejlesztésére. |
| MarketplaceSyndicationEnabled | Igaz/hamis | Meghatározza, hogy-e a Marketplace-en felügyeleti funkció érhető el a portálon. Állítsa az igaz értékre, ha az internetkapcsolattal rendelkező regisztrálása. "False" értékűre, ha regisztrálja az kapcsolat nélküli környezetekben is. Kapcsolat nélküli regisztrációkhoz a [offline szindikálási eszköz](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) letöltése a marketplace-elemek is használható. |
| UsageReportingEnabled | Igaz/hamis | Az Azure Stack használati metrikai jelentések alapértelmezés szerint. Kapacitás használt vagy leválasztott környezetben támogató kezelők kell kikapcsolni a használati jelentések készítése. Ez a paraméter megengedett értékei a következők: IGAZ, hamis. |
| AgreementNumber | String |  |
| RegistrationName | String | Állítsa be egy egyedi nevet a regisztráció, ha futtatja a regisztrációs szkriptet az Azure Stack több példányának használatával az ugyanabban az Azure előfizetés-azonosító. A paraméter alapértelmezett értéke van **AzureStackRegistration**. Azonban ha ugyanazzal a névvel egynél több Azure Stack-példányt használ, a parancsfájl futtatása sikertelen. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken egy regisztrációs jogkivonatot hoz létre a bemeneti paraméterek.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Paraméter | Típus | Leírás |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | A használt hitelesítő adatok [az emelt szintű végpontot](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). A felhasználónevet a következő formátumban kell **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String |  Egy előre konfigurált távoli PowerShell-konzolt, amely biztosít, mint például a naplógyűjtés és egyéb post telepítési feladatokat. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) cikk. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | A számlázási modell, amely az előfizetés használja. Ez a paraméter megengedett értékei a következők: Kapacitás, PayAsYouUse és fejlesztésére. |
| MarketplaceSyndicationEnabled | Igaz/hamis |  |
| UsageReportingEnabled | Igaz/hamis | Az Azure Stack használati metrikai jelentések alapértelmezés szerint. Kapacitás használt vagy leválasztott környezetben támogató kezelők kell kikapcsolni a használati jelentések készítése. Ez a paraméter megengedett értékei a következők: IGAZ, hamis. |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>Regisztrációs hibák

Az alábbi hibák egyike az Azure Stack-regisztráció közben jelenhetnek meg:
1. Nem sikerült beolvasni $hostName kötelező hardver adatait. Ellenőrizze a fizikai gazdagép és a kapcsolatot, majd próbálja meg újra futtatni a regisztrációs.

2. Nem lehet csatlakozni a hardver adatainak beolvasása –. Ellenőrizze a fizikai gazdagép és a kapcsolatot, majd próbálja meg újra futtatni a regisztrációs $hostName.

> OK: Ennek oka általában azt próbálja meg beszerezni a hardveradatokat, például UUID azonosító, a BIOS-ban és a Processzor próbálják végrehajtani az aktiválást a gazdagépekről, és nem sikerült a nem lehet kapcsolódni a a fizikai gazdagép miatt.

Amikor megpróbál hozzáférni a Marketplace-en felügyeleti, hiba történik a termékek szindikálása tett kísérlet során. 
> OK: Ez általában akkor fordul elő, ha az Azure Stack nem tudja a regisztrációs erőforrás elérésére. Ez az egyik gyakori oka az, hogy ha egy Azure-előfizetés címtárbérlő alaphelyzetbe állítja a regisztráció. Az Azure Stack piactéren vagy a jelentés használati nem érhető el, ha az előfizetéshez tartozó címtárbérlő módosította. A probléma megoldásához újra regisztrálnia kell.

Marketplace-en felügyeleti továbbra is kéri, hogy regisztrálja, és aktiválja az Azure Stack, akkor is, ha már regisztrálta a blokk-a leválasztott folyamat segítségével. 
> OK: Ez a leválasztott környezetekhez egy ismert probléma. A következő ellenőrizheti a regisztrációs állapot [ezeket a lépéseket](azure-stack-registration.md#verify-azure-stack-registration). Marketplace-en felügyeleti használatához meg kell használandó [a kapcsolat nélküli eszközt](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario). 

## <a name="next-steps"></a>További lépések

[Az Azure marketplace-elemek letöltése](azure-stack-download-azure-marketplace-item.md)
