---
title: A ASDK regisztrálása az Azure-ral |} A Microsoft Docs
description: Ismerteti, hogyan lehet regisztrálni az Azure Stack az Azure Marketplace-en tartalomtípus-gyűjtési és használati jelentések engedélyezése.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 4f63c19f5d489610cdaf60197bbe40f2934045bd
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64983885"
---
# <a name="azure-stack-registration"></a>Azure Stack-regisztráció
Regisztrálhat az Azure Stack Development Kit (ASDK) telepítése az Azure marketplace-elemek letöltése az Azure-ból, és megkezdheti a Microsoft kereskedelmi adatok beállítása. Regisztráció teljes Azure Stack-funkciók, többek között a piactér tartalomtípus-gyűjtési támogatásához szükséges. Regisztráció szükséges ahhoz, hogy tesztelje fontos Azure Stack-funkciók, például a Marketplace-en tartalomtípus-gyűjtési és használati jelentések készítése. Miután regisztrálta Azure Stack, az Azure kereskedelmi jelentett használati. Láthatja a regisztráció során használt előfizetés alatt. Azonban ASDK felhasználók nem számítunk fel díjat minden használati jelentést.

Ha nem regisztrál az ASDK, megjelenhet egy **aktiválás szükséges** figyelmeztetés, ami arról, hogy kell regisztrálni az Azure Stack Development Kit kapcsolódjanak. Ez várt működés.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt ezeket az utasításokat a ASDK regisztrálni az Azure-ral, győződjön meg arról, hogy az Azure Stack PowerShell telepítése és az Azure Stack eszközök letöltött leírtak szerint a [üzembe helyezés utáni konfigurációs](asdk-post-deploy.md) cikk.

Emellett a PowerShell nyelvmód értékre kell állítani **FullLanguageMode** azon a számítógépen, az Azure-ral a ASDK regisztrálhatók. Annak ellenőrzéséhez, hogy a jelenlegi nyelvmód beállítása teljes nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsokat:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, a kimenetet visszaadja **FullLanguageMode**. Ha bármilyen más nyelvmód adja vissza, egy másik számítógépen kell futtatnia kell a regisztrációs vagy a nyelvmód kell beállítani **FullLanguageMode** a folytatás előtt.

Az Azure AD-szolgáltatásfiók rendelkezik hozzáféréssel az Azure-előfizetést, és a címtárban, az adott előfizetéshez tartozó identitást használó alkalmazások és az egyszerű szolgáltatások létrehozásához szükséges engedélyek regisztrációt használt. Azt javasoljuk, hogy regisztrálja az Azure Stack az Azure által a minimális jogosultságon alapuló felügyeleti [regisztrációs használandó szolgáltatásfiók létrehozására](../operator/azure-stack-registration-role.md) globális rendszergazdai hitelesítő adatok használata helyett.

## <a name="register-azure-stack-with-azure"></a>Regisztráljon az Azure Stack az Azure-ral
Kövesse az alábbi lépéseket a ASDK regisztrálni az Azure-ral.

> [!NOTE]
> Ezeket a lépéseket minden olyan számítógépen, amelyen a kiemelt végponthoz való hozzáférést adatbázisból kell futtatni. A ASDK Ez a fejlesztői csomag gazdaszámítógépen.

1. Nyisson meg egy PowerShell-konzolt rendszergazdaként.  

