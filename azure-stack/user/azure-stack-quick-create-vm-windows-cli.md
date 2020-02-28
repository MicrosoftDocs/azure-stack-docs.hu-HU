---
title: Windows rendszerű virtuális gép létrehozása Azure Stack hub-ban az Azure CLI használatával
description: Windows rendszerű virtuális gép létrehozása Azure Stack hub-ban az Azure CLI használatával
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 5e5827cd0667a46d30147b0b6283f2a94d67158f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701955"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-using-azure-cli-in-azure-stack-hub"></a>Gyors útmutató: Windows Server rendszerű virtuális gép létrehozása az Azure CLI használatával Azure Stack hub-ban

Az Azure CLI használatával létrehozhat egy Windows Server 2016 rendszerű virtuális gépet. A virtuális gép létrehozásához és használatához kövesse a cikkben ismertetett lépéseket. A cikk a következő lépéseket is biztosítja:

* Kapcsolódjon a virtuális géphez egy távoli ügyféllel.
* Telepítse az IIS-webkiszolgálót, és tekintse meg az alapértelmezett kezdőlapot.
* Távolítsa el az erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy a Azure Stack hub-kezelő a **Windows Server 2016** rendszerképet adta hozzá a Azure stack hub piactérhez.

* Azure Stack hub-nak az Azure CLI egy adott verziójára van szüksége az erőforrások létrehozásához és kezeléséhez. Ha nem rendelkezik az Azure CLI-vel Azure Stack hubhoz konfigurálva, kövesse az [Azure CLI telepítésének és konfigurálásának](azure-stack-version-profiles-azurecli2.md)lépéseit.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amely Azure Stack hub-erőforrások üzembe helyezésére és kezelésére szolgál. Az Azure Stack hub-környezetből futtassa az az [Group Create](/cli/azure/group#az-group-create) parancsot egy erőforráscsoport létrehozásához.

> [!NOTE]
>  Az értékek a kódban szereplő összes változóhoz vannak rendelve. Szükség esetén azonban új értékeket is hozzárendelhet.

A következő példában létrehozunk egy myResourceGroup nevű erőforráscsoportot a helyi helyen:

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet (VM) az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal. A következő példa egy myVM nevű virtuális gépet hoz létre. Ez a példa egy rendszergazdai felhasználónevet használ, és a Demouser@123 rendszergazdai jelszóként. Módosítsa ezeket az értékeket a környezetének megfelelő értékre.

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

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy alapszintű Windows Server rendszerű virtuális gépet telepített. Ha többet szeretne megtudni a Azure Stack hub virtuális gépekről, folytassa az [Azure stack hub Virtual Machinesével kapcsolatos szempontokat](azure-stack-vm-considerations.md).
