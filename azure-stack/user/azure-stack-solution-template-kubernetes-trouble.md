---
title: Az Azure Stack hub Kubernetes-telepítésének hibáinak megoldása
description: Megtudhatja, hogyan lehet elhárítani a Kubernetes központi telepítését Azure Stack hubhoz.
author: mattbriggs
ms.topic: article
ms.author: mabrigg
ms.date: 12/16/2020
ms.reviewer: waltero
ms.lastreviewed: 11/14/2019
ms.openlocfilehash: d15b6d3cbd4cac10c3df0d8c215a869c1fa398b7
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873877"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack-hub"></a>Az Azure Stack hub Kubernetes-telepítésének hibáinak megoldása

> [!NOTE]  
> A fürtök Kubernetes való üzembe helyezéséhez csak az Azure Stack hub Marketplace-elemmel használható. Azure Stack hub támogatott Kubernetes-fürtök esetén használja [az AK-motort](azure-stack-kubernetes-aks-engine-overview.md).

Ez a cikk azt ismerteti, hogyan lehet elhárítani a Kubernetes-fürtöt. A hibaelhárítás megkezdéséhez tekintse át a központi telepítéshez szükséges elemeket. Előfordulhat, hogy össze kell gyűjtenie az üzembe helyezési naplókat Azure Stack hub-ból vagy a Kubernetes-t futtató linuxos virtuális gépekről. A naplók felügyeleti végpontból való lekéréséhez forduljon a Azure Stack hub rendszergazdájához.

## <a name="overview-of-kubernetes-deployment"></a>Az Kubernetes üzembe helyezésének áttekintése

A fürt hibakeresése előtt tekintse át az Azure Stack hub Kubernetes-fürt telepítési folyamatát. Az üzemelő példány egy Azure Resource Manager megoldás sablonnal hozza létre a virtuális gépeket, és telepíti a fürthöz tartozó AKS-Engine.

### <a name="kubernetes-deployment-workflow"></a>Kubernetes üzembe helyezési munkafolyamat

A következő ábra a fürt üzembe helyezésének általános folyamatát mutatja be.

