---
title: Az AD FS-integráció az Azure Stack ellenőrzése
description: Az Azure Stack-készültségi ellenőrző segítségével ellenőrizze az AD FS-integráció az Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: dcc473d270d0a72b2ebf5f31c67fffa6827c9ecc
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828418"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>Az AD FS-integráció az Azure Stack ellenőrzése

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) használatával, hogy az készen áll az Active Directory összevonási szolgáltatások (AD FS) integrációja az Azure Stack használatával. Adatközpont integrációja megkezdése előtt, illetve az Azure Stack üzembe helyezés előtt érvényesíteni a az AD FS-integráció.

A készenléti ellenőrző ellenőrzi:

* A *összevonási metaadatok* összevonáshoz érvényes XML-elemeket tartalmazza.
* A *AD FS SSL-tanúsítvány* is kérhető le, és megbízhatósági is láncolatától felépítve. Stamp az AD FS meg kell bíznia az SSL-tanúsítvány lánc. A tanúsítvánnyal kell aláírni azonos *hitelesítésszolgáltató* az Azure Stack-központitelepítési tanúsítványok vagy egy megbízható legfelső szintű szolgáltató partner által használt. A megbízható legfelső szintű szolgáltató partnereink teljes listájáért lásd: [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* A *AD FS-aláíró tanúsítványa* megbízható és nem hamarosan lejárati van.

Azure Stack adatközpont-integrációval kapcsolatos további információkért lásd: [Azure Stack adatközpont integrációja - identitás](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) legújabb verziójának letöltése a [PowerShell-galériából](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek vonatkoznak a helyen kell lennie.

**A számítógép, amelyen az eszköz fut:**

* Windows 10-es vagy Windows Server 2016, a tartományi kapcsolat.
* A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsot, és tekintse át a *fő* verzió és *kisebb* verziók:  
   > `$PSVersionTable.PSVersion`
* Legújabb verzióját a [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszközt.

**Az Active Directory összevonási szolgáltatások környezetben:**

Legalább egy, a következő formátumokban metaadatok lesz szüksége:

* Az AD FS összevonási metaadatainak URL-címe. Például: `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* Az összevonási metaadatainak XML-fájl. Ez például akkor federationmetadata.XML mintát követi.

## <a name="validate-ad-fs-integration"></a>Ellenőrizze az AD FS-integráció

1. Egy számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazdai PowerShell-parancssort, és futtassa a következő parancsot az AzsReadinessChecker telepítéséhez:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. A PowerShell használatával a következő paranccsal ellenőrzés indításához. Adja meg az értékét **- CustomADFSFederationMetadataEndpointUri** , az összevonási metaadatok URI Azonosítóját.

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, hogy a állapota OK: az AD FS integrációs követelményeinek. Egy sikeres ellenőrzése az alábbi példához hasonló:

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

Éles környezetben az operátor Munkaállomás megbízhatósági tanúsítványláncok tesztelés nem a nyilvános kulcsokra épülő infrastruktúra megbízhatósági állapotáról, az Azure Stack-infrastruktúra teljes jelezhetnek. Az Azure Stack-blokk nyilvános VIP-hálózat a kapcsolatot kell létesítenie a visszavont tanúsítványok Listáját a PKI-infrastruktúra.

## <a name="report-and-log-file"></a>A jelentés és-naplófájl

Minden egyes alkalommal érvényesítési fut, az eredmények naplózza **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Ezek a fájlok helyét jelenik meg az ellenőrzés eredményét a PowerShellben.

Az érvényesítés fájlok segítségével megoszthatja állapot érvényesítési problémák vizsgálatához vagy az Azure Stack üzembe helyezése előtt. Mindkét fájlt megmarad, egyes további ellenőrzés eredményeit. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segíthet a telepítés vagy a támogatási csapat érvényesítési problémák kivizsgálásában.

Alapértelmezés szerint mindkét fájlt írt `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Használat:

* **-OutputPath**: A *elérési út* paraméter adja meg egy másik jelentés helyét a futtatási parancs végén.
* **-CleanReport**: A parancs futtatásával törölje az előző jelentési adatok AzsReadinessCheckerReport.json végén paramétert. További információkért lásd: [Azure Stack érvényesítési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy ellenőrzés nem sikerül, a hiba részleteit a PowerShell-ablakban jelennek meg. Az eszköz is naplózza az adatokat *AzsReadinessChecker.log*.

Az alábbi példák gyakori ellenőrzési hibákat ad útmutatást.

### <a name="command-not-found"></a>A parancs nem található

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Ok**: PowerShell Autoload nem sikerült megfelelően betölteni a készültségi ellenőrző modul.

**Feloldási**: A készenléti ellenőrző modul importálása explicit módon. Másolja és illessze be a következő kódot a Powershellt, és a frissítés \<verzió\> a jelenleg telepített verzió számát.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>További lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)  
