---
title: Azure Graph-integráció ellenőrzése
titleSuffix: Azure Stack Hub
description: Az Azure Stack hub Readiness-ellenőrzővel ellenőrizheti Azure Stack hub Graph-integrációját.
author: BryanLa
ms.topic: how-to
ms.date: 03/04/2020
ms.author: bryanla
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: e4af68d1d4f9a44bb957f318f0f13ec1d2c9ecdd
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82173843"
---
# <a name="validate-graph-integration-for-azure-stack-hub"></a>Azure Stack hub gráf-integrációjának ellenőrzése

Az Azure Stack hub Readiness-ellenőrző eszköz (AzsReadinessChecker) segítségével ellenőrizze, hogy a környezet készen áll-e a Graph-integrációra Azure Stack hub-nal. Az adatközpont-integráció megkezdése előtt vagy egy Azure Stack hub üzembe helyezése előtt ellenőrizze a Graph-integrációt.

A készültség-ellenőrző ellenőrzi a következőket:

* A Graph-integrációhoz létrehozott szolgáltatásfiók hitelesítő adatai rendelkeznek a Active Directory lekérdezéséhez szükséges jogosultságokkal.
* A *globális katalógus* feloldható és felhasználható.
* A KDC feloldható és felhasználható.
* A szükséges hálózati kapcsolat van érvényben.

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
* Active Directory PowerShell-modul.
* Az [Microsoft Azure stack hub Readiness-ellenőrző](https://aka.ms/AzsReadinessChecker) eszköz legújabb verziója.

**Active Directory környezet:**

* Azonosítsa a Graph szolgáltatáshoz tartozó fiók felhasználónevét és jelszavát a meglévő Active Directory-példányban.
* Azonosítsa az Active Directory erdő legfelső szintű FQDN-jét.

## <a name="validate-the-graph-service"></a>A Graph szolgáltatás ellenőrzése

1. Egy olyan számítógépen, amely megfelel az előfeltételeknek, nyisson meg egy rendszergazdai PowerShell-parancssort, majd futtassa a következő parancsot a AzsReadinessChecker telepítéséhez:

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

1. A PowerShell-parancssorból futtassa a következő parancsot a *$graphCredential* változónak a Graph-fiókhoz való beállításához. Cserélje `contoso\graphservice` le a t a fiókra `domain\username` a formátum használatával.

    ```powershell
    $graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"
    ```

1. A PowerShell-parancssorból futtassa a következő parancsot a Graph szolgáltatás érvényesítésének megkezdéséhez. A értéket az erdő `-ForestFQDN` gyökeréhez tartozó FQDN értékként kell megadni.

    ```powershell
    Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential
    ```

1. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, hogy a gráf-integráció követelményeinek állapota OK. A sikeres érvényesítés az alábbi példához hasonló:

    ```powershell
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

Éles környezetekben a kezelői munkaállomás hálózati kapcsolatának tesztelése nem teljesen jelzi az Azure Stack hub számára elérhető kapcsolat teljes körű jelzését. Az Azure Stack hub Stamp nyilvános VIP-hálózatának szüksége van az LDAP-forgalom kapcsolatára az identitás-integráció elvégzéséhez.

## <a name="report-and-log-file"></a>Jelentés és naplófájl

A rendszer minden alkalommal futtatja az eredményeket a **AzsReadinessChecker. log** és a **AzsReadinessCheckerReport. JSON**fájlban. A fájlok helye a PowerShell érvényesítési eredményeivel jelenik meg.

Az érvényesítési fájlok segítségével megoszthatja az állapotot az Azure Stack hub üzembe helyezése vagy az érvényesítési problémák vizsgálata előtt. Mindkét fájl megőrzi az összes további érvényesítési ellenőrzés eredményét. A jelentés megadja az üzembe helyezési csoportnak az identitás konfigurációjának megerősítését. A naplófájl segítséget nyújthat az üzembe helyezéshez vagy a támogatási csoporthoz az érvényesítési problémák kivizsgálásához.

Alapértelmezés szerint mindkét fájl íródik a `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`következőre:.

Használja

* `-OutputPath`: A futtatási parancs végén található *path* paraméter egy másik jelentés helyének megadásához.
* `-CleanReport`: A Run parancs végén található paraméterrel törli a korábbi jelentési információk *AzsReadinessCheckerReport. JSON* fájlját. További információ: [Azure stack hub-ellenőrzési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy érvényesítési ellenőrzés sikertelen, a hiba részletei megjelennek a PowerShell ablakban. Az eszköz az *AzsGraphIntegration. log naplófájlba*is naplózza az adatokat.

## <a name="next-steps"></a>További lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack hub integrációs szempontjai](azure-stack-datacenter-integration.md)  
