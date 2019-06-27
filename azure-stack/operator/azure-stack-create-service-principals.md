---
title: Egy alkalmazás-erőforrások eléréséhez identitás használata
description: A bejelentkezési és az erőforrásokhoz való hozzáférést a szerepköralapú hozzáférés-vezérléssel, használható egyszerű szolgáltatás kezelését ismerteti.
services: azure-resource-manager
documentationcenter: na
author: BryanLa
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2019
ms.author: bryanla
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 8c27948185df5f98926a3500db0981a1ccddc321
ms.sourcegitcommit: c9d11be7d27c73797bdf279d4fcabb7a22451541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2019
ms.locfileid: "67397315"
---
# <a name="use-an-app-identity-to-access-resources"></a>Egy alkalmazás-erőforrások eléréséhez identitás használata

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack Development Kit (ASDK)*

Alkalmazás üzembe helyezése vagy erőforrásokat az Azure Resource Managerrel, konfigurálni kell egy egyszerű szolgáltatásfiókkal kell képviseli. Ugyanúgy, mint a felhasználó egy egyszerű képviseli, egy egyszerű szolgáltatást egy alkalmazás képviselő rendszerbiztonsági tag típusát. Egyszerű szolgáltatás egy identitást biztosít az alkalmazásnak, amely lehetővé teszi, hogy egyszerű szolgáltatás csak a szükséges engedélyeket delegálhatnak.  

Tegyük fel szükség lehet egy Azure Resource Manager leltározhatók az Azure-erőforrások használó configuration management alkalmazás. Ebben a forgatókönyvben egyszerű szolgáltatás létrehozása, az Olvasó szerepkör biztosítani az egyszerű szolgáltatást, és korlátozhatja a csak olvasási hozzáféréssel az configuration management alkalmazás. 

## <a name="overview"></a>Áttekintés

Egy felhasználó rendszerbiztonsági tag hasonlóan egy egyszerű szolgáltatásfiókkal kell jelen szükséges hitelesítő adatokat a hitelesítés során két elemből áll:

- Egy **Alkalmazásazonosító**néha nevezik egy ügyfél-azonosító. Ez az egy GUID Azonosítót, amely egyedileg azonosítja az alkalmazás regisztrálása az Active Directory-bérlőben.
- A **titkos** társított az alkalmazás azonosítója. Hozzon létre egy ügyfél titkos karakterlánc (hasonlóan a jelszóra), vagy adjon meg egy X509 (amely a nyilvános kulcs) tanúsítványt. 

Egyszerű szolgáltatás identitása alatt alkalmazás futtatása célszerű a fut. azt a felhasználó egyszerű mert:

 - Szolgáltatásnév-X509 használhatja a tanúsítvány **erősebb hitelesítő adatok**.  
 - Hozzárendelhet **több korlátozást tartalmazó engedélyekre** a szolgáltatásnévhez. Általában ezek az engedélyek korlátozottak csak az alkalmazás milyen szüksége van, más néven a *elvét*.
 - Egyszerű szolgáltatás **hitelesítő adataival és engedélyeivel nem változnak gyakran,** , felhasználói hitelesítő adatokat. Például ha módosítja a felhasználó felelőssége, jelszó igények miatt a módosítása, vagy egy felhasználó távozik a szervezettől.

