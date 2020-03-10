---
title: Alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez
description: Megtudhatja, hogyan kezelheti az Azure Stack hub egyszerű szolgáltatásait. Az egyszerű szolgáltatás a bejelentkezéshez és az erőforrásokhoz való hozzáféréshez szerepköralapú hozzáférés-vezérléssel használható.
author: BryanLa
ms.author: bryanla
ms.topic: how-to
ms.date: 11/11/2019
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 1c96ee9520285e0bc2b9784fa5d310a1ec2ae60f
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366141"
---
# <a name="use-an-app-identity-to-access-azure-stack-hub-resources"></a>Alkalmazás-identitás használata Azure Stack hub-erőforrások eléréséhez

Egy egyszerű szolgáltatásnak egy olyan alkalmazást kell képviselnie, amelynek az erőforrásait Azure Resource Manager használatával kell telepítenie vagy konfigurálnia. Ahogy a felhasználó egy egyszerű felhasználói tag, az egyszerű szolgáltatásnév olyan rendszerbiztonsági tag, amely egy alkalmazást jelöl. Az egyszerű szolgáltatás identitást biztosít az alkalmazás számára, amely lehetővé teszi, hogy csak az adott egyszerű szolgáltatáshoz szükséges engedélyeket delegálja.  

Előfordulhat például, hogy rendelkezik egy Azure Resource Managert használó Configuration Management-alkalmazással az Azure-erőforrások leltározásához. Ebben az esetben létrehozhat egy egyszerű szolgáltatásnevet, megadhatja az olvasó szerepkört az egyszerű szolgáltatásnév számára, és korlátozhatja a Configuration Management alkalmazást a csak olvasási hozzáférésre.

## <a name="overview"></a>Áttekintés

Az egyszerű felhasználónévhöz hasonlóan a szolgáltatásnak a hitelesítő adatokkal kell rendelkeznie a hitelesítés során. Ez a hitelesítés két elemet tartalmaz:

- Egy **alkalmazás-azonosító**, más néven ügyfél-azonosító. Ez egy GUID, amely egyedileg azonosítja az alkalmazás regisztrációját a Active Directory-bérlőben.
- Az alkalmazás-AZONOSÍTÓhoz tartozó **titok** . Létrehozhat egy ügyfél titkos karakterláncot (a jelszóhoz hasonlóan), vagy megadhat egy X509-tanúsítványt (amely a nyilvános kulcsot használja).

Az alkalmazás egy egyszerű szolgáltatásnév identitása alatt való futtatása előnyben részesített egy felhasználói tag alatt, mert:

 - Az egyszerű szolgáltatásnév X509-tanúsítványt használhat az **erősebb hitelesítő adatokhoz**.  
 - Egy egyszerű szolgáltatáshoz **több korlátozó jogosultságot** is hozzárendelhet. Ezek az engedélyek jellemzően csak az alkalmazás működéséhez szükségesek, azaz a *legalacsonyabb jogosultsági szint elve*.
 - Az egyszerű szolgáltatás **hitelesítő adatai és engedélyei nem változnak a** felhasználó hitelesítő adataival. Ha például a felhasználó feladatai változnak, a jelszóra vonatkozó követelmények megszabják a változást, vagy a felhasználó elhagyja a vállalatot.

