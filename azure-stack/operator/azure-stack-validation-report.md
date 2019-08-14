---
title: Azure Stack ellenőrzési jelentés | Microsoft Docs
description: Az ellenőrzési eredmények áttekintéséhez használja a Azure Stack Readiness-ellenőrző jelentését.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: c00ce005ac72fcde34b58a1afe7e134c27274247
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991724"
---
# <a name="azure-stack-validation-report"></a>Azure Stack ellenőrzési jelentés

Használja a *Azure stack Readiness-ellenőrző* eszközt olyan érvényesítések futtatására, amelyek támogatják egy Azure stack-környezet telepítését és karbantartását. Az eszköz az eredményeket egy. JSON-jelentési fájlba írja. A jelentés részletes és összegzett információkat jelenít meg az Azure Stack telepítéséhez szükséges előfeltételek állapotáról. A jelentés a meglévő Azure Stack központi telepítések titkainak rotációs adatait is megjeleníti.  

## <a name="where-to-find-the-report"></a>Hol található a jelentés

Az eszköz futtatásakor a naplózza az eredményeket a **AzsReadinessCheckerReport. JSON**fájlban. Az eszköz egy **AzsReadinessChecker. log**nevű naplót is létrehoz. A fájlok helye a PowerShell érvényesítési eredményeivel együtt jelenik meg:

![run-validation](./media/azure-stack-validation-report/validation.png)

Mindkét fájl megőrzi a további ellenőrzési ellenőrzések eredményét, ha ugyanazon a számítógépen futnak. Például az eszköz futtatható a tanúsítványok érvényesítéséhez, újra futtatva az Azure Identity érvényesítéséhez, majd egy harmadik alkalommal a regisztráció érvényesítéséhez. Mindhárom érvényesítés eredménye elérhető a létrejövő. JSON-jelentésben.  

Alapértelmezés szerint mindkét fájl a **C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.JSON**-be van írva.  

- Egy másik jelentés helyének megadásához használja a parancssor végén található paramétert.`-OutputPath <path>`
- A parancssor végén található paraméterreltörölhetiazeszközelőzőfuttatásávalkapcsolatosinformációkataAzsReadinessCheckerReport.JSONfájlból`-CleanReport` .

## <a name="view-the-report"></a>A jelentés megtekintése

Ha a jelentést a PowerShellben szeretné megtekinteni, adja meg a jelentés elérési `-ReportPath`útját a következő értékként:. Ez a parancs megjeleníti a jelentés tartalmát, és azonosítja azokat az érvényességeket, amelyek még nem rendelkeznek eredménnyel.

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

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>A jelentés összegzésének megtekintése

A jelentés összegzésének megtekintéséhez adja hozzá a `-summary` paramétert a PowerShell-parancs végéhez. Példa:

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

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Szűrt jelentés megtekintése

Ha egy olyan jelentést szeretne megtekinteni, amely egyetlen ellenőrzési típusra van szűrve, használja a **-ReportSections** paramétert az alábbi értékek egyikével:

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
