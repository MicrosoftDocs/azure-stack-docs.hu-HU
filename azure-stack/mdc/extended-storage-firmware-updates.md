---
title: Azure Stack – a moduláris adatközpont kiterjesztett tárterületének belső vezérlőprogram frissítése
description: Ez a cikk útmutatást nyújt a belső vezérlőprogram-frissítések kibővített tárolóban történő végrehajtásához a moduláris adatközpont blob Storage-hoz.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 23430fd05254b1509677bf2d7ddedab75c7c2355
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182976"
---
# <a name="updating-onefs-and-firmware-for-the-datacenter-extended-storage"></a>A OneFS és a belső vezérlőprogram frissítése az adatközpont kiterjesztett tárolóján

Ez a cikk útmutatást nyújt a OneFS és az adatközpont kiterjesztett tárolási hardverének belső vezérlőprogram frissítéséhez. 

A kiterjesztett tárolók hardveres integrációjának és üzembe helyezésének utolsó lépései a hardver ellenőrzésének lépései, és készen állnak a konfigurálásra. A lépések a [kiterjesztett tárterület állapotának ellenőrzése](extended-storage-health-checks.md)című témakörben találhatók.

## <a name="update-onefs"></a>OneFS frissítése

Frissítse a OneFS, hogy a rendszer a legújabb operációsrendszer-javításokat alkalmazza a belső vezérlőprogram frissítése előtt. 

1. Ellenőrizze, hogy a fürt rendelkezik-e elegendő szabad hellyel a javítás telepítéséhez.
1. Nyisson meg egy SSH-kapcsolatokat a fürt bármely csomópontján, és jelentkezzen be a root fiók használatával.

   >[!NOTE]
   >Ha a javítást egy megfelelőségi üzemmódú fürtre telepítik, jelentkezzen be a compadmin fiók használatával.

1. Másolja a 8.2.2_GA-RUP_2020-07_PSP -40. tgz fájlt a fürt/IFS/Data/Isilon_Support könyvtárába.
1. Futtassa a következő parancsot a/IFS/Data/Isilon_Support könyvtárra való váltáshoz: 

   ```
   /ifs/data/Isilon_Support
   pwd
   ```

1. A javítás fájljának kibontásához futtassa a következő parancsot:

   ```
   -zxvf 8.2.2_GA-RUP_2020-07_PSP-40.tgz
   ```

1. A javítás telepítéséhez futtassa a következő parancsot:
   >[!WARNING]
   >A következő parancs egyszerre fogja újraindítani a fürt összes csomópontját. A javítás telepítéséhez, majd az egyes csomópontok egymás után történő újraindításához hagyja ki a `-simultaneous` paramétert. További információt a README telepítési/eltávolítási hatásai című szakaszában talál.

   ```
   isi upgrade patches install 8.2.2_GA-RUP_2020-07_PSP-40.pkg --simultaneous
   ```

1. A javítás telepítésének ellenőrzéséhez futtassa a következő parancsot:

   ```
   isi upgrade patches list
   ```

1. Győződjön meg arról, hogy az 8.2.2_GA-RUP_2020-07_PSP-40 megjelenik a telepített csomagok listájában, és az állapot beállítása telepített.

