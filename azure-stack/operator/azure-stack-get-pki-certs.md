---
title: Tanúsítvány-aláírási kérelmek előállítása Azure Stack hubhoz | Microsoft Docs
description: Megtudhatja, hogyan hozhatja Azure Stack hub PKI-tanúsítványokhoz tartozó tanúsítvány-aláírási kérelmeket Azure Stack hub integrált rendszerekben.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 9eba64e86a375f275c159e6d571e3557f2164726
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818264"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Tanúsítvány-aláírási kérelmek előállítása Azure Stack hubhoz

Az Azure Stack hub Readiness-ellenőrző eszköz használatával tanúsítvány-aláírási kérelmeket (munkatársakat) hozhat létre Azure Stack központi telepítéshez. A tanúsítványokat az üzembe helyezés előtt elég időt kell kérni, generálni és érvényesíteni. Az eszközt [a PowerShell-galériaból](https://aka.ms/AzsReadinessChecker)kérheti le.

Az Azure Stack hub Readiness-ellenőrző eszköz (AzsReadinessChecker) használatával a következő tanúsítványokat kérheti le:

- **Szabványos tanúsítványkérelmek** a [tanúsítvány-aláírási kérelem előállítása](azure-stack-get-pki-certs.md#generate-certificate-signing-requests)alapján.
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

## <a name="generate-certificate-signing-requests"></a>Tanúsítvány-aláírási kérelmek előállítása

Ezekkel a lépésekkel előkészítheti és érvényesítheti az Azure Stack hub PKI-tanúsítványait:

1. Telepítse a AzsReadinessChecker-t egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarálja a **tárgyat**. Példa:

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > Ha köznapi nevet (CN) ad meg, a rendszer minden tanúsítványkérelem esetében konfigurálva lesz. Ha a rendszer kihagyja a CN-t, az Azure Stack hub szolgáltatás első DNS-neve lesz konfigurálva a tanúsítványkérelem során.

3. Deklaráljon egy már létező kimeneti könyvtárat. Példa:

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
    > a `<regionName>.<externalFQDN>` a Azure Stack hub összes külső DNS-neve alapján hozza létre az alapokat. Ebben a példában a portál `portal.east.azurestack.contoso.com`.  

6. Tanúsítvány-aláírási kérelmek előállítása az üzembe helyezéshez:

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Más Azure Stack hub-szolgáltatásokhoz tartozó tanúsítványkérelmek létrehozásához módosítsa a `-CertificateType`értékét. Példa:

    ```powershell  
    # App Services
    New-AzsCertificateSigningRequest -certificateType AppServices -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsCertificateSigningRequest -certificateType DBAdapter -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHub
    New-AzsCertificateSigningRequest -certificateType EventHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsCertificateSigningRequest -certificateType IoTHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. A fejlesztési és tesztelési környezetekben egyetlen, több tulajdonos alternatív névvel rendelkező **RequestType SingleCSR** paramétert és értéket (éles környezetekben**nem** ajánlott) hozhat létre.

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Tekintse át a kimenetet:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1912.1082.37 started.
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\checker\Documents\AzureStackCSR\wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20191219140359.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9.  Küldje el a t **.** A CA-hoz GENERÁLT REQ-fájl (belső vagy nyilvános). A **New-AzsCertificateSigningRequest** kimeneti könyvtára tartalmazza a hitelesítésszolgáltatóhoz való beküldéshez szükséges CSR (ka) t. A könyvtár tartalmazza a hivatkozáshoz tartozó alárendelt könyvtárat is, amely a tanúsítványkérelem létrehozásakor használt INF-fájl (oka) t tartalmazza. Győződjön meg arról, hogy a HITELESÍTÉSSZOLGÁLTATÓ olyan tanúsítványokat hoz létre a létrehozott kérelem alapján, amelyek megfelelnek az [Azure stack hub PKI követelményeinek](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub PKI-tanúsítványok előkészítése](azure-stack-prepare-pki-certs.md)
