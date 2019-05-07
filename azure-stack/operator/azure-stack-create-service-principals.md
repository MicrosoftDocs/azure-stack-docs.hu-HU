---
title: Kezelheti egy egyszerű szolgáltatást az Azure Stackhez |} A Microsoft Docs
description: Egy új egyszerű szolgáltatást, amely a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager-erőforrásokhoz való hozzáférés kezelésére használható kezelését ismerteti.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 8d6548ada50a25350f622d7bc7460005f4abc401
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64295125"
---
# <a name="provide-applications-access-to-azure-stack"></a>Hozzáférést biztosít az alkalmazásoknak az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Amikor egy alkalmazás központi telepítése, illetve az Azure Resource Manager erőforrások konfigurálása az Azure Stackben hozzáférésre van szüksége, hozzon létre egy egyszerű szolgáltatás, amely az alkalmazás hitelesítő adatot. Ezután delegálhat egyszerű szolgáltatást csak a szükséges engedélyekkel.  

Tegyük fel szükség lehet egy Konfigurációkezelő eszközzel, amely a készlet az Azure-erőforrások Azure Resource Manager használatával. Ebben a forgatókönyvben egyszerű szolgáltatás létrehozása, az Olvasó szerepkör biztosítani az egyszerű szolgáltatást, és korlátozhatja a csak olvasási hozzáféréssel az eszközbe. 

Szolgáltatásnevek használata előnyösebb az alkalmazást a saját hitelesítő adatokkal futtatja, mivel:

 - Engedélyeket rendelhet a szolgáltatás egyszerű, amelyek eltérnek a saját fiók engedélyeit. Ezek az engedélyek jellemzően csak azt engedélyezik, amire az alkalmazásnak szüksége van.
 - Nem kell módosítani az alkalmazás hitelesítő adatait, ha az Ön feladatkörei módosítása.
 - A tanúsítvány segítségével automatizálhatja a hitelesítést egy felügyelet nélküli parancsfájl végrehajtása közben.  

## <a name="getting-started"></a>Első lépések

Attól függően, hogyan telepített Azure Stack első lépésként létrehozni egy szolgáltatásnevet. Ez a dokumentum ismerteti, hogy a szolgáltatásnév létrehozásához:

- Az Azure Active Directory (Azure AD). Azure ad-ben egy több-bérlős, felhőalapú címtár, és identitáskezelési szolgáltatása. Használhatja az Azure AD a csatlakoztatott Azure Stack segítségével.
- Az Active Directory összevonási szolgáltatások (AD FS). Az AD FS egyszerű, biztonságos identitás-összevonási és webes egyszeri bejelentkezés (SSO) képességeket biztosít. Az AD FS csatlakoztatott és a leválasztott Azure Stack-példányokkal is használhatja.

Az egyszerű szolgáltatás létrehozása után az AD FS és az Azure Active Directory közös ismertetett lépések segítségével a szerepkör engedélyeket delegálhatnak.

## <a name="manage-service-principal-for-azure-ad"></a>Az Azure ad egyszerű szolgáltatás kezelése