2. Futtassa a következő PowerShell-parancsokat a ASDK telepítési regisztrálni az Azure-ral. Jelentkezzen be az Azure számlázási előfizetés-Azonosítóját és a helyi ASDK telepítési is kell. Ha nem rendelkezik az Azure számlázási előfizetés-azonosító, még akkor is [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Az Azure-előfizetése ingyenes regisztrálása az Azure Stack tekintetében.<br><br>Állítsa be egy egyedi nevet a regisztráció futtatásakor a **Set-AzsRegistration** parancsmagot. A **RegistrationName** paraméterének alapértelmezett értéke **AzureStackRegistration**. Azonban, ha ugyanazzal a névvel egynél több Azure Stack-példányt használ, a parancsfájl futtatása sikertelen lesz.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. Miután a parancsfájl futása befejeződött, az üzenetnek kell megjelennie: **A környezet már regisztrálva van, és aktiválta a megadott paraméterekkel.**

    ![A környezet már regisztrálva van](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Regisztrálja a kapcsolat nélküli környezetben
Ha regisztrál az Azure Stack kapcsolat nélküli környezetben (az internet-hozzáférés nélküli), szerezze be egy regisztrációs tokent az Azure Stack-környezet, majd ezt a jogkivonatot egy számítógépen, amely Azure-ban regisztrálja, és hozzon létre egy aktiválási képes kapcsolódni kell az erőforrás ASDK környezete számára.
 
 > [!IMPORTANT]
 > Mielőtt regisztrálja az Azure Stack használatával ezeket az utasításokat, győződjön meg arról, hogy az Azure Stack PowerShell telepítése és az Azure Stack eszközök letöltött leírtak szerint a [üzembe helyezés utáni konfigurációs](asdk-post-deploy.md) cikk mindkét a ASDK gazdagépen számítógép és a számítógép csatlakozik az Azure és a regisztráció internet-hozzáféréssel rendelkező.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Szerezze be egy regisztrációs tokent az Azure Stack-környezet
A ASDK gazdaszámítógépen indítsa el a Powershellt rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** jön létre, amikor az Azure Stack eszközök letöltött könyvtár. A következő PowerShell-parancsok segítségével importálhatja a **RegisterWithAzure.psm1** modult, majd a **Get-AzsRegistrationToken** parancsmagot, hogy a regisztrációs jogkivonat lekérése:  

   ```powershell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

A regisztrációs jogkivonat használatra mentse az internethez csatlakozó számítógépen. A fájl vagy a szöveg átmásolhatja a fájlt a $FilePathForRegistrationToken paraméter által létrehozott.

### <a name="connect-to-azure-and-register"></a>Csatlakozás az Azure és a regisztráció
Az interneten csatlakoztatott számítógépen, a következő PowerShell-parancsok használata importálása a **RegisterWithAzure.psm1** modult, majd a **Register-AzsEnvironment** parancsmag regisztrálhat az Azure-ban az imént létrehozott regisztrációs jogkivonatot, és egyedi regisztrációs nevét:  

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Másik lehetőségként használhatja a **Get-tartalom** parancsmagot, hogy a regisztrációs jogkivonatot tartalmazó fájlra mutat:

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Regisztrációs befejeződése után üzenetnek kell megjelennie egy hasonló **az Azure Stack-környezet már regisztrálva van az Azure-ral.**

> [!IMPORTANT]
> Ne zárja be a PowerShell-ablakban. 

A regisztrációs jogkivonatot, és a regisztrációs erőforrás neve a későbbiekben takaríthat meg.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Az Azure-regisztráció erőforrás-aktiválási kulcs lekérése

Továbbra is használja az internetkapcsolattal rendelkező számítógépen **és az ugyanazon PowerShell-konzolablakot**, kérje le egy aktiválási kulcsot hozott létre, amikor az Azure-ban regisztrált regisztrációs erőforrásból.

Az aktiválási kulcs lekéréséhez futtassa a következő PowerShell-parancsokat pedig a ugyanazon egyedi regisztrációs regisztrálásakor az Azure-ral az előző lépésben megadott név értéket:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```
### <a name="create-an-activation-resource-in-azure-stack"></a>Hozzon létre egy aktiválási erőforrást az Azure Stackben

Az Azure Stack-környezet térjen vissza a fájl- vagy szöveget az aktiválási kulcs alapján létrehozott **Get-AzsActivationKey**. Futtassa a következő PowerShell-parancsokat az aktiválási erőforrás létrehozásához az Azure Stack a aktiválási kulccsal:   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Másik lehetőségként használhatja a **Get-tartalom** parancsmagot, hogy a regisztrációs jogkivonatot tartalmazó fájlra mutat:

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Az aktiválás befejeződése után üzenetnek kell megjelennie egy hasonló **a környezet a regisztrációs és aktiválási folyamat befejeződött.**

## <a name="verify-the-registration-was-successful"></a>Ellenőrizze a regisztráció sikeres volt

Használhatja a **régiók kezelése** csempére kattintva ellenőrizze, hogy az Azure Stack-regisztráció sikeres volt-e. Ez a csempe nem érhető el az alapértelmezett irányítópult, a felügyeleti portálon.

1. Jelentkezzen be a [Azure Stack felügyeleti portálon](https://adminportal.local.azurestack.external).

2. Az irányítópulton, válassza ki a **régiók kezelése**.

    [![Régió felügyeleti csempe](media/asdk-register/admin1sm.png "régió felügyeleti csempe")](media/asdk-register/admin1.png#lightbox)

3. Válassza ki **tulajdonságok**. Ezen a panelen látható, az állapot és a környezet sajátosságait. Az állapot lehet **regisztrált** vagy **nincs regisztrálva**. Ha regisztrált, azt is bemutatja az Azure-előfizetés azonosítója segítségével regisztrálja az Azure Stack, a regisztrációs erőforráscsoportot és a neve mellett.

## <a name="move-a-registration-resource"></a>Regisztrációs erőforrás áthelyezése
Regisztrációs erőforrás ugyanahhoz az előfizetéshez tartozó erőforráscsoportok közötti áthelyezése **van** támogatott. Erőforrások áthelyezése új erőforráscsoport kapcsolatos további információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>További lépések

- [Azure Stack piactéren elem hozzáadása](../operator/azure-stack-marketplace.md)
