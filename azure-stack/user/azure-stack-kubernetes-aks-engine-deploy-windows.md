---
title: Az AK-motor üzembe helyezése Windows rendszeren Azure Stack hub-ban
description: Megtudhatja, hogyan helyezhet üzembe és kezelhet egy Kubernetes-fürtöt egy Windows rendszerű gépen az Azure Stack hub-ban az AK-motor üzemeltetéséhez.
author: mattbriggs
ms.topic: article
ms.date: 3/4/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/4/2021
ms.openlocfilehash: a8525cb770bb833d338887e010a365bb411e8721
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231472"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack-hub"></a>Az AK-motor telepítése a Windows rendszerű Azure Stack központban

A Kubernetes-fürtök üzembe helyezéséhez és kezeléséhez használhatja a Azure Stack hub egyik Windows rendszerű számítógépét. Ebből a cikkből megtudhatja, hogyan készítheti elő az ügyfél virtuális gépet a fürt kezeléséhez a csatlakoztatott és a leválasztott Azure Stack hub-példányok esetében, ellenőrizze a telepítést, és állítsa be az ügyfél virtuális gépet a ASDK.

## <a name="prepare-the-client-vm"></a>Az ügyfél virtuális gép előkészítése

A Kubernetes-fürt üzembe helyezéséhez és kezeléséhez az AK-motor egy parancssori eszköz. A motor a Azure Stack hub egyik gépén is futtatható. Ebből a gépről a rendszer végrehajtja az AK-motort a fürt futtatásához szükséges IaaS-erőforrások és szoftverek üzembe helyezéséhez. Ezután használhatja a motort futtató gépet a felügyeleti feladatok végrehajtásához a fürtön.

Az ügyfélszámítógép kiválasztásakor vegye figyelembe a következőket:

1. Ha az ügyfélszámítógép vészhelyzet esetén helyreállítható.
3. Hogyan fog csatlakozni az ügyfélszámítógéphez, és hogy a gép hogyan működjön együtt a fürttel.

## <a name="install-in-a-connected-environment"></a>Telepítés csatlakoztatott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt felügyeletére egy, az internethez csatlakozó Azure Stack hub használatával.

