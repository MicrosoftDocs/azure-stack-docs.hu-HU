---
title: Windows virtuális gép létrehozása az Azure Stacken Azure parancssori felületével |} A Microsoft Docs
description: Ismerje meg, hogyan Windows virtuális gép létrehozása az Azure Stack az Azure CLI használatával
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 67e0ccfa883e79d66eb9ca38a6cf15f00154c487
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64301142"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Gyors útmutató: Windows Server virtuális gép létrehozása az Azure Stack az Azure parancssori felület használatával

‎*A következőkre vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Létrehozhat egy Windows Server 2016 virtuális gép az Azure parancssori felület használatával. Kövesse a cikkben hozhat létre és használhat egy virtuális gépet. Ez a cikk is biztosít az alábbi lépéseket:

* Csatlakozzon a virtuális géphez a távoli ügyfélhez.
* Az IIS-webkiszolgáló telepítéséhez, és az alapértelmezett kezdőlap megtekintéséhez.
* Az erőforrások törlése.

## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy az Azure Stack – operátor hozzáadása a **Windows Server 2016** lemezképet az Azure Stack piactéren.

* Az Azure Stack egy adott verzióját az Azure CLI használatával az erőforrások létrehozásához és kezeléséhez szükséges. Ha nincs konfigurálva az Azure Stackhez készült Azure CLI, kövesse a lépéseket [telepítése és konfigurálása az Azure CLI-vel](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Egy erőforráscsoport olyan logikai tároló, ahol üzembe helyezése és kezelése az Azure Stack-erőforrások. Az Azure Stack környezettel, futtassa a [az csoport létrehozása](/cli/azure/group#az-group-create) parancs használatával hozzon létre egy erőforráscsoportot.

> [!NOTE]
>  Hitelesítésikód-példák az összes változót értékek vannak hozzárendelve. Megadhat azonban új értéket, ha szeretné.

Az alábbi példa egy helyi helyen myResourceGroup nevű erőforráscsoportot hoz létre.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet (VM) használatával a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) parancsot. Az alábbi példában a myVM nevű virtuális Gépet hoz létre. Ebben a példában használja Bemutatofelhasznalo rendszergazdanevet és Demouser@123 felhasználói jelszót. Ezeket az értékeket módosítsa úgy, hogy válasszon a környezetének megfelelő.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

A virtuális gép létrehozásakor a **PublicIPAddress** paraméter a kimenet tartalmazza a virtuális gép nyilvános IP-címét. Jegyezze fel ezt a címet, mert szüksége lesz rá a virtuális gép eléréséhez.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Mivel ez a virtuális gép futtatása az IIS-webkiszolgálón történik, meg kell nyitnia az internetes forgalmat a 80-as porton.

Használja a [az vm open-port](/cli/azure/vm) parancs használatával nyissa meg a 80-as porton.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A következő paranccsal hozzon létre egy távoli asztali kapcsolatot a virtuális gép. Cserélje le a "Nyilvános IP-cím" a virtuális gép IP-címmel. Amikor a rendszer kéri, adja meg a felhasználónevet és jelszót, amelyet a virtuális gép használt.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Az IIS telepítése a PowerShell-lel

Most, hogy a virtuális gép jelentkezett be, a PowerShell használatával telepítse az IIS szolgáltatást. Indítsa el a Powershellt a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Egy tetszőleges webböngészőben használhatja az alapértelmezett IIS kezdőlapjának megtekintéséhez. Az előző szakaszban leírt nyilvános IP-cím használatával keresse fel az alapértelmezett oldalt.

![Alapértelmezett IIS-webhely](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyeket többé nem kell törölni. Használja a [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett egy alapszintű Windows Server virtuális gépet. További információ a virtuális gépekről az Azure Stack, továbbra is [szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md).
