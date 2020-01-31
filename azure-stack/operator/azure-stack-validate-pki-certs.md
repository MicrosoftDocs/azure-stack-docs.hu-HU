---
title: Azure Stack hub nyilvános kulcsú infrastruktúra-tanúsítványok ellenőrzése Azure Stack hub integrált rendszerek telepítéséhez
description: Ismerteti, hogyan ellenőrizhető a Azure Stack hub PKI-tanúsítványai Azure Stack hub integrált rendszerekhez. Az Azure Stack hub tanúsítvány-ellenőrzési eszközének használatát ismerteti.
author: ihenkel
ms.topic: article
ms.date: 07/23/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 8ade18f01f9d0636e3a5903307ee9513c44470f7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882596"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>Azure Stack hub PKI-tanúsítványok ellenőrzése

A jelen cikkben ismertetett Azure Stack hub Readiness ellenőrző eszköz [a PowerShell-Galéria](https://aka.ms/AzsReadinessChecker)érhető el. Az eszközzel ellenőrizheti, hogy a [GENERÁLT PKI-tanúsítványok](azure-stack-get-pki-certs.md) megfelelőek-e az előzetes központi telepítéshez. A tanúsítványok érvényesítéséhez elegendő időt kell hagyni a tanúsítványok tesztelésére és kikiadására, ha szükséges.

A készenléti ellenőrző eszköz a következő tanúsítványokat hajtja végre:

- **PFX elemzése**  
    Ellenőrzi, hogy érvényes-e a PFX-fájl, a helyes jelszó, valamint azt, hogy a nyilvános adatokat a jelszó védi-e. 
- **Lejárat dátuma**  
    Ellenőrzi a minimális érvényességet 7 nap. 
- **Aláírási algoritmus**  
    Ellenőrzi, hogy az aláírási algoritmus nem SHA1-e.
- **Titkos kulcs**  
    Ellenőrzi, hogy a titkos kulcs megtalálható-e, és a helyi gép attribútummal van-e exportálva. 
- **Tanúsítvány lánca**  
    Ellenőrzi a tanúsítványlánc épségét, beleértve az önaláírt tanúsítványok ellenőrzését is.
- **DNS-nevek**  
    Ellenőrzi, hogy a SAN tartalmazza-e az egyes végpontok releváns DNS-neveit, illetve hogy van-e egy támogató helyettesítő karakter.
- **Kulcshasználat**  
    Ellenőrzi, hogy a kulcshasználat tartalmazza-e a digitális aláírást és a kulcs titkosítási, és a kibővített kulcshasználat kiszolgálói hitelesítést és ügyfél-hitelesítést tartalmaz.
- **Kulcs mérete**  
    Ellenőrzi, hogy a kulcs mérete 2048 vagy nagyobb.
- **Lánc sorrendje**  
    Ellenőrzi a többi tanúsítvány sorrendjét, hogy a megrendelés helyes-e.
- **Egyéb tanúsítványok**  
    Győződjön meg arról, hogy a megfelelő levél-tanúsítvány és a lánca nem tartalmaz más tanúsítványokat a PFX-ben.

> [!IMPORTANT]  
> A PKI-tanúsítvány egy PFX-fájl, és a jelszót bizalmas információként kell kezelni.

## <a name="prerequisites"></a>Előfeltételek

A rendszernek meg kell felelnie a következő előfeltételeknek, mielőtt ellenőrzi a PKI-tanúsítványokat egy Azure Stack hub központi telepítéshez:

- Microsoft Azure Stack hub Readiness-ellenőrző
- Az [előkészítési útmutatást](azure-stack-prepare-pki-certs.md) követően exportált SSL-tanúsítvány (ok)
- DeploymentData. JSON
- Windows 10 vagy Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Alapszintű szolgáltatások tanúsítványának ellenőrzése

Ezekkel a lépésekkel előkészítheti és érvényesítheti az Azure Stack hub PKI-tanúsítványait az üzembe helyezéshez és a titkos kulcsok elforgatásához:

1. Telepítse a **AzsReadinessChecker** -t egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Hozza létre a tanúsítvány könyvtárának struktúráját. Az alábbi példában megváltoztathatja a `<C:\Certificates\Deployment>`t az Ön által választott új könyvtár elérési útjára.
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > A AD FS és a Graph használata akkor szükséges, ha az Ön identitási rendszereként AD FS használ. Példa:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Helyezze a tanúsítvány (oka) t az előző lépésben létrehozott megfelelő címtárakba. Példa:  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. A PowerShell ablakban módosítsa a **RegionName** és a **teljes tartománynév** értékét a Azure stack hub-környezetnek megfelelően, és futtassa a következőt:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsCertificateValidation -CertificateType Deployment -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Győződjön meg arról, hogy a kimenet és az összes tanúsítvány megfelel az összes tesztnek. Példa:

    ```powershell
    Invoke-AzsCertificateValidation v1.1912.1082.37 started.
    Testing: KeyVaultInternal\adminvault.pfx
    Thumbprint: B1CB76****************************565B99
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: ARM Public\management.pfx
    Thumbprint: 44A35E****************************36052A
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: Admin Portal\adminportal.pfx
    Thumbprint: 3F5E81****************************9EBF9A
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: Public Portal\portal.pfx

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

    Más Azure Stack hub-szolgáltatások tanúsítványainak érvényesítéséhez módosítsa ```-CertificateType```értékét. Példa:

    ```powershell  
    # App Services
    Invoke-AzsCertificateValidation -CertificateType AppServices -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsCertificateValidation -CertificateType DBAdapter -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHubs
    Invoke-AzsCertificateValidation -CertificateType EventHubs -CertificatePath C:\Certificates\EventHubs -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsCertificateValidation -CertificateType IoTHub -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
Minden mappának tartalmaznia kell egy PFX-fájlt a tanúsítvány típusához, ha a tanúsítvány típusa több tanúsítványra vonatkozó követelményt tartalmaz, az egyes tanúsítványokhoz beágyazott mappák szükségesek, és megkülönbözteti a nevet.  A következő kód egy példaként szolgáló mappát/tanúsítványt mutat be az összes tanúsítvány típusához, valamint a ```-CertificateType``` és ```-CertificatePath```megfelelő értékét.
    
    ```powershell  
    C:\>tree c:\SecretStore /A /F
        Folder PATH listing
        Volume serial number is 85AE-DF2E
        C:\SECRETSTORE
        \---AzureStack
            +---CertificateRequests
            \---Certificates
                +---AppServices         # Invoke-AzsCertificateValidation `
                |   +---API             #     -CertificateType AppServices `
                |   |       API. pfx #-CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc. pfx
                |   |
                |   +---Identitás
                |   |       SSO. pfx
                |   |
                |   \-– közzététel
                |           FTP. pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob. pfx #-CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue. pfx
               ./. ./. ./. ./. ./. ./. ./.    <- Deployment certificate tree trimmed.
                |   \---Public Portal
                |           portal.pfx
                |
                +---EventHubs           # Invoke-AzsCertificateValidation `
                |       eventhubs.pfx   #   -CertificateType EventHubs `
                |                       #   -CertificatePath C:\Certificates\EventHubs
                |
                \---IoTHub              # Invoke-AzsCertificateValidation `
                        iothub.pfx      #   -CertificateType IoTHub `
                                        #   -CertificatePath C:\Certificates\IoTHub
    ```
### <a name="known-issues"></a>Ismert problémák

**Tünet**: a tesztek kimaradnak

**OK**: a AzsReadinessChecker kihagy bizonyos teszteket, ha függőség nem teljesül:

 - A rendszer kihagyja a többi tanúsítványt, ha a tanúsítványlánc meghiúsul.

    ```powershell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Megoldás**: kövesse az eszköz útmutatását a Részletek szakaszban az egyes tanúsítványokhoz tartozó tesztek alapján.

## <a name="certificates"></a>Tanúsítványok

| Könyvtár | Tanúsítvány |
| ---    | ----        |
| acsBlob | wildcard_blob_\<régió > _\<externalFQDN > |
| ACSQueue  |  wildcard_queue_\<régió > _\<externalFQDN > |
| ACSTable  |  wildcard_table_\<régió > _\<externalFQDN > |
| Felügyeleti bővítmény gazdagépe  |  wildcard_adminhosting_\<régió > _\<externalFQDN > |
| Felügyeleti portál  |  adminportal_\<régió > _\<externalFQDN > |
| ARM-rendszergazda  |  adminmanagement_\<régió > _\<externalFQDN > |
| ARM nyilvános  |  management_\<régió > _\<externalFQDN > |
| KeyVault  |  wildcard_vault_\<régió > _\<externalFQDN > |
| KeyVaultInternal  |  wildcard_adminvault_\<régió > _\<externalFQDN > |
| Nyilvános kiterjesztésű gazdagép  |  wildcard_hosting_\<régió > _\<externalFQDN > |
| Nyilvános portál  |  portal_\<régió > _\<externalFQDN > |

## <a name="using-validated-certificates"></a>Ellenőrzött tanúsítványok használata

Miután a AzsReadinessChecker érvényesítette a tanúsítványokat, készen áll arra, hogy a Azure Stack hub üzemelő példányában vagy Azure Stack hub Secret rotációs szolgáltatásban is használhassa őket. 

 - Az üzembe helyezéshez biztonságosan vigye át a tanúsítványokat a telepítési mérnökbe, hogy azok a [Azure stack hub PKI-követelmények dokumentációjában](azure-stack-pki-certs.md)megadott módon másolja őket a központi telepítési gazdagépre.
 - A Secret rotációs szolgáltatásban a tanúsítványok segítségével frissítheti az Azure Stack hub-környezet nyilvános infrastruktúra-végpontjának régi tanúsítványait az [Azure stack hub Secret rotációs dokumentációjának](azure-stack-rotate-secrets.md)követésével.
 - A Pásti-szolgáltatások esetében a tanúsítványok segítségével telepítheti az SQL, a MySQL és a App Services erőforrás-szolgáltatót Azure Stack hub-ban az [Azure stack hub dokumentációjában elérhető szolgáltatások áttekintését](service-plan-offer-subscription-overview.md)követve.

## <a name="next-steps"></a>Következő lépések

[Adatközpont identitásának integrációja](azure-stack-integrate-identity.md)
