---
title: Az AK-motor telepítése Linux rendszeren Azure Stack hub-ban
description: Megtudhatja, hogyan használhatja az Kubernetes-fürtök üzembe helyezéséhez és kezeléséhez a Azure Stack hub-ban található Linux-gépet az AK-motor üzemeltetéséhez.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: 9b99f0d7184030d513d2fab375ebb9040ec370c8
ms.sourcegitcommit: f0ee2a3af78dd6d6e2806710681d52b763948967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84533671"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack-hub"></a>Az AK-motor telepítése Linux rendszeren Azure Stack hub-ban

A Kubernetes-fürtök üzembe helyezéséhez és kezeléséhez a Azure Stack hub egyik Linux rendszerű számítógépét használhatja az AK-motor üzemeltetéséhez. Ebből a cikkből megtudhatja, hogyan készítheti elő az ügyfél virtuális gépet a fürt kezeléséhez a csatlakoztatott és a leválasztott Azure Stack hub-példányok esetében, ellenőrizze a telepítést, és állítsa be az ügyfél virtuális gépet a ASDK.

## <a name="prepare-the-client-vm"></a>Az ügyfél virtuális gép előkészítése

A Kubernetes-fürt üzembe helyezéséhez és kezeléséhez az AK-motor egy parancssori eszköz. A motor a Azure Stack hub egyik gépén is futtatható. Ebből a gépről a rendszer végrehajtja az AK-motort a fürt futtatásához szükséges IaaS-erőforrások és szoftverek üzembe helyezéséhez. Ezután használhatja a motort futtató gépet a felügyeleti feladatok végrehajtásához a fürtön.

Az ügyfélszámítógép kiválasztásakor vegye figyelembe a következőket:

1. Ha az ügyfélszámítógép vészhelyzet esetén helyreállítható.
2. Hogyan fog csatlakozni az ügyfélszámítógéphez, és hogy a gép hogyan működjön együtt a fürttel.

## <a name="install-in-a-connected-environment"></a>Telepítés csatlakoztatott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt felügyeletére egy, az internethez csatlakozó Azure Stack hub használatával.

1. Hozzon létre egy linuxos virtuális gépet a Azure Stack hub-ban. Útmutatásért lásd: gyors útmutató [: Linux Server rendszerű virtuális gép létrehozása az Azure stack hub portál használatával](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).
2. Kapcsolódjon a virtuális géphez.
3. Keresse meg az AK-motor verzióját a [támogatott Kubernetes-verziók](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions) táblázatban. Az AK alaprendszerképének elérhetőnek kell lennie az Azure Stack hub piactéren. A parancs futtatásakor meg kell adnia a verziót `--version v0.48.0` . Ha nem adja meg a verziót, a parancs telepíti a legújabb verziót, amelynek szüksége lehet egy VHD-lemezképre, amely nem érhető el a piactéren.
4. Futtassa az alábbi parancsot:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.48.0
    ```

    > [!Note]  
    > Ha nem sikerül a telepítés, kipróbálhatja a [leválasztott környezetben](#install-in-a-disconnected-environment)megjelenő lépéseket, vagy [kipróbálhatja a GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), egy másik csomagkezelő-kezelőt.

## <a name="install-in-a-disconnected-environment"></a>Telepítés leválasztott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt felügyeletére egy Azure Stack hub-kapcsolattal, amely nem kapcsolódik az internethez.

1.  Egy internettel rendelkező gépről nyissa meg a GitHub [Azure/AK-Engine-](https://github.com/Azure/aks-engine/releases/latest)t. Töltse le a Linux rendszerű gépek archívumát (*. tar. gz), például: `aks-engine-v0.xx.x-linux-amd64.tar.gz` .

2.  Hozzon létre egy Storage-fiókot a Azure Stack hub-példányban, hogy feltöltse az archív fájlt (*. tar. gz) az KABAi motor bináris fájljával. A Azure Storage Explorer használatával kapcsolatos utasításokért lásd: [Azure Storage Explorer Azure stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Hozzon létre egy linuxos virtuális gépet a Azure Stack hub-ban. Útmutatásért lásd: gyors útmutató [: Linux Server rendszerű virtuális gép létrehozása az Azure stack hub portál használatával](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).

3.  Töltse le a fájlt a felügyeleti virtuális gépre a Azure Stack hub Storage-fiók blob URL-címéről, amelybe feltöltötte az archív fájlt (*. tar. gz). Bontsa ki az archívumot a könyvtárba `/usr/local/bin` .

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

3. Ha Azure Resource Manager végpont önaláírt tanúsítványt használ, explicit módon hozzá kell adnia a főtanúsítványt a számítógép megbízható tanúsítványtárolóhoz. A főtanúsítvány a virtuális gépen található a következő könyvtárban:/var/lib/waagent/Certificates.pem. Másolja a tanúsítványfájl a következő paranccsal: 

   ```bash
   sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt 
   sudo update-ca-certificates
   ```

Ha nem tudja ellenőrizni, hogy telepítette-e az AK-motort az ügyfél virtuális gépén, tekintse meg a következő témakört: az [AK-motor telepítésének](azure-stack-kubernetes-aks-engine-troubleshoot.md)


## <a name="asdk-installation"></a>ASDK-telepítés

Hozzá kell adnia egy tanúsítványt, amikor a ASDK található, az AK-motorhoz tartozó ügyfél virtuális gépet futtatja.

Ha ASDK használ, a Azure Resource Manager végpont önaláírt tanúsítványt használ, explicit módon hozzá kell adnia ezt a tanúsítványt a gép megbízható tanúsítványtárolójában. A ASDK főtanúsítványa bármely, a ASDK üzembe helyezett virtuális gépen megtalálható. Egy Ubuntu rendszerű virtuális gépen például megtalálhatja ezt a könyvtárat `/var/lib/waagent/Certificates.pem` . 

Másolja a tanúsítványfájl a következő paranccsal:

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on](azure-stack-kubernetes-aks-engine-deploy-cluster.md)