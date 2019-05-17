---
title: Az Azure Stackkel integrált rendszerek üzembe helyezés az Azure Stack a nyilvános kulcsú infrastruktúra tanúsítványainak előállításához |} A Microsoft Docs
description: Azure Stack integrált rendszerek Azure Stack PKI tanúsítvány üzembe helyezési folyamatát ismerteti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 1c342b1edb86629fff95dc04735fd5b6d98fc70a
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782272"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Az Azure Stack-tanúsítványok aláíró kérés létrehozása

Az Azure Stack készültségi ellenőrző eszköz segítségével létre tanúsítvány aláírási kérelem (tanúsítvány-aláírási kérelmeinek), az Azure Stack üzemelő példányához. Tanúsítványokat kell kért, jönnek létre és ellenőrzi a telepítés előtt tesztelje ideje. Az eszköz kap [a PowerShell-galériából](https://aka.ms/AzsReadinessChecker).

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) segítségével a következő tanúsítványt igényelni:

- **Standard szintű Eszköztanúsítvány-igénylések** szerint [PKI-tanúsítványok létrehozása az Azure Stack üzembe helyezéséhez](azure-stack-get-pki-certs.md).
- **Platform-as-a-Service**: Platform--szolgáltatásként (PaaS) nevek a megadott tanúsítványok is kérhető [Azure Stack a nyilvános kulcsú infrastruktúra tanúsítványkövetelmények – nem kötelező PaaS-tanúsítványok](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Előfeltételek

A rendszer a PKI-tanúsítványai az Azure Stack üzemelő példányához tartozó CSR(s) létrehozása előtt kell felelnie a következő előfeltételek vonatkoznak:

- A Microsoft Azure Stack készültség ellenőrzése
- Tanúsítvány attribútumai:
  - Régió neve
  - Külső, teljesen minősített tartománynevét (FQDN)
  - Subject
- Windows 10 vagy Windows Server 2016

  > [!NOTE]  
  > Ha megkapta a tanúsítványok biztonsági a hitelesítésszolgáltatótól származó lépéseit [előkészítése az Azure Stack PKI-tanúsítványokat](azure-stack-prepare-pki-certs.md) hajtható végre, ugyanazon a rendszeren kell!

## <a name="generate-certificate-signing-requests"></a>A tanúsítvány-aláírási kérelem (kérelmek) létrehozása

Ezen lépések előkészítéséhez és az Azure Stack PKI-tanúsítványok ellenőrzése:

1. Telepítse a AzsReadinessChecker PowerShell parancssorból (5.1-es vagy újabb), a következő parancsmagot:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarálja a **tulajdonos** , egy rendezett szótárban. Példa:

    ```powershell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Ha egy köznapi név (CN) meg van adva ez felülírja az első DNS-nevét a tanúsítványkérelemben.

3. Deklaráljon egy már létező kimeneti könyvtárat. Példa:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Deklarálja a identitásrendszer

    Azure Active Directory

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory összevonási szolgáltatások

    ```powershell
    $IdentitySystem = "ADFS"
    ```

5. Deklarálja **régió neve** és a egy **külső FQDN** szánt az Azure Stack üzemelő példányához.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` alapjául szolgál, amelyen az összes külső DNS-neveket az Azure Stackben jönnek létre, ebben a példában, a portál `portal.east.azurestack.contoso.com`.  

6. Tanúsítvány-aláírási kérelem minden DNS-név generálása:

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Tartalmazza a PaaS-szolgáltatások, adja meg a kapcsoló ```-IncludePaaS```

7. Azt is megteheti, fejlesztési-tesztelési környezetek létrehozása több tulajdonos alternatív nevekkel egyetlen tanúsítványkérelem hozzáadása **- RequestType SingleCSR** paraméter és érték (**nem** ajánlott éles környezetben):

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Tartalmazza a PaaS-szolgáltatások, adja meg a kapcsoló ```-IncludePaaS```

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

9. Küldje el a **. KÉRÉS** fájlt hoz létre, a hitelesítésszolgáltató (belső vagy nyilvános).  A kimeneti könyvtárba, **New-AzsCertificateSigningRequest** egy hitelesítésszolgáltatónak elküldéséhez szükséges CSR(s) tartalmazza.  A könyvtár tartalmaz, referenciaként egy gyermek könyvtárra, amelyben a tanúsítvány-kérelem létrehozása során használt INF fájlt vagy fájlokat. Győződjön meg, hogy a a hitelesítésszolgáltató tanúsítványokat a generált kérést a megfelelő állít elő a [Azure Stack nyilvános kulcsokra épülő infrastruktúra követelményei](azure-stack-pki-certs.md).

## <a name="next-steps"></a>További lépések

[Készítse elő az Azure Stack PKI-tanúsítványok](azure-stack-prepare-pki-certs.md)
