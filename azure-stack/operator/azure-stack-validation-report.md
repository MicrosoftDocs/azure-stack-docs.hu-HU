---
title: Azure Stack hub ellenőrzési jelentés
titleSuffix: Azure Stack Hub
description: Ellenőrzési jelentés létrehozásához használja az Azure Stack hub Readiness-ellenőrző eszközt.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: 5cc3f24c62fc9c91637744cf031da39c6c7279fb
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "78366500"
---
# <a name="azure-stack-hub-validation-report"></a>Azure Stack hub ellenőrzési jelentés

Az [Azure stack hub Readiness-ellenőrző eszköz](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.2002.1111.69) használatával olyan érvényesítéseket futtathat, amelyek támogatják egy Azure stack hub-környezet telepítését és karbantartását. Az eszköz az eredményeket egy. JSON-jelentési fájlba írja. A jelentés részletes és összegzett információkat jelenít meg az Azure Stack hub üzembe helyezéséhez szükséges előfeltételek állapotáról. A jelentés a meglévő Azure Stack hub-telepítések titkainak rotációs adatait is megjeleníti.  

## <a name="where-to-find-the-report"></a>Hol található a jelentés

Az eszköz futtatásakor a naplózza az eredményeket a **AzsReadinessCheckerReport. JSON**fájlban. Az eszköz egy **AzsReadinessChecker. log**nevű naplót is létrehoz. A fájlok helye a PowerShell érvényesítési eredményeivel együtt jelenik meg:

![Azure Stack hub Readiness-ellenőrző futtatásának érvényesítési eredményei](./media/azure-stack-validation-report/validation.png)

Mindkét fájl megőrzi a további ellenőrzési ellenőrzések eredményét, ha ugyanazon a számítógépen futnak. Például az eszköz futtatható a tanúsítványok érvényesítéséhez, újra futtatva az Azure Identity érvényesítéséhez, majd egy harmadik alkalommal a regisztráció érvényesítéséhez. Mindhárom érvényesítés eredménye elérhető a létrejövő. JSON-jelentésben.  

Alapértelmezés szerint mindkét fájl íródik a `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`következőre:.  

- Egy másik `-OutputPath <path>` jelentés helyének megadásához használja a parancssor végén található paramétert.
- A parancssor `-CleanReport` végén található paraméterrel törölheti az eszköz előző futtatásával kapcsolatos információkat a **AzsReadinessCheckerReport. JSON**fájlból.

## <a name="view-the-report"></a>A jelentés megtekintése

Ha a jelentést a PowerShellben szeretné megtekinteni, adja meg a jelentés elérési `-ReportPath`útját a következő értékként:. Ez a parancs megjeleníti a jelentés tartalmát, és azonosítja azokat az érvényességeket, amelyek még nem rendelkeznek eredménnyel.

Ha például a jelentést egy olyan PowerShell-parancssorból szeretné megtekinteni, amelyen a jelentés helye található, futtassa a következő parancsot:

```powershell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

A kimenet a következő példához hasonló:

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>A jelentés összegzésének megtekintése

A jelentés összegzésének megtekintéséhez adja hozzá a `-summary` paramétert a PowerShell-parancs végéhez. Például:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

Az összefoglalás olyan érvényesítéseket mutat be, amelyek nem rendelkeznek eredménnyel, és a befejezett érvényesítések esetén a pass vagy a Fail argumentumot jelzi. A kimenet a következő példához hasonló:

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Szűrt jelentés megtekintése

Ha egy olyan jelentést szeretne megtekinteni, amely egyetlen ellenőrzési típusra van szűrve, használja a `-ReportSections` paramétert az alábbi értékek egyikével:

- Tanúsítvány
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Feladatok
- Összes  

Ha például csak a tanúsítványok jelentésének összegzését szeretné megtekinteni, használja a következő PowerShell-parancssort:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
