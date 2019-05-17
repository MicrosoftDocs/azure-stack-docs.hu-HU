---
title: Az Azure Stack gyors üzembe helyezés – virtuális gép portál létrehozása
description: Az Azure Stack a rövid útmutató – Linux rendszerű virtuális gép létrehozása a portálon
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
ms.openlocfilehash: 2c0997599b8da1f77f61e0953eb56f834f718efb
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782833"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Gyors útmutató: Linux rendszerű kiszolgáló virtuális gép létrehozása az Azure Stack portálon

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Létrehozhat egy Ubuntu Server 16.04 LTS virtuális gépet az Azure Stack-portál használatával. Kövesse a cikkben hozhat létre és használhat egy virtuális gépet. Ez a cikk is biztosít a lépéseket:

* Csatlakozzon a virtuális géphez a távoli ügyfélhez.
* Telepítse az NGINX-webkiszolgálót.
* Az erőforrások törlése.

> [!NOTE]  
> A képernyőképek ebben a cikkben az Azure Stack verzió 1808 bevezetett változások megfelelően frissülnek. 1808 használatának támogatásával bővült *felügyelt lemezek* nem felügyelt lemezek mellett. Egy korábbi verzióját használja, ha bizonyos képek a feladatokat, mint a lemezkiválasztás különbözhet mi jelenjen meg ez a cikk.  


## <a name="prerequisites"></a>Előfeltételek

* **Egy Linuxos rendszerképet az Azure Stack piactéren**

   Az Azure Stack piactéren alapértelmezés szerint nem rendelkezik egy Linuxos rendszerképet. Ellenőrizze, hogy az Azure Stack operátori biztosít a **Ubuntu Server 16.04 LTS** lemezképet a Marketplace-en. Az operátor leírt lépéseket követve használhat a [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](../operator/azure-stack-download-azure-marketplace-item.md) cikk.

