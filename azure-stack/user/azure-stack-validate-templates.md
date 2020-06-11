---
title: Az Azure Stack hub sablon-ellenőrzési eszközének használata
description: A sablon-ellenőrzési eszközzel megkeresheti Azure Stack hubhoz való központi telepítéshez szükséges sablonokat.
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: 7e003a8b42e479fcc732b8e1146fceb6128cf5f1
ms.sourcegitcommit: d91e47a51a02042f700c6a420f526f511a6db9a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666277"
---
# <a name="use-the-template-validation-tool-in-azure-stack-hub"></a>Az Azure Stack hub sablon-ellenőrzési eszközének használata

A sablon-ellenőrzési eszköz használatával győződjön meg arról, hogy a Azure Resource Manager [sablonjai](azure-stack-arm-templates.md) készen állnak-e a Azure stack hubhoz való üzembe helyezésre. A sablon-ellenőrzési eszköz az Azure Stack hub Tools GitHub-tárházban érhető el. Töltse le az Azure Stack hub-eszközöket az [eszközök letöltése a githubról](../operator/azure-stack-powershell-download.md)című témakörben ismertetett lépések segítségével.

## <a name="overview"></a>Áttekintés

A sablon érvényesítéséhez először létre kell majd adnia egy felhőalapú képesség-fájlt, majd futtatnia kell az ellenőrző eszközt. Használja az alábbi PowerShell-modulokat Azure Stack Hub-eszközökről:

- A **CloudCapabilities** mappában: **AzureRM. CloudCapabilities. psm1** létrehoz egy felhőalapú képességek JSON-fájlt, amely egy Azure stack hub-felhőben lévő szolgáltatásokat és verziókat jelképezi.
- A **TemplateValidator** mappában: a **AzureRM. TemplateValidator. psm1** Felhőbeli képességekkel rendelkező JSON-fájlt használ a sablonok tesztelésére Azure stack hub-ban történő üzembe helyezéshez.

## <a name="build-the-cloud-capabilities-file"></a>A Cloud képességek fájljának összeállítása

A sablon-érvényesítő használata előtt futtassa a **AzureRM. CloudCapabilities** PowerShell-modult, és hozzon létre egy JSON-fájlt.

> [!NOTE]
> Ha frissíti az integrált rendszerét, vagy új szolgáltatásokat vagy virtuális bővítményeket ad hozzá, futtassa újra ezt a modult.

1. Ellenőrizze, hogy van-e kapcsolata Azure Stack hubhoz. Ezek a lépések a Azure Stack Development Kit (ASDK) gazdagépről hajthatók végre, vagy [VPN-](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) kapcsolattal is csatlakozhatnak a munkaállomásról.
2. Importálja a **AzureRM. CloudCapabilities** PowerShell-modult:

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. A **Get-CloudCapabilities** parancsmag használatával lekérheti a szolgáltatási verziókat, és létrehozhatja a FELHŐALAPÚ képességek JSON-fájlját. Ha nem ad meg `-OutputPath` , a (z) **AzureCloudCapabilities.js** fájl az aktuális könyvtárban jön létre. A tényleges Azure-beli hely használata:

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Sablonok ellenőrzése

Ezekkel a lépésekkel érvényesítheti a sablonokat a **AzureRM. TemplateValidator** PowerShell-modul használatával. Saját sablonokat is használhat, vagy használhatja az Azure Stack hub gyors üzembe helyezési [sablonjait](https://github.com/Azure/AzureStack-QuickStart-Templates).

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
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Következő lépések

- [Sablonok üzembe helyezése Azure Stack hubhoz](azure-stack-arm-templates.md)
- [Sablonok fejlesztése Azure Stack hubhoz](azure-stack-develop-templates.md)
