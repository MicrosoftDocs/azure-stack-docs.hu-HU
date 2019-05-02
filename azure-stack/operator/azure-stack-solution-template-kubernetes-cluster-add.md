---
title: Adja hozzá a Kubernetes az Azure Stack piactéren |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá a Kubernetes az Azure Stack piactéren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 09fa7b503f0c594d2af0c6f16a6d4618cec0fac3
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64294432"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Adja hozzá a Kubernetes az Azure Stack piactéren

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió.

Elérhetővé teheti Kubernetes Piactéri elem, a felhasználók számára. A felhasználók számára, majd telepítheti Kubernetes egyetlen, koordinált műveletben.

A következő cikkben tekintse meg telepítése és jogosultságok kiosztása az erőforrások különálló Kubernetes-fürthöz tartozó Azure Resource Manager-sablon használatával. A Kezdés előtt ellenőrizze az Azure Stack és az Azure-bérlő globális beállításokat. Az Azure Stack a szükséges információkat gyűjt. Adja hozzá a szükséges erőforrásokat a bérlőn, és az Azure Stack piactéren. A fürt egy Ubuntu-kiszolgálót, egyéni parancsfájlt és a Kubernetes-fürt Piactéri elem kell lennie a Marketplace-en függ.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Csomag, ajánlat és előfizetés létrehozása

Hozzon létre egy csomag, ajánlat és a Kubernetes Piactéri elem előfizetést. Megadhatja egy meglévő csomagot használja, és elérhetővé teheti.

