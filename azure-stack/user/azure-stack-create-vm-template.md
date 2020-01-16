---
title: Virtuális gép létrehozása közösségi sablon használatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy virtuális gépet egy előre definiált sablonnal és egy GitHub egyéni sablonnal a Azure Stack Development Kit (ASDK) használatával.
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
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: b709d51a430530c50cf9a6e06896d405c435383e
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023245"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Oktatóanyag: virtuális gép létrehozása közösségi sablon használatával

Azure Stack hub-kezelőként vagy felhasználóként létrehozhat egy virtuális gépet (VM) [Egyéni GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates) -gyorsindítási sablonok használatával ahelyett, hogy manuálisan telepítené a sablont az Azure stack hub Piactérről.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Stack hub gyors üzembe helyezési sablonjainak használata
> * Virtuális gép létrehozása egyéni GitHub-sablonnal
> * Minikube elindítása és alkalmazások telepítése

## <a name="azure-stack-hub-quickstart-templates"></a>Azure Stack hub gyors üzembe helyezési sablonjai

Azure Stack hub rövid útmutató sablonjait a GitHub globális Azure Stack hub gyors üzembe helyezési [sablonok tárházában](https://github.com/Azure/AzureStack-QuickStart-Templates)tárolja. Ez a tárház Azure Resource Manager telepítési sablonokat tartalmaz, amelyeket teszteltek a Microsoft Azure Stack Development Kit (ASDK) szolgáltatással. A segítségével könnyebben kiértékelheti Azure Stack hubot, és használhatja a ASDK környezetet.

Az idő múlásával számos GitHub-felhasználó járult hozzá a tárházhoz, így több mint 400 üzembe helyezési sablonból álló gyűjteményt eredményezett. Ez a tárház jó kiindulópont annak megértéséhez, hogyan helyezhet üzembe különböző környezeteket Azure Stack hubhoz.

>[!IMPORTANT]
> A sablonok némelyikét a Közösség tagjai hozzák létre, és nem a Microsoft. Az egyes sablonok licence a tulajdonosa, nem pedig a Microsoft. A Microsoft nem felelős ezekre a sablonokra, és nem biztonsági, kompatibilitási vagy teljesítménybeli képernyő. A közösségi sablonok bármely Microsoft-támogatási program vagy szolgáltatás esetében nem támogatottak, és az "adott állapotban" elérhetővé válnak, mindennemű garancia nélkül.

Ha hozzá szeretne járulni Azure Resource Manager-sablonokhoz a GitHubhoz, tegye a hozzájárulását a [AzureStack-Gyorsindítás-templates](https://github.com/Azure/AzureStack-QuickStart-Templates) tárházhoz. Ha többet szeretne megtudni erről a tárházról, és arról, hogy miként járulhat hozzá, tekintse meg a [Readme fájlt](https://aka.ms/aa6zktg).

## <a name="create-a-vm-using-a-custom-github-template"></a>Virtuális gép létrehozása egyéni GitHub-sablonnal

Ebben a példában a [101-VM-Linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Azure stack hub gyors üzembe helyezési sablonja egy Ubuntu 16,04 virtuális gép telepítésére használható a minikube-t futtató Azure stack hubhoz egy Kubernetes-fürt kezeléséhez.

A Minikube egy olyan eszköz, amely megkönnyíti a Kubernetes helyi futtatását. A Minikube egy virtuális gépen belül egy egycsomópontos Kubernetes-fürtöt futtat, így kipróbálhatja a Kubernetes, vagy fejlesztheti azt napról napra. Támogatja a Linux rendszerű virtuális gépeken futó egyszerű, egycsomópontos Kubernetes-fürtöt. A Minikube a leggyorsabb és legegyszerűbb módja annak, hogy teljesen működőképes Kubernetes-fürtöt futtasson. Lehetővé teszi a fejlesztők számára a Kubernetes-alapú alkalmazások helyi gépeken való fejlesztését és tesztelését. A Minikube virtuális gép a fő-és az ügynök-csomópont-összetevőket is helyileg futtatja:

* A fő csomópont-összetevők, például az API-kiszolgáló, a Scheduler és a [etcd-kiszolgáló](https://coreos.com/etcd/) egyetlen, **LocalKube**nevű Linux-folyamaton futnak.
* Az ügynök-csomópont összetevői a Docker-tárolókban pontosan úgy futnak, ahogy egy normál ügynök-csomóponton futnak. Az alkalmazások központi telepítésének szempontjából nincs különbség az alkalmazás Minikube vagy normál Kubernetes-fürtben való telepítése között.

Ez a sablon a következő összetevőket telepíti:

* Ubuntu 16,04 LTS virtuális gép
* [Docker – CE](https://download.docker.com/linux/ubuntu)
* [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
* [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
* xFCE4
* xRDP

> [!IMPORTANT]
> Ezen lépések végrehajtása előtt az Ubuntu VM-rendszerkép (Ubuntu Server 16,04 LTS, ebben a példában) már hozzá lett adva a Azure Stack hub Marketplace-hez.

1. Válassza **az + erőforrás létrehozása**, majd az **Egyéni**, majd a **template Deployment**lehetőséget.

    ![Sablon létrehozása](media/azure-stack-create-vm-template/1.PNG)

2. Válassza a **Sablon szerkesztése**lehetőséget.

    ![Sablon szerkesztése](media/azure-stack-create-vm-template/2.PNG)

3. Válassza ki a **Gyorsindítás sablont**.

    ![Gyorsindítási sablon](media/azure-stack-create-vm-template/3.PNG)

4. Válassza a **101-VM-Linux-minikube** lehetőséget az elérhető sablonok közül a **sablon kiválasztása** legördülő listából, majd kattintson az **OK**gombra.

    ![Sablonválasztás](media/azure-stack-create-vm-template/4.PNG)

5. Ha módosítani szeretné a sablon JSON-fájlját, ezt megteheti. Ha nem, vagy ha elkészült, kattintson a **Mentés** gombra a **Sablon szerkesztése** párbeszédpanel bezárásához.

    ![Sablon mentése](media/azure-stack-create-vm-template/5.PNG)

6. Válassza a **Paraméterek**lehetőséget, töltse ki vagy módosítsa a rendelkezésre álló mezőket szükség szerint, majd kattintson **az OK**gombra.

    ![Paraméterek](media/azure-stack-create-vm-template/6.PNG)

7. Válassza ki a használni kívánt előfizetést, majd hozzon létre vagy válasszon ki egy meglévő erőforráscsoport-nevet. Ezután válassza a **Létrehozás** elemet a sablon központi telepítésének elindításához.

    ![Előfizetés kiválasztása](media/azure-stack-create-vm-template/7.PNG)

8. Az erőforráscsoport üzembe helyezése több percet vesz igénybe az egyéni sablon alapú virtuális gép létrehozásakor. A telepítési állapotot az értesítések és az erőforráscsoport tulajdonságai között követheti nyomon.

    ![Üzembe helyezés](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > A virtuális gép a telepítés befejeződése után fog futni.

## <a name="start-minikube-and-install-an-application"></a>Minikube elindítása és alkalmazások telepítése

Most, hogy sikeresen létrejött a Linux rendszerű virtuális gép, bejelentkezhet a Minikube elindításához és az alkalmazások telepítéséhez.

1. Az üzembe helyezés befejezése után válassza a **Kapcsolódás** lehetőséget a linuxos virtuális géphez való kapcsolódáshoz használni kívánt nyilvános IP-cím megtekintéséhez.

    ![Kapcsolódás](media/azure-stack-create-vm-template/9.PNG)

2. Egy rendszergazda jogú parancssorból futtassa az **mstsc. exe fájlt** a távoli asztali kapcsolat megnyitásához, majd kapcsolódjon az előző lépésben felderített LINUXOS virtuális gép nyilvános IP-címéhez. Amikor a rendszer felszólítja, hogy jelentkezzen be a xRDP-be, használja a virtuális gép létrehozásakor megadott hitelesítő adatokat.

    ![Távoli](media/azure-stack-create-vm-template/10.PNG)

3. Nyissa meg a Terminal Emulatort, és adja meg a következő parancsokat a Minikube indításához:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![Parancsok futtatása](media/azure-stack-create-vm-template/11.PNG)

4. Nyisson meg egy böngészőt, és lépjen a Kubernetes irányítópult-címeként. Gratulálunk, már rendelkezik egy teljes körűen működő Kubernetes-telepítéssel a Minikube használatával!

    ![Irányítópult](media/azure-stack-create-vm-template/12.PNG)

5. Egy minta alkalmazás üzembe helyezéséhez látogasson el a hivatalos Kubernetes dokumentációs oldalára, és hagyja ki a "Minikube-fürt létrehozása" szakaszt, ahogy már létrehozott egyet. Ugorjon a "Node. js-alkalmazás létrehozása" szakaszra a következő helyen: https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tudnivalók a Azure Stack hub gyors üzembe helyezési sablonjairól
> * Virtuális gép létrehozása egyéni GitHub-sablonnal
> * Minikube elindítása és alkalmazások telepítése
