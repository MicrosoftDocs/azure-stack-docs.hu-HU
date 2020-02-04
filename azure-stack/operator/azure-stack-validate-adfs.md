---
title: AD FS integráció ellenőrzése
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan használhatja az Azure Stack hub Readiness-ellenőrzőt az Azure Stack hub AD FS-integrációjának ellenőrzéséhez.
services: azure-stack
documentationcenter: ''
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 786ee290aba91c855211d3f470f439c3e9b2c01a
ms.sourcegitcommit: 5f53810d3c5917a3a7b816bffd1729a1c6b16d7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972590"
---
# <a name="validate-ad-fs-integration-for-azure-stack-hub"></a>Azure Stack hub AD FS integrációjának ellenőrzése

Az Azure Stack hub Readiness-ellenőrző eszközének (AzsReadinessChecker) használatával ellenőrizze, hogy a környezet készen áll-e a Active Directory összevonási szolgáltatások (AD FS) (AD FS) integrációra Azure Stack hub-nal. Ellenőrizze AD FS integrációt, mielőtt megkezdené az adatközpont-integrációt vagy egy Azure Stack hub üzembe helyezését.

A készültség-ellenőrző ellenőrzi a következőket:

* Az *összevonási metaadatok* tartalmazzák az összevonás érvényes XML-elemeit.
* Az *AD FS SSL-tanúsítvány* beolvasható, és a megbízhatósági lánc is létrehozható. A Stamp esetében AD FS meg kell bíznia az SSL-tanúsítványlánc láncában. A *tanúsítványt ugyanazzal a hitelesítésszolgáltatóval* kell aláírni, amelyet az Azure stack hub központi telepítési tanúsítványokhoz vagy egy megbízható legfelső szintű szolgáltatói partnerhez kell kötni. A megbízható legfelső szintű szolgáltatói partnerek teljes listájáért lásd: [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* A *AD FS aláíró tanúsítvány* megbízható, és nem közeledik a lejárathoz.

Az Azure Stack hub Datacenter-integrációval kapcsolatos további információkért lásd: [Azure stack hub Datacenter Integration-Identity](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>A Readiness-ellenőrző eszköz beszerzése

Töltse le az Azure Stack hub Readiness-ellenőrző eszközének (AzsReadinessChecker) legújabb verzióját a [PowerShell-Galéria](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételeknek kell teljesülniük.

**Az a számítógép, amelyen az eszköz fut:**

* Windows 10 vagy Windows Server 2016 tartományi kapcsolattal.
* PowerShell 5,1 vagy újabb. A verzió ellenőrzéséhez futtassa a következő *PowerShell-parancsot* , majd tekintse át a főverziót *és az alverziókat* :  
    ```powershell
    $PSVersionTable.PSVersion
    ```
* Az [Microsoft Azure stack hub Readiness-ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz legújabb verziója.

**Active Directory összevonási szolgáltatások (AD FS) környezet:**

A metaadatok alábbi formái közül legalább egy szükséges:

- AD FS összevonási metaadatok URL-címe Például: `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* Az összevonási metaadatok XML-fájlja. Például: FederationMetadata. xml.

## <a name="validate-ad-fs-integration"></a>AD FS integráció ellenőrzése

1. Egy olyan számítógépen, amely megfelel az előfeltételeknek, nyisson meg egy rendszergazdai PowerShell-parancssort, majd futtassa a következő parancsot a AzsReadinessChecker telepítéséhez:

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

1. A PowerShell-parancssorból futtassa a következő parancsot az érvényesítés indításához. A **-CustomADFSFederationMetadataEndpointUri** értéket a összevonási metaadatok URI azonosítójának megadásával adhatja meg.

     ```powershell
     Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml
     ```

1. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, hogy az AD FS az integráció követelményeinek megfelelő állapotú. A sikeres érvényesítés az alábbi példához hasonló:

    ```powershell
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

Éles környezetekben az operátorok munkaállomása által megbízhatónak ítélt tanúsítványlánc tesztelése nem teljes mértékben jelzi a PKI-megbízhatósági testhelyzetet az Azure Stack hub-infrastruktúrában. A Azure Stack hub Stamp nyilvános VIP-hálózatának szüksége van a nyilvános kulcsokra épülő infrastruktúrához tartozó CRL-hez való kapcsolódásra.

## <a name="report-and-log-file"></a>Jelentés és naplófájl

A rendszer minden alkalommal futtatja az eredményeket a **AzsReadinessChecker. log** és a **AzsReadinessCheckerReport. JSON**fájlban. A fájlok helye a PowerShell érvényesítési eredményeivel jelenik meg.

Az érvényesítési fájlok segítségével megoszthatja az állapotot az Azure Stack hub üzembe helyezése vagy az érvényesítési problémák vizsgálata előtt. Mindkét fájl megőrzi az összes további érvényesítési ellenőrzés eredményét. A jelentés megadja az üzembe helyezési csoportnak az identitás konfigurációjának megerősítését. A naplófájl segítséget nyújthat az üzembe helyezéshez vagy a támogatási csoporthoz az érvényesítési problémák kivizsgálásához.

Alapértelmezés szerint mindkét fájl a `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`ba íródik.

Használja

* `-OutputPath`: a futtatási parancs végén található *path* paraméter egy másik jelentés helyének megadásához.
* `-CleanReport`: a futtatási parancs végén található paraméter a korábbi jelentési információk AzsReadinessCheckerReport. JSON fájljának törléséhez. További információ: [Azure stack hub-ellenőrzési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy érvényesítési ellenőrzés sikertelen, a hiba részletei megjelennek a PowerShell ablakban. Az eszköz az *AzsReadinessChecker. log naplófájlba*is naplózza az adatokat.

Az alábbi példák útmutatást nyújtanak a gyakori ellenőrzési hibákról.

### <a name="command-not-found"></a>A parancs nem található

```powershell
Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
```

**OK**: a PowerShell automatikus betöltése nem tudta megfelelően betölteni a készültség-ellenőrző modult.

**Megoldás**: explicit módon importálja a készültség-ellenőrző modult. Másolja és illessze be a következő kódot a PowerShellbe, és frissítse `<version>` a jelenleg telepített verzió számával.

```powershell
Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force
```

## <a name="next-steps"></a>Következő lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack hub integrációs szempontjai](azure-stack-datacenter-integration.md)  
