---
title: Az Azure Stack Hub PKI-tanúsítványok ellenőrzése
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan érvényesítheti Azure Stack hub integrált rendszerek PKI-tanúsítványait az Azure Stack hub Readiness-ellenőrző eszköz használatával.
services: azure-stack
documentationcenter: ''
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 10/19/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: d260c8486090dbe94931c2527102c06cf4b98314
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761651"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>Az Azure Stack Hub PKI-tanúsítványok ellenőrzése

A jelen cikkben ismertetett Azure Stack hub Readiness ellenőrző eszköz [a PowerShell-Galéria](https://aka.ms/AzsReadinessChecker)érhető el. Az eszközzel ellenőrizheti, hogy a [generált nyilvános kulcsokra épülő infrastruktúra (PKI) tanúsítványai](azure-stack-get-pki-certs.md) megfelelőek-e az üzembe helyezés előtt. A tanúsítványok érvényesítéséhez elegendő időt kell hagyni a tanúsítványok tesztelésére és kikiadására, ha szükséges.

A készenléti ellenőrző eszköz a következő tanúsítványokat hajtja végre:

- **PFX elemzése**  
    Ellenőrzi, hogy érvényes-e a PFX-fájl, a helyes jelszó, valamint azt, hogy a nyilvános adatokat a jelszó védi-e.
- **A lejárat dátuma**  
    A minimális érvényességet ellenőrzi hét nap alatt.
- **Aláírási algoritmus**  
    Ellenőrzi, hogy az aláírási algoritmus nem SHA1-e.
- **Titkos kulcs**  
    Ellenőrzi, hogy a titkos kulcs megtalálható-e, és a helyi gép attribútummal van-e exportálva. 
- **Tanúsítvány lánca**  
    Ellenőrzi a tanúsítványlánc épségét, beleértve az önaláírt tanúsítványok ellenőrzését is.
- **DNS-nevek**  
    Ellenőrzi, hogy a SAN tartalmazza-e az egyes végpontok releváns DNS-neveit, illetve hogy van-e egy támogató helyettesítő karakter.
- **Kulcshasználat**  
    Ellenőrzi, hogy a kulcshasználat tartalmazza-e a digitális aláírást és a kulcs titkosítási, valamint ellenőrzi, hogy a kibővített kulcshasználat kiszolgáló-és ügyfél-hitelesítést tartalmaz-e.
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

- Microsoft Azure Stack hub Readiness-ellenőrzője.
- Az [előkészítési útmutatást](azure-stack-prepare-pki-certs.md)követően exportált SSL-tanúsítvány (ok).
- DeploymentData.jsbekapcsolva.
- Windows 10 vagy Windows Server 2016.

## <a name="perform-core-services-certificate-validation"></a>Az alapvető szolgáltatások tanúsítványainak ellenőrzése

Ezekkel a lépésekkel ellenőrizheti a Azure Stack hub PKI-tanúsítványait az üzembe helyezéshez és a titkos kulcsok elforgatásához:

1. Telepítse a **AzsReadinessChecker** -t egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```

2. Hozza létre a tanúsítvány könyvtárának struktúráját. Az alábbi példában megváltoztathatja a `<C:\Certificates\Deployment>` kívánt új könyvtár elérési útját.
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS és gráfra akkor van szükség, ha az identitásrendszer AD FS használja. Például:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Helyezze a tanúsítvány (oka) t az előző lépésben létrehozott megfelelő címtárakba. Például:  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. A PowerShell ablakban módosítsa a (z) `RegionName` és az Azure stack hub-környezet értékeit, `FQDN` `IdentitySystem` és futtassa a következő parancsmagot:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsHubDeploymentCertificateValidation -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Ellenőrizze a kimenetet, és győződjön meg arról, hogy minden tanúsítvány megfelel az összes tesztnek. Például:

    ```powershell
    Invoke-AzsHubDeploymentCertificateValidation v1.2005.1286.272 started.
    Testing: KeyVaultInternal\KeyVaultInternal.pfx
    Thumbprint: E86699****************************4617D6
        PFX Encryption: OK
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
    Testing: ARM Public\ARMPublic.pfx
    Thumbprint: 8DC4D9****************************69DBAA
        PFX Encryption: OK
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
    Testing: Admin Portal\AdminPortal.pfx
    Thumbprint: 6F9055****************************4AC0EA
        PFX Encryption: OK
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
    Testing: Public Portal\PublicPortal.pfx


    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsHubDeploymentCertificateValidation Completed

    ```

    Ha más Azure Stack hub-szolgáltatásokhoz szeretne tanúsítványokat érvényesíteni, módosítsa a értékét ```-CertificateType``` . Például:

    ```powershell  
    # App Services
    Invoke-AzsHubAppServicesCertificateValidation -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsHubDBAdapterCertificateValidation -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHubs
    Invoke-AzsHubEventHubsCertificateValidation -CertificatePath C:\Certificates\EventHubs -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsHubIoTHubCertificateValidation -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
    Minden mappának tartalmaznia kell egyetlen PFX-fájlt a tanúsítvány típusához. Ha a tanúsítvány típusa több tanúsítványra vonatkozó követelmény, az egyes tanúsítványokhoz tartozó beágyazott mappák várhatóak és megkülönböztetik a nevet. A következő kód egy példaként szolgáló mappát/tanúsítványt mutat be az összes tanúsítvány típusához, valamint a megfelelő értéket a és a esetében ```-CertificateType``` ```-CertificatePath``` .
    
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
                |   |       api.pfx     #     -CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc.pfx
                |   |
                |   +---Identity
                |   |       sso.pfx
                |   |
                |   \---Publishing
                |           ftp.pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob.pfx #   -CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue.pfx
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

**Tünet**: a http CRL-ellenőrzés sikertelen, annak ellenére, hogy x509-bővítményekre írt http-CDP van.

**OK**: a AzsReadinessChecker jelenleg nem tud http CDP-t megkeresni bizonyos nyelveken.

**Megoldás**: az érvényesítés futtatásához az operációs rendszer nyelvét állítsa be az en-us értékre.

## <a name="certificates"></a>Tanúsítványok

| Címtár | Tanúsítvány |
| ---    | ----        |
| ACSBlob | `wildcard_blob_<region>_<externalFQDN>` |
| ACSQueue  |  `wildcard_queue_<region>_<externalFQDN>` |
| ACSTable  |  `wildcard_table_<region>_<externalFQDN>` |
| Felügyeleti bővítmény gazdagépe  |  `wildcard_adminhosting_<region>_<externalFQDN>` |
| Felügyeleti portál  |  `adminportal_<region>_<externalFQDN>` |
| ARM-rendszergazda  |  `adminmanagement_<region>_<externalFQDN>` |
| ARM nyilvános  |  `management_<region>_<externalFQDN>` |
| KeyVault  |  `wildcard_vault_<region>_<externalFQDN>` |
| KeyVaultInternal  |  `wildcard_adminvault_<region>_<externalFQDN>` |
| Nyilvános kiterjesztésű gazdagép  |  `wildcard_hosting_<region>_<externalFQDN>` |
| Nyilvános portál  |  `portal_<region>_<externalFQDN>` |

## <a name="using-validated-certificates"></a>Ellenőrzött tanúsítványok használata

Miután a AzsReadinessChecker érvényesíti a tanúsítványokat, készen áll arra, hogy a Azure Stack hub üzemelő példányában vagy Azure Stack hub Secret rotációs szolgáltatásban is használhassa őket.

 - Az üzembe helyezéshez biztonságosan vigye át a tanúsítványokat a telepítési mérnökbe, hogy azok a [Azure stack hub PKI-követelmények dokumentációjában](azure-stack-pki-certs.md)megadott módon másolja őket a központi telepítési gazdagépre.
 - A Secret rotációs szolgáltatásban a tanúsítványok segítségével frissítheti az Azure Stack hub-környezet nyilvános infrastruktúra-végpontjának régi tanúsítványait az [Azure stack hub Secret rotációs dokumentációjának](azure-stack-rotate-secrets.md)követésével.
 - A Pásti-szolgáltatások esetében a tanúsítványok segítségével telepítheti az SQL, a MySQL és a App Services erőforrás-szolgáltatót Azure Stack hub-ban az [Azure stack hub dokumentációjában elérhető szolgáltatások áttekintését](service-plan-offer-subscription-overview.md)követve.

## <a name="next-steps"></a>További lépések

[Adatközpont identitásának integrációja](azure-stack-integrate-identity.md)