* **Egy SSH-ügyféllel való hozzáférés**

   Az Azure Stack Development Kit (ASDK) használatakor, előfordulhat, hogy nincs hozzáférése egy SSH-ügyfél. Egy ügyfél van szüksége, van-e több SSH-ügyfelet tartalmazó csomagot. Például a putty-kapcsolaton keresztül egy SSH-ügyfél és az SSH megosztottelérésikulcs-készítő (puttygen.exe) tartalmaz. Elérhető csomagok kapcsolatos további információkért olvassa el az Azure-cikket: [Nyilvános SSH-kulcs használatával](azure-stack-dev-start-howto-ssh-public-key.md).

   Ez a rövid útmutató használja a putty-kapcsolaton keresztül az SSH-kulcsok létrehozása és a Linux-kiszolgáló virtuális géphez csatlakozni. Töltse le és telepítse a Puttyt, keresse fel a [ https://www.putty.org/ ](https://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Szüksége lesz egy SSH-kulcspár ebben a cikkben minden lépést befejezéséhez. Ha rendelkezik egy meglévő SSH-kulcspárral, kihagyhatja ezt a lépést.

1. Keresse meg a PuTTY telepítési mappájából (az alapértelmezett hely a `C:\Program Files\PuTTY`), és futtassa `puttygen.exe`.
2. A PuTTY Key Generator ablakában győződjön meg arról a **készítése a kulcs típusa,** értékre van állítva **RSA**, és a **létrehozott kulcs bitjeinek száma** értékre van állítva **2048**. Amikor elkészült, kattintson a **Generate**.

   ![PuTTY Key Generator-konfiguráció](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Hozzon létre egy kulcsot, mozgassa az egérmutatót véletlenszerűen a PuTTY Key Generator ablakban.
4. Kattintson a kulcs létrehozásának befejezése **mentés nyilvános kulcs** majd **titkos kulcs mentése** fájlokat menteni a kulcsok.

   ![PuTTY Key Generator eredmények](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be az Azure Stack portálon

Jelentkezzen be az Azure Stack portálon. Az Azure Stack portálon címe attól függ, melyik Azure Stack termék csatlakozik:

* Nyissa meg az Azure Stack Development Kit (ASDK): https://portal.local.azurestack.external.
* Az Azure Stackkel integrált rendszer nyissa meg az Azure Stack-operátorokról megadott URL-CÍMÉT.

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

1. Kattintson a **erőforrás létrehozása** az Azure Stack portal bal felső sarkában található.

2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
   
   ![Válassza ki a Linux-kiszolgálón](media/azure-stack-quick-linux-portal/select.png)
1. Kattintson a **Create** (Létrehozás) gombra.

4. Írja be a virtuális gép adatait. A **Hitelesítés típusa** résznél válassza az **SSH nyilvános kulcs** lehetőséget. Illessze be az SSH nyilvános kulcs mentése, és kattintson a **OK**.

   > [!NOTE]
   > Ellenőrizze, hogy ezek billentyűt a eltávolít minden kezdő vagy záró térközt.

   ![Alapvető beállítások panel – a virtuális gép konfigurálása](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Válassza ki **D1** a virtuális gép.

   ![Méret panel – a virtuális gép méretének kiválasztása](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Az a **beállítások** lapon, végezze el a kívánt módosításokat, az alapértelmezett értékeket.
   
   - Verziójától kezdve az Azure Stack 1808, konfigurálhat **tárolási** , amelyek kiválaszthatja használandó *felügyelt lemezek*. Korábbi 1808 csak a nem felügyelt lemezek is használható.    
     ![A felügyelt lemezek tároló konfigurálása](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
     Ha a beállítások elkészültek, válassza ki a **OK** folytatásához.

7. Az a **összefoglalás** kattintson **OK** a virtuális gép üzembe helyezésének megkezdéséhez.  
   ![Üzembe helyezés](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

1. Kattintson a **Connect** a virtuális gép oldalon. Megtalálhatja az SSH-kapcsolati karakterláncot, amely a virtuális gépnek csatlakoznia kell. 

2. Nyissa meg a PuTTY eszközt.

3. A PuTTY konfigurációs képernyőjén fogja használni a **kategória** ablakban görgessen felfelé vagy lefelé. Görgessen le a **SSH**, bontsa ki a **SSH**, és kattintson a **Auth**. Kattintson a **Tallózás** , és válassza ki a mentett titkos kulcs fájlját.
   ![Virtuális gép csatlakoztatása](media/azure-stack-quick-linux-portal/putty03.PNG)

4. A másolatot görgessen a **kategória** ablakot, és kattintson **munkamenet**.
5. Az a **gazdagép nevét (vagy IP-cím)** mezőbe illessze be az Azure Stack portálon látható kapcsolati karakterláncban. Ebben a példában a karakterlánc van `asadmin@192.168.102.34`.

   ![PuTTY konfigurációs kapcsolati karakterlánc](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Kattintson a **nyissa meg a** a virtuális gép egy munkamenet megnyitását.

   ![Linux-munkamenet](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

Az alábbi bash-parancsok használatával frissítse a csomagforrásokat, és telepítse a legújabb NGINX-csomagot a virtuális gépen.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Ha befejezte az NGINX telepítése, zárja be az SSH-munkamenetet, és nyissa meg a virtuális gépek – áttekintés oldalra az Azure Stack portálon.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

A hálózati biztonsági csoport (NSG) feladata a bejövő és kimenő forgalom védelme. Ha egy virtuális gép létrehozása az Azure Stack portálon, egy bejövő szabályt a 22-es portot SSH-kapcsolatok jön létre. A virtuális gép egy webkiszolgálót futtat, mert egy NSG-szabályt kell létrehozni a 80-as portot a webes forgalom engedélyezéséhez.

1. A virtuális gépen **áttekintése** lap, kattintson a nevére a **erőforráscsoport**.
2. Válassza ki a **hálózati biztonsági csoport** a virtuális gép. A hálózati biztonsági csoport a **Típus** oszlop segítségével azonosítható.
3. A bal oldali menüben a **beállítások**, kattintson a **bejövő biztonsági szabályok**.
4. Kattintson a **Hozzáadás**lehetőségre.
5. A **Név** mezőbe írja be a **http** karakterláncot. Ügyeljen rá, hogy a **Porttartomány** értéke 80, a **Művelet** értéke pedig **Engedélyezés** legyen.
6. Kattintson az **OK** gombra

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Az NGINX telepítve van, és nyissa meg a virtuális gépen a 80-as porton a webkiszolgáló, a virtuális gép nyilvános IP-cím használatával is elérheti. (A nyilvános IP-cím jelenik meg a virtuális gép – áttekintés oldalra.)

Nyisson meg egy webböngészőt, és keresse meg a `http://<public IP address>`.

![Az NGINX web server kezdőlap](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyeket többé nem kell törölni. A virtuális gép és az erőforrások törléséhez válassza ki az erőforráscsoportot a virtuális gép oldalon, és kattintson a **törlése**.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett egy egyszerű Linux server virtuális géphez a webkiszolgáló. További információ a virtuális gépekről az Azure Stack, továbbra is [szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md).
