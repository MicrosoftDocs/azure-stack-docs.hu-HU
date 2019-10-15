---
title: Sablonok fejlesztése a Azure Stackhoz | Microsoft Docs
description: Ismerje meg, hogyan fejleszthet Azure Resource Manager-sablonokat az Azure és a Azure Stack közötti alkalmazás-hordozhatósághoz.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 96e43607809c192b9498c092b4a2584cec40a515
ms.sourcegitcommit: 7226979ece29d9619c959b11352be601562b41d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72304101"
---
# <a name="develop-templates-for-azure-stack-with-azure-resource-manager"></a>Sablonok fejlesztése a Azure Stackhoz Azure Resource Manager

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az alkalmazás fejlesztése során fontos, hogy az Azure és a Azure Stack között a sablon hordozhatósága legyen. Ez a cikk a Azure Resource Manager- [sablonok](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)fejlesztésének szempontjait ismerteti. Ezekkel a sablonokkal az alkalmazás prototípusát és tesztelési célú üzembe helyezését az Azure-ban Azure Stack környezethez való hozzáférés nélkül végezheti el.

## <a name="resource-provider-availability"></a>Erőforrás-szolgáltató rendelkezésre állása

A telepíteni kívánt sablonnak csak Microsoft Azure szolgáltatásokat kell használnia, amelyek már elérhetők vagy előzetes verzióban, Azure Stack.

## <a name="public-namespaces"></a>Nyilvános névterek

Mivel Azure Stack a saját adatközpontjában található, különböző szolgáltatási végponti névterek vannak, mint az Azure nyilvános felhője. Ennek eredményeképpen a Azure Resource Manager-sablonokban található, rögzített nyilvános végpontok meghiúsulnak, amikor Azure Stackre próbálják telepíteni őket. A `reference` és a `concatenate` függvények használatával dinamikusan építhet ki szolgáltatási végpontokat az erőforrás-szolgáltató értékének az üzembe helyezés során való lekéréséhez. Például ahelyett, hogy a sablonban nem a `blob.core.windows.net` kódolást használ, a [primaryEndpoints. blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) beolvasásával dinamikusan állíthatja be a *osDisk. URI* végpontot:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>API-verziószámozás

Az Azure-szolgáltatási verziók eltérőek lehetnek az Azure-ban és a Azure Stackban. Minden erőforráshoz a **apiVersion** attribútum szükséges, amely meghatározza a kínált képességeket. Az Azure Stack API-verziójának kompatibilitásának biztosítása érdekében a következő API-verziók érvényesek minden erőforrás-szolgáltatónál:

| Erőforrás-szolgáltató | apiVersion |
| --- | --- |
| Számítási szolgáltatások |**2015-06-15** |
| Network (Hálózat) |**2015-06-15**, **2015-05-01 – előzetes** verzió |
| Adattárolás |**2016-01-01**, **2015-06-15**, **2015-05-01 – előzetes** verzió |
| KeyVault | **2015-06-01** |
| App Service |**2015-08-01** |

## <a name="template-functions"></a>Sablonfüggvények

Azure Resource Manager [függvények](/azure/azure-resource-manager/resource-group-template-functions) biztosítják a dinamikus sablonok létrehozásához szükséges képességeket. A függvények például a következő feladatokhoz használhatók:

* Karakterláncok összefűzése vagy formázása.
* Más erőforrásokból származó értékekre hivatkozik.
* Több példány üzembe helyezéséhez szükséges erőforrások megismétlése.

Ezek a függvények nem érhetők el Azure Stackban:

* Kihagyás
* Eltarthat

## <a name="resource-location"></a>Erőforrás helye

Azure Resource Manager-sablonok egy `location` attribútummal helyezik el az erőforrásokat az üzembe helyezés során. Az Azure-ban a helyszínek egy régióra, például az USA nyugati régiójára vagy Dél-Amerikára vonatkoznak. Azure Stack a helyszínek eltérnek, mert Azure Stack az adatközpontjában van. Annak biztosítása érdekében, hogy a sablonok átvihetők legyenek az Azure és a Azure Stack között, az egyes erőforrások telepítésekor az erőforráscsoport helyére kell hivatkoznia. Ezt a `[resourceGroup().Location]` használatával végezheti el, hogy az összes erőforrás örökölje az erőforráscsoport helyét. A következő kód egy példa arra, hogyan használhatja ezt a függvényt egy Storage-fiók telepítésekor:

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>Következő lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok üzembe helyezése az Azure CLI-vel](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
