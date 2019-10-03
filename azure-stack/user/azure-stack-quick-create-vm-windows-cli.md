---
title: Windows rendszerű virtuális gép létrehozása Azure Stack az Azure CLI használatával | Microsoft Docs
description: Windows rendszerű virtuális gép létrehozása Azure Stack az Azure CLI-vel
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b88d65806abfe83dfff59307d3bdcd4e99adf96d
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824264"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-using-azure-cli-in-azure-stack"></a>Gyors útmutató: Windows Server rendszerű virtuális gép létrehozása az Azure CLI használatával Azure Stack

@no__t – 0Applies: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az Azure CLI használatával létrehozhat egy Windows Server 2016 rendszerű virtuális gépet. A virtuális gép létrehozásához és használatához kövesse a cikkben ismertetett lépéseket. A cikk a következő lépéseket is biztosítja:

* Kapcsolódjon a virtuális géphez egy távoli ügyféllel.
* Telepítse az IIS-webkiszolgálót, és tekintse meg az alapértelmezett kezdőlapot.
* Távolítsa el az erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a Azure Stack operátor a **Windows Server 2016** rendszerképet adta hozzá a Azure stack Marketplace-hez.

* Azure Stack az erőforrások létrehozásához és kezeléséhez az Azure CLI adott verziójára van szükség. Ha nem rendelkezik a Azure Stackhoz konfigurált Azure CLI-vel, kövesse az [Azure CLI telepítésének és konfigurálásának](azure-stack-version-profiles-azurecli2.md)lépéseit.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoportok olyan logikai tárolók, amelyekben Azure Stack erőforrásokat lehet üzembe helyezni és felügyelni. Az Azure Stack-környezetből futtassa az az [Group Create](/cli/azure/group#az-group-create) parancsot egy erőforráscsoport létrehozásához.

> [!NOTE]
>  Az értékek a kódban szereplő összes változóhoz vannak rendelve. Szükség esetén azonban új értékeket is hozzárendelhet.

A következő példában létrehozunk egy myResourceGroup nevű erőforráscsoportot a helyi helyen:

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet (VM) az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal. A következő példa egy myVM nevű virtuális gépet hoz létre. Ez a példa egy rendszergazdai felhasználónévhez és a @no__t – 0 rendszergazdai jelszóhoz használja a demouser-t. Módosítsa ezeket az értékeket a környezetének megfelelő értékre.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

A virtuális gép létrehozásakor a kimenetben található **PublicIPAddress** paraméter a virtuális gép nyilvános IP-címét tartalmazza. Jegyezze fel ezt a címeket, mert szüksége van rá a virtuális gép használatára.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Mivel ez a virtuális gép az IIS-webkiszolgálót fogja futtatni, meg kell nyitnia az 80-as portot az internetes forgalomhoz.

A 80-es port megnyitásához használja az az [VM Open-port](/cli/azure/vm) parancsot:

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A következő parancs használatával hozzon létre egy Távoli asztal-kapcsolódást a virtuális géphez. Cserélje le a "nyilvános IP-cím" kifejezést a virtuális gép IP-címére. Ha a rendszer kéri, adja meg a virtuális géphez használt felhasználónevet és jelszót.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Az IIS telepítése a PowerShell-lel

Most, hogy bejelentkezett a virtuális gépre, a PowerShell használatával telepítheti az IIS-t. Indítsa el a PowerShellt a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Az alapértelmezett IIS-Kezdőlap megtekintésére használhat egy tetszőleges böngészőt. Használja az előző szakaszban felsorolt nyilvános IP-címet az alapértelmezett lap felkereséséhez:

![Alapértelmezett IIS-webhely](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje azokat az erőforrásokat, amelyekre már nincs szüksége. Az az [Group delete](/cli/azure/group#az-group-delete) parancs használatával távolítsa el az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy alapszintű Windows Server rendszerű virtuális gépet telepített. Ha többet szeretne megtudni a Azure Stack virtuális gépekről, folytassa a [Azure Stack Virtual Machinesével kapcsolatos szempontokat](azure-stack-vm-considerations.md).
