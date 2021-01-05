---
title: Tanúsítvány-aláírási kérelem létrehozása az Azure Stack Hubhoz
description: Megtudhatja, hogyan hozhatja Azure Stack hub PKI-tanúsítványokhoz tartozó tanúsítvány-aláírási kérelmeket Azure Stack hub integrált rendszerekben.
author: PatAltimore
ms.topic: article
ms.date: 10/19/2020
ms.author: patricka
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: b03766efe531683310b81dbf2d03de8e990deec0
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870443"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Tanúsítvány-aláírási kérelem létrehozása az Azure Stack Hubhoz

Az Azure Stack hub Readiness-ellenőrző eszköz használatával tanúsítvány-aláírási kérelmeket (munkatársakat) hozhat létre Azure Stack központi telepítéshez. A tanúsítványokat az üzembe helyezés előtt elég időt kell kérni, generálni és érvényesíteni. Az eszközt [a PowerShell-galériaból](https://aka.ms/AzsReadinessChecker)kérheti le.

Az Azure Stack hub Readiness-ellenőrző eszköz (AzsReadinessChecker) használatával a következő tanúsítványokat kérheti le:

- **Szabványos tanúsítványkérelmek** [az új központi telepítések tanúsítvány-aláírási kérelmének előállítása](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments)alapján.
- Tanúsítványkérelmek **megújítása** tanúsítvány- [aláírási kérelem létrehozásához a tanúsítvány megújításához](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal).
- **Szolgáltatásként szolgáló platform**: az [Azure stack hub nyilvános kulcsokra épülő infrastruktúrájának tanúsítványára vonatkozó követelmények – opcionális Péter-tanúsítványok –](azure-stack-pki-certs.md#optional-paas-certificates)esetében a tanúsítványokra vonatkozó platform-szolgáltatásként (kitöltendő) neveket kérhet.

## <a name="prerequisites"></a>Előfeltételek

A rendszernek meg kell felelnie a következő előfeltételeknek, mielőtt a PKI-tanúsítványokhoz tartozó ügyfélszolgálati munkatársakat hozna létre egy Azure Stack hub üzembe helyezéséhez:

- Microsoft Azure Stack hub Readiness-ellenőrző
- Tanúsítvány attribútumai:
  - Régió neve
  - Külső teljesen minősített tartománynév (FQDN)
  - Tárgy
- Windows 10 vagy Windows Server 2016 vagy újabb

  > [!NOTE]  
  > Amikor visszakapja a tanúsítványokat a hitelesítésszolgáltatótól, a [Azure stack hub PKI-tanúsítványok előkészítésének](azure-stack-prepare-pki-certs.md) lépéseit ugyanazon a rendszeren kell végrehajtania.

## <a name="generate-certificate-signing-requests-for-new-deployments"></a>Tanúsítvány-aláírási kérelmek előállítása új központi telepítésekhez

A következő lépésekkel készítheti elő a tanúsítvány-aláírási kérelmeket az új Azure Stack hub PKI-tanúsítványokhoz:

1. Telepítse a AzsReadinessChecker-t egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarálja a **tárgyat**. Például:

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > Ha köznapi nevet (CN) ad meg, a rendszer minden tanúsítványkérelem esetében konfigurálva lesz. Ha a rendszer kihagyja a CN-t, az Azure Stack hub szolgáltatás első DNS-neve lesz konfigurálva a tanúsítványkérelem során.

3. Deklaráljon egy már létező kimeneti könyvtárat. Például:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Állapítsa meg a személyazonossági rendszereket.

    Azure Active Directory (Azure AD):

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory összevonási szolgáltatások (AD FS) (AD FS):

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > A paraméter csak a CertificateType telepítéséhez szükséges.

5. Deklarálja a **régió nevét** és a Azure stack hub-telepítéshez szánt **külső teljes tartománynevet** .

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` az alapot képezi, hogy az Azure Stack hub összes külső DNS-neve létrejön. Ebben a példában a portál a következő lesz: `portal.east.azurestack.contoso.com` .  

6. Tanúsítvány-aláírási kérelmek előállítása az üzembe helyezéshez:

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Más Azure Stack hub-szolgáltatásokhoz tartozó tanúsítványkérelmek létrehozásához módosítsa a értékét `-CertificateType` . Például:

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDbAdapterCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIoTHubCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. A fejlesztési és tesztelési környezetekben egyetlen, több tulajdonos alternatív névvel rendelkező **RequestType SingleCSR** paramétert és értéket (éles környezetekben **nem** ajánlott) hozhat létre.

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Tekintse át a kimenetet:

    ```powershell  
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710165538.req
    Certreq.exe output: CertReq: Request Created
    ```

9.  Küldje el a t **.** A CA-hoz GENERÁLT REQ-fájl (belső vagy nyilvános). A **New-AzsCertificateSigningRequest** kimeneti könyvtára tartalmazza a hitelesítésszolgáltatóhoz való beküldéshez szükséges CSR (ka) t. A könyvtár tartalmazza a hivatkozáshoz tartozó alárendelt könyvtárat is, amely a tanúsítványkérelem létrehozásakor használt INF-fájl (oka) t tartalmazza. Győződjön meg arról, hogy a HITELESÍTÉSSZOLGÁLTATÓ olyan tanúsítványokat hoz létre a létrehozott kérelem alapján, amelyek megfelelnek az [Azure stack hub PKI követelményeinek](azure-stack-pki-certs.md).

## <a name="generate-certificate-signing-requests-for-certificate-renewal"></a>Tanúsítvány-aláírási kérelmek előállítása a tanúsítvány megújításához

Ezekkel a lépésekkel előkészítheti a tanúsítvány-aláírási kérelmeket a meglévő Azure Stack hub PKI-tanúsítványok megújításához:

1. Telepítse a AzsReadinessChecker-t egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```

2. Deklarálja a **stampEndpoint** az Azure stack hubrendszer regionname.domain.com formájában. Például (ha a Azure Stack hub bérlői portál címe <code> https://</code> <code>portal.east.azurestack.contoso.com</code> ):

    ```powershell  
    $stampEndpoint = 'east.azurestack.contoso.com'
    ```

    > [!NOTE]  
    > A fenti Azure Stack hub rendszerhez HTTPS-kapcsolat szükséges.
    > A Readiness-ellenőrző a stampendpoint (régió és tartomány) használatával hoz létre egy mutatót a tanúsítvány típusa által igényelt meglévő tanúsítványokhoz, például a "portál" központi telepítési tanúsítványok előtagértéke, az eszköz által, így a portal.east.azurestack.contoso.com a tanúsítványok klónozásához, AppServices sso.appservices.east.azurestack.contoso.com stb. A számított végponthoz kötött tanúsítvány az attribútumok, például a tulajdonos, a kulcs hossza és az aláírási algoritmus klónozására szolgál.  Ha módosítani szeretné ezen attribútumok bármelyikét, kövesse az [új központi telepítések tanúsítvány-aláírási kérelmének létrehozása](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments) című témakör lépéseit.

3. Deklaráljon egy már létező kimeneti könyvtárat. Például:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Tanúsítvány-aláírási kérelmek előállítása az üzembe helyezéshez:

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

    Más Azure Stack hub-szolgáltatásokhoz tartozó tanúsítványkérelmek létrehozásához használja a következőt:

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDBAdapterCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIotHubCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

5. A fejlesztési és tesztelési környezetekben egyetlen, több tulajdonos alternatív névvel rendelkező **RequestType SingleCSR** paramétert és értéket (éles környezetekben **nem** ajánlott) hozhat létre.

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampendpoint -OutputRequestPath $OutputDirectory -RequestType SingleCSR
    ```

6.  Tekintse át a kimenetet:

    ```powershell  
    Querying StampEndpoint portal.east.azurestack.contoso.com for existing certificate
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710122723.req
    Certreq.exe output: CertReq: Request Created
    ```

7.  Küldje el a t **.** A CA-hoz GENERÁLT REQ-fájl (belső vagy nyilvános). A **New-AzsCertificateSigningRequest** kimeneti könyvtára tartalmazza a hitelesítésszolgáltatóhoz való beküldéshez szükséges CSR (ka) t. A könyvtár tartalmazza a hivatkozáshoz tartozó alárendelt könyvtárat is, amely a tanúsítványkérelem létrehozásakor használt INF-fájl (oka) t tartalmazza. Győződjön meg arról, hogy a HITELESÍTÉSSZOLGÁLTATÓ olyan tanúsítványokat hoz létre a létrehozott kérelem alapján, amelyek megfelelnek az [Azure stack hub PKI követelményeinek](azure-stack-pki-certs.md).

## <a name="next-steps"></a>További lépések

[Azure Stack hub PKI-tanúsítványok előkészítése](azure-stack-prepare-pki-certs.md)
