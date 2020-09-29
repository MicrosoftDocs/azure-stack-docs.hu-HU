---
title: Felügyelt lemezek Resource Manager-sablonjainak használata Azure Stack hub-ban
description: Ismerje meg a felügyelt és a nem felügyelt lemezek közötti különbséget Azure Resource Manager sablonok használata esetén.
author: sethmanheim
ms.author: sethm
ms.date: 8/25/2020
ms.topic: conceptual
ms.reviewer: wellsluo
ms.lastreviewed: 8/25/2020
ms.openlocfilehash: 2d2f274fffd1a07857b79255587b659003a3a1ce
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88823251"
---
# <a name="use-vm-managed-disks-templates"></a>VM Managed Disks-sablonok használata

Ez a cikk a felügyelt és nem felügyelt lemezek közötti különbségeket ismerteti, ha Azure Resource Manager sablonokat használ a virtuális gépek Azure Stack hub-ban való kiépítéséhez. A példák segítségével a nem felügyelt lemezeket használó meglévő sablonokat felügyelt lemezekre alakíthatja át.

## <a name="unmanaged-disks-template-formatting"></a>Nem felügyelt lemezek sablonjának formázása

Először vessünk egy pillantást a nem felügyelt lemezek üzembe helyezésének módjára. Nem felügyelt lemezek létrehozásakor szükség van egy Storage-fiókra a VHD-fájlok tárolásához. Létrehozhat egy új Storage-fiókot, vagy használhat ilyet, amely már létezik. Hozzon létre egy új Storage-fiók erőforrást a sablon erőforrás-blokkjában a következő módon:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2017-10-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

A virtuálisgép-objektumon belül adjon hozzá egy függőséget a Storage-fiókhoz, hogy az a virtuális gép előtt legyen létrehozva. A `storageProfile` szakaszon belül határozza meg a VHD hely teljes URI-ját, amely a Storage-fiókra hivatkozik, és amely az operációsrendszer-lemez és az adatlemezek esetében szükséges. Az alábbi példa egy operációsrendszer-lemezt hoz létre a rendszerképből és egy üres adatlemezt 1023 GB nál-mérettel:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Felügyelt lemezek sablonjának formázása

Az Azure Managed Disks használatával a lemez legfelső szintű erőforrás lesz, és már nem szükséges a felhasználó által létrehozott Storage-fiók létrehozása. A felügyelt lemezek először az API-verzióban lettek bevezetve `2017-03-30` . A következő szakasz végigvezeti az alapértelmezett beállításokon, és ismerteti a lemezek további testreszabásának módját.

### <a name="default-managed-disk-settings"></a>A felügyelt lemez alapértelmezett beállításai

A felügyelt lemezekkel rendelkező virtuális gépek létrehozásához többé nem kell létrehoznia a Storage-fiók erőforrását. Az alábbi példában az előző, nem felügyelt lemezekre vonatkozó példák között talál néhány különbséget:

- A a egy `apiVersion` "virtualMachines" típusú erőforrástípus verziója, amely támogatja a felügyelt lemezeket.
- `osDisk` és `dataDisks` a továbbiakban nem hivatkozik a VHD-re vonatkozó egyedi URI-ra.
- Ha további tulajdonságok megadása nélkül végzi a telepítést, a lemez a virtuális gép méretétől függően tárolási típust használ. Ha például olyan virtuálisgép-méretet használ, amely támogatja a Premium Storage-ot (például Standard_DS2_v2), akkor a prémium szintű lemezek alapértelmezés szerint lesznek konfigurálva. Ezt a tárolási típus megadásához a lemez SKU-beállítása segítségével módosíthatja.
- Ha nincs megadva a lemez neve, a `<VMName>_OsDisk_1_<randomstring>` rendszer az operációsrendszer-lemez és az `<VMName>_disk<#>_<randomstring>` egyes adatlemezek formátumát veszi alapul.
  - Ha egy virtuális gépet egyéni rendszerképből hoztak létre, akkor a rendszer az egyéni rendszerkép-erőforrásban definiált lemez tulajdonságaiból kéri le a Storage-fiók típusának és a lemez nevének alapértelmezett beállításait. Ezek felülbírálása a sablon értékeinek megadásával lehetséges.
- Alapértelmezés szerint a lemezes gyorsítótárazás az operációsrendszer-lemezre **írható/** olvasható, és **nincs** az adatlemezek esetében.
- A következő példában még mindig van egy Storage-fiók függőség, bár ez csak a diagnosztika tárolására szolgál, és a lemezes tároláshoz nem szükséges:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="use-a-top-level-managed-disk-resource"></a>Legfelső szintű felügyelt lemez erőforrásának használata

A virtuálisgép-objektumban a lemez konfigurációjának megadására Alternatív megoldásként létrehozhat egy legfelső szintű lemez-erőforrást, és csatolhatja azt a virtuális gép létrehozása részeként. Ügyeljen arra, hogy `2017-03-30` az erőforrás-API-verzióként legyen használatban `disks` . Létrehozhat például egy lemezes erőforrást az alábbiak szerint adatlemezként való használatra. Ebben a példában `vmName` a lemez neve részeként van használatban:

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2017-03-30",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

A virtuálisgép-objektumon belül hivatkozzon a csatolni kívánt lemez objektumra. A tulajdonságban létrehozott felügyelt lemez erőforrás-AZONOSÍTÓjának megadásával `managedDisk` engedélyezheti a lemez mellékletét a virtuális gép létrehozásakor. A `apiVersion` virtuális gép erőforrásának beállítása a következőre van beállítva: `2017-12-01` . A rendszer hozzáadja a lemez erőforrásának függőségét annak érdekében, hogy a virtuális gép létrehozása előtt sikeresen létrejött legyen:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Felügyelt rendelkezésre állási készletek létrehozása felügyelt lemezeket használó virtuális gépekkel

Managed Disks-t használó virtuális gépekkel felügyelt rendelkezésre állási csoportok létrehozásához adja hozzá az `sku` objektumot a rendelkezésre állási csoport erőforráshoz, és állítsa a `name` tulajdonságot értékre `Aligned` . Ez a tulajdonság biztosítja, hogy az egyes virtuális gépek lemezei elég elszigeteltek legyenek egymástól az egyes meghibásodási pontok elkerülése érdekében. Azt is vegye figyelembe, hogy a `apiVersion` rendelkezésre állási csoport erőforrása a következőre van beállítva `2017-12-01` :

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 1,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

## <a name="next-steps"></a>Következő lépések

<!--
* For full templates that use managed disks visit the following Azure Quickstart Repo links.
    * [Windows VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
-->

- További információ a felügyelt lemezekről: [Azure stack hub Managed Disks](azure-stack-managed-disk-considerations.md) .
- Tekintse át a [Microsoft. számítás/virtualMachines sablonjának](/azure/templates/microsoft.compute/2017-12-01/virtualmachines)dokumentációját a virtuális gépek erőforrásaihoz című dokumentumban.
- Tekintse át a [Microsoft. számítási/lemezek sablonjának](/azure/templates/microsoft.compute/2017-03-30/disks) dokumentációját a lemez erőforrásaihoz című dokumentumban.
