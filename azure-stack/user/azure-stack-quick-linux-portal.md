---
title: Linuxos virtuális gép létrehozása Azure Stack hub használatával
description: Hozzon létre egy Linux-alapú virtuális gépet Azure Stack hub használatával.
author: mattbriggs
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/06/2020
ms.openlocfilehash: 2addd1c2e6c486afcdf3f5c6307653d65a9f545e
ms.sourcegitcommit: 8187658b1d45dceed727aca3ae1b9b57aca04392
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630804"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-stack-hub-portal"></a>Gyors útmutató: linuxos kiszolgálói virtuális gép létrehozása az Azure Stack hub portál használatával

Az Azure Stack hub portál használatával létrehozhat egy Ubuntu Server 16,04 LTS virtuális gépet (VM). Ebben a cikkben egy virtuális gépet hoz létre és használ. A cikk azt is bemutatja, hogyan végezheti el a következőket:

* Kapcsolódjon a virtuális géphez egy távoli ügyféllel.
* Telepítsen egy NGINX-webkiszolgálót.
* Távolítsa el az erőforrásokat.

> [!NOTE]  
> A cikkben szereplő rendszerképek megfelelnek a Azure Stack hub 1808-es verziójában bevezetett változásoknak. Az 1808-es verzió támogatja a nem felügyelt lemezeken kívül a *felügyelt lemezek* használatát. Ha korábbi verziót használ, bizonyos feladatokhoz (például a lemez kiválasztásához) tartozó képek eltérhetnek a felhasználói felületen megjelenített adatoktól.  

## <a name="prerequisites"></a>Előfeltételek

* Linux-rendszerkép a Azure Stack hub piactéren

   A Azure Stack hub piactér alapértelmezés szerint nem rendelkezik Linux-rendszerképpel. Az Azure Stack hub operátorral adja meg a szükséges Ubuntu Server 16,04 LTS-rendszerképet. Az operátor a [Marketplace-elemek Azure-ból Azure stack hubhoz való letöltésének](../operator/azure-stack-download-azure-marketplace-item.md)utasításait is használhatja.

* Hozzáférés egy SSH-ügyfélhez

   Ha a Azure Stack Development Kit (ASDK) használja, lehet, hogy nem fér hozzá egy Secure Shell-(SSH-) ügyfélhez. Ha ügyfélre van szüksége, több csomag is tartalmaz egy SSH-ügyfelet. Például a PuTTY tartalmaz egy SSH-ügyfelet és egy SSH-kulcs létrehozóját (puttygen.exe). További információ az elérhető csomagokról: [SSH nyilvános kulcs használata](azure-stack-dev-start-howto-ssh-public-key.md).