## <a name="update-firmware-on-nodes"></a>A belső vezérlőprogram frissítése a csomópontokon

   1. Tekintse át az aktuális meghajtó belső vezérlőprogram és a meghajtó támogatási [csomagjának kiadásait](https://aka.ms/currentextstoragerelease).
   1. Töltse le a fájlokat a helyi gépre.  
   1. Töltse fel a meghajtó és a csomópont belső vezérlőprogram-csomagjait a Isilon-fájlmegosztásba, és helyezze a fájlokat a következő megfelelő címtárakba:
    
        * Drive_Support_ *. tgz:/IFS/Data/Isilon_Support/DSP/
        * IsiFw_Package_ *. tar:/IFS/Data 

## <a name="verify-disk-drive-firmware"></a>Lemezmeghajtó belső vezérlőprogramja ellenőrzése

Először hajtsa végre a meghajtót a frissítés és ellenőrzés támogatásával. Ezzel megtekintheti, hogy a lemezmeghajtó belső vezérlőprogramja naprakész-e a megadott csomaggal. Ha nem, a csomag használható a szükséges frissítések végrehajtásához:

   1. Aktív SSH-munkamenetből futtassa a következőt:

       ```
       cd /ifs/data/Isilon_Support/dsp/
       ```

   1. Győződjön meg arról, hogy a Drive_Support_ *. tgz ebben a könyvtárban van az ls parancs futtatásával.
   1. Bontsa ki a. tgz fájlt a következő parancs futtatásával. Győződjön meg arról, hogy rendelkezik a teljes fájlnévvel, és cserélje le a * értéket a csomag tényleges verziószámával:
       ```
       tar -zxvf Drive_Support_v1.*.tgz
       ```
       Ha például a csomag verziószáma Drive_Support_v1 .33. tgz, futtassa a következő parancsot:
       ```
       tar -zxvf Drive_Support_v1.33.tgz
       ```
   1. Ez három fájlra bővül:
    
        * Drive_Support_v1.33. tar
        * Drive_Support_v1.33. tar. MD5
        * Drive_Support_v1.33. tar. sha256
   1. Futtassa a következő parancsot a meghajtó-támogatási szoftver telepítéséhez/ellenőrzéséhez:
       ```
       isi_dsp_install Drive_Support_v1.*.tar
       ```
       Ha például a csomag verziószáma Drive_Support_v1 .33. tgz, futtassa a következő parancsot:
       ```
       isi_dsp_install Drive_Support_v1.33.tar
       ```
   1. Ez a parancs ellenőrzi az ellenőrzéseket, és telepíti a meghajtó-támogatási csomagot. Ha a csomagra szükség van, a rendszer telepíti; Ellenkező esetben azt fogja kiépíteni, hogy telepítve van, és kihagyja a meghajtó-támogatási szoftver további frissítéseit. A v 1.33 kimenete a következő:
    
        ```
        Running local CHECKS for patch Drive_Support_v1.33
        INSTALL operation has been requested for patch Drive_Support_v1.33
        Installed Drive_Support_v1.33
        DELETE operation has been requested for patch
        Drive_Support_v1.33
        Removed Drive_Support_v1.33 package files
        DSP Install Succeeded, 170.605088949 sec elapsed
        ```
        
   1. A végleges megerősítést a következő parancs futtatásával teheti meg. A "kívánt" oszlopban található "-" oszloptól eltérő érték azt jelzi, hogy a meghajtót frissíteni kell:
       ```
       isi devices drive firmware list --node-lnn all
       ```
   1. Ha vannak olyan meghajtók, amelyeket frissíteni kell, a következő parancsot kell használni a lemez belső vezérlőprogram-frissítéseinek elvégzéséhez:
       ```
       isi devices drive firmware update start all --node-lnn all
       ```
   1. Ezt a folyamatot a következő parancs használatával lehet figyelni:
       ```
       isi devices drive firmware update list
       ```
   1. Ha elkészült, a meghajtók állapotát a következő paranccsal ellenőrizheti:
       ```
       isi devices drive list --node-lnn all
       ```
## <a name="install-node-firmware"></a>Csomópont belső vezérlőprogram telepítése

Ha a meghajtó-támogatási frissítések befejeződtek/ellenőrizve lettek, a csomópont belső vezérlőprogram telepíthető a IsiFw_Package_ *. tar használatával.

   1. Aktív SSH-munkamenetből futtassa a következőt:
        ```
        cd /ifs/data/
        ```
   1. Győződjön meg arról, hogy a IsiFw_Package_ *. tar ebben a könyvtárban van az ls parancs futtatásával.
   1. Futtassa a következő parancsot a belső vezérlőprogram a csomópontokon történő előkészítéséhez és telepítéséhez. Győződjön meg arról, hogy rendelkezik a teljes fájlnévvel, és cserélje le a * értéket a csomag tényleges verziószámára:
       ```
       isi upgrade patches install IsiFw_Package_v*.tar --rolling=false
       ```
       Ha például a verziószám a v 10.3.3. tar, futtassa a következő parancsot:
       ```
       isi upgrade patches install IsiFw_Package_v10.3.3.tar --rolling=false
       ```
   1. A végrehajtás után a rendszer a következő üzenetet küldi el megerősítésként:
       ```
        Requested install of patch IsiFw_Package_v10.3.3.
       ```
   1. Ellenőrizze a fázis/telepítés állapotát a következő paranccsal:
       ```
       isi upgrade patches list
       ```
   1. A folyamat befejeződése után a "status" (állapot) oszlop frissül, és a rendszer a következő példában látható módon megjeleníti a függőben lévő, ismeretlen, telepített és telepített állapotot:
        
       ![Belső vezérlőprogram frissítési állapota](media/extended-storage-firmware-updates/firmware-update-status.png)
        
   1. Most, hogy a IsiFw_Package *. tar előre beállított/telepített, a frissített belső vezérlőprogram üzembe helyezése elindítható a csomópontok között a következő paranccsal:
        ```
        isi upgrade cluster firmware start --no-verify
        ```
   1. Ezzel a művelettel a következő megerősítés jelenik meg, a folytatáshoz írja be az "igen" értéket:
       ```
       You are about to start a Rolling Firmware UPGRADE, are you sure? (yes/[no]):
       ```
        
       > [!NOTE]
       > A csomópont belső vezérlőprogram-frissítése több óráig is eltarthat, és a csomópontok offline állapotba kerülnek a folyamat során. Mivel az összes parancs végrehajtása az 1. csomópontból történt, a rendszer a kapcsolódást elveti, mivel ez az első csomópont, amelyet frissíteni kell. Ugyanez vonatkozik a OneFS-portálra – várjon 5-10 percet, mielőtt újracsatlakozik az ellenőrzési állapothoz (ha szükséges, egy másik, nem frissített felügyeleti felületen keresztüli kapcsolat is lehetséges).
   1. Aktív felügyeleti felületen/kapcsolaton keresztül a belső vezérlőprogram frissítésének folyamatos állapota nyomon követhető a következő paranccsal:
       ```
       isi upgrade cluster nodes firmware progress list
       ```
       Vagy használja a OneFS portált a fürtszolgáltatás – javítások és belső vezérlőprogram területen:
        
       ![Fürtkezelés](media/extended-storage-firmware-updates/cluster-management.png)
        
       Ha elkészült, az állapot **ismeretlenről** **telepítettre**változik.

## <a name="remove-firmware-packages"></a>Belső vezérlőprogram csomagjainak eltávolítása

Távolítsa el a belső vezérlőprogram-csomagokat, miután a meghajtókon és csomópontokon teljesen telepítették a következő parancsokat. Győződjön meg arról, hogy rendelkezik a teljes fájlnévvel, és cserélje le a v *-t a csomag aktuális verziójára:
   ```
   isi upgrade patches uninstall IsiFw_Package_ v*.tar --rolling=false
   ```
   Ha például a csomag IsiFw_Package_ v 10.3.1. tar verziójú, futtassa a következő parancsot:
   ```
   isi upgrade patches uninstall IsiFw_Package_ v10.3.1.tar --rolling=false
   ```
Aktív SSH-munkamenetből futtassa a következőt:
   ```
   cd /ifs/data/
   rm -f Drive_Support_v*.tgz
   rm -f Drive_Support_v*.tar
   rm -f Drive_Support_v*.tar.md5
   rm -f Drive_Support_v*.tar.sha256
   ```
   Például:
   ```
   cd /ifs/data/
   rm -f Drive_Support_v10.3.1.tgz
   rm -f Drive_Support_v10.3.1.tar
   rm -f Drive_Support_v10.3.1.tar.md5
   rm -f Drive_Support_v10.3.1.tar.sha256
   ```


        
<!-- ## Contact Dell EMC for support -->

Forduljon Microsoft ügyfélszolgálatahoz, és kérjen segítséget az esetleges problémákról.

## <a name="next-steps"></a>Következő lépések

- [Kiterjesztett tárterület állapotának ellenőrzése](extended-storage-health-checks.md)
