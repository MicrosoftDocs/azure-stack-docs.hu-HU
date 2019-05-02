---
title: Ellenőrizze az Azure Stack Azure Graph-integráció
description: Az Azure Stack-készültségi ellenőrző segítségével ellenőrizze a graph-integráció az Azure Stackhez.
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
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: a34d070e5e26113216fe8a5fd598b0b5e27687f2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984546"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Ellenőrizze a graph-integráció az Azure Stackhez

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) használatával, hogy az készen áll az Azure Stack graph-integráció. Adatközpont integrációja megkezdése előtt, illetve az Azure Stack üzembe helyezés előtt érvényesíteni a graph-integráció.

A készenléti ellenőrző ellenőrzi:

* A hitelesítő adatokat a szolgáltatás fiók létrehozása a graph-integráció az Active Directory lekérdezés megfelelő jogosultságokkal rendelkezik.
* A *globális katalógus* feloldható, és felerősítéséhez.
* A KDC feloldható és felerősítéséhez.
* Szükséges hálózati kapcsolat van.

Azure Stack adatközpont-integrációval kapcsolatos további információkért lásd: [Azure Stack adatközpont integrációja - identitás](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>A készenléti-ellenőrző eszköz beolvasása

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) legújabb verziójának letöltése a [PowerShell-galériából](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek vonatkoznak a helyen kell lennie.

**A számítógép, amelyen az eszköz fut:**

* Windows 10-es vagy Windows Server 2016, a tartományi kapcsolat.
* A PowerShell 5.1-es vagy újabb. A verzió ellenőrzéséhez futtassa a következő PowerShell-parancsot, és tekintse át a *fő* verzió és *kisebb* verziók:  
   > `$PSVersionTable.PSVersion`
* Az Active Directory PowerShell-modul.
* Legújabb verzióját a [a Microsoft Azure Stack készültségi ellenőrző](https://aka.ms/AzsReadinessChecker) eszközt.

**Az Active Directory-környezetet:**

* Azonosítsa a felhasználónevet és jelszót egy olyan fiók, a graph szolgáltatás a meglévő Active Directory-példányban.
* Azonosítsa az Active Directory-erdő gyökerének teljes Tartománynevét.

## <a name="validate-the-graph-service"></a>A graph szolgáltatás ellenőrzése

1. A számítógépen, amely megfelel az előfeltételeknek nyisson meg egy rendszergazdai PowerShell-parancssort, és futtassa a következő parancsot a AzsReadinessChecker telepítéséhez:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. A PowerShell használatával futtassa az alábbi parancsot a *$graphCredential* változót a gráfadatbázis-fiók. Cserélje le `contoso\graphservice` használatával a fiókjához a `domain\username` formátumban.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. A PowerShell használatával a következő parancsot a graph szolgáltatás ellenőrzés indításához. Adja meg az értékét **- ForestFQDN** az erdő legfelső szintű FQDN-ként.

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Az eszköz futtatása után tekintse át a kimenetet. Győződjön meg arról, hogy az állapot rendben grafikon integrációs követelményeinek. Egy sikeres ellenőrzése az alábbi példához hasonló:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

Éles környezetben az operátor munkaállomás a hálózati kapcsolat tesztelése nem teljes mértékben az Azure Stack számára elérhető kapcsolatok jelezhetnek. Az Azure Stack-blokk nyilvános VIP-hálózat kapcsolódnia kell az LDAP-forgalom identitásintegráció végrehajtásához.

## <a name="report-and-log-file"></a>A jelentés és-naplófájl

Minden egyes alkalommal érvényesítési fut, az eredmények naplózza **AzsReadinessChecker.log** és **AzsReadinessCheckerReport.json**. Ezek a fájlok helyét jelenik meg az ellenőrzés eredményét a PowerShellben.

Az érvényesítés fájlok segítségével megoszthatja állapot érvényesítési problémák vizsgálatához vagy az Azure Stack üzembe helyezése előtt. Mindkét fájlt megmarad, egyes további ellenőrzés eredményeit. A jelentés tartalmazza a telepítési csapat megerősítés az identitás-konfiguráció. A naplófájl segíthet a telepítés vagy a támogatási csapat érvényesítési problémák kivizsgálásában.

Alapértelmezés szerint mindkét fájlt írt `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Használat:

* **-OutputPath**: A *elérési út* paraméter adja meg egy másik jelentés helyét a futtatási parancs végén.
* **-CleanReport**: A paraméter, törölje a futtatási parancs végén *AzsReadinessCheckerReport.json* előző jelentés információk. További információkért lásd: [Azure Stack érvényesítési jelentés](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Érvényesítési hibák

Ha egy ellenőrzés nem sikerül, a hiba részleteit a PowerShell-ablakban jelennek meg. Az eszköz is naplózza az adatokat *AzsGraphIntegration.log*.

## <a name="next-steps"></a>További lépések

[A készültségi jelentés megtekintése](azure-stack-validation-report.md)  
[Általános Azure Stack-integráció szempontok](azure-stack-datacenter-integration.md)  
