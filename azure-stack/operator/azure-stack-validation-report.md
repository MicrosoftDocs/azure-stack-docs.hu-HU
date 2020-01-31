---
title: Azure Stack hub ellenőrzési jelentése
description: Az ellenőrzési eredmények áttekintéséhez használja az Azure Stack hub Readiness-ellenőrző jelentését.
author: ihenkel
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 21c19a368b62a35e3b2daeef2a0e36f84eb4e527
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880574"
---
# <a name="azure-stack-hub-validation-report"></a>Azure Stack hub ellenőrzési jelentés

Az *Azure stack hub Readiness-ellenőrző* eszköz használatával olyan érvényesítéseket futtathat, amelyek támogatják egy Azure stack hub-környezet telepítését és karbantartását. Az eszköz az eredményeket egy. JSON-jelentési fájlba írja. A jelentés részletes és összegzett információkat jelenít meg az Azure Stack hub üzembe helyezéséhez szükséges előfeltételek állapotáról. A jelentés a meglévő Azure Stack hub-telepítések titkainak rotációs adatait is megjeleníti.  

## <a name="where-to-find-the-report"></a>Hol található a jelentés

Az eszköz futtatásakor a naplózza az eredményeket a **AzsReadinessCheckerReport. JSON**fájlban. Az eszköz egy **AzsReadinessChecker. log**nevű naplót is létrehoz. A fájlok helye a PowerShell érvényesítési eredményeivel együtt jelenik meg:

![Futtatás – ellenőrzés](./media/azure-stack-validation-report/validation.png)

Mindkét fájl megőrzi a további ellenőrzési ellenőrzések eredményét, ha ugyanazon a számítógépen futnak. Például az eszköz futtatható a tanúsítványok érvényesítéséhez, újra futtatva az Azure Identity érvényesítéséhez, majd egy harmadik alkalommal a regisztráció érvényesítéséhez. Mindhárom érvényesítés eredménye elérhető a létrejövő. JSON-jelentésben.  

Alapértelmezés szerint mindkét fájl a **C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.JSON**-be van írva.  

- Egy másik jelentés helyének megadásához használja a parancssor végén található `-OutputPath <path>` paramétert.
- A parancssor végén található `-CleanReport` paraméterrel törölheti az eszköz előző futtatásával kapcsolatos információkat a **AzsReadinessCheckerReport. JSON**fájlból.

## <a name="view-the-report"></a>A jelentés elolvasása

Ha a jelentést a PowerShellben szeretné megtekinteni, adja meg a jelentés elérési útját `-ReportPath`értékként. Ez a parancs megjeleníti a jelentés tartalmát, és azonosítja azokat az érvényességeket, amelyek még nem rendelkeznek eredménnyel.

Ha például a jelentést egy olyan PowerShell-parancssorból szeretné megtekinteni, amely a jelentés helyét tartalmazó helyre van nyitva, futtassa a következő parancsot:

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

A jelentés összegzésének megtekintéséhez hozzáadhatja a `-summary` paramétert a PowerShell-parancs végéhez. Példa:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

Az összefoglalás olyan érvényesítéseket mutat be, amelyek nem rendelkeznek eredményekkel, és a befejezett érvényesítések esetén a pass vagy a Fail utasítást jelzi. A kimenet a következő példához hasonló:

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

Ha egy olyan jelentést szeretne megtekinteni, amely egyetlen ellenőrzési típusra van szűrve, használja a **-ReportSections** paramétert az alábbi értékek egyikével:

- Tanúsítvány
- AzureRegistration
- AzureIdentity
- Gráf
- ADFS
- Feladatok
- Mind  

Ha például csak a tanúsítványok jelentésének összegzését szeretné megtekinteni, használja a következő PowerShell-parancssort:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