1. Jelentkezzen be a [felügyeleti portálján.](https://adminportal.local.azurestack.external)

1. Hozzon létre egy csomag az alapszintű csomag. Útmutatásért lásd: [csomag létrehozása az Azure Stack](azure-stack-create-plan.md).

1. Hozzon létre egy ajánlatot. Útmutatásért lásd: [ajánlat létrehozása az Azure Stack](azure-stack-create-offer.md).

1. Válassza ki **kínál**, és keresse meg a létrehozott ajánlat.

1. Válassza ki **áttekintése** az ajánlat panelen.

1. Válassza ki **Állapotváltozáshoz**. Válassza ki **nyilvános**.

1. Válassza ki **+ erőforrás létrehozása** > **ajánlatok és csomagok** > **előfizetés** -előfizetés létrehozása.

    a. Adjon meg egy **megjelenítendő név**.

    b. Adjon meg egy **felhasználói**. A tenanthoz társított Azure AD-fiókot használni.

    c. **Társzolgáltató leírása**

    d. Állítsa be a **címtárbérlő** az Azure stack az Azure AD-bérlőhöz. 

    e. Válassza ki **ajánlat**. Válassza ki a létrehozott ajánlat nevét. Jegyezze fel az előfizetés-azonosító.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Hozzon létre egy egyszerű szolgáltatást, és a hitelesítő adatokat az AD FS-ben

Ha az Active Directory összevonási szolgáltatásokban (AD FS) az identity management szolgáltatás használja, szüksége lesz a létre szolgáltatásnevet a Kubernetes-fürt üzembe helyezése felhasználók számára.

1. Hozzon létre, és a szolgáltatásnév létrehozásához használt önaláírt tanúsítvány exportálása. 

    - Az alábbi adatokra lesz szüksége:

       | Érték | Leírás |
       | ---   | ---         |
       | Jelszó | Adja meg egy új jelszót a tanúsítványhoz. |
       | Helyi tanúsítvány elérési útja | Adja meg a tanúsítvány elérési útja és fájlneve. Például:`c:\certfilename.pfx` |
       | Tanúsítvány neve | Adja meg a tanúsítvány nevét jelöli. |
       | Tanúsítványtár helye |  Például: `Cert:\LocalMachine\My` |

    - Nyisson meg egy rendszergazda jogú parancssorba PowerShell. Futtassa a következő szkriptet a paraméterekkel az értékek a frissített:

        ```powershell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "CN=<certificate name>"
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2.  Jegyezze fel az új tanúsítvány azonosító jelenik meg a PowerShell-munkamenetet a `1C2ED76081405F14747DC3B5F76BB1D83227D824`. Az azonosító használható az egyszerű szolgáltatás létrehozásakor.

    ```powershell  
    VERBOSE: Generated new certificate 'CN=<certificate name>' (1C2ED76081405F14747DC3B5F76BB1D83227D824).
    ```

3. Szolgáltatásnév létrehozása a tanúsítvány használatával.

    - Az alábbi adatokra lesz szüksége:

       | Érték | Leírás                     |
       | ---   | ---                             |
       | ERCS IP | A ASDK a kiemelt végponthoz van általában `AzS-ERCS01`. |
       | Alkalmazásnév | Adja meg az egyszerű szolgáltatás egyszerű nevét. |
       | Tanúsítványtár helye | A számítógépen, a tanúsítványt tároló elérési útja. Ez jelzi a tárolási helynek, és a tanúsítvány Azonosítóját az első lépésben létrehozott. Például:`Cert:\LocalMachine\My\1C2ED76081405F14747DC3B5F76BB1D83227D824` |

       Amikor a rendszer kéri, használja a következő hitelesítő adatok a jogosultság végponthoz csatlakozik. 
        - Felhasználónév: Adja meg a CloudAdmin fiók formátumban `<Azure Stack domain>\cloudadmin`. (ASDK, a felhasználói név azurestack\cloudadmin.)
        - Jelszó: Adja meg ugyanazt a jelszót a Azurestack tartományi rendszergazdai fiók a telepítés során megadott.

    - Futtassa a következő szkriptet a paraméterekkel az értékek a frissített:

        ```powershell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        $certStoreLocation="<certificate location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

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
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - A szolgáltatás egyszerű részleteket tekintse meg az alábbi kódrészlethez hasonló

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Egy Ubuntu server-rendszerkép hozzáadása

Adja hozzá a következő Ubuntu Server-lemezképet a Marketplace-en:

1. Jelentkezzen be a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás**, majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `Ubuntu Server` (igen) kifejezést.

1. Válassza ki a kiszolgálót a legújabb verziója. A teljes verziószám, és győződjön meg arról, hogy a legújabb verzióval rendelkezik:
    - **Közzétevő**: Canonical
    - **Ajánlat**: UbuntuServer
    - **Verzió**: 16.04.201806120 (vagy a legújabb verzió)
    - **TERMÉKVÁLTOZAT**: 16.04-LTS

1. Válassza ki **letöltése.**

## <a name="add-a-custom-script-for-linux"></a>Adjon hozzá egy egyéni parancsfájl linuxhoz

Adja hozzá a Kubernetes a Marketplace-ről:

1. Nyissa meg a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás** , majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `Custom Script for Linux` (igen) kifejezést.

1. Válassza ki a parancsprogramot, a következő profilhoz:
   - **Ajánlat**: Egyéni parancsfájl 2.0 linuxhoz
   - **Verzió**: 2.0.6-os (vagy a legújabb verzió)
   - **Közzétevő**: Microsoft Corp

     > [!Note]  
     > Egyéni parancsfájl Linux több verziója is szerepelhet. Adja hozzá a legfrissebb verziója, az elem kell.

1. Válassza ki **letöltése.**


## <a name="add-kubernetes-to-the-marketplace"></a>Kubernetes felvétele a Marketplace-en

1. Nyissa meg a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás** , majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `Kubernetes` (igen) kifejezést.

1. Válassza a(z) `Kubernetes Cluster` lehetőséget.

1. Válassza ki **letöltése.**

    > [!note]  
    > A Piactéri elem jelenik meg a Marketplace-en öt percig is eltarthat.

    ![Kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Frissítheti vagy eltávolíthatja a Kubernetes 

Amikor frissíti a Kubernetes elem, akkor eltávolítja az előző elemet a Marketplace-en. Az útmutatást követve ebben a cikkben a Kubernetes frissítése a Marketplace-en való hozzáadásához.

A Kubernetes-elem eltávolítása:

1. Csatlakozhat az Azure Stack PowerShell-kezelőként. További útmutatást lásd: [csatlakozhat az Azure Stack a PowerShell-lel kezelőként](azure-stack-powershell-configure-admin.md).

2. Keresse meg az aktuális Kubernetes-fürt elemet a katalógusban.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Vegye figyelembe például az aktuális elem neve `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. A következő PowerShell-parancsmag segítségével eltávolítja az elemet:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>További lépések

[A Kubernetes az Azure Stack üzembe helyezése](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Az Azure Stackben szolgáltatásajánlások áttekintése](azure-stack-offer-services-overview.md)
