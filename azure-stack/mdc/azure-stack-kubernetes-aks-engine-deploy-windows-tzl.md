---
title: Az AK-motor üzembe helyezése Azure Stack hub-ban (Windows)
description: Megtudhatja, hogyan helyezhet üzembe és kezelhet egy Kubernetes-fürtöt egy MDC egy Windows rendszerű gépen a Azure Stack hub-ban.
author: mattbriggs
ms.topic: article
ms.date: 10/26/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 4479903f2d24cdd53ab6f27bbb20e5c1fa985225
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598622"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack-hub---modular-data-center-mdc"></a>Az AK-motor telepítése Windows rendszeren Azure Stack hub-moduláris adatközpontban (MDC)

A Kubernetes-fürtök üzembe helyezéséhez és kezeléséhez használhatja a Azure Stack hub egyik Windows rendszerű számítógépét. Ebben a cikkben előkészíti az ügyfél virtuális gépet a fürt felügyeletére a csatlakoztatott és leválasztott Azure Stack hub-példányok esetében, és ellenőrizze a telepítést.
<!-- TZLASDKFIX2 Line above was previously:
In this article, we look at preparing the client VM to manage your cluster for both connected and disconnected Azure Stack Hub instances, check the install, and setting up the client VM on the ASDK.
-->
## <a name="prepare-the-client-vm"></a>Az ügyfél virtuális gép előkészítése

A Kubernetes-fürt üzembe helyezéséhez és kezeléséhez az AK-motor egy parancssori eszköz. A motor a Azure Stack hub egyik gépén is futtatható. Ebből a gépről a rendszer végrehajtja az AK-motort a fürt futtatásához szükséges IaaS-erőforrások és szoftverek üzembe helyezéséhez. Ezután használhatja a motort futtató gépet a felügyeleti feladatok végrehajtásához a fürtön.

Az ügyfélszámítógép kiválasztásakor vegye figyelembe a következőket:

- Ha az ügyfélszámítógép vészhelyzet esetén helyreállítható.
- Hogyan fog csatlakozni az ügyfélszámítógéphez, és hogy a gép hogyan működjön együtt a fürttel.

## <a name="install-in-a-connected-environment"></a>Telepítés csatlakoztatott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt felügyeletére egy, az internethez csatlakozó Azure Stack hub használatával.

1. Hozzon létre egy Windows rendszerű virtuális gépet az Azure Stack hub-ban. Útmutatásért lásd: gyors útmutató [: Windows Server rendszerű virtuális gép létrehozása az Azure stack hub portál használatával](../user/azure-stack-quick-windows-portal.md).
2. Kapcsolódjon a virtuális géphez.
3. [A chocolatey telepítése a PowerShell-utasítások használatával](https://chocolatey.org/install#install-with-powershellexe). 

    A chocolatey webhelye szerint: a chocolatey egy Windows rendszerű csomagkezelő, például az apt-get vagy a yum, de a Windows. A rendszer úgy lett kialakítva, hogy egy decentralizált keretrendszer legyen a szükséges alkalmazások és eszközök gyors telepítéséhez. A szolgáltatás a NuGet-infrastruktúrára épül, amely jelenleg a PowerShell-t használja, mivel a disztribúciók csomagjait a saját ajtajára, az err-re, a számítógépekre irányítja.
4. Keresse meg az AK-motor verzióját a [támogatott Kubernetes-verziók](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions) táblázatban. Az AK-alapú alapmotornak elérhetőnek kell lennie az Azure Stack hub piactéren. A parancs futtatásakor meg kell adnia a verziót `--version v0.48.0` . Ha nem adja meg a verziót, a parancs telepíti a legújabb verziót, amelynek szüksége lehet egy VHD-lemezképre, amely nem érhető el a piactéren.
5. Futtassa a következő parancsot egy emelt szintű parancssorból, és adja meg a verziószámot:

    ```PowerShell  
        choco install aks-engine --version 0.55.4 -y
    ```

> [!NOTE]  
> Ha ez a telepítési módszer meghiúsul, kipróbálhatja a [leválasztott környezetben](#install-in-a-disconnected-environment)megjelenő lépéseket, vagy [kipróbálhatja a GoFish](../user/azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), egy másik csomagkezelő-kezelőt.

## <a name="install-in-a-disconnected-environment"></a>Telepítés leválasztott környezetben

Telepítheti az ügyfél virtuális gépet a Kubernetes-fürt felügyeletére egy Azure Stack hub-kapcsolattal, amely nem kapcsolódik az internethez.

1.  Egy internettel rendelkező gépről nyissa meg a GitHub [Azure/AK-Engine-](https://github.com/Azure/aks-engine/releases/latest)t. Töltse le a Windows rendszerű gépek archívumát (*. tar. gz), például: `aks-engine-v0.38.8-windows-amd64.tar.gz` .

2.  Hozzon létre egy Storage-fiókot a Azure Stack hub-példányban, hogy feltöltse az archív fájlt (*. tar. gz) az KABAi motor bináris fájljával. A Azure Storage Explorer használatával kapcsolatos utasításokért lásd: [Azure Storage Explorer Azure stack hub](../user/azure-stack-storage-connect-se.md).

3. Hozzon létre egy Windows rendszerű virtuális gépet az Azure Stack hub-ban. Útmutatásért lásd: gyors útmutató [: Windows Server rendszerű virtuális gép létrehozása az Azure stack hub portál használatával](../user/azure-stack-quick-windows-portal.md)

4.  Töltse le a fájlt a felügyeleti virtuális gépre a Azure Stack hub Storage-fiók blob URL-címéről, amelybe feltöltötte az archív fájlt (*. tar. gz). Bontsa ki az archívumot egy olyan könyvtárba, amelyhez hozzáféréssel rendelkezik a parancssorból.

5. Kapcsolódjon a virtuális géphez.

6. [A chocolatey telepítése a PowerShell-utasítások használatával](https://chocolatey.org/install#install-with-powershellexe). 

7.  Futtassa a következő parancsot egy emelt szintű parancssorból. Adja meg a megfelelő verziószámot:

    ```PowerShell  
        choco install aks-engine --version 0.55.4 -y
    ```

## <a name="verify-the-installation"></a>A telepítés ellenőrzése

Ha az ügyfél virtuális gépe be van állítva, győződjön meg arról, hogy telepítette az AK-motort.

1. Kapcsolódjon az ügyfél virtuális géphez.
2. Futtassa az alábbi parancsot:

    ```PowerShell  
    aks-engine version
    ```

Ha nem tudja ellenőrizni, hogy telepítette-e az AK-motort az ügyfél virtuális gépén, tekintse meg a következőt: az [AK-motor telepítésének hibája](../user/azure-stack-kubernetes-aks-engine-troubleshoot.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on](../user/azure-stack-kubernetes-aks-engine-deploy-cluster.md)
