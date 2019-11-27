---
title: Az AK-motor telepítése Linux rendszeren a Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Stack Linux rendszerű gépeket a Kubernetes-fürtök üzembe helyezéséhez és kezeléséhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 3095ede91ce8ac98f1571307c61b28e80aa90fba
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310263"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack"></a>Az KABAi motor telepítése Linux rendszeren Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Kubernetes-fürtök üzembe helyezéséhez és kezeléséhez a Azure Stack Linux rendszerű gépeket is használhat. Ez a cikk azt tekinti át, hogyan készítheti elő az ügyfél virtuális gépet a fürt kezeléséhez a csatlakoztatott és a leválasztott Azure Stack-példányok esetében, ellenőrizze a telepítést, és állítsa be az ügyfél virtuális gépet a ASDK.

## <a name="prepare-the-client-vm"></a>Az ügyfél virtuális gép előkészítése

A Kubernetes-fürt üzembe helyezéséhez és kezeléséhez az AK-motor egy parancssori eszköz. A motort a Azure Stack egy gépen futtathatja. Ebből a gépről a rendszer végrehajtja az AK-motort a fürt futtatásához szükséges IaaS-erőforrások és szoftverek üzembe helyezéséhez. Ezután használhatja a motort futtató gépet a felügyeleti feladatok végrehajtásához a fürtön.

Az ügyfélszámítógép kiválasztásakor vegye figyelembe a következőket:

1. Ha az ügyfélszámítógép vészhelyzet esetén helyreállítható.
2. Hogyan fog csatlakozni az ügyfélszámítógéphez, és hogy a gép hogyan működjön együtt a fürttel.

## <a name="install-in-a-connected-environment"></a>Telepítés csatlakoztatott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt kezelésére az internethez csatlakozó Azure Stackon.

1. Hozzon létre egy Linux rendszerű virtuális gépet a Azure Stack. Útmutatásért lásd: gyors útmutató [: Linux Server rendszerű virtuális gép létrehozása a Azure stack portál használatával](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).
2. Kapcsolódjon a virtuális géphez.
3. Keresse meg az AK-motor verzióját a [támogatott Kubernetes-verziók](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) táblázatban. Az AK-alapú alapmotornak elérhetőnek kell lennie a Azure Stack piactéren. A parancs futtatásakor meg kell adnia a `--version v0.43.0`verzióját. Ha nem adja meg a verziót, a parancs telepíti a legújabb verziót, amelynek szüksége lehet egy VHD-lemezképre, amely nem érhető el a piactéren.
4. Futtassa az alábbi parancsot:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.43.0
    ```

    > [!Note]  
    > Ha nem sikerül a telepítés, kipróbálhatja a [leválasztott környezetben](#install-in-a-disconnected-environment)megjelenő lépéseket, vagy [kipróbálhatja a GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), egy másik csomagkezelő-kezelőt.

## <a name="install-in-a-disconnected-environment"></a>Telepítés leválasztott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt kezelésére az internetről leválasztott Azure Stack.

1.  Egy internettel rendelkező gépről nyissa meg a GitHub [Azure/AK-Engine-](https://github.com/Azure/aks-engine/releases/latest)t. Töltse le a Linux rendszerű gépek archívumát (*. tar. gz), például `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Hozzon létre egy Storage-fiókot a Azure Stack-példányban, hogy feltöltse az archív fájlt (*. tar. gz) az KABAi motor bináris fájljával. A Azure Storage Explorer használatával kapcsolatos utasításokért lásd: [Azure Storage Explorer a Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Hozzon létre egy Linux rendszerű virtuális gépet a Azure Stack. Útmutatásért lásd: gyors útmutató [: Linux Server rendszerű virtuális gép létrehozása a Azure stack portál használatával](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).

3.  Töltse le a fájlt a felügyeleti virtuális gépre a Azure Stack Storage-fiók blob URL-címéről, ahová feltöltötte az archív fájlt (*. tar. gz). Bontsa ki az archívumot a könyvtárba `/usr/local/bin`.

4. Kapcsolódjon a virtuális géphez.

5.  Futtassa az alábbi parancsot:

    ```bash  
    curl -o aks-engine-v0.xx.x-linux-amd64.tar.gz <httpurl/aks-engine-v0.xx.x-linux-amd64.tar.gz>
    tar xvzf aks-engine-v0.xx.x-linux-amd64.tar.gz -C /usr/local/bin
    ```

## <a name="verify-the-installation"></a>A telepítés ellenőrzése

Ha az ügyfél virtuális gépe be van állítva, győződjön meg arról, hogy telepítette az AK-motort.

1. Kapcsolódjon az ügyfél virtuális géphez.
2. Futtassa az alábbi parancsot:

    ```bash  
    aks-engine version
    ```

Ha nem tudja ellenőrizni, hogy telepítette-e az AK-motort az ügyfél virtuális gépén, tekintse meg a következő témakört: az [AK-motor telepítésének](azure-stack-kubernetes-aks-engine-troubleshoot.md)


## <a name="asdk-installation"></a>ASDK-telepítés

Hozzá kell adnia egy tanúsítványt, amikor a ASDK található, az AK-motorhoz tartozó ügyfél virtuális gépet futtatja.

Ha ASDK használ, a Azure Resource Manager végpont önaláírt tanúsítványt használ, explicit módon hozzá kell adnia ezt a tanúsítványt a gép megbízható tanúsítványtárolójában. A ASDK főtanúsítványa bármely, a ASDK üzembe helyezett virtuális gépen megtalálható. Egy Ubuntu rendszerű virtuális gépen például megtalálhatja ezt a könyvtárat `/var/lib/waagent/Certificates.pem`. 

Másolja a tanúsítványfájl a következő paranccsal:

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)