* Ez a rövid útmutató a PuTTY-t használja az SSH-kulcsok létrehozásához és a Linux Server rendszerű virtuális géphez való kapcsolódáshoz. [Töltse le és telepítse a PuTTY-t](https://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

A cikkben szereplő összes lépés befejezéséhez egy SSH-kulcspár szükséges. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.

SSH kulcspár létrehozása:

1. Nyissa meg a PuTTY telepítési mappáját (az alapértelmezett hely a *C:\Program Files\PuTTY* ), és futtassa a következő parancsot:

    `puttygen.exe`

1. A **Putty kulcs-generátor** ablakban állítsa be a **kulcs típusát** az **RSA** értékre, majd állítsa be a **bitek számát a generált kulcsban** a **2048** értékre.

   ![A PuTTY Key Generator konfigurálása](media/azure-stack-quick-linux-portal/Putty01aa.png)

1. Válassza a **készítés** lehetőséget.

1. Kulcs létrehozásához a **kulcs** mezőben véletlenszerűen mozgassa a mutatót.

1. Ha a kulcs létrehozása befejeződött, válassza a **nyilvános kulcs mentése** lehetőséget, majd válassza a **titkos kulcs mentése** lehetőséget a kulcsok fájlokra való mentéséhez.

   ![A PuTTY Key Generator eredményei](media/azure-stack-quick-linux-portal/Putty02aa.png)

## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Jelentkezzen be az Azure Stack hub portálra

Az Azure Stack hub-portál címe attól függ, hogy melyik Azure Stack hub-termékhez csatlakozik:

* A ASDK nyissa meg a következőt: `https://portal.local.azurestack.external` .

* Azure Stack hub integrált rendszer esetén keresse meg azt az URL-címet, amelyet a Azure Stack hub-operátor biztosít.

## <a name="create-the-vm"></a>A virtuális gép létrehozása

1. Válassza **az erőforrás létrehozása**  >  **számítás** lehetőséget. Keressen a `Ubuntu Server 16.04 LTS` kifejezésre. Válassza ki a nevet.

   ![Linux-kiszolgáló létrehozása](media/azure-stack-quick-linux-portal/image1a.png)

1. Kattintson a **Létrehozás** gombra.

   ![Linux-kiszolgáló létrehozása – létrehozás](media/azure-stack-quick-linux-portal/image2a.png)

1. Adja meg a virtuális gép adatait. Válassza a hitelesítési típushoz az **SSH nyilvános kulcs** lehetőséget, majd illessze be a mentett nyilvános SSH-kulcsot, majd kattintson **az OK gombra**.

    > [!NOTE]  
    > Győződjön meg arról, hogy a kulcshoz tartozó kezdő vagy záró szóközöket távolítja el.

   ![Hitelesítés](media/azure-stack-quick-linux-portal/image3a.png)

1. Válassza ki a virtuális gép méretének **D1_v2** .

> [!Note]  
> Ha GPU-t támogató virtuális gépet hoz létre, válassza a GPU VM **NC_v3** vagy **NC4as_T4_v3** lehetőséget a virtuális gép méretéhez.

   ![Linux-kiszolgáló létrehozása – méret](media/azure-stack-quick-linux-portal/image4a.png)

1. Adja meg az alapértelmezett **beállításokat a beállítások** panelen a felügyelt lemezek használatával. Ha engedélyeznie kell az SSH-hozzáférést, válassza az **SSH (22)** lehetőséget a port megnyitásához. Ha elkészült a konfigurációk, kattintson **az OK gombra**.

   ![Linux-kiszolgáló létrehozása – beállítások](media/azure-stack-quick-linux-portal/image5a.png)

1. A virtuális gép telepítésének elindításához kattintson az **OK gombra** az összegzésben. Válassza **Virtual Machines** az új virtuális gép megjelenítéséhez, keresse meg a virtuális gép nevét, majd válassza ki a virtuális gépet a keresési eredmények között.

![Linux-kiszolgáló létrehozása – összefoglalás](media/azure-stack-quick-linux-portal/image5a.png)

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. A virtuális gép lapon válassza a **kapcsolat** lehetőséget. Megtalálhatja a virtuális géphez való kapcsolódáshoz szükséges SSH kapcsolati karakterláncot. 

1. A **Putty konfigurációja** oldalon, a **Kategória** ablaktáblán görgessen le az **SSH** -ra, és bontsa ki az **Auth** elemet. 

   ![Virtuális gép összekötése](media/azure-stack-quick-linux-portal/putty03aa.png)

1. Válassza a **Tallózás** lehetőséget, majd válassza ki a mentett titkos kulcsot.

1. A **Kategória** panelen görgessen felfelé, és válassza a **munkamenet** lehetőséget.

1. Az **állomásnév (vagy IP-cím)** mezőbe illessze be az Azure stack hub portálon megjelenített kapcsolatok karakterláncát. Ebben a példában a karakterlánc a következő: *asadmin@192.168.102.34* .

1. Válassza a **Megnyitás** lehetőséget a virtuális gép munkamenetének megnyitásához.

   ![Linux-munkamenet](media/azure-stack-quick-linux-portal/Putty05aa.png)

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

A csomag forrásainak frissítéséhez és a legújabb NGINX-csomag telepítéséhez a virtuális gépen adja meg a következő bash-parancsokat:

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Amikor befejezte az NGINX telepítését, akkor a Azure Stack hub portálon nyissa meg az SSH-munkamenetet, és nyissa meg a virtuális gép **áttekintése** lapot.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

A hálózati biztonsági csoport (NSG) biztosítja A bejövő és a kimenő forgalmat. Amikor létrehoznak egy virtuális gépet az Azure Stack hub portálon, a 22-es porton létrejön egy bejövő szabály az SSH-kapcsolatokhoz. Mivel ez a virtuális gép egy webkiszolgálót üzemeltet, létre kell hoznia egy NSG-szabályt, amely engedélyezi a webes forgalmat a 80-es porton.

1. A virtuális gép **áttekintése** lapon válassza ki az **erőforráscsoport** nevét.

1. Válassza ki a **hálózati biztonsági csoportot** a virtuális géphez. A NSG a **Type (típus** ) oszlop használatával azonosíthatja.

1. A bal oldali ablaktábla **Beállítások** területén válassza a **bejövő biztonsági szabályok** elemet.

1. Válassza a **Hozzáadás** lehetőséget.

1. A név mezőbe írja be a **http** **nevet** . 

1. Győződjön meg arról, hogy a **porttartomány** értéke 80, a **művelet** értéke pedig **Engedélyezés**.

1. Kattintson az **OK** gombra.

## <a name="view-the-welcome-to-nginx-page"></a>Az üdvözli a Nginx oldal megtekintése

Ha az NGINX telepítve van, és a 80-es port meg van nyitva a virtuális gépen, a virtuális gép nyilvános IP-címének használatával érheti el a webkiszolgálót. (A nyilvános IP-cím a virtuális gép **Áttekintés** lapján látható.)

Nyisson meg egy webböngészőt, és lépjen a *http:// \<public IP address>* lapra.

![Az NGINX webkiszolgáló kezdőlapja](media/azure-stack-quick-linux-portal/linux-05aa.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje azokat az erőforrásokat, amelyekre már nincs szüksége. A virtuális gép és erőforrásai törléséhez válassza ki az erőforráscsoportot a virtuális gép lapon, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy alapszintű Linux Server-alapú virtuális gépet telepített egy webkiszolgálóval. Ha többet szeretne megtudni Azure Stack hub virtuális gépekről, folytassa a [Azure stack hub-beli virtuális gépekkel kapcsolatos szempontokat](azure-stack-vm-considerations.md).
