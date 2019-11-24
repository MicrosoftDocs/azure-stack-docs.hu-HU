---
title: Azure Stack nyilvános kulcsokra épülő infrastruktúra tanúsítványainak ellenőrzése Azure Stack integrált rendszerek üzembe helyezéséhez | Microsoft Docs
description: Ismerteti, hogyan lehet érvényesíteni a Azure Stack PKI-tanúsítványokat Azure Stack integrált rendszerekhez. A Azure Stack Certificate-ellenőrzési eszköz használatát ismerteti.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 3823aa73d58af48c662690aa0d8e8a21180b4ed6
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283237"
---
# <a name="validate-azure-stack-pki-certificates"></a>PKI-tanúsítványok Azure Stack ellenőrzése

A jelen cikkben ismertetett Azure Stack készültség-ellenőrző eszköz [a PowerShell-Galéria](https://aka.ms/AzsReadinessChecker)érhető el. Az eszközzel ellenőrizheti, hogy a [GENERÁLT PKI-tanúsítványok](azure-stack-get-pki-certs.md) megfelelőek-e az előzetes központi telepítéshez. A tanúsítványok érvényesítéséhez elegendő időt kell hagyni a tanúsítványok tesztelésére és kikiadására, ha szükséges.

A készenléti ellenőrző eszköz a következő tanúsítványokat hajtja végre:

- **PFX olvasása**  
    Ellenőrzi az érvényes PFX-fájlt, a helyes jelszót, valamint azt, hogy a jelszó nem védi-e a nyilvános adatokat. 
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
- **Nincs profil**  
    Ellenőrzi, hogy egy új felhasználó be tudja-e tölteni a PFX-adatmennyiséget felhasználói profil nélkül, amely a gMSA-fiókok viselkedését utánozza a tanúsítványok karbantartása során.

> [!IMPORTANT]  
> A PKI-tanúsítvány egy PFX-fájl, és a jelszót bizalmas információként kell kezelni.

## <a name="prerequisites"></a>Előfeltételek

A rendszernek meg kell felelnie a következő előfeltételeknek, mielőtt ellenőrzi a PKI-tanúsítványokat egy Azure Stack központi telepítéshez:

- Microsoft Azure Stack Readiness-ellenőrző
- Az [előkészítési útmutatást](azure-stack-prepare-pki-certs.md) követően exportált SSL-tanúsítvány (ok)
- DeploymentData.json
- Windows 10 vagy Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Alapszintű szolgáltatások tanúsítványának ellenőrzése

Ezekkel a lépésekkel előkészítheti és érvényesítheti a Azure Stack PKI-tanúsítványokat az üzembe helyezéshez és a titkos elforgatáshoz:

1. Telepítse a **AzsReadinessChecker** -t egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Hozza létre a tanúsítvány könyvtárának struktúráját. Az alábbi példában megváltoztathatja a `<c:\certificates>`t az Ön által választott új könyvtár elérési útjára.
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > A AD FS és a Graph használata akkor szükséges, ha az Ön identitási rendszereként AD FS használ. Például:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Helyezze a tanúsítvány (oka) t az előző lépésben létrehozott megfelelő címtárakba. Például:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. A PowerShell ablakban módosítsa a **RegionName** és a **teljes tartománynév** értékét a Azure stack-környezetre, és futtassa a következőt:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Győződjön meg arról, hogy a kimenet és az összes tanúsítvány megfelel az összes tesztnek. Például:

```powershell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
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
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Megoldás**: kövesse az eszköz útmutatását a Részletek szakaszban az egyes tanúsítványokhoz tartozó tesztek alapján.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Platform végrehajtása szolgáltatás-tanúsítvány érvényesítése

Ezekkel a lépésekkel előkészítheti és érvényesítheti az Azure Stack PKI-tanúsítványokat a platform szolgáltatásként szolgáló tanúsítványok számára, ha az SQL/MySQL vagy App Services üzemelő példányok tervezése megtörtént.

1.  Telepítse a **AzsReadinessChecker** -t egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Hozzon létre egy beágyazott szórótábla, amely tartalmazza az elérési utakat és a jelszót az egyes Péter-tanúsítványok érvényesítéséhez. A PowerShell-ablakban futtassa a következőket:

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Módosítsa a **RegionName** és a **teljes tartománynév** értékét úgy, hogy az megfeleljen a Azure stack-környezetnek az ellenőrzés elindításához. Majd futtassa ezt:

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Győződjön meg arról, hogy a kimenet és az összes tanúsítvány az összes tesztet átadja.

    ```powershell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="certificates"></a>Tanúsítványok

| Címtár | Tanúsítvány |
| ---    | ----        |
| acsBlob | wildcard_blob_\<region>_\<externalFQDN> |
| ACSQueue  |  wildcard_queue_\<region>_\<externalFQDN> |
| ACSTable  |  wildcard_table_\<régió > _\<externalFQDN > |
| Felügyeleti bővítmény gazdagépe  |  wildcard_adminhosting_\<region>_\<externalFQDN> |
| Felügyeleti portál  |  adminportal_\<region>_\<externalFQDN> |
| ARM-rendszergazda  |  adminmanagement_\<region>_\<externalFQDN> |
| ARM nyilvános  |  management_\<region>_\<externalFQDN> |
| KeyVault  |  wildcard_vault_\<region>_\<externalFQDN> |
| KeyVaultInternal  |  wildcard_adminvault_\<region>_\<externalFQDN> |
| Nyilvános kiterjesztésű gazdagép  |  wildcard_hosting_\<region>_\<externalFQDN> |
| Nyilvános portál  |  portal_\<régió > _\<externalFQDN > |

## <a name="using-validated-certificates"></a>Ellenőrzött tanúsítványok használata

Miután az AzsReadinessChecker hitelesítette a tanúsítványokat, készen áll azok használatára az Azure Stack-környezetben vagy az Azure Stack titkos kulcsainak rotálásához. 

 - Az üzembe helyezéshez biztonságosan vigye át a tanúsítványokat a telepítési mérnökbe, hogy azok a [Azure stack PKI-követelmények dokumentációjában](azure-stack-pki-certs.md)megadott módon másolja őket a központi telepítési gazdagépre.
 - A Secret rotációs szolgáltatásban a tanúsítványok segítségével frissítheti Azure Stack környezete nyilvános infrastruktúra-végpontjának régi tanúsítványait a [Azure stack Secret rotációs dokumentációjának](azure-stack-rotate-secrets.md)követésével.
 - A Pásti-szolgáltatások esetében a tanúsítványokkal telepítheti az SQL, a MySQL és a App Services erőforrás-szolgáltatót Azure Stack az [Azure stack dokumentációjában elérhető szolgáltatások áttekintését](service-plan-offer-subscription-overview.md)követve.

## <a name="next-steps"></a>További lépések

[Adatközpont identitásának integrációja](azure-stack-integrate-identity.md)
