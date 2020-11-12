---
title: Az Azure Stack hub sablon-ellenőrzési eszközének használata
description: A sablon-ellenőrzési eszközzel megkeresheti Azure Stack hubhoz való központi telepítéshez szükséges sablonokat.
author: sethmanheim
ms.topic: article
ms.date: 10/01/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: 35fe7fbd2a3d7004d70e343ca763ea49563f2597
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546565"
---
# <a name="use-the-template-validation-tool-in-azure-stack-hub"></a>Az Azure Stack hub sablon-ellenőrzési eszközének használata

A sablon-ellenőrzési eszköz használatával győződjön meg arról, hogy a Azure Resource Manager [sablonjai](azure-stack-arm-templates.md) készen állnak-e a Azure stack hubhoz való üzembe helyezésre. A sablon-ellenőrzési eszköz az Azure Stack hub Tools GitHub-tárházban érhető el. Töltse le az Azure Stack hub-eszközöket az [eszközök letöltése a githubról](../operator/azure-stack-powershell-download.md)című témakörben ismertetett lépések segítségével.

## <a name="overview"></a>Áttekintés

A sablon érvényesítéséhez először létre kell majd adnia egy felhőalapú képesség-fájlt, majd futtatnia kell az ellenőrző eszközt. Használja az alábbi PowerShell-modulokat Azure Stack Hub-eszközökről:

- A **CloudCapabilities** mappában: az **. CloudCapabilities. psm1** létrehoz egy felhőalapú képességek JSON-fájlt, amely egy Azure stack hub-felhőben lévő szolgáltatásokat és verziókat jelképezi.
- A **TemplateValidator** mappában: **az. TemplateValidator. psm1** egy Felhőbeli képességekkel rendelkező JSON-fájlt használ a sablonok tesztelésére a Azure stack hub üzembe helyezéséhez.

## <a name="build-the-cloud-capabilities-file"></a>A Cloud képességek fájljának összeállítása

A sablon-érvényesítő használata előtt futtassa az az **. CloudCapabilities** PowerShell-modult a JSON-fájl létrehozásához.

> [!NOTE]
> Ha frissíti az integrált rendszerét, vagy új szolgáltatásokat vagy virtuális bővítményeket ad hozzá, futtassa újra ezt a modult.

1. Ellenőrizze, hogy van-e kapcsolata Azure Stack hubhoz. Ezek a lépések a Azure Stack Development Kit (ASDK) gazdagépről hajthatók végre, vagy [VPN-](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) kapcsolattal is csatlakozhatnak a munkaállomásról.
2. Importálja az az **. CloudCapabilities** PowerShell-modult:

    ```powershell
    Import-Module .\CloudCapabilities\Az.CloudCapabilities.psm1
    ```

3. A **Get-CloudCapabilities** parancsmag használatával lekérheti a szolgáltatási verziókat, és létrehozhatja a FELHŐALAPÚ képességek JSON-fájlját. Ha nem ad meg `-OutputPath` , a (z) **AzureCloudCapabilities.js** fájl az aktuális könyvtárban jön létre. A tényleges Azure-beli hely használata:

    ```powershell
    Get-AzCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Sablonok ellenőrzése

Ezekkel a lépésekkel érvényesítheti a sablonokat az az **. TemplateValidator** PowerShell-modul használatával. Saját sablonokat is használhat, vagy használhatja az Azure Stack hub gyors üzembe helyezési [sablonjait](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importálja az az **. TemplateValidator. psm1** PowerShell-modult:

    ```powershell
    cd "c:\AzureStack-Tools-az\TemplateValidator"
    Import-Module .\Az.TemplateValidator.psm1
    ```

2. Futtassa a sablon-ellenőrzőt:

    ```powershell
    Test-AzTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

A sablon-ellenőrzési figyelmeztetések vagy hibák a PowerShell-konzolon jelennek meg, és a forrás könyvtárában lévő HTML-fájlba íródnak. Az alábbi képernyőkép egy ellenőrzési jelentésre mutat be példát:

![Sablon-ellenőrzési jelentés](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Paraméterek

A sablon-érvényesítő parancsmag a következő paramétereket támogatja.

| Paraméter | Leírás | Kötelező |
| ----- | -----| ----- |
| `TemplatePath` | Megadja Azure Resource Manager sablonok rekurzív megkeresésének elérési útját. | Yes |
| `TemplatePattern` | Megadja az egyeztetendő sablonfájlok nevét. | No |
| `CapabilitiesPath` | Megadja a Felhőbeli képességek JSON-fájljának elérési útját. | Yes |
| `IncludeComputeCapabilities` | Magában foglalja a IaaS-erőforrások, például a virtuálisgép-méretek és a virtuálisgép-bővítmények értékelését. | No |
| `IncludeStorageCapabilities` | Magában foglalja a tárolási erőforrások, például az SKU-típusok kiértékelését. | No |
| `Report` | Megadja a generált HTML-jelentés nevét. | No |
| `Verbose` | Naplózza a hibákat és a figyelmeztetéseket a konzolon. | No|

### <a name="examples"></a>Példák

Ez a példa a helyi tárterületre letöltött összes [Azure stack hub](https://github.com/Azure/AzureStack-QuickStart-Templates) gyors üzembe helyezési sablont érvényesíti. A példa a virtuális gép (VM) méretét és bővítményeit is ellenőrzi a ASDK képességekkel szemben:

```powershell
test-AzTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Következő lépések

- [Sablonok üzembe helyezése Azure Stack hubhoz](azure-stack-arm-templates.md)
- [Sablonok fejlesztése Azure Stack hubhoz](azure-stack-develop-templates.md)
