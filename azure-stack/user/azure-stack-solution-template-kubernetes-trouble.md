---
title: Hibaelhárítás a Kubernetes üzembe helyezés az Azure Stackhez |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a Kubernetes üzembe helyezés az Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabrigg
ms.date: 06/18/2019
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 89138601d1049f192946473d0a1fdb2c21df3e4c
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308719"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack"></a>Az Azure Stack a Kubernetes üzembe helyezés hibaelhárítása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió. Csak a Piactéri elem használata fejlesztéshez és teszteléshez.

Ez a cikk áttekinti a Kubernetes-fürt hibaelhárítása. Hibaelhárítás megkezdéséhez tekintse át az elemek, a telepítéshez szükséges. Szüksége lehet a telepítési naplók gyűjtése az Azure Stack vagy a Linux rendszerű virtuális gépek, amelyeken Kubernetes. Naplók lekérése egy felügyeleti végpont, lépjen kapcsolatba az Azure Stack-rendszergazdához.

## <a name="overview-of-kubernetes-deployment"></a>Kubernetes telepítésének áttekintése

Mielőtt a fürthöz, a hibaelhárításhoz tekintse át az Azure Stack-beli Kubernetes-fürt üzembe helyezési folyamat. A központi telepítés a virtuális gépek létrehozása és telepítése az AKS-motor a fürt számára egy megoldás Azure Resource Manager-sablon használatával.

### <a name="kubernetes-deployment-workflow"></a>Kubernetes-telepítési munkafolyamat

Az alábbi ábrán látható, az általános folyamat a fürt üzembe helyezéséhez.