Először hozzon létre egy új alkalmazás regisztrálása a címtárban, amely létrehoz egy társított [szolgáltatásnév-objektum](/azure/active-directory/develop/developer-glossary#service-principal-object) , amelyek az alkalmazás identitásának címtáron belül. Ez a dokumentum ismerteti a folyamat létrehozásának és kezelésének egy egyszerű szolgáltatást, attól függően, a címtár az Azure Stack-példány számára is választott:

- Az Azure Active Directory (Azure AD). Azure ad-ben egy több-bérlős, felhőalapú címtár, és identitáskezelési szolgáltatása. Használhatja az Azure AD a csatlakoztatott Azure Stack-példánnyal.
- Az Active Directory összevonási szolgáltatások (AD FS). Az AD FS egyszerű, biztonságos identitás-összevonási és webes egyszeri bejelentkezés (SSO) képességeket biztosít. Az AD FS csatlakoztatott és a leválasztott Azure Stack-példányokkal is használhatja.

Először bemutatja egy egyszerű szolgáltatás kezelése, majd az egyszerű szolgáltatás hozzárendelése egy szerepkört, az erőforrás-hozzáférés korlátozása.

## <a name="manage-an-azure-ad-service-principal"></a>Az Azure AD egyszerű szolgáltatás kezelése 

Ha telepítette az Azure Stack az Azure Active Directoryval (Azure AD), az identity management-szolgáltatás, létrehozhat egyszerű szolgáltatásokat, mint az Azure-ban végezhet el. Ez a szakasz bemutatja, hogyan végezheti el a lépéseket az Azure Portalon keresztül. Ellenőrizze, hogy rendelkezik a [szükséges Azure AD-engedélyekről](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) megkezdése előtt.

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>Ügyfél titkos hitelesítő adatokat használó egyszerű szolgáltatás létrehozása

Ebben a szakaszban az alkalmazás használatával az Azure Portalon, amely a szolgáltatásnév-objektumot hoz létre az Azure AD-bérlő regisztrálásához. Ebben a példában az egyszerű szolgáltatás titkos ügyfél-hitelesítőt a létrejön, de a portál is támogatja a X509 tanúsítványalapú hitelesítő adatokat.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) Azure-fiókjával.
2. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk** > **új regisztrációs**.
3. Adjon meg egy **neve** az alkalmazáshoz. 
4. Válassza ki a megfelelő **támogatott fióktípusok**.
5. Alatt **átirányítási URI-t**válassza **webes** az alkalmazás típusát, és (opcionálisan) adja meg a átirányítási URI-t, ha az alkalmazás számára szükséges. 
6. Miután beállította az értékeket, válassza ki a **regisztrálása**. Az alkalmazás regisztrációja jön létre, és a **áttekintése** lap jelenik meg.
7. Másolás a **Alkalmazásazonosító** az alkalmazás kódjában használható. Ennek az értéknek is nevezik az ügyfél-azonosító.
8. Ügyfél titkos kulcs létrehozásához válassza a **tanúsítványok és titkos kulcsok** lapot. Válassza az **Új titkos ügyfélkód** lehetőséget.
9. Adjon meg egy **leírás** számára a titkos kulcsot, és a egy **lejár** időtartama. 
10. Ha elkészült, válassza **Hozzáadás**.
11. A titkos kód jelenik meg. Másolja és mentse egy másik helyen, ezt az értéket, mivel később nem lehet beolvasni. A titkos kulcsot az alkalmazás azonosítójával adja meg az ügyfélalkalmazásban szolgáltatás egyszerű bejelentkezés során. 

    ![mentett kulcs](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>Az AD FS szolgáltatás egyszerű kezelése

Ha telepítette az Active Directory összevonási szolgáltatások (AD FS) az Azure Stack, az identity management-szolgáltatás, a PowerShell kezelheti a szolgáltatásnevet kell használnia. Példák az alábbiak kezelésére egyszerű szolgáltatás hitelesítő adatai, mindkét egy X509 bemutatására szolgáló tanúsítvány és a egy titkos ügyfélkulcsát.

A parancsfájlokat egy emelt szintű ("Futtatás rendszergazdaként") PowerShell-konzolon, ezzel megnyílik az Azure Stack-példány egy emelt szintű végpontot üzemeltető virtuális gép egy másik munkamenetben kell futtatni. A kiemelt végponthoz munkamenet létrehozása után további parancsmag hajtsa végre, és az egyszerű szolgáltatás kezelése. A kiemelt végponthoz kapcsolatos további információkért lásd: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>Egy tanúsítványalapú hitelesítő adatot használó egyszerű szolgáltatás létrehozása

Ha tanúsítványt hoz létre a egyszerű szolgáltatás hitelesítő adatait az alábbi követelményeknek kell teljesülniük:

 - A kriptográfiai szolgáltató (CSP) örökölt kulcsszolgáltató kell lennie.
 - A tanúsítvány formátuma kell lennie a PFX-fájl, mivel a nyilvános és titkos kulcsok szükségesek. Windows-kiszolgálók használata a nyilvános kulcs fájlját (SSL-tanúsítványfájlja) tartalmazó .pfx fájlok és a kapcsolódó titkos kulcs fájlját.
 - Éles környezetben a tanúsítvány egy belső hitelesítésszolgáltató vagy egy nyilvános hitelesítésszolgáltató kell kiállítani. Nyilvános hitelesítésszolgáltató használata, ha a Microsoft megbízható legfelső szintű hatóság Program részeként alap operációs rendszer lemezképét kell tartalmazza a szolgáltatót. A teljes listáját megtalálhatja [a Microsoft megbízható Root Certificate Program: Résztvevők](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Az Azure Stack-infrastruktúra a hitelesítésszolgáltató visszavont tanúsítványok listája (CRL) helyre a tanúsítványt a közzétett hálózati hozzáféréssel kell rendelkeznie. A CRL-t egy HTTP-végpontot kell lennie.

Ha már rendelkezik egy tanúsítványt, használja az alábbi PowerShell-parancsfájlt az alkalmazás regisztrálásához és egyszerű szolgáltatás létrehozása. Az egyszerű szolgáltatás Azure-bA bejelentkezni is használhatja. Cserélje le a helyőrzőket a saját értékeit:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A kiemelt jogosultságú végponton virtuális gép az Azure Stack-példány neve. | "AzS-ERCS01" |
| \<YourCertificateLocation\> | A X509 helyét a tanúsítványt a helyi tanúsítványtárolóban. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<YourAppName\> | Egy leíró nevet az új alkalmazás regisztrálása | "Saját felügyeleti eszköz" |

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő szkriptet:

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

    # Using the stamp info for your Azure Stack instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint
    Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. A szkript befejezése után az alkalmazás regisztrációs információ, beleértve a szolgáltatásnév hitelesítő adatainak jeleníti meg. Ahogyan, is a `ClientID` és `Thumbprint` jelentkezzen be a szolgáltatásnév-identitás segítségével. Sikeres bejelentkezés után a szolgáltatásnév-identitás későbbi engedélyezés és Azure Resource Manager által kezelt erőforrások eléréséhez használható. 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

A PowerShell-konzol munkamenetet megnyitva, tartsa együtt használja, mivel a `ApplicationIdentifier` érték a következő szakaszban.

### <a name="update-a-service-principals-certificate-credential"></a>A szolgáltatásnév tanúsítvány hitelesítő adat frissítése

Most, hogy létrehozott egy egyszerű szolgáltatást, ez a szakasz bemutatja, hogyan való:

1. Hozzon létre egy új önaláírt X509 tanúsítvány teszteléséhez.
2. Frissítse az egyszerű szolgáltatás hitelesítő adatait, frissítésével a **ujjlenyomat** az új tanúsítvány megfelelő tulajdonság.

Frissítés a PowerShell-lel, a helyőrzőket a saját értékei behelyettesítésével tanúsítványalapú hitelesítő adatot:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A kiemelt jogosultságú végponton virtuális gép az Azure Stack-példány neve. | "AzS-ERCS01" |
| \<YourAppName\> | Egy leíró nevet az új alkalmazás regisztrálása | "Saját felügyeleti eszköz" |
| \<YourCertificateLocation\> | A X509 helyét a tanúsítványt a helyi tanúsítványtárolóban. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | Az alkalmazásregisztráció hozzárendelt azonosító | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. A rendszergazda jogú Windows PowerShell-munkamenetet, futtassa a következő parancsmagokat:

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

2. A szkript befejezése után a frissített alkalmazás regisztrációs információ, beleértve az új önaláírt tanúsítványt a ujjlenyomat értékét jeleníti meg.

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>Ügyfél titkos hitelesítő adatokat használó egyszerű szolgáltatás létrehozása

> [!IMPORTANT]
> Az ügyfél titkos kulcs a következő kevésbé biztonságos, mint egy X509 használata tanúsítvány-hitelesítő adat. Nem csak a hitelesítési mechanizmus kevésbé biztonságos, de általában is szükséges a titkos kulcs beágyazása az ügyféloldali alkalmazás forráskódját. Mint ilyen az éles környezetben, Ön erősen javasolt egy tanúsítványalapú hitelesítő adatot.

Most hozzon létre egy másik alkalmazásregisztráció, de ezúttal meg titkos ügyfél-hitelesítőt. Egy tanúsítványalapú hitelesítő adatot, ellentétben a könyvtár képes létrehozni a titkos ügyfél-hitelesítőt. Ezért helyett adja meg az ügyfél titkos kulcsát, használható a `-GenerateClientSecret` kapcsoló kérni, hogy akkor jöjjön létre. Cserélje le a helyőrzőket a saját értékeit:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A kiemelt jogosultságú végponton virtuális gép az Azure Stack-példány neve. | "AzS-ERCS01" |
| \<YourAppName\> | Egy leíró nevet az új alkalmazás regisztrálása | "Saját felügyeleti eszköz" |

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

     # Using the stamp info for your Azure Stack instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint
     Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. A szkript befejezése után az alkalmazás regisztrációs információ, beleértve a szolgáltatásnév hitelesítő adatainak jeleníti meg. Ahogyan, is a `ClientID` létrehozott `ClientSecret` jelentkezzen be a szolgáltatásnév-identitás segítségével. Sikeres bejelentkezés után a szolgáltatásnév-identitás későbbi engedélyezés és Azure Resource Manager által kezelt erőforrások eléréséhez használható.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

A PowerShell-konzol munkamenetet megnyitva, tartsa együtt használja, mivel a `ApplicationIdentifier` érték a következő szakaszban.

### <a name="update-a-service-principals-client-secret"></a>Szolgáltatásnév ügyfél titkos kód frissítése

Az ügyfél titkos hitelesítő adat frissítése PowerShell-lel, használja a **ResetClientSecret** paraméter, amely azonnal megváltoztatja az ügyfél titkos kulcsát. Cserélje le a helyőrzőket a saját értékeit:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A kiemelt jogosultságú végponton virtuális gép az Azure Stack-példány neve. | "AzS-ERCS01" |
| \<AppIdentifier\> | Az alkalmazásregisztráció hozzárendelt azonosító | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. A rendszergazda jogú Windows PowerShell-munkamenetet, futtassa a következő parancsmagokat:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. A szkript befejezése után megjelenik a frissített alkalmazás regisztrációs információ, beleértve az újonnan létrehozott ügyfél titkos kulcsát.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
     PSComputerName        : azs-ercs01
     RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal"></a>Szolgáltatásnév eltávolítása

Most már megjelenik hogyan eltávolítása vagy törlése az alkalmazás regisztrációját a címtárban, és a hozzá társított szolgáltatás egyszerű objektum, a PowerShell használatával. 

Cserélje le a helyőrzőket a saját értékeit:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A kiemelt jogosultságú végponton virtuális gép az Azure Stack-példány neve. | "AzS-ERCS01" |
| \<AppIdentifier\> | Az alkalmazásregisztráció hozzárendelt azonosító | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

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

Nem lesznek nincs az emelt szintű végpont hívása a Remove-GraphApplication parancsmag által visszaadott kimenetet, de a konzol kimenetét szó megerősítő látni fogja a parancsmag végrehajtása során:

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>Szerepkör hozzárendelése

Felhasználók és az alkalmazások Azure-erőforrásokhoz való hozzáférés engedélyezése szerepköralapú hozzáférés-vezérlés (RBAC) révén. Ahhoz, hogy egy alkalmazás hozzáférését az erőforrásokhoz az előfizetésben, az egyszerű szolgáltatás használatával, meg kell *hozzárendelése* az egyszerű szolgáltatás egy *szerepkör* egy adott *erőforrás*. Először döntse el, melyik szerepkör jelöli jobb *engedélyek* az alkalmazáshoz. Az elérhető szerepkörök kapcsolatos további információkért lásd: [beépített szerepkörök az Azure-erőforrások](/azure/role-based-access-control/built-in-roles).

Úgy is dönt erőforrás típusa, hozza létre a *hozzáférési hatókörre* egyszerű szolgáltatás számára. A hozzáférési hatókör állíthatja be az előfizetés, erőforráscsoport vagy erőforrás szintjén. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazást egy erőforráscsoportot az "Olvasó" szerepkör hozzáadása, azt jelenti, hogy azt az erőforráscsoportot és az összes benne található erőforrást olvashatja.

1. Jelentkezzen be a megfelelő portálra, az Azure Stack könyvtárat alapján (az Azure portal Azure AD-hez vagy az AD FS Szolgáltatásokhoz, például az Azure Stack felhasználói portál) telepítése. Ebben a példában bemutatjuk, a felhasználó bejelentkezett az Azure Stack felhasználói portálon.

   > [!NOTE]
   > Szerepkör-hozzárendelését egy adott erőforrás hozzáadása, a felhasználói fiók egy deklaráló szerepkörhöz kell tartoznia a `Microsoft.Authorization/roleAssignments/write` engedéllyel. Például, vagy a [tulajdonosa](/azure/role-based-access-control/built-in-roles#owner) vagy [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator) beépített szerepkörök.  
2. Keresse meg az erőforrást szeretné, hogy a szolgáltatás egyszerű hozzáférés. Ebben a példában rendelje hozzá az egyszerű szolgáltatás egy szerepkörhöz az előfizetések szintjén kiválasztásával **előfizetések**, majd egy adott előfizetésben. Ehelyett kiválaszthatja egy erőforráscsoportot, vagy egy adott erőforrással, például egy virtuális gépet. 

     ![Válasszon hozzárendelés előfizetést](./media/azure-stack-create-service-principal/select-subscription.png)

3. Válassza ki a **hozzáférés-vezérlés (IAM)** oldal, amely univerzális el az összes olyan erőforrást, amelyek támogatják az RBAC.
4. Válassza ki **+ hozzáadása**
5. A **szerepkör**, válasszon a az alkalmazáshoz hozzárendelni kívánt szerepkört.
6. A **kiválasztása**, keresse meg az alkalmazás használatával teljes vagy részleges alkalmazás nevét. Regisztráció során az alkalmazás neve jön létre *Azurestack -\<YourAppName\>-\<ClientId\>* . Például, ha az alkalmazás nevét, a használt *App2*, és a ClientId *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff* hozzárendelésének létrehozása során, a teljes neve lesz  *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*. Kereshet a pontos karakterláncokat, vagy egy részét, például *Azurestack* vagy *Azurestack-App2*.
7. Miután megtalálta az alkalmazást, jelölje ki, és alatt megjelenik **kiválasztott tagok**.
8. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. 

     [ ![Szerepkör hozzárendelése](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. Amikor végzett, az alkalmazás és az aktuális hatókör, az adott szerepkörhöz hozzárendelt rendszerbiztonsági tagok közül a listában jelennek meg.

     [ ![Hozzárendelt szerepkör](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

Most, hogy már létrehozott egy egyszerű szolgáltatást és szerepkört rendel hozzá, megkezdheti ezzel az Azure Stack-erőforrások eléréséhez az alkalmazáson belül.  

## <a name="next-steps"></a>További lépések

[Felhasználók hozzáadása az AD FS-hez](azure-stack-add-users-adfs.md)  
[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)  
[Az Azure Active Directory dokumentációja](https://docs.microsoft.com/azure/active-directory)  
[Active Directory összevonási szolgáltatások](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
