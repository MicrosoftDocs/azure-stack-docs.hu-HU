---
title: Az AK-motor hibáinak megoldása a Azure Stackon | Microsoft Docs
description: Ez a témakör a Azure Stack AK-beli motorjának hibaelhárítási lépéseit ismerteti.
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: eb8a46c5b226d1be40d922a78c6ecdcdda5e45ad
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019193"
---
# <a name="troubleshoot-the-aks-engine-on-azure-stack"></a>Az AK-motor hibáinak megoldása Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Előfordulhat, hogy a Azure Stack-on lévő AK-motor telepítésekor vagy használatakor problémába ütközik. Ez a cikk az AK-motor üzembe helyezésével kapcsolatos hibaelhárítási lépéseket ismerteti, adatokat gyűjt az AK-motorról, gyűjti össze a Kubernetes-naplókat, áttekinti az egyéni szkriptek bővítményének hibakódait, valamint útmutatást nyújt a GitHub-probléma megmegnyitásához

> [!IMPORTANT]
> Az AK-motor jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="troubleshoot-the-aks-engine-install"></a>Az KABAi motor telepítésének hibája

### <a name="try-gofish"></a>GoFish kipróbálása

Ha a telepítési lépések sikertelenek voltak, a GoFish csomagkezelő használatával próbálkozhat a telepítéssel. A [GoFish](https://gofi.sh) platformfüggetlen Homebrew-ként mutatja be magát.

#### <a name="install-the-aks-engine-with-gofish-on-linux"></a>Az AK-motor telepítése a GoFish Linux rendszeren

Telepítse a GoFish a [telepítés](https://gofi.sh/#install) lapról.

1. Egy bash-parancssorból futtassa a következő parancsot:

    ```bash
    curl -fsSL https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.sh | bash
    ```

2.  Futtassa az alábbi parancsot a GoFish-mel rendelkező AK-motor telepítéséhez:

    ```bash
    Run "gofish install aks-engine"
    ```

#### <a name="install-the-aks-engine-with-gofish-on-windows"></a>Az AK-motor telepítése a GoFish Windows rendszeren

Telepítse a GoFish a [telepítés](https://gofi.sh/#install) lapról.

1. Futtassa a következő parancsot egy rendszergazda jogú PowerShell-parancssorból:

    ```PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force
iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.ps1'))
    ```

2.  Futtassa az alábbi parancsot ugyanabban a munkamenetben az AK-motor GoFish való telepítéséhez:

    ```PowerShell
    gofish install aks-engine
    ```

### <a name="checklist-for-common-deployment-issues"></a>Általános telepítési problémák ellenőrzőlistája

Ha a Kubernetes-fürt az KABAi motor használatával történő telepítésekor hibákat észlel, a következőket végezheti el:

1.  A megfelelő egyszerű szolgáltatás hitelesítő adatait (SPN) használja?
2.  Az SPN "közreműködői" szerepkörrel rendelkezik a Azure Stack-előfizetéshez?
3. Nagy mennyiségű kvóta van a Azure Stack tervében?
4.  A Azure Stack példányon van a javítás vagy a frissítés alkalmazása?

További információ: az **Azure/AK-Engine GitHub-** tárház [hibaelhárítási](https://github.com/Azure/aks-engine/blob/master/docs/howto/troubleshooting.md) cikke.

## <a name="collect-aks-engine-logs"></a>AK-beli keresőmotor-naplók gyűjtése

Elérheti az AK-motor által létrehozott felülvizsgálati információkat. Az AK-motor jelentéseinek állapota és a hibák, ahogy az alkalmazás fut. A kimenetet áthelyezheti egy szövegfájlba, vagy közvetlenül a parancssori konzolról másolhatja.

1.  A standard kimenet és a hiba összegyűjtése az AK-motor parancssori eszközében megjelenő adatokból.

2. Naplók beolvasása helyi fájlból. A kimeneti könyvtárat a **--output-Directory** paraméterrel állíthatja be.

    A naplók helyi elérési útjának beállítása:

    ```bash  
    aks-engine --output-directory <path to the directory>
    ```

## <a name="collect-kubernetes-logs"></a>Kubernetes-naplók gyűjtése

Az AK-beli motor naplóin kívül az Kubernetes-összetevők állapot-és hibaüzeneteket hoznak. Ezeket a naplókat a [Getkuberneteslogs.sh](https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.0/diagnosis.zip)bash-parancsfájllal is összegyűjtheti.

Ez a szkript automatizálja a következő naplók összegyűjtésének folyamatát: 

 - Microsoft Azure Linux Agent-(waagent-) naplók
 - Egyéni szkriptek bővítményének naplói
 - A Kube-rendszer tároló metaadatainak futtatása
 - Kube rendszerű tároló-naplók futtatása
 - Kubelet szolgáltatás állapota és naplója
 - Etcd szolgáltatás állapota és naplója
 - Gyűjtemény DVM naplói
 - Kube – rendszerpillanatkép

A szkript nélkül a fürt mindegyik csomópontjára csatlakoznia kell, és manuálisan le kell töltenie a naplókat. Emellett a parancsfájl igény szerint feltöltheti az összegyűjtött naplókat egy olyan Storage-fiókba, amelyet a naplók másokkal való megosztásához használhat.

Követelmények:

 - Linux rendszerű virtuális gép, git bash vagy bash Windows rendszeren.
 - Az [Azure CLI](azure-stack-version-profiles-azurecli2.md) telepítve van a gépen, ahonnan a szkript futni fog.
 - Az egyszerű szolgáltatás identitása egy Azure CLI-munkamenetbe jelentkezett be Azure Stackba. Mivel a szkript képes az ARM-erőforrások felfedésére és létrehozására, hogy elvégezze a munkáját, az Azure CLI és egy egyszerű szolgáltatásnév szükséges.
 - A felhasználói fiók (előfizetés), amelyben a Kubernetes-fürt már ki van választva a környezetben. 
1. Töltse le a szkript tar-fájljának legújabb kiadását az ügyfél virtuális gépére, egy olyan gépre, amely hozzáfér a Kubernetes-fürthöz, vagy ugyanaz a gép, amelyet a fürt az AK-motorral való üzembe helyezéséhez használt.

    Futtassa az alábbi parancsot:

    ```bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    wget https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.0/diagnosis.tar.gz
    tar xvzf diagnosis.tar.gz -C ./
    ```

2. Keresse meg a `getkuberneteslogs.sh` parancsfájlhoz szükséges paramétereket. A szkript a következő paramétereket fogja használni:

    | Paraméter | Leírás | Kötelező | Példa |
    | --- | --- | --- | --- |
    | -h,-– Súgó | A parancs használatának nyomtatása. | nem | 
    -u,--felhasználó | A fürt virtuális gépei rendszergazdai felhasználóneve | igen | azureuser<br>(alapértelmezett érték) |
    | -i,--Identity-file | A Kubernetes-fürt létrehozásához használt nyilvános kulcshoz kötött RSA titkos kulcs (néha "id_rsa" néven)  | igen | `./rsa.pem`Putty<br>`~/.ssh/id_rsa`SSH |
    |   -g, --resource-group    | Kubernetes fürterőforrás-csoport | igen | k8sresourcegroup |
    |   -n,--User-Namespace               | Naplók gyűjtése a tárolókban a megadott névterekben (a Kube-rendszernaplókat mindig gyűjti a rendszer) | nem |   figyelés |
    |       --API-Model                    | Megőrzi a apimodel. JSON fájlt egy Azure Stack Storage-fiókban. Töltse fel a apimodel. JSON fájlt a Storage-fiókba, ha a--upload-logs paraméter is meg van adni. | nem | `./apimodel.json` |
    | – az összes névtér               | Naplók gyűjtése a tárolókban az összes névtérben. Felülbírálja a--User-Namespace | nem | |
    | – naplók feltöltése                  | Beolvasott naplókat tart fenn egy Azure Stack Storage-fiókban. A naplók a KubernetesLogs erőforráscsoporthoz találhatók | nem | |
    --host-key-Check letiltása    | Az SSH StrictHostKeyChecking beállítását "nem" értékre állítja a parancsfájl végrehajtása közben. Csak biztonságos környezetben használható. | nem | |

3. Futtassa az alábbi parancsok bármelyikét az adataival:

    ```bash
    ./getkuberneteslogs.sh -u azureuser -i private.key.1.pem -g k8s-rg
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --disable-host-key-checking
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg -n default -n monitoring
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs --api-model clusterDefinition.json
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs
    ```

## <a name="review-custom-script-extension-error-codes"></a>Egyéni parancsfájl-kiterjesztési hibakódok áttekintése

A fürt futtatásához tekintse meg az egyéni szkriptek bővítménye (CSE) által létrehozott hibakódok listáját. Az ÜGYFÉLOLDALI hiba a probléma kiváltó okának diagnosztizálásakor hasznos lehet. A Kubernetes-fürtben használt Ubuntu Serverhez készült ÜGYFÉLOLDALI szolgáltatás számos, az AK-motorral kapcsolatos műveletet támogat. További információ az ÜGYFÉLOLDALI kilépési kódokról: [cse_helpers. sh](https://github.com/Azure/aks-engine/blob/master/parts/k8s/cloud-init/artifacts/cse_helpers.sh).

## <a name="open-github-issues"></a>GitHub-problémák megnyitása

Ha nem tudja feloldani a telepítési hibát, megnyithatja a GitHub-problémát. 

1. Nyisson meg egy [GitHub-problémát](https://github.com/Azure/aks-engine/issues/new) az AK-motor adattárában.
2. Adja hozzá a címet a következő formátum használatával: C`SE error: exit code <INSERT_YOUR_EXIT_CODE>`.
3. A probléma a következő információkat tartalmazza:

    - A fürt üzembe helyezéséhez `apimodel json`használt fürtözött konfigurációs fájl. A GitHubon való közzététel előtt távolítsa el az összes titkot és kulcsot.  
     - A következő **kubectl** -parancs `get nodes`kimenete.  
     - A tartalma `/var/log/azure/cluster-provision.log` és`/var/log/cloud-init-output.log`

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack AK-beli motorról](azure-stack-kubernetes-aks-engine-overview.md)