Ha helyezte az Azure Stack az Azure Active Directoryval (Azure AD), az identity management-szolgáltatás, létrehozhat egyszerű szolgáltatásokat, mint az Azure-ban végezhet el. Ez a szakasz bemutatja, hogyan végezheti el a lépéseket a portálon keresztül. Ellenőrizze, hogy rendelkezik az [Azure ad-ben permissions]((/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) megkezdése előtt szükséges.

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ebben a szakaszban az alkalmazást képviselő Azure AD-alkalmazásokhoz (egyszerű szolgáltatásnevének) létrehozása.

1. Jelentkezzen be az Azure-fiók révén a [az Azure portal](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **alkalmazásregisztrációk** > **új alkalmazás regisztrálása**
3. Adja meg az alkalmazás nevét és URL-címét. Válassza ki vagy **webalkalmazás / API** vagy **natív** szeretne létrehozni az alkalmazás számára. Miután beállította az értékeket, válassza ki a **létrehozás**.

Létrehozott egy egyszerű szolgáltatást az alkalmazás.

### <a name="get-credentials"></a>Hitelesítő adatok beolvasása

Ha programozott módon jelentkezik be, ezt az Azonosítót használja az alkalmazáshoz, és a egy webalkalmazás / API-t, a hitelesítési kulcs. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. A **alkalmazásregisztrációk** az Active Directoryban, válassza ki az alkalmazását.

2. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában. A mintául szolgáló alkalmazások szakaszban az alkalmazások ügyfél-azonosítóként hivatkoznak ezt az értéket

     ![Ügyfélazonosító](./media/azure-stack-create-service-principal/image12.png)
3. Webes alkalmazás a hitelesítési kulcs létrehozásához / API-t, jelölje ki **beállítások** > **kulcsok**. 

4. Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.

A kulcs mentése után megjelenik a kulcs értéke. Másolja a Jegyzettömbbe vagy egy másik ideiglenes helyre ezt az értéket, mivel később nem tudja lekérni a kulcsot. A kulcs értékét az alkalmazás aláírásához az alkalmazás azonosítójával adja meg. Store a kulcsértéket olyan helyen, ahol az alkalmazás le tudja kérni.

![mentett kulcs](./media/azure-stack-create-service-principal/image15.png)

Ha elkészült, hozzárendelheti egy szerepkörhöz az alkalmazás.

## <a name="manage-service-principal-for-ad-fs"></a>Az AD FS szolgáltatás egyszerű kezelése

Ha telepítette az Active Directory összevonási szolgáltatások (AD FS) az Azure Stack, az identity management-szolgáltatás, PowerShell segítségével hozzon létre egy egyszerű szolgáltatást, a hozzáféréshez szerepkör hozzárendelése, és jelentkezzen be az identitásukat.

A szolgáltatásnév létrehozásához az AD FS-sel két módszer egyikét használhatja. A következőket teheti:
 - [Egy tanúsítvány használatával egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Ügyfél titkos kulcs használatával egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Feladatok kezelése az AD FS szolgáltatás rendszerbiztonsági tagok.

| Típus | Műveletek |
| --- | --- |
| Az AD FS-tanúsítványt | [Létrehozás](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Az AD FS-tanúsítványt | [Update](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| Az AD FS-tanúsítványt | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Az AD FS titkos Ügyfélkód | [Létrehozás](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Az AD FS titkos Ügyfélkód | [Update](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Az AD FS titkos Ügyfélkód | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Egy tanúsítvány használatával egyszerű szolgáltatás létrehozása

Egyszerű szolgáltatás létrehozása során az AD FS-identitás, amikor egy tanúsítvány is használható.

#### <a name="certificate"></a>Tanúsítvány

Egy tanúsítványra szükség.

**Tanúsítványkövetelmények**

 - A kriptográfiai szolgáltató (CSP) örökölt kulcsszolgáltató kell lennie.
 - A tanúsítvány formátuma kell lennie a PFX-fájl, mivel a nyilvános és titkos kulcsok szükségesek. Windows-kiszolgálók használata a nyilvános kulcs fájlját (SSL-tanúsítványfájlja) tartalmazó .pfx fájlok és a kapcsolódó titkos kulcs fájlját.
 - Éles környezetben a tanúsítvány egy belső hitelesítésszolgáltató vagy egy nyilvános hitelesítésszolgáltató kell kiállítani. Nyilvános hitelesítésszolgáltató használata, ha a Microsoft megbízható legfelső szintű hatóság Program részeként alap operációs rendszer lemezképét kell tartalmazza a szolgáltatót. A teljes listáját megtalálhatja [a Microsoft megbízható Root Certificate Program: Résztvevők](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Az Azure Stack-infrastruktúra a hitelesítésszolgáltató visszavont tanúsítványok listája (CRL) helyre a tanúsítványt a közzétett hálózati hozzáféréssel kell rendelkeznie. A CRL-t egy HTTP-végpontot kell lennie.

#### <a name="parameters"></a>Paraméterek

Az alábbi adatokra szükség az automation-paraméterek bemenetként:

|Paraméter|Leírás|Példa|
|---------|---------|---------|
|Name (Név)|Az SPN-fiók nevét|MyAPP|
|ClientCertificates|Tanúsítvány-objektumok tömbje|X509 tanúsítvány|
|ClientRedirectUris<br>(Választható lehetőség)|Alkalmazás átirányítási URI-ja|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása a PowerShell használatával

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > Érvényesítési jellegű önaláírt tanúsítvány használatával hozható létre az alábbi példában:

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Az automation befejezését követően a szükséges adatokat, az egyszerű szolgáltatásnév használatával jeleníti meg. Javasoljuk, hogy a kimenetet későbbi használatra.

   Példa:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Az egyszerű szolgáltatás tanúsítványának frissítése az AD FS-hez

Ha telepítette az AD FS az Azure Stack, a PowerShell használatával frissíteni az egyszerű szolgáltatás titkos kulcsát.

A parancsfájl-ERCS virtuális gépen fut az emelt szintű végpontról.

#### <a name="parameters"></a>Paraméterek

Az alábbi adatokra szükség az automation-paraméterek bemenetként:

|Paraméter|Leírás|Példa|
|---------|---------|---------|
|Name (Név)|Az SPN-fiók nevét|MyAPP|
|ApplicationIdentifier|Egyedi azonosító|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Tanúsítvány-objektumok tömbje|X509 tanúsítvány|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Az AD FS egyszerű szolgáltatás frissítése – példa

A példa létrehoz egy önaláírt tanúsítványt. A parancsmagok futtatásakor éles környezetben használja [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) a tanúsítványobjektumot is használni szeretné a tanúsítvány lekéréséhez.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Az automation befejezését követően az SPN-hitelesítéshez szükséges frissített ujjlenyomat értékét jeleníti meg.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Ügyfél titkos kulcs használatával egyszerű szolgáltatás létrehozása

Egyszerű szolgáltatás létrehozása során az AD FS-identitás, amikor egy tanúsítvány is használható. A kiemelt jogosultságú végpont-parancsmagok futtatásához használandó.

Ezek a szkriptek a kiemelt végponthoz-ERCS virtuális gépeken futnak. A kiemelt jogosultságú végpontot kapcsolatos további információkért lásd: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).

#### <a name="parameters"></a>Paraméterek

Az alábbi adatokra szükség az automation-paraméterek bemenetként:

| Paraméter | Leírás | Példa |
|----------------------|--------------------------|---------|
| Name (Név) | Az SPN-fiók nevét | MyAPP |
| GenerateClientSecret | Titkos kód létrehozása |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Az egyszerű szolgáltatás létrehozásához használja a ERCS PrivilegedEndpoint

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Parancsmagok futtatása után a rendszerhéj használata az egyszerű Szolgáltatásnevet a szükséges adatokat jeleníti meg. Ellenőrizze, hogy a titkos ügyfélkulcsot tárolja.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Az egyszerű szolgáltatás titkos Ügyfélkód frissítése az AD FS-hez

Egy új titkos ügyfélkulcsot a PowerShell-parancsmag által létrehozott automatikus.

A parancsfájl-ERCS virtuális gépen fut az emelt szintű végpontról.

##### <a name="parameters"></a>Paraméterek

Az alábbi adatokra szükség az automation-paraméterek bemenetként:

| Paraméter | Leírás | Példa |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Az egyedi azonosítója. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | A titkos ügyfélkulcsot 2880 perc alatt, ahol a régi titkos kulcs még mindig érvényes helyettesítő időszakkal változik. |  |
| ResetClientSecret | A titkos ügyfélkulcsot azonnal módosítása |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Az AD FS ügyfélkódot frissítés – példa

A példában a **ResetClientSecret** paraméter, amely azonnal megváltoztatja az ügyfél titkos kulcsát.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Az automation befejezését követően az újonnan létrehozott titkos SPN-hitelesítéshez szükséges jeleníti meg. Ellenőrizze, hogy az új titkos ügyfélkulcsot tárolja.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Távolítsa el az AD FS egyszerű szolgáltatás

Ha telepítette az AD FS az Azure Stack, a PowerShell használatával egy egyszerű szolgáltatás törlése.

A parancsfájl-ERCS virtuális gépen fut az emelt szintű végpontról.

#### <a name="parameters"></a>Paraméterek

Az alábbi adatokra szükség az automation-paraméterek bemenetként:

|Paraméter|Leírás|Példa|
|---------|---------|---------|
| Paraméter | Leírás | Példa |
| ApplicationIdentifier | Egyedi azonosító | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Az összes meglévő egyszerű szolgáltatásokat és azok alkalmazásazonosító listájának megtekintéséhez, a get-graphapplication parancs is használható.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Az AD FS egyszerű szolgáltatás eltávolítása – példa

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Szerepkör hozzárendelése

Az előfizetésben lévő erőforrások eléréséhez, hozzá kell rendelnie az alkalmazás egy szerepkörhöz. Döntse el, melyik szerepkör jelöli az alkalmazást a megfelelő engedélyekkel. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: A beépített Roles]((/azure/role-based-access-control/built-in-roles).

Beállíthatja a hatókör szintjén is az előfizetés, erőforráscsoport vagy erőforrás. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazás az Olvasó szerepkörhöz, egy erőforráscsoport hozzáadása azt jelenti, hogy azt az erőforráscsoportot és az összes benne található erőforrást olvashatja.

1. Az Azure Stack portálon lépjen a hatókör az alkalmazást hozzárendelni kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **előfizetések**. Ehelyett kiválaszthatja egy erőforráscsoportra vagy erőforrásra.

2. Válassza ki az adott előfizetés (erőforráscsoportra vagy erőforrásra) az alkalmazás hozzárendelése a.

     ![Válasszon hozzárendelés előfizetést](./media/azure-stack-create-service-principal/image16.png)

3. Válassza ki **hozzáférés-vezérlés (IAM)**.

     ![Jelölje be a hozzáférés](./media/azure-stack-create-service-principal/image17.png)

4. Válassza ki **szerepkör-hozzárendelés hozzáadása**.

5. Válassza ki a az alkalmazáshoz hozzárendelni kívánt szerepkört.

6. Keresse meg az alkalmazást, és válassza ki azt.

7. Válassza ki **OK** befejeződik, a szerepkör hozzárendelése. Láthatja, hogy az alkalmazás a felhasználók az adott hatókörnél szerepköre a listában.

Most, hogy már létrehozott egy egyszerű szolgáltatást és szerepkört rendel hozzá, megkezdheti ezzel az Azure Stack-erőforrások eléréséhez az alkalmazáson belül.  

## <a name="next-steps"></a>További lépések

[Felhasználók hozzáadása az AD FS-hez](azure-stack-add-users-adfs.md)  
[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)  
[Az Azure Active Directory dokumentációja](https://docs.microsoft.com/azure/active-directory)  
[Active Directory összevonási szolgáltatások](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
