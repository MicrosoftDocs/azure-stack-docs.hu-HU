---
title: Hozzon létre egy Linuxos virtuális gép az Azure CLI használatával az Azure Stackben |} A Microsoft Docs
description: Hozzon létre egy Linuxos virtuális gép az Azure Stack parancssori felület használatával.
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 19c856bdf981775b0b3a8ee923046b51e6ef79ca
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782775"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-using-azure-cli-in-azure-stack"></a>Gyors útmutató: Azure CLI használatával az Azure Stackben Linux rendszerű kiszolgáló virtuális gép létrehozása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Létrehozhat egy Ubuntu Server 16.04 LTS virtuális gépet az Azure parancssori felület használatával. Kövesse a cikkben hozhat létre és használhat egy virtuális gépet. Ez a cikk is biztosít a lépéseket:

* Csatlakozzon a virtuális géphez a távoli ügyfélhez.
* Az NGINX-webkiszolgálót, és az alapértelmezett kezdőlap megtekintéséhez.
* A fel nem használt erőforrások törlése.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Linuxos rendszerképet az Azure Stack piactéren**

   Az Azure Stack piactéren alapértelmezés szerint nem tartalmaz a Linux-rendszerképen. Adja meg az Azure Stack-operátorokról lekérése a **Ubuntu Server 16.04 LTS** rendszerképre van szüksége. Az operátor leírt lépéseket követve használhat a [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](../operator/azure-stack-download-azure-marketplace-item.md) cikk.

* Az Azure Stack egy adott verzióját az Azure CLI használatával az erőforrások létrehozásához és kezeléséhez szükséges. Ha nem rendelkezik az Azure CLI, Azure Stack konfigurált, jelentkezzen be a [development Kitet](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (vagy egy Windows-alapú külső ügyfél Ha [VPN-kapcsolaton keresztül csatlakozó](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)), és kövesse a lépéseket a [ Telepítse és konfigurálja az Azure CLI-vel](azure-stack-version-profiles-azurecli2.md).

* Egy nyilvános SSH-kulcsot a név id_rsa.pub mentése a Windows felhasználói profilja .ssh könyvtárában. Az SSH-kulcsok létrehozásával kapcsolatos részletes információkért lásd: [nyilvános SSH-kulcs használatával](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Egy erőforráscsoport olyan logikai tároló, ahol üzembe helyezése és kezelése az Azure Stack-erőforrások. A fejlesztői készlet vagy az Azure Stack integrált rendszerek, futtassa a [az csoport létrehozása](/cli/azure/group#az-group-create) parancs használatával hozzon létre egy erőforráscsoportot.

> [!NOTE]
> A Kódminták minden változóját értékek vannak hozzárendelve. Megadhat azonban új értéket, ha szeretné.

Az alábbi példa létrehoz egy helyi helyen myResourceGroup nevű erőforráscsoportot: 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gép létrehozása a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) parancsot. Az alábbi példában a myVM nevű virtuális Gépet hoz létre. Ebben a példában Bemutatofelhasznalo használ egy rendszergazdai felhasználónevet és a Demouser@123 rendszergazdai jelszót. Ezeket az értékeket módosítsa úgy, hogy válasszon a környezetének megfelelő.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

A nyilvános IP-címet adja vissza a **PublicIpAddress** paraméter. Jegyezze fel ezt a címet, mert szüksége lesz, hogy a virtuális gép használja.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Mivel a virtuális gép futtatása az IIS-webkiszolgálón történik, meg kell nyitnia az internetes forgalmat a 80-as porton. Használja a [az vm open-port](/cli/azure/vm) parancsot a kívánt port megnyitásához: 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Csatlakozzon a virtuális géphez SSH-val

Az ssh-val telepített egy ügyfélszámítógépen a csatlakozás a virtuális géphez. Ha dolgozik, a Windows-ügyfélen, [Putty](https://www.putty.org/) a kapcsolat létrehozásához. A virtuális géphez csatlakozni, használja a következő parancsot:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

Csomag erőforrásokat, és telepítse a legújabb NGINX-csomagot, futtassa a következő szkriptet:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Az NGINX telepítve van, és nyissa meg a virtuális gépen a 80-as porton a webkiszolgáló, a virtuális gép nyilvános IP-cím használatával is elérheti. Nyisson meg egy böngészőt, és váltson ```http://<public IP address>```.

![Az NGINX web server kezdőlap](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyeket többé nem kell törölni. Használhatja a [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal eltávolítható az ezekhez az erőforrásokhoz. Az erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa a következő parancsot:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett egy egyszerű Linux server virtuális géphez a webkiszolgáló. További információ a virtuális gépekről az Azure Stack, továbbra is [szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md).
