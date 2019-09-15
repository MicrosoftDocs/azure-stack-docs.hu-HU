---
title: A ASDK regisztrálása az Azure-ban | Microsoft Docs
description: Ismerje meg, hogyan regisztrálhatók a Azure Stack Development Kit (ASDK) az Azure-ban a Piactéri hírszolgáltatás és a használati jelentéskészítés lehetővé tételéhez.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 2dea14ab5d43aab8deab2632b07277d7094e33c4
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974628"
---
# <a name="register-the-asdk-with-azure"></a>A ASDK regisztrálása az Azure-ban

Az Azure-ban regisztrálhat a Azure Stack Development Kit (ASDK) telepítésére, és az Azure-ban letöltheti a Piactéri elemeket, és beállíthatja a kereskedelmi adatokat a Microsoftnak. Regisztrációra van szükség a teljes Azure Stack funkció támogatásához, beleértve a piactér-hírszolgáltatást is. A regisztráció szükséges ahhoz, hogy tesztelje a fontos Azure Stack funkciókat, például a Piactéri hírszolgáltatást és a használati jelentéseket. Azure Stack regisztrálása után a rendszer a használatot az Azure Commerce szolgáltatásnak jelenti. A regisztrációhoz használt előfizetés alatt látható. A ASDK-felhasználók azonban nem számítanak fel díjat a jelentésben szereplő használati díjakért.

Ha nem regisztrálja a ASDK, az **aktiválást igénylő** figyelmeztetés figyelmezteti a ASDK regisztrálására. Ez várt működés.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt felhasználja ezeket az utasításokat a ASDK az Azure-ban való regisztrálásához, győződjön meg arról, hogy telepítette a Azure Stack PowerShellt, és letöltötte a Azure Stack eszközöket a [Telepítés utáni konfiguráció](asdk-post-deploy.md) című cikkben leírtak szerint.

A ASDK Azure-ban való regisztrálásához használt számítógépen a PowerShell nyelvi üzemmódját is be kell állítani a **FullLanguage** értékre. Annak ellenőrzéséhez, hogy az aktuális nyelvi mód a teljes értékre van-e állítva, nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsokat:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, hogy a kimenet **FullLanguage**ad vissza. Ha más nyelvi módot ad vissza, a regisztrációt másik számítógépen kell futtatni, vagy a folytatás előtt a **FullLanguage** beállított nyelvi mód.

A regisztrációhoz használt Azure AD-fióknak hozzáféréssel kell rendelkeznie az Azure-előfizetéshez, és engedélyekkel kell rendelkeznie az identitás-alkalmazások és-szolgáltatások létrehozásához az adott előfizetéshez tartozó címtárban. Javasoljuk, hogy regisztrálja Azure Stack az Azure [-ban egy olyan szolgáltatásfiók létrehozásával, amely a](../operator/azure-stack-registration-role.md) globális rendszergazdai hitelesítő adatok helyett a regisztrációhoz használható.

## <a name="register-the-asdk"></a>A ASDK regisztrálása

A ASDK az Azure-ban való regisztrálásához kövesse az alábbi lépéseket.

> [!NOTE]
> Ezeket a lépéseket olyan számítógépről kell futtatni, amely hozzáfér a Kiemelt végponthoz. A ASDK esetében ez a ASDK-gazda számítógép.

1. Nyisson meg egy PowerShell-konzolt rendszergazdaként.  