1. Hozzon létre egy Windows rendszerű virtuális gépet az Azure Stack hub-ban. Útmutatásért lásd: gyors útmutató [: Windows Server rendszerű virtuális gép létrehozása az Azure stack hub portál használatával](./azure-stack-quick-windows-portal.md).
2. Kapcsolódjon a virtuális géphez.
3. [A chocolatey telepítése a PowerShell-utasítások használatával](https://chocolatey.org/install#install-with-powershellexe). 

    A chocolatey webhelye szerint: a chocolatey egy Windows rendszerű csomagkezelő, például az apt-get vagy a yum, de a Windows. A rendszer úgy lett kialakítva, hogy egy decentralizált keretrendszer legyen a szükséges alkalmazások és eszközök gyors telepítéséhez. A szolgáltatás a NuGet-infrastruktúrára épül, amely jelenleg a PowerShell-t használja, mivel a disztribúciók csomagjait a saját ajtajára, az err-re, a számítógépekre irányítja.
4. Az AK motor [és a Azure stack verzió-hozzárendelési táblázat](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping) táblázatának megkeresése. Az AK-alapú alapmotornak elérhetőnek kell lennie az Azure Stack hub piactéren. A parancs futtatásakor meg kell adnia a verziót `--version v0.xx.x` . Ha nem adja meg a verziót, a parancs telepíti a legújabb verziót, amelynek szüksége lehet egy VHD-lemezképre, amely nem érhető el a piactéren.
    > [!NOTE]  
    > A Azure Stack hub és az AK motor verziószámának leképezése az [AK-motor kibocsátási megjegyzései](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping)között található.
5. Futtassa a következő parancsot egy emelt szintű parancssorból, és adja meg a verziószámot:

    ```PowerShell  
        choco install aks-engine --version 0.xx.x -y
    ```

> [!NOTE]  
> Ha ez a telepítési módszer meghiúsul, kipróbálhatja a [leválasztott környezetben](#install-in-a-disconnected-environment)megjelenő lépéseket, vagy [kipróbálhatja a GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), egy másik csomagkezelő-kezelőt.

## <a name="install-in-a-disconnected-environment"></a>Telepítés leválasztott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt felügyeletére egy Azure Stack hub-kapcsolattal, amely nem kapcsolódik az internethez.

1.  Egy internettel rendelkező gépről nyissa meg a GitHub [Azure/AK-Engine-](https://github.com/Azure/aks-engine/releases/latest)t. Töltse le a Windows rendszerű gépek archívumát (*. tar. gz), például: `aks-engine-v0.xx.x-windows-amd64.tar.gz` . Keresse meg az AK-motor verzióját a [támogatott Kubernetes-verziók táblázatban](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping).

2.  Hozzon létre egy Storage-fiókot a Azure Stack hub-példányban, hogy feltöltse az archív fájlt (*. tar. gz) az KABAi motor bináris fájljával. A Azure Storage Explorer használatával kapcsolatos utasításokért lásd: [Azure Storage Explorer Azure stack hub](./azure-stack-storage-connect-se.md).

3. Hozzon létre egy Windows rendszerű virtuális gépet az Azure Stack hub-ban. Útmutatásért lásd: gyors útmutató [: Windows Server rendszerű virtuális gép létrehozása az Azure stack hub portál használatával](./azure-stack-quick-windows-portal.md)

4.  Töltse le a fájlt a felügyeleti virtuális gépre a Azure Stack hub Storage-fiók blob URL-címéről, amelybe feltöltötte az archív fájlt (*. tar. gz). Bontsa ki az archívumot egy olyan könyvtárba, amelyhez hozzáféréssel rendelkezik a parancssorból.

5. Kapcsolódjon a virtuális géphez.

6. [A chocolatey telepítése a PowerShell-utasítások használatával](https://chocolatey.org/install#install-with-powershellexe). 

7.  Futtassa a következő parancsot egy emelt szintű parancssorból. Adja meg a megfelelő verziószámot:

    ```PowerShell  
        choco install aks-engine --version 0.xx.x -y
    ```

## <a name="verify-the-installation"></a>A telepítés ellenőrzése

Ha az ügyfél virtuális gépe be van állítva, győződjön meg arról, hogy telepítette az AK-motort.

1. Kapcsolódjon az ügyfél virtuális géphez.
2. Futtassa az alábbi parancsot:

    ```PowerShell  
    aks-engine version
    ```

Ha nem tudja ellenőrizni, hogy telepítette-e az AK-motort az ügyfél virtuális gépén, tekintse meg a következőt: az [AK-motor telepítésének hibája](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>ASDK-telepítés

Hozzá kell adnia egy tanúsítványt, amikor a ASDK kívüli gépen futtatja az ASDK-os motorhoz tartozó ügyfél virtuális gépet. Ha a ASDK-környezeten belül egy Windows rendszerű virtuális gépet használ, a gép már megbízik az ASDK-tanúsítványban. Ha az ügyfélszámítógép kívül esik a ASDK, ki kell bontania a tanúsítványt a ASDK, és fel kell vennie a Windows rendszerű gépre.

Ha ASDK használ, az Azure Resource Manager-végpont önaláírt tanúsítványt használ, explicit módon hozzá kell adnia ezt a tanúsítványt a gép megbízható tanúsítványtárolójában. A ASDK főtanúsítványa bármely, a ASDK üzembe helyezett virtuális gépen megtalálható.

1. Exportálja a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt. Útmutatásért lásd: [az Azure stack hub hitelesítésszolgáltatói főtanúsítványának exportálása](../asdk/asdk-cli.md#export-the-azure-stack-hub-ca-root-certificate).
2. Bízza a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát. Útmutatásért tekintse meg [az Azure stack hub hitelesítésszolgáltatói főtanúsítvány megbízhatósága](../asdk/asdk-cli.md#trust-the-certificate)című témakört.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
