---
title: Az AK-motor üzembe helyezése Windows rendszeren Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe és kezelhet egy Kubernetes-fürtöt egy Windows rendszerű gépen a Azure Stackban az AK-motor üzemeltetéséhez.
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
ms.date: 10/09/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 9c19047629d95ba1ffe7c0fe6ab2da5c745576a0
ms.sourcegitcommit: 12034a1190d52ca2c7d3f05c8c096416120d8392
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037940"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack"></a>Az AK-motor telepítése Windows rendszeren Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit @ no__t-0

A Kubernetes-fürtök üzembe helyezéséhez és kezeléséhez használhatja a Azure Stackban található Windows-gépet az AK-motor üzemeltetéséhez. Ez a cikk azt tekinti át, hogyan készítheti elő az ügyfél virtuális gépet a fürt kezeléséhez a csatlakoztatott és a leválasztott Azure Stack-példányok esetében, ellenőrizze a telepítést, és állítsa be az ügyfél virtuális gépet a ASDK.

## <a name="prepare-the-client-vm"></a>Az ügyfél virtuális gép előkészítése

A Kubernetes-fürt üzembe helyezéséhez és kezeléséhez az AK-motor egy parancssori eszköz. A motort a Azure Stack egy gépen futtathatja. Ebből a gépről a rendszer végrehajtja az AK-motort a fürt futtatásához szükséges IaaS-erőforrások és szoftverek üzembe helyezéséhez. Ezután használhatja a motort futtató gépet a felügyeleti feladatok végrehajtásához a fürtön.

Az ügyfélszámítógép kiválasztásakor vegye figyelembe a következőket:

1. Ha az ügyfélszámítógép vészhelyzet esetén helyreállítható.
3. Hogyan fog csatlakozni az ügyfélszámítógéphez, és hogy a gép hogyan működjön együtt a fürttel.

## <a name="install-in-a-connected-environment"></a>Telepítés csatlakoztatott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt kezelésére az internethez csatlakozó Azure Stackon.

1. Hozzon létre egy Windows rendszerű virtuális gépet a Azure Stack. Útmutatásért lásd: [Quickstart: Hozzon létre egy Windows Server rendszerű virtuális gépet a Azure Stack Portal @ no__t-0 használatával.
2. Kapcsolódjon a virtuális géphez.
3. [A chocolatey telepítése a PowerShell-utasítások használatával](https://chocolatey.org/install#install-with-powershellexe). 

    A chocolaty webhelye szerint: A chocolatey a Windows rendszerhez készült csomagkezelő, például az apt-get vagy a yum, de a Windows. A rendszer úgy lett kialakítva, hogy egy decentralizált keretrendszer legyen a szükséges alkalmazások és eszközök gyors telepítéséhez. A szolgáltatás a NuGet-infrastruktúrára épül, amely jelenleg a PowerShell-t használja, mivel a disztribúciók csomagjait a saját ajtajára, az err-re, a számítógépekre irányítja.
4. Keresse meg az AK-motor verzióját a [támogatott Kubernetes-verziók](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) táblázatban. Az AK-alapú alapmotornak elérhetőnek kell lennie a Azure Stack piactéren. A parancs futtatásakor meg kell adnia a `--version v0.41.2` verziót. Ha nem adja meg a verziót, a parancs telepíti a legújabb verziót, amelynek szüksége lehet egy VHD-lemezképre, amely nem érhető el a piactéren.
5. Futtassa a következő parancsot egy emelt szintű parancssorból, és adja meg a verziószámot:

    ```PowerShell  
        choco install aks-engine --version v0.41.2 -y
    ```

> [!Note]  
> Ha ez a telepítési módszer meghiúsul, kipróbálhatja a [leválasztott környezetben](#install-in-a-disconnected-environment)megjelenő lépéseket, vagy [kipróbálhatja a GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), egy másik csomagkezelő-kezelőt.

## <a name="install-in-a-disconnected-environment"></a>Telepítés leválasztott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt kezelésére az internetről leválasztott Azure Stack.

1.  Egy internettel rendelkező gépről nyissa meg a GitHub [Azure/AK-Engine-](https://github.com/Azure/aks-engine/releases/latest)t. Töltsön le egy Windows rendszerű gép archívumát (*. tar. gz), például `aks-engine-v0.38.8-windows-amd64.tar.gz`.

2.  Hozzon létre egy Storage-fiókot a Azure Stack-példányban, hogy feltöltse az archív fájlt (*. tar. gz) az KABAi motor bináris fájljával. A Azure Storage Explorer használatával kapcsolatos utasításokért lásd: [Azure Storage Explorer a Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Hozzon létre egy Windows rendszerű virtuális gépet a Azure Stack. Útmutatásért lásd: [Quickstart: Windows Server rendszerű virtuális gép létrehozása a Azure Stack portál @ no__t-0 használatával

4.  Töltse le a fájlt a felügyeleti virtuális gépre a Azure Stack Storage-fiók blob URL-címéről, ahová feltöltötte az archív fájlt (*. tar. gz). Bontsa ki az archívumot egy olyan könyvtárba, amelyhez hozzáféréssel rendelkezik a parancssorból.

5. Kapcsolódjon a virtuális géphez.

6. [A chocolatey telepítése a PowerShell-utasítások használatával](https://chocolatey.org/install#install-with-powershellexe). 

7.  Futtassa a következő parancsot egy emelt szintű parancssorból. Adja meg a megfelelő verziószámot:

    ```PowerShell  
        choco install aks-engine --version v0.41.2 -y
    ```

## <a name="verify-the-installation"></a>A telepítés ellenőrzése

Ha az ügyfél virtuális gépe be van állítva, győződjön meg arról, hogy telepítette az AK-motort.

1. Kapcsolódjon az ügyfél virtuális géphez.
2. Futtassa a következő parancsot:

    ```PowerShell  
    aks-engine version
    ```

Ha nem tudja ellenőrizni, hogy telepítette-e az AK-motort az ügyfél virtuális gépén, tekintse meg a következő témakört: az [AK-motor telepítésének](azure-stack-kubernetes-aks-engine-troubleshoot.md)


## <a name="asdk-installation"></a>ASDK-telepítés

Hozzá kell adnia egy tanúsítványt, amikor a ASDK kívüli gépen futtatja az ASDK-os motorhoz tartozó ügyfél virtuális gépet. Ha a ASDK-környezeten belül egy Windows rendszerű virtuális gépet használ, a gép már megbízik az ASDK-tanúsítványban. Ha az ügyfélszámítógép kívül esik a ASDK, ki kell bontania a tanúsítványt a ASDK, és fel kell vennie azt a Windows rendszerű gépre.

Ha ASDK használ, a Azure Resource Manager végpont önaláírt tanúsítványt használ, explicit módon hozzá kell adnia ezt a tanúsítványt a gép megbízható tanúsítványtárolójában. A ASDK főtanúsítványa bármely, a ASDK üzembe helyezett virtuális gépen megtalálható.

1. Exportálja a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt. Útmutatásért lásd: [a Azure stack hitelesítésszolgáltatói főtanúsítvány exportálása](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#export-the-azure-stack-ca-root-certificate)
2. Bízza a Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát. Útmutatásért tekintse meg [a Azure stack hitelesítésszolgáltatói főtanúsítvány megbízhatósága](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#trust-the-azure-stack-ca-root-certificate)című témakört.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
