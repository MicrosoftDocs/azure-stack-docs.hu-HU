---
title: Tanúsítvány-aláírási kérelmek előállítása a Azure Stackhoz | Microsoft Docs
description: Megtudhatja, hogyan hozhatja Azure Stack PKI-tanúsítványokhoz tartozó tanúsítvány-aláírási kérelmeket Azure Stack integrált rendszerekben.
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
ms.openlocfilehash: 365f727f7e07c697dc2fd3cfe2a5c1bea5b68409
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019258"
---
# <a name="generate-certificate-signing-requests-for-azure-stack"></a>Tanúsítvány-aláírási kérelmek előállítása Azure Stackhoz

A Azure Stack Readiness-ellenőrző eszköz használatával tanúsítvány-aláírási kérelmeket (munkatársakat) hozhat létre Azure Stack központi telepítéshez. A tanúsítványokat az üzembe helyezés előtt elég időt kell kérni, generálni és érvényesíteni. Az eszközt [a PowerShell-galériaból](https://aka.ms/AzsReadinessChecker)kérheti le.

A következő tanúsítványok igényléséhez használhatja a Azure Stack Readiness-ellenőrző eszközt (AzsReadinessChecker):

- **Szabványos tanúsítványkérelmek** a [tanúsítvány-aláírási kérelem előállítása](azure-stack-get-pki-certs.md#generate-certificate-signing-requests)alapján.
- **Platform-as-a-Service**: A tanúsítványokra vonatkozóan a [nyilvános kulcsokra épülő infrastruktúra tanúsítványára vonatkozó követelményekben Azure stack](azure-stack-pki-certs.md#optional-paas-certificates)megadott platform-szolgáltatásként (a-Service-szolgáltatások (Pásti) nevét is kérheti – opcionális Péter-tanúsítványok.

## <a name="prerequisites"></a>Előfeltételek

A rendszernek meg kell felelnie a következő előfeltételeknek, mielőtt a PKI-tanúsítványokhoz tartozó összes ügyfélszolgálatot létrehoz egy Azure Stack központi telepítéshez:

- Microsoft Azure Stack Readiness-ellenőrző
- Tanúsítvány attribútumai:
  - Régió neve
  - Külső teljesen minősített tartománynév (FQDN)
  - Subject
- Windows 10 vagy Windows Server 2016 vagy újabb

  > [!NOTE]  
  > Amikor visszakapja a tanúsítványokat a hitelesítésszolgáltatótól, a [Azure stack PKI-tanúsítványok előkészítésének](azure-stack-prepare-pki-certs.md) lépéseit ugyanazon a rendszeren kell végrehajtania.

## <a name="generate-certificate-signing-requests"></a>Tanúsítvány-aláírási kérelmek előállítása

Ezekkel a lépésekkel előkészítheti és érvényesítheti a Azure Stack PKI-tanúsítványokat:

1. Telepítse a AzsReadinessChecker-t egy PowerShell-parancssorból (5,1 vagy újabb) a következő parancsmag futtatásával:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarálja a **tárgyat** rendezett szótárként. Példa:

    ```powershell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Köznapi név (CN) megadása esetén a tanúsítványkérelem első DNS-neve felül lesz írva.

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

5. Deklarálja a **régió nevét** és a Azure stack központi telepítéshez szánt **külső teljes tartománynevet** .

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > @no__t – 0 – a Azure Stack összes külső DNS-nevének létrehozása, ebben a példában a portál `portal.east.azurestack.contoso.com`.  

6. Tanúsítvány-aláírási kérelmek előállítása minden DNS-névhez:

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    A Péter-szolgáltatások belefoglalásához adja meg a kapcsolót ```-IncludePaaS```.

7. A fejlesztési és tesztelési környezetekben egyetlen, több tulajdonos alternatív névvel rendelkező **RequestType SingleCSR** paramétert és értéket (éles környezetekben**nem** ajánlott) hozhat létre.

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    A Péter-szolgáltatások belefoglalásához adja meg a kapcsolót @no__t – 0

8. Tekintse át a kimenetet:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. Küldje el a t **.** A CA-hoz GENERÁLT REQ-fájl (belső vagy nyilvános). A **New-AzsCertificateSigningRequest** kimeneti könyvtára tartalmazza a hitelesítésszolgáltatóhoz való beküldéshez szükséges CSR (ka) t. A könyvtár tartalmazza a hivatkozáshoz tartozó alárendelt könyvtárat is, amely a tanúsítványkérelem létrehozásakor használt INF-fájl (oka) t tartalmazza. Győződjön meg arról, hogy a HITELESÍTÉSSZOLGÁLTATÓ olyan tanúsítványokat hoz létre, amelyek megfelelnek a [Azure stack PKI követelményeinek](azure-stack-pki-certs.md).

## <a name="next-steps"></a>További lépések

[PKI-tanúsítványok Azure Stack előkészítése](azure-stack-prepare-pki-certs.md)
