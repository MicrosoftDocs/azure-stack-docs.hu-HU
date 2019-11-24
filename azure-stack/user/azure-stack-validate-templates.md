---
title: A sablon-ellenőrzési eszköz használata a Azure Stackban | Microsoft Docs
description: A sablon-ellenőrzési eszközzel megvizsgálhatja, hogy Azure Stack-e a központi telepítéshez szükséges sablonok.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: a5e86f0f0719e30ef693c736ac4c70f05830c3bb
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961666"
---
# <a name="use-the-template-validation-tool-in-azure-stack"></a>A sablon-ellenőrzési eszköz használata a Azure Stack

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A sablon-ellenőrzési eszköz használatával győződjön meg arról, hogy a Azure Resource Manager [sablonjai](azure-stack-arm-templates.md) készen állnak-e a Azure stack üzembe helyezésére. A sablon-ellenőrzési eszköz a Azure Stack Tools GitHub-tárház részeként érhető el. Töltse le a Azure Stack-eszközöket az [eszközök letöltése a githubról](../operator/azure-stack-powershell-download.md)című témakörben ismertetett lépések segítségével.

## <a name="overview"></a>Áttekintés

A sablon érvényesítéséhez először létre kell majd adnia egy felhőalapú képesség-fájlt, majd futtatnia kell az ellenőrző eszközt. A következő PowerShell-modulokat használja Azure Stack eszközökről:

- A **CloudCapabilities** mappában: **AzureRM. CloudCapabilities. psm1** létrehoz egy FELHŐALAPÚ képességeket tartalmazó JSON-fájlt, amely egy Azure stack-felhőben található szolgáltatásokat és verziókat jelképezi.
- A **TemplateValidator** mappában: a **AzureRM. TemplateValidator. psm1** a Cloud képességek JSON-fájlját használja a sablonok teszteléséhez a Azure stack-ben való üzembe helyezéshez.

## <a name="build-the-cloud-capabilities-file"></a>A Cloud képességek fájljának összeállítása

A sablon-érvényesítő használata előtt futtassa a **AzureRM. CloudCapabilities** PowerShell-modult, és hozzon létre egy JSON-fájlt.

>[!NOTE]
> Ha frissíti az integrált rendszerét, vagy új szolgáltatásokat vagy virtuális bővítményeket ad hozzá, futtassa újra ezt a modult.

1. Ellenőrizze, hogy van-e kapcsolata Azure Stackhoz. Ezek a lépések a Azure Stack Development Kit (ASDK) gazdagépről hajthatók végre, vagy [VPN-](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) kapcsolattal is csatlakozhatnak a munkaállomásról.
2. Importálja a **AzureRM. CloudCapabilities** PowerShell-modult:

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. A **Get-CloudCapabilities** parancsmag használatával lekérheti a szolgáltatási verziókat, és létrehozhatja a FELHŐALAPÚ képességek JSON-fájlját. Ha nem ad meg `-OutputPath`, a **AzureCloudCapabilities. JSON** fájl az aktuális könyvtárban jön létre. A tényleges Azure-beli hely használata:

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Sablonok ellenőrzése

Ezekkel a lépésekkel érvényesítheti a sablonokat a **AzureRM. TemplateValidator** PowerShell-modul használatával. Használhatja a saját sablonjait, vagy használhatja a Azure Stack gyors üzembe helyezési [sablonokat](https://github.com/Azure/AzureStack-QuickStart-Templates)is.

1. Importálja a **AzureRM. TemplateValidator. psm1** PowerShell-modult:

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Futtassa a sablon-ellenőrzőt:

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

A sablon-ellenőrzési figyelmeztetések vagy hibák a PowerShell-konzolon jelennek meg, és a forrás könyvtárában lévő HTML-fájlba íródnak. Az alábbi képernyőkép egy ellenőrzési jelentésre mutat be példát:

![Sablon-ellenőrzési jelentés](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Paraméterek

A sablon-érvényesítő parancsmag a következő paramétereket támogatja.

| Paraméter | Leírás | Kötelező |
| ----- | -----| ----- |
| `TemplatePath` | Megadja Azure Resource Manager sablonok rekurzív megkeresésének elérési útját. | Igen |
| `TemplatePattern` | Megadja az egyeztetendő sablonfájlok nevét. | Nem |
| `CapabilitiesPath` | Megadja a Felhőbeli képességek JSON-fájljának elérési útját. | Igen |
| `IncludeComputeCapabilities` | Magában foglalja a IaaS-erőforrások, például a virtuálisgép-méretek és a virtuálisgép-bővítmények értékelését. | Nem |
| `IncludeStorageCapabilities` | Magában foglalja a tárolási erőforrások, például az SKU-típusok kiértékelését. | Nem |
| `Report` | Megadja a generált HTML-jelentés nevét. | Nem |
| `Verbose` | Naplózza a hibákat és a figyelmeztetéseket a konzolon. | Nem|

### <a name="examples"></a>Példák

Ez a példa a helyi tárterületre letöltött összes Azure Stack rövid útmutató- [sablont](https://github.com/Azure/AzureStack-QuickStart-Templates) érvényesíti. A példa a virtuális gép (VM) méretét és bővítményeit is ellenőrzi a ASDK képességekkel szemben:

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>További lépések

- [Sablonok üzembe helyezése Azure Stack](azure-stack-arm-templates.md)
- [Sablonok fejlesztése az Azure Stackhez](azure-stack-develop-templates.md)
