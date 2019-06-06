---
title: Hozzon létre egy virtuális Gépet, közösségi sablon használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Stack Development Kit (ASDK) hozzon létre egy virtuális Gépet egy előre definiált sablon és a egy GitHub-egyéni sablon használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: d82cdb1cccd52cc5e4c11571d85914be3f79a74e
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691572"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Oktatóanyag: Hozzon létre egy virtuális Gépet, közösségi sablon használatával

Azure Stack-operátorokról vagy felhasználóként, létrehozhat egy virtuális gép (VM) az [egyéni GitHub gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) ahelyett, hogy az Azure Stack piactéren manuálisan a sablonok telepítésével.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Stack gyorsindítási sablonok használata
> * Hozzon létre egy virtuális Gépet egy egyéni GitHub-sablon használatával
> * Minikube indítása és a egy alkalmazás telepítése

## <a name="azure-stack-quickstart-templates"></a>Az Azure Stack-gyorssablonok

Az Azure Stack-gyorsindítási sablonok GitHub tárolt [globális Azure Stack a rövid útmutató sablonok tárház](https://github.com/Azure/AzureStack-QuickStart-Templates). Ebben a tárházban (adattár), amely az a Microsoft Azure Stack Development Kit (ASDK) teszteltük az Azure Resource Manager központi telepítési sablonok tartalmazza. Hogy egyszerűbb legyen, hogy az Azure Stack értékelje ki és használja a ASDK környezetet használhatja őket.

Az idő múlásával GitHub számos felhasználó által a tárház eredményez olyan gyűjteménye, több mint 400 központi telepítési sablonok. Ebben a tárházban kiváló kiindulási pontot annak jobb megértése, hogyan helyezhet a különféle környezetek az Azure Stackhez.

>[!IMPORTANT]
> Ezeket a sablonokat nem a Microsoft, hanem a Közösség tagjai által jön létre. A tulajdonosa, nem a Microsoft egyes sablonok licencelése licencszerződés keretében. A Microsoft nem felelős a sablonokért, és nem vállal biztonság, kompatibilitás és teljesítmény. Közösségi sablonok bármely Microsoft terméktámogatási programja vagy szolgáltatása nem támogatja, és elérhető "Adott állapotában", bármiféle garancia nélkül.

Ha szeretne közreműködés GitHub Azure Resource Manager-sablonok, győződjön meg a hozzájárulást az [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) adattárat. Ebben a tárházban, és hogyan működhet közre a azt kapcsolatos további információkért tekintse meg a [információs fájl](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md).

## <a name="create-a-vm-using-a-custom-github-template"></a>Hozzon létre egy virtuális Gépet egy egyéni GitHub-sablon használatával

Ebben a példában az oktatóanyagban a [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Azure Stack gyorsindítási sablon üzembe helyezése az Azure Stacken futó Kubernetes-fürtök kezeléséhez Minikube egy Ubuntu 16.04-virtuális gép szolgál.

Minikube olyan eszköz, amely megkönnyíti a Kubernetes helyi futtatásához. Minikube fut egy egycsomópontos Kubernetes-fürt egy virtuális gépen, lehetővé téve a kipróbálhatja a Kubernetes vagy fejlesztheti napi. Egyszerű és a egy csomópontos Kubernetes-fürt egy Linux rendszerű virtuális gépen futó támogatja. Minikube a leggyorsabb és legegyszerűbb módja egy teljesen működőképes futtatott Kubernetes-fürt. Lehetővé teszi a fejlesztők számára, hogy a fejlesztés és tesztelés a Kubernetes-alapú telepítések a helyi számítógépeken. Tekintve a Minikube VM fő és ügynök csomópont-összetevőinek helyben fut:

* Csomópont-összetevőinek API-kiszolgálóhoz, a Feladatütemező, például a fő és [etcd kiszolgáló](https://coreos.com/etcd/) nevű egyetlen Linux folyamat futtatása **LocalKube**.
* Az ügynök csomópont-összetevőinek módon futtatja a docker-tárolókon belüli pontosan lenne egy normál ügynököt a csomóponton futnak. Alkalmazás központi telepítési szempontjából nem az alkalmazás egy Minikube vagy egy reguláris Kubernetes-fürt üzembe helyezése között nincs különbség.

Ez a sablon a következő összetevőket telepíti:

* Ubuntu 16.04 LTS VM
* [Docker-CE](https://download.docker.com/linux/ubuntu)
* [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
* [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
* xFCE4
* xRDP

> [!IMPORTANT]
> Az Ubuntu Virtuálisgép-rendszerképet (Ubuntu Server 16.04 LTS, ebben a példában) kell már hozzáadni az Azure Stack piactéren ezek a lépések végrehajtása előtt.

1. Válassza ki **+ erőforrás létrehozása**, majd **egyéni**, majd **sablonalapú telepítés**.

    ![Sablon létrehozása](media/azure-stack-create-vm-template/1.PNG)

2. Válassza ki **szerkesztési sablon**.

    ![Sablon szerkesztése](media/azure-stack-create-vm-template/2.PNG)

3. Válassza ki **gyorsindítási sablon**.

    ![Gyorsindítási sablon](media/azure-stack-create-vm-template/3.PNG)

4. Válassza ki **101-vm-linux-minikube** használatával a rendelkezésre álló sablonok közül a **válasszon ki egy sablont** legördülő listában, és kattintson a **OK**.

    ![Válasszon ki egy sablont](media/azure-stack-create-vm-template/4.PNG)

5. Ha azt szeretné, a sablon JSON-JÁT módosításokat, megteheti. Ha nem, vagy amikor végzett, válassza ki a **mentése** gombra kattintva zárja be a **szerkesztési sablon** párbeszédpanel.

    ![Sablon mentése](media/azure-stack-create-vm-template/5.PNG)

6. Válassza ki **paraméterek**, adja meg, vagy a rendelkezésre álló mezőkben, szükség esetén módosítása, és kattintson **OK**.

    ![Paraméterek](media/azure-stack-create-vm-template/6.PNG)

7. Válassza ki az előfizetést, használata és hozzon létre vagy válasszon egy meglévő erőforráscsoport nevét. Ezután válassza ki **létrehozás** a sablon üzembe helyezésének megkezdéséhez.

    ![Válasszon előfizetést](media/azure-stack-create-vm-template/7.PNG)

8. Az erőforráscsoportok üzembe helyezése az egyéni sablon-alapú virtuális gép létrehozása több percet vesz igénybe. Figyelemmel kísérheti a telepítési állapotát, értesítéseket és az erőforráscsoport-tulajdonságai a.

    ![Üzembe helyezés](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > Az üzembe helyezés befejeződésekor a virtuális gép fog futni.

## <a name="start-minikube-and-install-an-application"></a>Minikube indítása és a egy alkalmazás telepítése

Most, hogy a Linux rendszerű virtuális gép sikeresen létrejött, bejelentkezhet Minikube elindításához, és a egy alkalmazás telepítése.

1. Az üzembe helyezés befejezése után jelölje be az **Connect** a Linux rendszerű virtuális Géphez való csatlakozáshoz használt nyilvános IP-címének megtekintéséhez.

    ![Kapcsolódás](media/azure-stack-create-vm-template/9.PNG)

2. Egy rendszergazda jogú parancssorból futtassa **mstsc.exe** távoli asztali kapcsolat megnyitásához, és a Linux rendszerű virtuális gép nyilvános IP-címet az előző lépésben felderített való kapcsolódáshoz. Amikor a rendszer kéri, jelentkezzen be a xRDP, a virtuális gép létrehozásakor megadott hitelesítő adatokat használja.

    ![távoli](media/azure-stack-create-vm-template/10.PNG)

3. Nyissa meg a terminált emulátor, és Minikube elindításához a következő parancsokat:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![Parancsok futtatása](media/azure-stack-create-vm-template/11.PNG)

4. Nyisson meg egy böngészőt, és folytassa a Kubernetes-irányítópult címe. Gratulálunk, most már teljes körűen működik Minikube Kubernetes-telepítését!

    ![Irányítópult](media/azure-stack-create-vm-template/12.PNG)

5. Üzembe helyezünk egy mintaalkalmazást, látogasson el a hivatalos Kubernetes dokumentációs oldalon, és ahogy már létrehozott egyet, hagyja ki a "Minikube fürt létrehozása" szakaszban. Ugrás a szakasz "A Node.js-alkalmazás létrehozása" https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * További tudnivalók az Azure Stack gyorsindítási sablonok
> * Hozzon létre egy virtuális Gépet egy egyéni GitHub-sablon használatával
> * Minikube indítása és a egy alkalmazás telepítése