2. Futtassa a következő PowerShell-parancsokat a ASDK-telepítés az Azure-ban való regisztrálásához. Jelentkezzen be az Azure számlázási előfizetés-azonosítójával és a helyi ASDK-telepítéssel. Ha még nem rendelkezik Azure számlázási előfizetés-AZONOSÍTÓval, [itt hozhat létre ingyenes Azure-fiókot](https://azure.microsoft.com/free/?b=17.06). Az Azure-előfizetéshez tartozó Azure Stack regisztrációját nem kell fizetnie.<br><br>Állítsa be a regisztráció egyedi nevét a **set-AzsRegistration** parancsmag futtatásakor. A **RegistrationName** paraméter alapértelmezett értéke **AzureStackRegistration**. Ha azonban ugyanazt a nevet használja a Azure Stack több példányán, a parancsfájl sikertelen lesz.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
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

3. A szkript befejezésekor a következő üzenetnek kell megjelennie: **A környezet most már regisztrálva van és aktiválva van a megadott paraméterek használatával.**

    ![A környezet már regisztrálva van](media/asdk-register/1.PNG)

## <a name="register-in-disconnected-environments"></a>Regisztráció leválasztott környezetekben

Ha Azure Stackt regisztrál egy leválasztott környezetben (internetkapcsolat nélkül), akkor regisztrálnia kell egy regisztrációs jogkivonatot a Azure Stack-környezetből, majd ezt a tokent kell használnia egy olyan számítógépen, amely csatlakozhat az Azure-hoz a regisztráláshoz és az aktiválás létrehozásához. a ASDK-környezet erőforrása.

 > [!IMPORTANT]
 > Mielőtt felhasználja ezeket az utasításokat a Azure Stack regisztrálásához, győződjön meg arról, hogy telepítette a PowerShellt a Azure Stackhoz, és letöltötte a Azure Stack eszközöket a [Telepítés utáni konfigurációs](asdk-post-deploy.md) cikkben leírt módon a ASDK gazdagépen és a Az Azure-hoz való kapcsolódáshoz és a regisztráláshoz használt internet-hozzáféréssel rendelkező számítógép.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Regisztrációs jogkivonat beszerzése a Azure Stack-környezetből

A ASDK gazdaszámítógépen indítsa el a PowerShellt rendszergazdaként, és navigáljon a **AzureStack-Tools-Master** könyvtár **regisztrációs** mappájához, amelyet a Azure stack eszközök letöltésekor hozott létre. A következő PowerShell-parancsokkal importálhatja a **RegisterWithAzure. psm1** modult, majd a **Get-AzsRegistrationToken** parancsmag használatával beolvashatja a regisztrációs jogkivonatot:  

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

Mentse ezt a regisztrációs jogkivonatot az internethez csatlakoztatott számítógépen való használatra. A (z) `$FilePathForRegistrationToken` paraméterrel létrehozott fájlból vagy szövegből is másolhatja a fájlt.

### <a name="connect-to-azure-and-register"></a>Kapcsolódjon az Azure-hoz, és regisztráljon

Az internethez csatlakoztatott számítógépen a következő PowerShell-parancsokkal importálja a **RegisterWithAzure. psm1** modult, majd a **Register-AzsEnvironment** parancsmag használatával regisztrálja az Azure-t az imént létrehozott regisztrációs jogkivonat használatával. egy egyedi regisztrációs név:  

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

Azt is megteheti, hogy a **Get-Content** parancsmaggal egy olyan fájlra mutat, amely tartalmazza a regisztrációs jogkivonatot:

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

A regisztráció befejezésekor a következőhöz hasonló üzenetnek kell megjelennie: **Az Azure Stack-környezet már regisztrálva van az Azure-ban.**

> [!IMPORTANT]
> Ne **zárjuk be** a PowerShell ablakot.

Mentse a regisztrációs jogkivonatot és a regisztrációs erőforrás nevét a későbbi referenciához.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Aktiválási kulcs beolvasása az Azure regisztrációs erőforrásból

Továbbra is az internethez csatlakoztatott számítógépet **és a PowerShell-konzolt futtató ablakot**kell beolvasnia az Azure-ban regisztrált regisztrációs erőforrásból.

Az aktiválási kulcs lekéréséhez futtassa a következő PowerShell-parancsokat. Használja ugyanazt az egyedi regisztrációs nevet, amelyet az Azure-ban az előző lépésben való regisztráláskor adott meg:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

### <a name="create-an-activation-resource-in-azure-stack"></a>Aktiválási erőforrás létrehozása Azure Stack

Térjen vissza a Azure Stack-környezetbe a **Get-AzsActivationKey**által létrehozott aktiválási kulcsból származó fájllal vagy szöveggel. A következő PowerShell-parancsok futtatásával hozzon létre egy aktiválási erőforrást Azure Stack az adott aktiválási kulccsal:   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Azt is megteheti, hogy a **Get-Content** parancsmaggal egy olyan fájlra mutat, amely tartalmazza a regisztrációs jogkivonatot:

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

Az aktiválás befejezésekor a következőhöz hasonló üzenetnek kell megjelennie: **A környezet befejezte a regisztrációs és aktiválási folyamatot.**

## <a name="verify-the-registration-was-successful"></a>Ellenőrizze, hogy a regisztráció sikeres volt-e

A **régió kezelése** csempével ellenőrizheti, hogy a Azure stack regisztrációja sikeres volt-e. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján érhető el.

1. Jelentkezzen be a [Azure stack felügyeleti portálra](https://adminportal.local.azurestack.external).

2. Az irányítópulton válassza a **régió kezelése**lehetőséget.

    [![Régió-felügyeleti csempe Azure stack felügyeleti portálon](media/asdk-register/admin1sm.png "Régió-felügyeleti csempe")](media/asdk-register/admin1.png#lightbox)

3. Válassza ki **tulajdonságok**. Ez a panel a környezet állapotát és részleteit jeleníti meg. Az állapot regisztrálható vagy **nem regisztrálható**. Ha regisztrálva van, megjeleníti a Azure Stack regisztrálásához használt Azure-előfizetés AZONOSÍTÓját, valamint a regisztrációs erőforráscsoportot és a nevet is.

## <a name="move-a-registration-resource"></a>Regisztrációs erőforrás áthelyezése
Az ugyanahhoz az előfizetéshez tartozó erőforráscsoportok közötti regisztrációs erőforrás áthelyezése támogatott. Az erőforrások új erőforráscsoporthoz való áthelyezésével kapcsolatos további információkért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>További lépések

- [Azure Stack Marketplace-elem hozzáadása](../operator/azure-stack-marketplace.md)
