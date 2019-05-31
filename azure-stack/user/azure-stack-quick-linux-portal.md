---
title: Egy Linux-kiszolgáló virtuális gép létrehozása az Azure Stack használatával |} A Microsoft Docs
description: Egy Linux-kiszolgáló virtuális gép létrehozása az Azure Stack használatával.
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: ce06ffbe48848a30de98025c42711e25ca9a312a
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394442"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-stack-portal"></a>Gyors útmutató: Egy Linux-kiszolgáló virtuális gép létrehozása az Azure Stack-portál használatával

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Létrehozhat egy Ubuntu Server 16.04 LTS virtuális gép (VM) az Azure Stack-portál használatával. Ebben a cikkben hozzon létre és használhat egy virtuális gépet. Ez a cikk emellett bemutatja, hogyan való:

* Csatlakozzon a virtuális géphez a távoli ügyfélhez.
* Telepítse az NGINX-webkiszolgálót.
* Az erőforrások törlése.

> [!NOTE]  
> Ebben a cikkben található rendszerképek 1808 Azure Stack verzióban bevezetett módosítások megfelelően frissülnek. 1808 verziót támogat a *felügyelt lemezek* nem felügyelt lemezek mellett. Egy korábbi verzióját használja, ha egyes feladatok lemezkiválasztás, például képek különböznek a felhasználói felületen jelenik meg.  

## <a name="prerequisites"></a>Előfeltételek

* Egy Linuxos rendszerképet az Azure Stack piactéren

   Az Azure Stack piactéren, alapértelmezés szerint nincs Linux-rendszerképen. Az Azure Stack-operátorokról, adja meg az Ubuntu Server 16.04 LTS rendszerképet kell rendelkeznie. Az operátor kövesse az utasításokat a [letöltése Marketplace-elemek az Azure-ból az Azure Stackhez](../operator/azure-stack-download-azure-marketplace-item.md).

* Egy SSH-ügyféllel való hozzáférés

   Ha az Azure Stack Development Kit (ASDK) használ, hogy előfordulhat, hogy nem lehet hozzáférni egy Secure Shell (SSH) ügyfél. Ha egy ügyfél van szüksége, több csomagot tartalmaznak egy SSH-ügyfél. Például a putty-kapcsolaton keresztül egy SSH-ügyfél és az SSH Key Generator (puttygen.exe) tartalmaz. Elérhető csomagok kapcsolatos további információkért lásd: [nyilvános SSH-kulcs használatával](azure-stack-dev-start-howto-ssh-public-key.md).

