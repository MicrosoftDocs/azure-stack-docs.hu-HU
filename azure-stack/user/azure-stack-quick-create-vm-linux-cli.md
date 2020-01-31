---
title: Linuxos virtuális gép létrehozása az Azure CLI használatával Azure Stack hub-ban
description: Hozzon létre egy linuxos virtuális gépet az Azure CLI használatával Azure Stack hub-ban.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e1444688d529b2bce148e96dff745b0a86e37a18
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884519"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack-hub"></a>Gyors útmutató: Linux Server rendszerű virtuális gép létrehozása az Azure CLI használatával Azure Stack hub-ban

Az Azure CLI használatával létrehozhat egy Ubuntu Server 16,04 LTS virtuális gépet (VM). Ebben a cikkben egy virtuális gépet hoz létre és használ. A cikk azt is bemutatja, hogyan végezheti el a következőket:

* Kapcsolódjon a virtuális géphez egy távoli ügyféllel.
* Telepítsen egy NGINX-webkiszolgálót, és tekintse meg az alapértelmezett kezdőlapot.
* Törölje a használaton kívüli erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

* Linux-rendszerkép a Azure Stack hub piactéren

   Az Azure Stack hub piactér alapértelmezés szerint nem tartalmaz Linux-rendszerképet. Az Azure Stack hub operátorral adja meg a szükséges Ubuntu Server 16,04 LTS-rendszerképet. Az operátor a [Marketplace-elemek Azure-ból Azure stack hubhoz való letöltésének](../operator/azure-stack-download-azure-marketplace-item.md)utasításait is használhatja.

* Azure Stack hub-nak az Azure CLI egy adott verziójára van szüksége az erőforrások létrehozásához és kezeléséhez. Ha nem rendelkezik az Azure CLI-vel Azure Stack hubhoz konfigurálva, jelentkezzen be a [Azure stack Development Kitba](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (vagy egy Windows-alapú külső ügyfélre, ha [VPN-kapcsolaton keresztül csatlakozik](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)), és kövesse az [Azure CLI telepítésére és konfigurálására](azure-stack-version-profiles-azurecli2.md)vonatkozó utasításokat.

* A (z) *id_rsa. pub* nevű nyilvános Secure Shell-(SSH-) kulcs a Windows felhasználói profil *. ssh* könyvtárába lett mentve. Az SSH-kulcsok létrehozásával kapcsolatos részletes információkért lásd: [SSH nyilvános kulcs használata](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amely Azure Stack hub-erőforrások üzembe helyezésére és kezelésére szolgál. A fejlesztői készletből vagy az Azure Stack hub integrált rendszerből futtassa az az [Group Create](/cli/azure/group#az-group-create) parancsot egy erőforráscsoport létrehozásához.

> [!NOTE]
> A következő példákban az összes változóhoz rendeltünk értékeket. Azonban a saját értékeit is hozzárendelheti.

A következő példában létrehozunk egy myResourceGroup nevű erőforráscsoportot a helyi helyen: 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal. A következő példa egy myVM nevű virtuális gépet hoz létre. A példában a *demouser* rendszergazdai felhasználónevet használ, és a *Demouser@123* rendszergazdai jelszóként. Módosítsa ezeket az értékeket úgy, hogy az a környezetének megfelelő legyen.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

A rendszer a nyilvános IP-címet adja vissza a **PublicIpAddress** paraméterben. Jegyezze fel a címeket, hogy később használhassa a virtuális gépet.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Mivel ez a virtuális gép az IIS-webkiszolgálót fogja futtatni, meg kell nyitnia az 80-as portot az internetes forgalomhoz. A port megnyitásához használja az az [VM Open-port](/cli/azure/vm) parancsot: 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>SSH használata a virtuális géphez való kapcsolódáshoz

Egy olyan ügyfélszámítógépről, amelyen telepítve van az SSH, csatlakozzon a virtuális géphez. Ha Windows-ügyfélen dolgozik, használja a [Putty](https://www.putty.org/) -t a kapcsolat létrehozásához. A virtuális géphez való kapcsolódáshoz használja a következő parancsot:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

A csomag erőforrásainak frissítéséhez és a legújabb NGINX-csomag telepítéséhez futtassa a következő parancsfájlt:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Ha telepítette az NGINX-webkiszolgálót, és a 80-es portot nyitja meg a virtuális gépen, a webkiszolgálót a virtuális gép nyilvános IP-címének használatával érheti el. Ehhez nyisson meg egy böngészőt, és lépjen a ```http://<public IP address>```.

![Az NGINX webkiszolgáló kezdőlapja](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje azokat az erőforrásokat, amelyekre már nincs szüksége. Az az [Group delete](/cli/azure/group#az-group-delete) paranccsal távolíthatja el őket. Futtassa az alábbi parancsot:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy alapszintű Linux Server-alapú virtuális gépet telepített egy webkiszolgálóval. Az Azure Stack hub virtuális gépekkel kapcsolatos további információkért lásd: [Azure stack hub-beli virtuális gépek szempontjai](azure-stack-vm-considerations.md).
