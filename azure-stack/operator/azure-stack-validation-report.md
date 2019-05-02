---
title: Ellenőrzési jelentés az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack készültségi ellenőrző jelentés segítségével tekintse át az ellenőrzési eredmények.
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
ms.date: 04/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: d02173731f8cf7834160a0228c589b036aac7fe6
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984019"
---
# <a name="azure-stack-validation-report"></a>Az Azure Stack érvényesítési jelentés

Használja a *Azure Stack készültségi ellenőrző* eszköz, amely támogatja az üzembe helyezés és a egy Azure Stack-környezet karbantartás ellenőrzések futtatását. Az eszköz eredmények jelentés .JSON kiterjesztésű fájlba írja. A jelentés üzembe helyezhető Azure Stacket előfeltételei állapotával kapcsolatos részletes és összesített adatokat jeleníti meg. A jelentés emellett az Azure Stack-telepítések meglévő titkos kódok rotációja információit jeleníti meg.  

## <a name="where-to-find-the-report"></a>Hol található a jelentés

Az eszköz futtatásakor naplózza az eredményeket **AzsReadinessCheckerReport.json**. Az eszközt is létrehoz egy naplófájlt nevű **AzsReadinessChecker.log**. Ezek a fájlok helyét az ellenőrzés eredményét a PowerShellben együtt jelennek meg:

![run-validation](./media/azure-stack-validation-report/validation.png)

Mindkét fájl továbbra is fennáll, további érvényességi ellenőrzések üzemidejének a ugyanazon a számítógépen. Ha például az eszköz futtatható tanúsítványok érvényesítésére, és futtassa újra az Azure-identitás ellenőrzése, majd harmadszor a regisztráció érvényesítése. Az összes három ellenőrzések eredményeit az eredményül kapott .json jelentés érhető el.  

Alapértelmezés szerint mindkét fájlt írt **C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Használja a `-OutputPath <path>` paraméter a parancssorban adja meg egy másik jelentés helyét végén.
- Használja a `-CleanReport` paraméter a parancssorban, törölje az eszköz az előző futtatásokat információ végén **AzsReadinessCheckerReport.json**.

## <a name="view-the-report"></a>A jelentés elolvasása

A jelentés megtekintése a PowerShellben, adja meg a jelentés elérési útja értéket `-ReportPath`. Ez a parancs megjeleníti a jelentés tartalmát, és azonosítja az ellenőrzés eredménye még nem rendelkező.

Például a jelentés egy PowerShell-parancssort, amely meg van nyitva a helyet, ahol a jelentés megtalálható a megtekintéséhez futtassa a következő parancsot:

```shell
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

## <a name="view-the-report-summary"></a>Összesítő jelentés megtekintése

A jelentés megtekintésére, hozzáadhatja a `-summary` paramétert a PowerShell-parancs végéhez. Példa:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

Az összefoglalás ellenőrzések, amelyek nem rendelkeznek az eredményeket megjeleníti, és azt jelzi, hogy továbbítja vagy, amelyek a teljes ellenőrzések sikertelenek. A kimenet a következő példához hasonló:

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

Szűrt jelentés egy adott típusú érvényesítési megtekintéséhez használja a **- ReportSections** paramétert a következő értékek egyikét:

- Tanúsítvány
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Feladatok
- Összes  

Például a jelentés megtekintéséhez a tanúsítványok összegzése csak, használja a következő PowerShell-parancssorból:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