* Ez a rövid útmutató putty-t használ, az SSH-kulcsok létrehozása és a Linux rendszerű virtuális gép kiszolgálóhoz való csatlakozáshoz. [Töltse le és telepítse a PuTTY-t](https://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Ebben a cikkben minden lépést befejezéséhez szüksége ssh-kulcs. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.

SSH-kulcspár létrehozása:

1. Nyissa meg a PuTTY telepítési mappájából (az alapértelmezett hely a *C:\Program Files\PuTTY*), és futtassa:

    `puttygen.exe`

1. Az a **PuTTY Key Generator** ablak **készítése a kulcs típusa,** való **RSA**, és állítsa be **létrehozott kulcs bitjeinek száma** való**2048**.

   ![PuTTY Key Generator-konfiguráció](media/azure-stack-quick-linux-portal/Putty01.PNG)

1. Válassza ki **készítése**.

1. Egy kulcsot létrehozni a **kulcs** mezőt, véletlenszerűen vigye a mutatót.

1. Ha a kulcs létrehozása befejeződött, válassza ki **mentés nyilvános kulcs**, majd válassza ki **titkos kulcs mentése** fájlokat menteni a kulcsokat.

   ![PuTTY Key Generator eredmények](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be az Azure Stack portálon

Az Azure Stack portálon címe attól függ, mely az Azure Stack-termék, amelyhez csatlakozik:

* A ASDK keresse https://portal.local.azurestack.external.

* Az Azure Stackkel integrált rendszer nyissa meg az Azure Stack-operátorokról megadott URL-CÍMÉT.

## <a name="create-the-vm"></a>Virtuális gép létrehozása

1. Az Azure Stack portal bal felső sarkában válassza **erőforrás létrehozása**.

1. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
   
   ![Válassza ki a Linux-kiszolgálón](media/azure-stack-quick-linux-portal/select.png)

1. Kattintson a **Létrehozás** gombra.

1. Írja be a virtuális gép adatait. A **hitelesítési típus**válassza **nyilvános SSH-kulcs**, illessze be az SSH nyilvános kulcs mentése során, és válassza ki **OK**.

   > [!NOTE]
   > Győződjön meg arról, hogy távolítsa el a kulcs bármilyen kezdő vagy záró térközt.

   ![Alapvető beállítások panel – konfigurálja a virtuális gép](media/azure-stack-quick-linux-portal/linux-01.PNG)

1. Válassza ki **D1** a virtuális gép számára.

   ![A méretezés panelen – Virtuálisgép-méretének kiválasztása](media/azure-stack-quick-linux-portal/linux-02.PNG)

1. Az a **beállítások** lapon, módosítja az alapértelmezett értékeket.
   
   Verziójától kezdve az Azure Stack 1808, konfigurálhat **tárolási** és használandó *felügyelt lemezek*. A 1808-nál korábbi verziókban nem felügyelt lemezek csak is használható.

   ![A felügyelt lemezek tároló konfigurálása](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
   Ha a beállítások elkészültek, válassza ki a **OK** folytatásához.

1. Az a **összefoglalás** lapon jelölje be **OK** a virtuális gép üzembe helyezésének megkezdéséhez.  

   ![Üzembe helyezés](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. Válassza ki **Connect** a virtuális gép oldalon. Az SSH-kapcsolati karakterláncot, amely a virtuális Géphez való csatlakozáshoz szüksége találja. 

1. Az a **PuTTY konfigurációs** lap a **kategória** panelen görgessen lefelé, és bontsa ki a **SSH**, majd válassza ki **Auth**. 

   ![Virtuális gép csatlakoztatása](media/azure-stack-quick-linux-portal/putty03.PNG)

1. Válassza ki **Tallózás**, majd válassza ki a mentett titkos kulcs fájlját.

1. Az a **kategória** panelen görgessen fel, majd válasszon **munkamenet**.

1. Az a **gazdagép nevét (vagy IP-cím)** mezőbe illessze be az Azure Stack portálon látható kapcsolati karakterláncot. Ebben a példában a karakterlánc van *asadmin@192.168.102.34* .

1. Válassza ki **nyissa meg a** a virtuális gép egy munkamenet megnyitását.

   ![Linux-munkamenet](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

Frissítse a csomagforrásokat, és telepítse a legújabb NGINX-csomagot a virtuális gépen, adja meg az alábbi bash-parancsokat:

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Ha befejezte az NGINX telepítése, zárja be az SSH-munkamenetet, és nyissa meg a virtuális gép **áttekintése** oldal az Azure Stack portálon.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Hálózati biztonsági csoport (NSG) védi a bejövő és kimenő forgalmat. Ha egy virtuális gép létrehozása az Azure Stack portálon, egy bejövő szabályt a 22-es portot SSH-kapcsolatok jön létre. A virtuális gép, amelyen egy webkiszolgáló, mert egy NSG-szabályt kell létrehozni a 80-as portot a webes forgalom engedélyezéséhez.

1. A virtuális gép **áttekintése** lapon, válassza ki a nevét, a **erőforráscsoport**.

1. Válassza ki a **hálózati biztonsági csoport** a virtuális gép számára. Használatával azonosíthatja az NSG-t a **típus** oszlop.

1. A bal oldali panelen a **beállítások**válassza **bejövő biztonsági szabályok**.

1. Válassza a **Hozzáadás** lehetőséget.

1. Az a **neve** mezőbe írja be **http**. 

1. Győződjön meg arról, hogy **porttartomány** 80 értékre van állítva, és **művelet** értékre van állítva **engedélyezése**.

1. Kattintson az **OK** gombra.

## <a name="view-the-welcome-to-nginx-page"></a>Üdvözli a nginx-lap megtekintése

Az NGINX telepítve van, és nyissa meg a virtuális Gépen a 80-as porton a webkiszolgáló elérheti, ha a virtuális gép nyilvános IP-cím használatával. (A nyilvános IP-cím jelenik meg a virtuális gép **áttekintése** lap.)

Nyisson meg egy webböngészőt, és váltson *http://\<nyilvános IP-cím >* .

![Az NGINX web server kezdőlapjának](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyeket többé nem kell törölni. A virtuális gép és erőforrásainak törléséhez válassza ki az erőforráscsoportot a virtuális gép oldalon, és válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy alapszintű Linux-kiszolgálót helyezett üzembe virtuális gép webkiszolgáló. Azure Stack virtuális gépekkel kapcsolatos további információkért folytassa [szempontok az Azure Stack-beli virtuális gépek](azure-stack-vm-considerations.md).
