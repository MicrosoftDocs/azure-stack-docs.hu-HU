---
title: Linux rendszerű virtuális gép létrehozása az Azure CLI-vel az Azure Stackben |} A Microsoft Docs
description: Linux rendszerű virtuális gép létrehozása az Azure Stack az Azure parancssori felület használatával.
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
ms.openlocfilehash: d47e5908e674a8b57b9e6d686e4596e1002b67c9
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394401"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack"></a>Gyors útmutató: Egy Linux-kiszolgáló virtuális gép létrehozása az Azure Stack az Azure parancssori felület használatával

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Létrehozhat egy Ubuntu Server 16.04 LTS virtuális gép (VM) az Azure parancssori felület használatával. Ebben a cikkben hozzon létre és használhat egy virtuális gépet. Ez a cikk emellett bemutatja, hogyan való:

* Csatlakozzon a virtuális géphez a távoli ügyfélhez.
* Egy NGINX-webkiszolgáló telepítéséhez, és az alapértelmezett kezdőlap megtekintéséhez.
* A fel nem használt erőforrások törlése.

## <a name="prerequisites"></a>Előfeltételek

* Egy Linuxos rendszerképet az Azure Stack piactéren

   Az Azure Stack piactéren alapértelmezés szerint nem tartalmaz a Linux-rendszerképen. Az Azure Stack-operátorokról, adja meg az Ubuntu Server 16.04 LTS rendszerképet kell rendelkeznie. Az operátor kövesse az utasításokat a [letöltése Marketplace-elemek az Azure-ból az Azure Stackhez](../operator/azure-stack-download-azure-marketplace-item.md).

* Az Azure Stack egy adott verzióját az Azure CLI használatával az erőforrások létrehozásához és kezeléséhez szükséges. Ha nem rendelkezik az Azure CLI, Azure Stack konfigurált, jelentkezzen be a [Azure Stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (vagy egy Windows-alapú külső ügyfél Ha [VPN-kapcsolaton keresztül csatlakozó](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)), és kövesse az utasításokat a [telepítése és konfigurálása az Azure CLI](azure-stack-version-profiles-azurecli2.md).

* Egy nyilvános Secure Shell (SSH) kulcs nevét *id_rsa.pub* menti a *.ssh* a Windows felhasználói profiljának. Az SSH-kulcsok létrehozásával kapcsolatos részletes információkért lásd: [nyilvános SSH-kulcsot használ](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Egy erőforráscsoport olyan logikai tároló, ahol üzembe helyezése és kezelése az Azure Stack-erőforrások. A fejlesztői készlet vagy az Azure Stack integrált rendszerek, futtassa a [az csoport létrehozása](/cli/azure/group#az-group-create) parancs használatával hozzon létre egy erőforráscsoportot.

> [!NOTE]
> A következő kódot a példákban az összes változók értékeit hozzárendelte azt. Azonban rendelhet hozzá a saját értékeire.

Az alábbi példa létrehoz egy helyi helyen myResourceGroup nevű erőforráscsoportot: 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gép létrehozása a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) parancsot. Az alábbi példában a myVM nevű virtuális Gépet hoz létre. A példában *Bemutatofelhasznalo* rendszergazdai felhasználónévként és *Demouser@123* rendszergazdai jelszót. Ezeket az értékeket módosítsa úgy, hogy válasszon a környezetének megfelelő.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

A nyilvános IP-címet adja vissza a **PublicIpAddress** paraméter. Vegye figyelembe a címet a virtuális gépet későbbi használatra.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Mivel a virtuális gép futtatása az IIS-webkiszolgálón történik, meg kell nyitnia az internetes forgalmat a 80-as porton. A port megnyitásához használja a [az vm open-port](/cli/azure/vm) parancsot: 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Csatlakozzon a virtuális géphez SSH-val

Az ssh-val telepített egy ügyfélszámítógépen a csatlakozás a virtuális géphez. Ha dolgozik, a Windows-ügyfélen, [PuTTY](https://www.putty.org/) a kapcsolat létrehozásához. A virtuális géphez csatlakozni, használja a következő parancsot:

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

Az NGINX webkiszolgáló is telepítve van, és nyissa meg a virtuális gépen a 80-as porton a webkiszolgáló elérheti, ha a virtuális gép nyilvános IP-cím használatával. Ehhez nyisson meg egy böngészőt, és nyissa meg ```http://<public IP address>```.

![Az NGINX web server kezdőlapjának](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyeket többé nem kell törölni. Használhatja a [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal távolítsa el őket. Futtassa a következő parancsot:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett egy egyszerű Linux server virtuális géphez a webkiszolgáló. Virtuális gépek az Azure Stack kapcsolatos további információkért lásd: [szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md).
