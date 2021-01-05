---
title: Sablonok fejlesztése Azure Stack hubhoz
description: Ismerje meg, hogyan fejleszthet Azure Resource Manager-sablonokat az Azure és az Azure Stack hub közötti alkalmazás-hordozhatósághoz.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 2d449adc59be9051188c1d942ae54d6a0761d28e
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874217"
---
# <a name="develop-templates-for-azure-stack-hub-with-azure-resource-manager"></a>Sablonok fejlesztése Azure Stack hubhoz Azure Resource Manager

Az alkalmazás fejlesztése során fontos, hogy az Azure és a Azure Stack hub között a sablon hordozhatósága is megtörténjen. Ez a cikk a Azure Resource Manager- [sablonok](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)fejlesztésének szempontjait ismerteti. Ezekkel a sablonokkal az alkalmazás prototípusát és tesztelési célú üzembe helyezését az Azure-ban Azure Stack hub-környezethez való hozzáférés nélkül végezheti el.

## <a name="resource-provider-availability"></a>Erőforrás-szolgáltató rendelkezésre állása

A telepíteni kívánt sablonnak csak Microsoft Azure szolgáltatásokat kell használnia, amelyek már elérhetők vagy előzetes verzióban, Azure Stack hub-ban.

## <a name="public-namespaces"></a>Nyilvános névterek

Mivel Azure Stack hub az adatközpontban üzemel, különböző szolgáltatási végponti névterek vannak, mint az Azure nyilvános felhője. Ennek eredményeképpen a Azure Resource Manager-sablonokban rögzített nyilvános végpontok meghiúsulnak, amikor az Azure Stack hubhoz próbálja telepíteni őket. A és a függvények használatával dinamikusan hozhat létre szolgáltatási végpontokat az `reference` `concatenate` erőforrás-szolgáltató értékének az üzembe helyezés során való lekéréséhez. Például ahelyett, hogy a sablonban rögzített kódolást használ `blob.core.windows.net` , a [primaryEndpoints. blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) beolvasásával dinamikusan állíthatja be a *osDisk. URI* végpontot:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>API-verziószámozás

Az Azure-szolgáltatási verziók eltérőek lehetnek az Azure és az Azure Stack hub között. Minden erőforráshoz a **apiVersion** attribútum szükséges, amely meghatározza a kínált képességeket. Az Azure Stack hub API-verziójának kompatibilitásának biztosításához a következő API-verziók érvényesek minden erőforrás-szolgáltató esetében:

| Erőforrás-szolgáltató | apiVersion |
| --- | --- |
| Compute |**2015-06-15** |
| Network (Hálózat) |**2015-06-15**, **2015-05-01 – előzetes** verzió |
| Storage |**2016-01-01**, **2015-06-15**, **2015-05-01 – előzetes** verzió |
| KeyVault | **2015-06-01** |
| App Service |**2015-08-01** |

## <a name="template-functions"></a>Sablonfüggvények

Azure Resource Manager [függvények](/azure/azure-resource-manager/resource-group-template-functions) biztosítják a dinamikus sablonok létrehozásához szükséges képességeket. A függvények például a következő feladatokhoz használhatók:

* Karakterláncok összefűzése vagy formázása.
* Más erőforrásokból származó értékekre hivatkozik.
* Több példány üzembe helyezéséhez szükséges erőforrások megismétlése.

Ezek a függvények nem érhetők el Azure Stack központban:

* Kihagyás
* Eltarthat

## <a name="resource-location"></a>Erőforrás helye

Azure Resource Manager-sablonok egy `location` attribútum használatával helyezik el az erőforrásokat az üzembe helyezés során. Az Azure-ban a helyszínek egy régióra, például az USA nyugati régiójára vagy Dél-Amerikára vonatkoznak. Azure Stack központban a helyszínek eltérőek, mert Azure Stack hub az adatközpontjában van. Annak biztosítása érdekében, hogy a sablonok átvihetők legyenek az Azure és a Azure Stack hub között, az egyes erőforrások telepítésekor az erőforráscsoport helyére kell hivatkoznia. Ezt a használatával `[resourceGroup().Location]` biztosíthatja, hogy az összes erőforrás örökölje az erőforráscsoport helyét. A következő kód egy példa arra, hogyan használhatja ezt a függvényt egy Storage-fiók telepítésekor:

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

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok üzembe helyezése az Azure CLI-vel](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