![Kubernetes folyamat üzembe helyezése](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>A központi telepítés lépései

1. Gyűjtsön bemeneti paramétereket a Marketplace-elemből.

    Adja meg a Kubernetes-fürt beállításához szükséges értékeket, beleértve a következőket:
    -  **User Name (Felhasználónév**): a Kubernetes-fürt és a DVM részét képező linuxos virtuális gépek (VM-EK) felhasználóneve.
    -  **Nyilvános SSH-kulcs**: a Kubernetes-fürt és-DVM részeként létrehozott összes Linux-gép engedélyezéséhez használt kulcs.
    -  **Egyszerű szolgáltatásnév**: a Kubernetes Azure Cloud Provider által használt azonosító. Az ügyfél-azonosító az egyszerű szolgáltatásnév létrehozásakor alkalmazás-AZONOSÍTÓként van meghatározva. 
    -  **Ügyfél titka**: az egyszerű szolgáltatásnév létrehozásakor létrehozott kulcs.

2. Hozza létre az üzembe helyezési virtuális gépet és az egyéni szkriptek bővítményét.
    -  Hozza létre az üzembe helyezési linuxos virtuális gépet a Marketplace Linux rendszerkép **Ubuntu Server 16,04-LTS** használatával.
    -  Töltse le és futtassa az egyéni szkriptek bővítményét a piactéren. A szkript a **Linux 2,0-es egyéni szkriptje**.
    -  Futtassa az egyéni DVM parancsfájlt. A szkript a következő feladatokat hajtja végre:
        1. Lekéri a katalógus végpontját a Azure Resource Manager metaadat-végpontból.
        2. Az Active Directory-erőforrás AZONOSÍTÓjának beolvasása a Azure Resource Manager metaadat-végpontból.
        3. Betölti az alkabai Motor API-modelljét.
        4. Üzembe helyezi az AK-motort a Kubernetes-fürtön, és menti a Azure Stack hub felhőalapú profilját `/etc/kubernetes/azurestackcloud.json` .
3. Hozza létre a fő virtuális gépeket.

4. Egyéni parancsfájl-bővítmények letöltése és futtatása.

5. Futtassa a fő parancsfájlt.

    A szkript a következő feladatokat hajtja végre:
    - Telepíti a etcd, a Docker-és a Kubernetes-erőforrásokat, például a kubelet-t. a etcd egy elosztott kulcsbeli érték tároló, amely lehetővé teszi az adattárolást a gépek egy fürtjén keresztül. A Docker támogatja a tárolóként ismert operációs rendszer nélküli operációsrendszer-virtualizációkat. A Kubelet az egyes Kubernetes csomópontokon futó csomópont-ügynök.
    - Beállítja a **etcd** szolgáltatást.
    - Beállítja a **kubelet** szolgáltatást.
    - Elindítja a kubelet. Ez a feladat a következő lépéseket foglalja magában:
        1. Elindítja az API-szolgáltatást.
        2. Elindítja a vezérlő szolgáltatást.
        3. Elindítja a Scheduler szolgáltatást.
6. Hozzon létre ügynökként működő virtuális gépeket.

7. Töltse le és futtassa az egyéni szkriptek bővítményét.

7. Futtassa az ügynök parancsfájlját. Az ügynök egyéni parancsfájlja a következő feladatokat hajtja végre:
    - A **etcd** telepítése.
    - Beállítja a **kubelet** szolgáltatást.
    - Csatlakozik a Kubernetes-fürthöz.

## <a name="steps-to-troubleshoot-kubernetes"></a>A Kubernetes hibaelhárításának lépései

A Kubernetes-fürtöt támogató virtuális gépeken összegyűjtheti és áttekintheti a telepítési naplókat. Forduljon a Azure Stack hub rendszergazdájához, és ellenőrizze, hogy a használni kívánt Azure Stack hub-verziót, valamint a telepítéshez kapcsolódó Azure Stack hub naplóit szeretné-e beolvasni.

1. Tekintse át az ARM-telepítés által visszaadott hibakódot azon erőforráscsoport **központi telepítések** paneljén, amelyben üzembe helyezte a fürtöt. A hibakódok leírása az AK-motor GitHub-tárházában található [hibaelhárítási](https://github.com/msazurestackworkloads/azurestack-gallery/blob/master/kubernetes/docs/troubleshooting.md) cikkben található. Ha a hiba leírása nem oldható meg, folytassa a következő lépésekkel.
2. Tekintse át a [központi telepítés állapotát](#review-deployment-status) , és kérje le a naplókat a Kubernetes-fürt fő csomópontján.
3. Győződjön meg arról, hogy az Azure Stack hub legújabb verzióját használja. Ha nem tudja biztosan, hogy melyik verziót használja, forduljon a Azure Stack hub rendszergazdájához.
4. Tekintse át a virtuális gépek létrehozásához tartozó fájlokat. Lehetséges, hogy a következő problémák léptek fel:  
    - Lehet, hogy a nyilvános kulcs érvénytelen. Tekintse át a létrehozott kulcsot.  
    - Lehetséges, hogy a virtuális gép létrehozása belső hibát váltott ki, vagy létrehozási hibát váltott ki. Számos tényező okozhat hibákat, beleértve a Azure Stack hub-előfizetés kapacitásának korlátozásait is.
    - Győződjön meg arról, hogy a virtuális gép teljes tartományneve (FQDN) ismétlődő előtaggal kezdődik.
5.  Ha a virtuális gép **rendben** van, akkor értékelje ki a DVM. Ha a DVM hibaüzenetet kap:
    - Lehet, hogy a nyilvános kulcs érvénytelen. Tekintse át a létrehozott kulcsot.  
    - A Kiemelt végpontok használatával lépjen kapcsolatba az Azure Stack hub rendszergazdájával, és kérje le a Azure Stack hub naplóit. További információ: [Azure stack hub Diagnostics Tools](../operator/azure-stack-get-azurestacklog.md).
5. Ha kérdése van az üzemelő példányával kapcsolatban, közzéteheti azt, vagy megtekintheti, hogy valaki már megválaszolta-e a kérdést az [Azure stack hub-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 


## <a name="review-deployment-status"></a>Központi telepítés állapotának áttekintése

A Kubernetes-fürt üzembe helyezésekor áttekintheti a központi telepítés állapotát, és ellenőrizheti, hogy vannak-e problémák.

1. Nyissa meg az Azure Stack hub portált `https://portal.local.azurestack.external` .
2. Válassza az **erőforráscsoportok** lehetőséget, majd válassza ki a Kubernetes-fürt telepítésekor használt erőforráscsoport nevét.
3. Válassza a **központi telepítések** lehetőséget, majd válassza ki a **központi telepítés nevét**.

    ![A Kubernetes hibáinak megoldása: válassza az üzembe helyezés lehetőséget](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Forduljon a hibakeresési ablakhoz. Minden telepített erőforrás a következő információkat tartalmazza:
    
    | Tulajdonság | Leírás |
    | ----     | ----        |
    | Erőforrás | Az erőforrás neve. |
    | Típus | Az erőforrás-szolgáltató és az erőforrás típusa. |
    | Állapot | Az tétel állapota. |
    | Időbélyeg | Az idő UTC-időbélyege. |
    | Művelet részletei | A művelet részleteit, például a műveletben érintett erőforrás-szolgáltatót, az erőforrás-végpontot és az erőforrás nevét. |

    Minden elemhez zöld vagy piros állapot ikon tartozik.

## <a name="review-deployment-logs"></a>Telepítési naplók áttekintése

Ha az Azure Stack hub-portál nem biztosít elegendő információt az üzembe helyezési hibák elhárításához vagy leállításához, a következő lépés a fürt naplófájljainak beásása. Az üzembe helyezési naplók manuális beolvasásához általában csatlakoznia kell a fürt egyik fő virtuális géphez. Egyszerűbb alternatív megoldás, ha letölti és futtatja az Azure Stack hub csapata által biztosított alábbi [bash-szkriptet](https://aka.ms/AzsK8sLogCollectorScript) . Ez a szkript csatlakozik a DVM és a fürt virtuális gépeihoz, összegyűjti a kapcsolódó rendszer-és fürtözött naplókat, és letölti azokat a munkaállomásra.

### <a name="prerequisites"></a>Előfeltételek

A Azure Stack hub felügyeletéhez használt gépen bash-kérésre van szükség. Windows rendszerű gépen a [git for Windows](https://git-scm.com/downloads)telepítésével kérheti a bash-parancssort. A telepítés után keresse meg a _git basht_ a Start menüben.

### <a name="retrieving-the-logs"></a>Naplók beolvasása

A következő lépésekkel gyűjtheti össze és töltheti le a fürtök naplóit:

1. Nyisson meg egy bash-parancssort. Egy Windows rendszerű gépről nyissa meg a _git bash_ vagy a Run parancsot: `C:\Program Files\Git\git-bash.exe` .

2. Töltse le a log Collector-szkriptet a következő parancsok a bash-parancssorban történő futtatásával:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Keresse meg a parancsfájlhoz szükséges információkat, és futtassa azt:

    | Paraméter           | Leírás                                                                                                      | Példa                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d,--VMD-Host      | A DVM nyilvános IP-címe vagy teljesen minősített tartományneve (FQDN). A virtuális gép neve a (z) karakterlánccal kezdődik `vmd-` . | IP: 192.168.102.38<br>DNS: VMD-myk8s. local. cloudapp. azurestack. external |
    | -h,-– Súgó  | A parancs használatának nyomtatása. | |
    | -i,--Identity-file | Az Kubernetes-fürt létrehozásakor a Piactéri tételnek átadott RSA titkos kulcsfájl elérési útja. A Kubernetes-csomópontokhoz való távoli bejelentkezéshez szükséges. | C:\data\ id_rsa. PEM (Putty)<br>~/.ssh/id_rsa (SSH)
    | -m,--Master-Host   | A Kubernetes fő csomópontjának nyilvános IP-címe vagy teljes tartományneve (FQDN). A virtuális gép neve a (z) karakterlánccal kezdődik `k8s-master-` . | IP: 192.168.102.37<br>FQDN: k8s-12345. local. cloudapp. azurestack. external      |
    | -u,--User          | A Kubernetes-fürt létrehozásakor a Piactéri tételnek átadott felhasználó neve. A Kubernetes-csomópontokhoz való távoli bejelentkezéshez szükséges. | Azureus (alapértelmezett érték) |


   A paraméterek értékeinek hozzáadásakor a parancs a következő példához hasonlóan néz ki:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Néhány perc elteltével a szkript kiírja az összegyűjtött naplókat egy nevű könyvtárba `KubernetesLogs_{{time-stamp}}` . Itt talál egy könyvtárat a fürthöz tartozó minden egyes virtuális géphez.

    A log Collector parancsfájl a naplófájlokban is hibákat keres, és hibaelhárítási lépéseket is tartalmaz, ha ismert problémát talál. Győződjön meg arról, hogy a szkript legújabb verzióját futtatja, hogy növelje az ismert problémák megtalálásának esélyét.

> [!NOTE]  
> Tekintse meg ezt a GitHub- [tárházat](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) a log Collector parancsfájl további részleteinek megismeréséhez.

## <a name="next-steps"></a>További lépések

[Kubernetes üzembe helyezése Azure Stack hubhoz](azure-stack-solution-template-kubernetes-deploy.md)

[Kubernetes-fürt hozzáadása a piactérhez (az Azure Stack hub-kezelőhöz)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[A Kubernetes az Azure-on](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
