---
title: Azure Stackkel integrált rendszerek üzembe helyezés az Azure Stack a nyilvános kulcsú infrastruktúra tanúsítványainak ellenőrzése |} A Microsoft Docs
description: Az Azure Stack PKI-tanúsítványokat az Azure Stack integrált rendszerek ellenőrzését ismerteti. Ismerteti az Azure Stack tanúsítvány ellenőrző eszközt.
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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: abf2d10c50271217220d9dc218b4d50f7411d867
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984529"
---
# <a name="validate-azure-stack-pki-certificates"></a>Az Azure Stack PKI-tanúsítványok ellenőrzése

A jelen cikkben ismertetett Azure Stack készültségi ellenőrző eszköz [a PowerShell-galériából](https://aka.ms/AzsReadinessChecker). Az eszköz segítségével ellenőrizze, hogy a [generált PKI-tanúsítványokat](azure-stack-get-pki-certs.md) központi telepítés előtti kiválóan alkalmasak. Ha elegendő időt, tesztelheti és szükség esetén a tanúsítványok, adja ki újból tanúsítványok érvényesítésére.

A készenléti-ellenőrző eszköz tanúsítványt hoz létre hajtja végre:

- **Olvassa el a PFX**  
    Ellenőrzi, hogy érvényes PFX-fájl helyes jelszót, és e a nyilvános információkat a jelszóval nem védett. 
- **Aláírási algoritmus**  
    Ellenőrzi, hogy az aláírási algoritmus nem SHA1.
- **Titkos kulcs**  
    Ellenőrzi, hogy a titkos kulcs található, és a helyi gép attribútummal van exportálva. 
- **Tanúsítványlánc**  
    Ellenőrzések tanúsítványlánc sértetlenek is, hogy önaláírt tanúsítványokat.
- **DNS-nevek**  
    A SAN tartalmaz minden végpont megfelelő DNS-neveit, vagy egy támogatása jelen-e helyettesítő ellenőrzi.
- **Kulcshasználat**  
    Ellenőrzi, ha a kulcshasználat tartalmazza a digitális aláírás és kulcstitkosítás és a kibővített kulcshasználat tartalmazza a kiszolgáló hitelesítése és ügyfél-hitelesítéshez.
- **Kulcsméret**  
    Ellenőrzi, hogy a kulcs mérete 2048 vagy nagyobb.
- **Lánc sorrendjében**  
    Ellenőrzi, hogy helyesen szerepel-e a rendelés érvényesítése többi tanúsítvány sorrendjét.
- **Más tanúsítványok**  
    Győződjön meg arról, nincsenek egyéb tanúsítványok van csomagolva a PFX nem megfelelő levéltanúsítványt vagy láncában.
- **Profil**  
    Ellenőrzi, hogy egy új felhasználót betöltheti a PFX-adatainak nélkül betöltődtek, a felhasználói profil mimicking csoportosan felügyelt szolgáltatásfiók fiókok viselkedését a tanúsítványt a karbantartás során.

> [!IMPORTANT]  
> A PKI-tanúsítványt egy PFX-fájlt és jelszót kell tekinteni a bizalmas adatokat.

## <a name="prerequisites"></a>Előfeltételek

A rendszer meg kell felelnie az alábbi előfeltételek PKI-tanúsítványokat az Azure Stack üzembe helyezésének ellenőrzése előtt:

- A Microsoft Azure Stack készültség ellenőrzése
- A következő exportált SSL tanúsítvány(ok) a [előkészületi útmutatás](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 vagy Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Alapvető szolgáltatások tanúsítvány-ellenőrzés végrehajtása

Használja ezeket a lépéseket, előkészítéséhez és az Azure Stack PKI-tanúsítványok üzembe helyezéséhez és a titkos Elforgatás ellenőrzése:

1. Telepítés **AzsReadinessChecker** PowerShell parancssorból (5.1-es vagy újabb), a következő parancsmagot:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. A tanúsítvány könyvtárstruktúrát létrehozása. Az alábbi példában módosíthatja `<c:\certificates>` , az új elérési utat a választott.
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > Az AD FS- és Graph szükségesek, ha az AD FS használja, a identitáskezelő rendszerbe. Példa:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - A tanúsítvány(ok) jelölje be a megfelelő könyvtárakat az előző lépésben létrehozott. Példa:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. A PowerShell-ablakban módosítsa az értékét **RegionName** és **FQDN** az Azure Stack környezettel való megfelelő, és futtassa a következő:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Ellenőrizze, hogy a kimenet és az összes tanúsítvány adja át az összes teszt. Példa:

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

**Jelenség**: A rendszer kihagyja tesztek

**Ok**: AzsReadinessChecker kihagyja bizonyos teszteket, ha egy függőség nem teljesül:

 - Ha a tanúsítványlánc nem sikerül, a rendszer kihagyja többi tanúsítvány.

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

**Megoldás**: Minden egyes tanúsítvány tesztek minden készlete a részletek szakaszában az eszköz útmutatót követve.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Platform végrehajtott egy szolgáltatási tanúsítvány érvényesítése

Segítségével ezeket a lépéseket előkészítése, és ellenőrizze a platform az Azure Stack PKI-tanúsítványokat, a platformszolgáltatás (PaaS) tanúsítványok, ha az SQL vagy MySQL vagy az App Services üzemelő példány tervezett.

1.  Telepítés **AzsReadinessChecker** PowerShell parancssorból (5.1-es vagy újabb), a következő parancsmagot:

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Hozzon létre egy beágyazott kivonattáblát tartalmazó elérési út és a jelszót, hogy minden egyes PaaS tanúsítvány érvényességi kellene. A PowerShell-ablakban futtassa:

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Módosítsa az értékeket a **RegionName** és **FQDN** megfelelően az Azure Stack-környezet indítsa el az ellenőrzést. Majd futtassa ezt:

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Ellenőrizze, hogy a kimenetet, és hogy minden tanúsítvány adja át az összes teszt.

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
| ACSTable  |  wildcard_table_\<region>_\<externalFQDN> |
| Admin Extension Host  |  wildcard_adminhosting_\<region>_\<externalFQDN> |
| Felügyeleti portál  |  adminportal_\<region>_\<externalFQDN> |
| ARM-rendszergazda  |  adminmanagement_\<region>_\<externalFQDN> |
| ARM-nyilvános  |  management_\<region>_\<externalFQDN> |
| KeyVault  |  wildcard_vault_\<region>_\<externalFQDN> |
| KeyVaultInternal  |  wildcard_adminvault_\<region>_\<externalFQDN> |
| A bővítmény nyilvános állomás  |  wildcard_hosting_\<region>_\<externalFQDN> |
| Nyilvános portálra  |  portal_\<region>_\<externalFQDN> |

## <a name="using-validated-certificates"></a>Ellenőrzött tanúsítványok használatával

A tanúsítványok ellenőrzése a AzsReadinessChecker szerint, ha készen áll használhatja őket az Azure Stack üzemelő példányban, vagy az Azure Stack titkos elforgatás. 

 - Üzembe helyezés biztonságos módon továbbítja a tanúsítványokat a központi telepítési szakértőhöz, hogy a megadott üzembe helyezési gazdagép alakzatot átmásolhassák az [Azure Stack nyilvános kulcsokra épülő infrastruktúra követelményei dokumentáció](azure-stack-pki-certs.md).
 - Titkos kód forgatását, használhatja a tanúsítványok frissítése az Azure Stack környezettel nyilvános infrastruktúra-végpontokra régi tanúsítványai a következő a [dokumentáció az Azure Stack titkos Elforgatás](azure-stack-rotate-secrets.md).
 - PaaS-szolgáltatások, használhatja a tanúsítványok telepítése SQL, a MySQL és az App Services erőforrás-szolgáltatók az Azure Stack a következő a [az Azure Stack-dokumentáció szolgáltatásajánlások áttekintése](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>További lépések

[Adatközpont-identitásintegráció](azure-stack-integrate-identity.md)