![Kubernetes-folyamat üzembe helyezése](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>A központi telepítés lépései

1. Bemeneti paraméterek gyűjteni a Piactéri elemet.

    Adja meg az értékeket, akkor be kell állítania a Kubernetes-fürtöt, többek között:
    -  **Felhasználónév**: A Linux rendszerű virtuális gépek (VM), a Kubernetes-fürt és a DVM tartozó felhasználónév.
    -  **Nyilvános SSH-kulcs**: A kulcs, amely az összes Linux-számítógép, a Kubernetes-fürt és a DVM részeként létrehozott engedély szolgál.
    -  **Egyszerű szolgáltatás**: A Kubernetes Azure felhőszolgáltató által használt azonosítója. Az ügyfél-azonosító az Alkalmazásazonosítót azonosította az eseményt, az egyszerű szolgáltatás létrehozásakor. 
    -  **Titkos Ügyfélkód**: A kulcsot hozott létre, az egyszerű szolgáltatás létrehozásakor.

2. Az üzemelő példány virtuális gép létrehozása és az egyéni szkriptek futtatására szolgáló bővítmény.
    -  Az üzembe helyezés Linux rendszerű virtuális gép létrehozása a marketplace Linux-rendszerképek használatával **Ubuntu Server 16.04-LTS**.
    -  Töltse le és futtassa az egyéni szkriptek futtatására szolgáló bővítmény a marketplace-ről. A parancsfájl **egyéni parancsfájl Linux 2.0**.
    -  A DVM egyéni parancsfájl futtatásával. A szkript a következő feladatokat hajtja végre:
        1. A katalógus végpont lekérése az Azure Resource Manager-metaadatok végpontja.
        2. Az active directory erőforrás-azonosító lekérése az Azure Resource Manager-metaadatok végpontja.
        3. Betölti az AKS-motor az API modelljében.
        4. Az AKS-motor telepíti a Kubernetes-fürt, és menti az Azure Stack felhő profilt `/etc/kubernetes/azurestackcloud.json`.
3. A fő virtuális gépek létrehozásához.

4. Töltse le és futtassa egyéni parancsfájl-kiterjesztés.

5. A fő parancsfájl futtatásával.

    A szkript a következő feladatokat hajtja végre:
    - Telepíti a etcd, a Docker és a Kubernetes erőforrások, például kubelet. etcd egy elosztott kulcs-érték tároló, amely lehetővé teszi a számítógépfürtökön tárolja adatait. Docker tárolók néven operációs csontot operációsrendszer-szintű virtualizations támogatja. Kubelet a csomóponti ügynök, amely a Kubernetes-csomópontokon.
    - Beállítja a **etcd** szolgáltatás.
    - Beállítja a **kubelet** szolgáltatás.
    - Kubelet elindul. Ez a feladat az alábbi lépésekből áll:
        1. Elindítja az API-szolgáltatást.
        2. A hálózativezérlő-szolgáltatás elindul.
        3. A scheduler szolgáltatás elindul.
6. Az ügynök virtuális gépek létrehozása.

7. Töltse le és futtassa az egyéni szkriptek futtatására szolgáló bővítmény.

7. Futtassa az ügynök parancsfájlt. Az ügynök egyéni szkript a következő feladatokat hajtja végre:
    - Telepíti a **etcd**.
    - Beállítja a **kubelet** szolgáltatás.
    - A Kubernetes-fürthöz csatlakozik.

## <a name="steps-to-troubleshoot-kubernetes"></a>Kubernetes hibaelhárítása

Összegyűjtheti, és tekintse át a telepítési naplók a virtuális gépeken, amelyek támogatják a Kubernetes-fürthöz. Az Azure Stack rendszergazdai ellenőrizni a verziószámot, amely használja, és a naplók lekérése az Azure Stacken, amely a központi telepítés kapcsolódó van szüksége az Azure Stack-kommunikációhoz.

1. Tekintse át a [központi telepítési állapot](#review-deployment-status) és a naplók lekérése a főcsomópont a Kubernetes-fürtben.
2. Győződjön meg arról, hogy használ-e az Azure Stack legújabb verzióját. Ha biztos abban, hogy melyik verziót használ, lépjen kapcsolatba az Azure Stack rendszergazdai.
3.  Tekintse át a virtuális gép létrehozása fájljait. Előfordulhat, hogy a következő problémák rendelkeztek:  
    - Lehet, hogy a nyilvános kulcs érvénytelen. Tekintse át a kulcsot, amelyet Ön hozott létre.  
    - A virtuális gép létrehozása előfordulhat, hogy indított belső hiba történt, vagy aktivált-létrehozási hiba. Számos tényezőtől okozhat hibát, beleértve a kapacitás-korlátozások az Azure Stack-előfizetéshez.
    - Győződjön meg arról, hogy a teljesen minősített tartománynevét (FQDN) a virtuális gép olyan ismétlődő előtaggal kezdődik.
4.  Ha a virtuális gép **OK**, majd kiértékelheti a DVM. Ha a DVM hibaüzenetet:

    - Lehet, hogy a nyilvános kulcs érvénytelen. Tekintse át a kulcsot, amelyet Ön hozott létre.  
    - Az Azure Stack rendszergazdájától kérheti le a a naplókat az Azure Stack használatával a privilegizált végpontok. További információkért lásd: [Azure Stack-diagnosztikai eszközök](../operator/azure-stack-diagnostics.md).
5. Ha az üzembe helyezéssel kapcsolatos kérdése van, közzéteheti, vagy tekintse meg, ha valaki már megválaszolta a kérdést a [Azure Stack-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Tekintse át a telepítés állapota

Ha a Kubernetes-fürtöt telepít, a telepítés állapota minden olyan problémákat tekintheti meg.

1. Nyissa meg a [Azure Stack portálon](https://portal.local.azurestack.external).
2. Válassza ki **erőforráscsoportok**, majd válassza ki a nevét, amelyet a Kubernetes-fürt üzembe helyezésekor használt erőforráscsoport.
3. Válassza ki **központi telepítések**, majd válassza ki a **üzemelő példány neve**.

    ![Hibáinak elhárítása a Kubernetes: jelölje be telepítési](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Olvassa el a Hibaelhárítás ablakot. Minden üzembe helyezett erőforrás a következő információkat biztosítja:
    
    | Tulajdonság | Leírás |
    | ----     | ----        |
    | Resource | Az erőforrás neve. |
    | Típus | Az erőforrás-szolgáltató és az erőforrás típusát. |
    | Állapot | Az elem állapota. |
    | TimeStamp | Az az idő, UTC-időbélyeg. |
    | Művelet részletei | A művelet részleteit, például az erőforrás-szolgáltató, amely során a műveletet az erőforrás-végpont és az erőforrás nevét. |

    Minden elem, zöld vagy a piros állapot ikonja van.

## <a name="review-deployment-logs"></a>Tekintse át a telepítési naplók

Ha az Azure Stack portal nem biztosít elég információt ahhoz, hogy az üzembe helyezési hibák leküzdeni vagy hibák elhárítása, a következő lépésre, és elemezhetik a fürt naplóit. A telepítési naplók manuális lekéréséhez általában kell egyet a fürt fő virtuális gépek csatlakozni. Egyszerűbb kereteit lenne, töltse le és futtassa a következő [Bash-szkript](https://aka.ms/AzsK8sLogCollectorScript) biztosított az Azure Stack fejlesztőcsapatának. Ez a szkript a DVM és a fürt virtuális gépek csatlakozik, releváns rendszer és a fürt naplóit gyűjti, és letölti azokat vissza a munkaállomáson.

### <a name="prerequisites"></a>Előfeltételek

Szüksége van a gépen, kezelheti az Azure Stack egy Bash-parancssort. Egy Windows-gépen, kap egy Bash parancssor telepítésével [Git for Windows](https://git-scm.com/downloads). Hely telepítése után a _a Git Bash_ a start menüben.

### <a name="retrieving-the-logs"></a>A naplók beolvasása

Kövesse az alábbi lépéseket gyűjtése és a fürt naplók letöltéséhez:

1. Nyissa meg a Bash parancssorban. Nyisson meg egy Windows-gépről _a Git Bash_ vagy futtatása: `C:\Program Files\Git\git-bash.exe`.

2. A naplózási gyűjtő szkript letöltése a Bash-parancssorban a következő parancsok futtatásával:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Keresse meg a parancsfájlhoz szükséges adatokat, és futtassa:

    | Paraméter           | Leírás                                                                                                      | Példa                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | A nyilvános IP-cím vagy a teljes tartománynevét (FQDN) a DVM. A virtuális gép neve kezdődik `vmd-`. | IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Nyomtatási parancs használata. | |
    | -i – identitás-fájlja | A Kubernetes-fürt létrehozásakor a Piactéri elem átadása az RSA titkos kulcsfájl elérési útja. Szükséges a távoli, a Kubernetes-csomópontokon. | C:\data\id_rsa.pem (Putty)<br>~/.ssh/id_rsa (SSH)
    | -m, --master-host   | A nyilvános IP-cím vagy a fő Kubernetes csomópont teljesen minősített tartománynevét (FQDN). A virtuális gép neve kezdődik `k8s-master-`. | IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u: – a felhasználó          | A Kubernetes-fürt létrehozásakor a Piactéri elem átadása a felhasználó nevét. Szükséges a távoli, a Kubernetes-csomópontokon. | azureuser (alapértelmezett érték) |


   Amikor hozzáadja a paraméterértékeket, a parancs a következőhöz hasonló ebben a példában:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Néhány perc múlva a parancsfájl kimenete nevű könyvtárat a gyűjtött naplók `KubernetesLogs_{{time-stamp}}`. Ott találja egy könyvtárat, amely a fürthöz tartozó virtuális gépek.

    A naplózási gyűjtő parancsfájlt is keresse meg a naplófájlokban szereplő hibákat, és tartalmazza a hibaelhárítási lépések, ha úgy találja, hogy egy ismert probléma. Ellenőrizze, hogy a legújabb verzióra, szoftver-és ismert problémák keresése növelését parancsfájl futtatásakor.

> [!Note]  
> Tekintse meg a GitHub [tárház](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) a naplózási gyűjtő parancsfájllal kapcsolatos további részleteket.

## <a name="next-steps"></a>További lépések

[Az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)

[Kubernetes-fürt hozzáadása a Marketplace-en (az Azure Stack-operátorokról)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