Első lépésként hozzon létre egy új alkalmazást a címtárban, amely létrehoz egy társított [egyszerű objektumot](/azure/active-directory/develop/developer-glossary#service-principal-object) , amely az alkalmazás identitását jelöli a címtárban. Ez a dokumentum leírja, hogyan kell létrehozni és felügyelni egy egyszerű szolgáltatásnevet a Azure Stack hub-példányhoz választott könyvtártól függően:

- Azure Active Directory (Azure AD). Az Azure AD egy több-bérlős, felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatás. Az Azure AD-t egy csatlakoztatott Azure Stack hub-példánnyal is használhatja.
- Active Directory összevonási szolgáltatások (AD FS). A AD FS egyszerűsített, biztonságos identitás-összevonást és webes egyszeri bejelentkezési (SSO) képességeket biztosít. AD FS a csatlakoztatott és a leválasztott Azure Stack hub-példányokkal is használható.

Először megtudhatja, hogyan kezelheti a szolgáltatást, majd hogyan rendelheti hozzá az egyszerű szolgáltatást egy szerepkörhöz, és korlátozza az erőforrás-hozzáférését.

## <a name="manage-an-azure-ad-service-principal"></a>Azure AD-szolgáltatásnév kezelése

Ha az Azure AD-val telepített Azure Stack hubot az identitáskezelési szolgáltatásként, akkor az Azure-hoz hasonló egyszerű szolgáltatásokat hozhat létre. Ez a szakasz bemutatja, hogyan hajthatja végre a lépéseket a Azure Portalon. A Kezdés előtt győződjön meg arról, hogy rendelkezik a [szükséges Azure ad-engedélyekkel](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) .

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>Ügyfél-titkos hitelesítő adatokat használó egyszerű szolgáltatásnév létrehozása

Ebben a szakaszban a Azure Portal használatával regisztrálja az alkalmazást, amely létrehozza az egyszerű szolgáltatásnév objektumot az Azure AD-bérlőben. Ebben a példában az egyszerű szolgáltatás ügyfél-titkos hitelesítő adatokkal jön létre, de a portál támogatja a X509 hitelesítő adatait is.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-fiók használatával.
2. Válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.
3. Adja meg az alkalmazás **nevét** .
4. Válassza ki a megfelelő **támogatott fióktípus-típusokat**.
5. Az **átirányítási URI**területen válassza a **web** lehetőséget az alkalmazás típusaként, és (opcionálisan) adjon meg egy átirányítási URI-t, ha az alkalmazáshoz szükség van.
6. Az értékek beállítása után válassza a **regisztráció**lehetőséget. Ekkor létrejön az alkalmazás regisztrálása, és megjelenik az **Áttekintés** oldal.
7. Másolja az **alkalmazás azonosítóját** az alkalmazás kódjában való használatra. Ezt az értéket ügyfél-AZONOSÍTÓnak is nevezzük.
8. Az ügyfél titkos kulcsának létrehozásához válassza a **tanúsítványok & titkok** lapot. Válassza az **Új titkos ügyfélkód** lehetőséget.
9. Adja meg a titkos kulcs **leírását** , valamint a **lejárat** időtartamát.
10. Ha elkészült, válassza a **Hozzáadás**lehetőséget.
11. A titkos kód megjelenített értéke. Másolja és mentse ezt az értéket egy másik helyre, mert később nem lehet lekérni. Adja meg a titkot az ügyfélalkalmazás alkalmazás-azonosítójával az egyszerű szolgáltatás beléptetése során.

    ![Kulcs mentve az ügyfél titkos kulcsaiban](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>AD FS egyszerű szolgáltatás kezelése

Ha a Azure Stack hub-t az identitáskezelési szolgáltatással AD FS, akkor a PowerShellt kell használnia az egyszerű szolgáltatás kezeléséhez. Az alábbiakban példákat talál a egyszerű szolgáltatásnév hitelesítő adatainak kezelésére, valamint az X509-tanúsítvány és az ügyfél titkos kódjának bemutatására.

A parancsfájlokat emelt szintű ("Futtatás rendszergazdaként") PowerShell-konzolon kell futtatni, amely egy másik munkamenetet nyit meg egy olyan virtuális géphez, amely az Azure Stack hub-példányhoz tartozó Kiemelt végpontot üzemeltet. Miután létrejött a privilegizált végpont-munkamenet, további parancsmagok is végrehajtják és kezelik a szolgáltatást. A Kiemelt végponttal kapcsolatos további információkért lásd: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>Tanúsítvány hitelesítő adatait használó egyszerű szolgáltatásnév létrehozása

Ha tanúsítványt hoz létre egy egyszerű szolgáltatásnév hitelesítő adataihoz, a következő követelményeknek kell teljesülniük:

 - Éles környezetben a tanúsítványt belső hitelesítésszolgáltatótól vagy nyilvános hitelesítésszolgáltatótól kell kibocsátani. Ha nyilvános hitelesítésszolgáltatót használ, a Microsoft megbízható legfelső szintű felügyeleti program részeként fel kell vennie a szolgáltatót az operációs rendszer rendszerképébe. A teljes listát megtalálhatja a [Microsoft megbízható Főtanúsítvány programjában: résztvevők](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca). Az "önaláírt" tesztelési tanúsítvány létrehozásához például később is megjelenik egy [egyszerű szolgáltatásnév tanúsítványának frissítése](#update-a-service-principals-certificate-credential)során. 
 - A kriptográfiai szolgáltatót a Microsoft örökölt kriptográfiai szolgáltató (CSP) kulcs szolgáltatójának kell megadnia.
 - A tanúsítvány formátumának PFX-fájlban kell lennie, mivel a nyilvános és a titkos kulcs is kötelező. A Windows-kiszolgálók a nyilvános kulcsfájl (SSL-tanúsítványfájl) és a hozzá tartozó titkos kulcs fájlját tartalmazó. pfx fájlokat használják.
 - Az Azure Stack hub-infrastruktúrának a tanúsítványban közzétett tanúsítvány-visszavonási lista (CRL) elérési helyéhez hálózati hozzáféréssel kell rendelkeznie. A CRL-nek HTTP-végpontnak kell lennie.

Ha rendelkezik tanúsítvánnyal, az alkalmazás regisztrálásához és az egyszerű szolgáltatásnév létrehozásához használja az alábbi PowerShell-szkriptet. Az egyszerű szolgáltatásnév használatával is bejelentkezhet az Azure-ba. Helyettesítse be a saját értékeit a következő helyőrzők esetében:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A rendszerjogosultságú végpont virtuális gép neve a Azure Stack hub-példányon. | "AzS-ERCS01" |
| \<YourCertificateLocation\> | A X509-tanúsítvány helye a helyi tanúsítványtárolóban. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<YourAppName\> | Az új alkalmazás regisztrálásának leíró neve. | "Saját felügyeleti eszköz" |

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsfájlt:

   ```powershell  
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
    $Creds = Get-Credential

    # Create a PSSession to the Privileged Endpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the Get-Item cmdlet to retrieve your certificate.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    # Use the privileged endpoint to create the new app registration (and service principal object)
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # Using the stamp info for your Azure Stack Hub instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack Hub instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. A parancsfájl befejeződése után megjeleníti az alkalmazás regisztrációs adatait, beleértve az egyszerű szolgáltatásnév hitelesítő adatait. Amint azt a bemutatta, a rendszer a `ClientID` és `Thumbprint` használja a szolgáltatásnév identitásának bejelentkezni. A sikeres bejelentkezést követően a rendszer a szolgáltatás egyszerű azonosítóját fogja használni a későbbi engedélyezéshez és a Azure Resource Manager által felügyelt erőforrásokhoz való hozzáféréshez.

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

Tartsa nyitva a PowerShell-konzol munkamenetét, ahogy a következő szakaszban `ApplicationIdentifier` értékkel használja.

### <a name="update-a-service-principals-certificate-credential"></a>Egyszerű szolgáltatásnév tanúsítványa hitelesítő adatainak frissítése

Most, hogy létrehozott egy egyszerű szolgáltatást, ez a szakasz bemutatja, hogyan végezheti el a következőket:

1. Hozzon létre egy új, önaláírt X509-tanúsítványt a teszteléshez.
2. Frissítse az egyszerű szolgáltatásnév hitelesítő adatait úgy, hogy az **ujjlenyomat** -tulajdonságát az új tanúsítványnak megfelelően frissíti.

Frissítse a tanúsítvány hitelesítő adatait a PowerShell használatával, és helyettesítse be a saját értékeit a következő helyőrzők esetében:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A rendszerjogosultságú végpont virtuális gép neve a Azure Stack hub-példányon. | "AzS-ERCS01" |
| \<YourAppName\> | Az új alkalmazás regisztrálásának leíró neve. | "Saját felügyeleti eszköz" |
| \<YourCertificateLocation\> | A X509-tanúsítvány helye a helyi tanúsítványtárolóban. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | Az alkalmazás regisztrálásához hozzárendelt azonosító. | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. Futtassa a következő parancsmagokat a rendszergazda jogú Windows PowerShell-munkamenet használatával:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Create a self-signed certificate for testing purposes. 
     $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
     # In production, use Get-Item and a managed certificate instead.
     # $Cert = Get-Item "<YourCertificateLocation>"

     # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ClientCertificates $using:NewCert}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. A parancsfájl befejeződése után megjeleníti az alkalmazás frissített regisztrációs adatait, beleértve az új önaláírt tanúsítvány ujjlenyomat értékét.

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>Ügyfél-titkos hitelesítő adatokat használó egyszerű szolgáltatás létrehozása

> [!IMPORTANT]
> Az ügyfél titkos kulcsa kevésbé biztonságos, mint a X509-tanúsítvány hitelesítő adatainak használata. A hitelesítési mechanizmus nem csupán kevésbé biztonságos, de általában a titkos kulcs beágyazását igényli az ügyfélalkalmazás forráskódjában. Az éles alkalmazások esetében javasoljuk, hogy a tanúsítvány hitelesítő adatait használja.

Most létrehoz egy másik alkalmazás-regisztrációt, de ezúttal megadja az ügyfél titkos hitelesítő adatait. A tanúsítvány hitelesítő adataival ellentétben a címtár képes az ügyfél titkos hitelesítő adatainak előállítására. Az ügyfél titkos kulcsának meghatározása helyett a `-GenerateClientSecret` kapcsolót kell használnia a létrehozásához. Helyettesítse be a saját értékeit a következő helyőrzők esetében:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A rendszerjogosultságú végpont virtuális gép neve a Azure Stack hub-példányon. | "AzS-ERCS01" |
| \<YourAppName\> | Az új alkalmazás regisztrálásának leíró neve. | "Saját felügyeleti eszköz" |

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

     ```powershell  
     # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
     $Creds = Get-Credential

     # Create a PSSession to the Privileged Endpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to create the new app registration (and service principal object)
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Using the stamp info for your Azure Stack Hub instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack Hub instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. A parancsfájl befejeződése után megjeleníti az alkalmazás regisztrációs adatait, beleértve az egyszerű szolgáltatásnév hitelesítő adatait. Amint azt a bemutatta, a rendszer a `ClientID` és a generált `ClientSecret` használja a szolgáltatásnév identitásának bejelentkezni. A sikeres bejelentkezést követően a rendszer a szolgáltatás egyszerű azonosítóját fogja használni a későbbi engedélyezéshez és a Azure Resource Manager által felügyelt erőforrásokhoz való hozzáféréshez.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

Tartsa nyitva a PowerShell-konzol munkamenetét, ahogy a következő szakaszban `ApplicationIdentifier` értékkel használja.

### <a name="update-a-service-principals-client-secret"></a>Egyszerű szolgáltatásnév ügyfél-titkos kódjának frissítése

Frissítse az ügyfél titkos hitelesítő adatait a PowerShell használatával a **ResetClientSecret** paraméter használatával, amely azonnal megváltoztatja az ügyfél titkos kulcsát. Helyettesítse be a saját értékeit a következő helyőrzők esetében:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A rendszerjogosultságú végpont virtuális gép neve a Azure Stack hub-példányon. | "AzS-ERCS01" |
| \<AppIdentifier\> | Az alkalmazás regisztrálásához hozzárendelt azonosító. | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. Futtassa a következő parancsmagokat a rendszergazda jogú Windows PowerShell-munkamenet használatával:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. A szkript befejeződése után megjeleníti az alkalmazás frissített regisztrációs adatait, beleértve az újonnan létrehozott ügyfél-titkos kulcsot is.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
     PSComputerName        : azs-ercs01
     RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal"></a>Egyszerű szolgáltatásnév eltávolítása

Most megtudhatja, hogyan távolíthat el vagy törölhet egy alkalmazás-regisztrációt a címtárból, valamint a hozzá tartozó szolgáltatásnév-objektumot a PowerShell használatával. 

Helyettesítse be a saját értékeit a következő helyőrzők esetében:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A rendszerjogosultságú végpont virtuális gép neve a Azure Stack hub-példányon. | "AzS-ERCS01" |
| \<AppIdentifier\> | Az alkalmazás regisztrálásához hozzárendelt azonosító. | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

```powershell  
# Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
$Creds = Get-Credential

# Create a PSSession to the PrivilegedEndpoint VM
$Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

# OPTIONAL: Use the privileged endpoint to get a list of applications registered in AD FS
$AppList = Invoke-Command -Session $Session -ScriptBlock {Get-GraphApplication}

# Use the privileged endpoint to remove the application and associated service principal object for <AppIdentifier>
Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier "<AppIdentifier>"}
```

A Remove-GraphApplication parancsmag nem hívható vissza a privilegizált végponton, de a parancsmag végrehajtása során a Verbatim visszaigazoló kimenetét fogja látni a konzolon:

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>Szerepkör kiosztása

A felhasználók és alkalmazások Azure-erőforrásokhoz való hozzáférését szerepköralapú Access Control (RBAC) engedélyezik. Ahhoz, hogy egy alkalmazás hozzáférhessen az előfizetéséhez tartozó erőforrásokhoz az adott szolgáltatásnév használatával, *hozzá kell rendelnie* a szolgáltatásnevet egy adott *erőforráshoz*tartozó *szerepkörhöz* . Először döntse el, hogy melyik szerepkör felel meg az alkalmazás megfelelő *engedélyeinek* . Az elérhető szerepkörökről az [Azure-erőforrások beépített szerepköreivel](/azure/role-based-access-control/built-in-roles)foglalkozó témakörben olvashat bővebben.

A választott erőforrás típusa az egyszerű szolgáltatásnév *hozzáférési hatókörét* is létrehozza. Megadhatja a hozzáférési hatókört az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Ha például hozzáad egy alkalmazást az erőforráscsoport "olvasó" szerepköréhez, az azt jelenti, hogy elolvashatja az erőforráscsoportot és a benne foglalt erőforrásokat.

1. Jelentkezzen be a megfelelő portálra a Azure Stack hub telepítésekor megadott könyvtár alapján (az Azure AD-Azure Portal vagy a AD FS Azure Stack hub felhasználói portálján, például:). Ebben a példában egy felhasználó bejelentkezett a Azure Stack hub felhasználói portálra.

   > [!NOTE]
   > Egy adott erőforráshoz tartozó szerepkör-hozzárendelések hozzáadásához a felhasználói fióknak olyan szerepkörhöz kell tartoznia, amely deklarálja az `Microsoft.Authorization/roleAssignments/write` engedélyt. Például a [tulajdonos](/azure/role-based-access-control/built-in-roles#owner) vagy a [felhasználói hozzáférés rendszergazdai](/azure/role-based-access-control/built-in-roles#user-access-administrator) beépített szerepkörei.  
2. Navigáljon ahhoz az erőforráshoz, amely számára engedélyezni szeretné az egyszerű szolgáltatásnév elérését. Ebben a példában rendelje hozzá az egyszerű szolgáltatást az előfizetés hatókörében lévő szerepkörhöz az **előfizetések**, majd egy adott előfizetés kiválasztásával. Ehelyett kijelölhet egy erőforráscsoportot, vagy egy adott erőforrást, például egy virtuális gépet.

     ![Előfizetés kiválasztása hozzárendeléshez](./media/azure-stack-create-service-principal/select-subscription.png)

3. Válassza ki a **Access Control (iam)** lapot, amely univerzális a RBAC támogató összes erőforráson.
4. Válassza a **+ Hozzáadás** lehetőséget
5. A **szerepkör**területen válassza ki az alkalmazáshoz hozzárendelni kívánt szerepkört.
6. A **kiválasztás**területen keresse meg az alkalmazást teljes vagy részleges alkalmazásnév használatával. A regisztráció során az alkalmazás neve *Azurestack-\<YourAppName\>-\<ClientId\>* . Ha például a *App2*nevű alkalmazás nevét használta, és a ClientId *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff* a létrehozás során lett hozzárendelve, a teljes név a következő lesz: *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*. Megkeresheti a pontos karakterláncot vagy egy részét, például a *Azurestack* vagy a *Azurestack-App2*.
7. Miután megtalálta az alkalmazást, jelölje ki, és a **kijelölt tagok**területen fog megjelenni.
8. A szerepkör hozzárendelésének befejezéséhez kattintson a **Mentés** gombra.

     [szerepkör-hozzárendelés ![](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. Ha elkészült, az alkalmazás megjelenik az aktuális hatókörhöz rendelt rendszerbiztonsági tag listájában a megadott szerepkörhöz.

     [Hozzárendelt szerepkör ![](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

Most, hogy létrehozott egy szolgáltatásnevet, és hozzárendelt egy szerepkört, megkezdheti az alkalmazáson belüli egyszerű szolgáltatásnév használatát Azure Stack hub-erőforrások eléréséhez.  

## <a name="next-steps"></a>Következő lépések

[Felhasználók hozzáadása az AD FS-hez](azure-stack-add-users-adfs.md)  
[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)  
[Az Azure Active Directory dokumentációja](https://docs.microsoft.com/azure/active-directory)  
[Active Directory összevonási szolgáltatások](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
