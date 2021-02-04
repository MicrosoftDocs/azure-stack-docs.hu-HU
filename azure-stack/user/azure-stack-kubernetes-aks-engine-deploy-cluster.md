---
title: Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on
description: Kubernetes-fürt üzembe helyezése Azure Stack hub-on az AK-motort futtató ügyfél virtuális gépről.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 38628b6fba6136149cb6ef7e7ce818e2e2039d11
ms.sourcegitcommit: af2bec84471795c0f3ac62dcaf1347a64e529906
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554125"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on

A Kubernetes-fürtöt Azure Stack hubhoz telepítheti egy olyan ügyfél virtuális gépről, amely az AK-motort futtatja. Ebben a cikkben a fürt specifikációjának írását, a fürt és a fájllal történő üzembe helyezését, `apimodel.json` valamint a fürt ellenőrzésével foglalkozunk a MySQL és a Helm használatával.

## <a name="define-a-cluster-specification"></a>Fürt specifikációjának megadása

A fürt specifikációját megadhatja egy dokumentum-fájlban az [API-modell](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram)nevű JSON-formátum használatával. Az KABAi motor a fürt létrehozásához az API-modellben egy fürt-specifikációt használ. 

### <a name="update-the-api-model"></a>Az API-modell frissítése

Ez a szakasz a fürthöz tartozó API-modell létrehozását vizsgálja.

1.  Először a [Linux](https://aka.ms/aksengine-json-example-raw) vagy a [Windows](https://aka.ms/aksengine-json-example-raw-win) rendszerhez készült Azure stack hub API-modellel, és a telepítéshez készítsen helyi másolatot. A gépről telepítette az AK motort:

    ```bash
    curl -o kubernetes-azurestack.json https://aka.ms/aksengine-json-example-raw
    ```

    > [!NOTE]  
    > Ha le van választva, letöltheti a fájlt, és manuálisan másolhatja azt a leválasztott gépre, ahol szerkeszteni szeretné. A fájlt a Linux rendszerű gépre másolhatja, például a [Putty vagy a megnyerő](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html)eszközzel.

2.  Az API-modell egy szerkesztőben való megnyitásához használhatja a nanot:

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!NOTE]  
    > Ha nincs telepítve a nano, akkor telepítheti a nanot Ubuntu-re: `sudo apt-get install nano` .

3.  A kubernetes-azurestack.jsfájlban keresse meg a orchestratorRelease és a orchestratorVersion. Válasszon egy támogatott Kubernetes-verziót. Például az `orchestratorRelease` 1,14-es vagy a 1,15-es, illetve a `orchestratorVersion` 1.14.7 vagy a 1.15.10 használata esetén. A `orchestratorRelease` as x. xx és orchestratorVersion x. xx. x néven kell megadnia. Az aktuális verziók listáját lásd: [támogatott AK-motor verziói](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions)

4.  Keresse meg `customCloudProfile` és adja meg a bérlői portál URL-címét. Például: `https://portal.local.azurestack.external`. 

5. Adja hozzá `"identitySystem":"adfs"` a AD FS-t. Példa:

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!NOTE]  
    > Ha az Azure AD-t használja az identitásrendszer számára, nem kell felvennie a **identitySystem** mezőt.

6. Keresse meg `portalURL` és adja meg a bérlői portál URL-címét. Például: `https://portal.local.azurestack.external`.

7.  A alkalmazásban `masterProfile` állítsa be a következő mezőket:

    | Mező | Leírás |
    | --- | --- |
    | dnsPrefix | Adjon meg egy egyedi karakterláncot, amely a virtuális gépek állomásneve azonosítására szolgál majd. Például egy név az erőforráscsoport neve alapján. |
    | count |  Adja meg a központi telepítéshez használni kívánt főkiszolgálók számát. HA egy HA üzemelő példány esetében a minimum a 3, az 1 érték nem lehet üzemelő példányokhoz. |
    | vmSize |  Adja meg [Azure stack hub által támogatott méretet](./azure-stack-vm-sizes.md)(példa `Standard_D2_v2` ). |
    | disztribúció | Írja be a következő szöveget: `aks-ubuntu-16.04`. |

8.  A `agentPoolProfiles` frissítés alatt:

    | Mező | Leírás |
    | --- | --- |
    | count | Adja meg az üzemelő példányhoz használni kívánt ügynökök számát. Az előfizetések által használandó csomópontok maximális száma 50. Ha egy előfizetéshez egynél több fürtöt telepít, győződjön meg arról, hogy az ügynökök teljes száma nem haladja meg az 50-ot. Ügyeljen arra, hogy a [minta API-modell JSON-fájljában](https://aka.ms/aksengine-json-example-raw)megadott konfigurációs elemeket használja.  |
    | vmSize | Adja meg [Azure stack hub által támogatott méretet](./azure-stack-vm-sizes.md)(példa `Standard_D2_v2` ). |
    | disztribúció | Írja be a következő szöveget: `aks-ubuntu-16.04`. |




9.  A `linuxProfile` frissítés alatt:

    | Mező | Leírás |
    | --- | --- |
    | adminUsername | Adja meg a virtuális gép rendszergazdájának felhasználónevét. |
    | SSH | Adja meg azt a nyilvános kulcsot, amelyet a virtuális gépekkel való SSH-hitelesítéshez kíván használni. Használja `ssh-rsa` , majd a kulcsot. A nyilvános kulcsok létrehozásával kapcsolatos utasításokért lásd: [SSH-kulcs létrehozása Linux rendszerhez](create-ssh-key-on-windows.md). |

    Ha egyéni virtuális hálózatra telepíti a szolgáltatást, a szükséges kulcsok és értékek megkereséséhez és hozzáadásához útmutatást talál a megfelelő tömbökhöz az API-modellben a [Kubernetes-fürt egyéni virtuális hálózatra történő telepítéséhez](kubernetes-aks-engine-custom-vnet.md).

    > [!NOTE]  
    > Az Azure Stack hub AK-motorja nem teszi lehetővé saját tanúsítványok megadását a fürt létrehozásához.

### <a name="more-information-about-the-api-model"></a>További információ az API-modellről

- Az API-modell összes elérhető beállításának teljes hivatkozását a [fürt definíciói](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)című témakörben találja.  
- Az Azure Stack hub adott lehetőségeinek kiemeléséhez tekintse meg a [Azure stack hub-fürt definíciójának sajátosságait](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Kubernetes-fürt üzembe helyezése

Miután összegyűjtötte az összes szükséges értéket az API-modellben, létrehozhat egy fürtöt. Ezen a ponton a következőket kell tennie:

Kérdezze meg a Azure Stack hub operátort a következőre:

- Ellenőrizze a rendszer állapotát, és javasolja a futtatását `Test-AzureStack` és az OEM-gyártó hardveres figyelési eszközét.
- Ellenőrizze a rendszerkapacitást, beleértve az erőforrásokat, például a memóriát, a tárterületet és a nyilvános IP-címeket.
- Adja meg az előfizetéshez társított kvóta részleteit, így ellenőrizheti, hogy van-e elegendő hely a használni kívánt virtuális gépek számához.

Fürt üzembe helyezésének folytatása:

1.  Tekintse át a Azure Stack hub [CLI-jelzők](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags)számára elérhető paramétereket az AK-motor számára.

    | Paraméter | Példa | Leírás |
    | --- | --- | --- |
    | Azure – env | AzureStackCloud | Annak jelzése, hogy a célként megadott platform Azure Stack hub által használt AK-motor `AzureStackCloud` . |
    | identitás-rendszerek | ADFS | Választható. Ha Active Directory összevont szolgáltatásokat (AD FS) használ, adja meg a személyazonosság-kezelési megoldást. |
    | location | helyi | Az Azure Stack hub régiójának neve. A ASDK esetében a régió a következőre van beállítva: `local` . |
    | resource-group | Kube – RG | Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevének alfanumerikusnak és kisbetűsnek kell lennie. |
    | API – modell | ./kubernetes-azurestack.jsbekapcsolva | A fürt konfigurációs fájljának vagy API-modellének elérési útja. |
    | kimenet – könyvtár | Kube – RG | Adja meg annak a könyvtárnak a nevét, amely a kimeneti fájlt `apimodel.json` és más létrehozott fájlokat is tartalmaz. |
    | ügyfél-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatásnév GUID azonosítóját. Az ügyfél-azonosító az alkalmazás AZONOSÍTÓJAként van azonosítva, amikor a Azure Stack hub rendszergazdája létrehozta a szolgáltatásnevet. |
    | ügyfél – titok | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatás titkos kulcsát. A szolgáltatás létrehozásakor az ügyfél titkát kell beállítania. |
    | előfizetés-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az előfizetés-AZONOSÍTÓját. Meg kell adnia egy előfizetést a bérlő. A felügyeleti előfizetésre való telepítés nem támogatott.  További információ: [előfizetés egy ajánlatra](./azure-stack-subscribe-services.md#subscribe-to-an-offer) |

    Alább bemutatunk egy példát:

    ```bash  
    aks-engine deploy \
    --azure-env AzureStackCloud \
    --location <for asdk is local> \
    --resource-group kube-rg \
    --api-model ./kubernetes-azurestack.json \
    --output-directory kube-rg \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

2.  Ha valamilyen oknál fogva a kimeneti könyvtár létrehozása után a végrehajtás meghiúsul, javítsa ki a hibát, és futtassa újra a parancsot. Ha újra futtatja az üzemelő példányt, és ugyanazt a kimeneti könyvtárat használta, az AK-motor hibát jelez, ami azt jelzi, hogy a könyvtár már létezik. A meglévő könyvtárat a Flag: paranccsal írhatja felül `--force-overwrite` .

3.  Mentse az KABAi motor fürtjének konfigurációját biztonságos, titkosított helyen.

    Keresse meg a fájlt `apimodel.json` . Mentse biztonságos helyre. Ezt a fájlt a rendszer az összes többi AK-motor-művelet bemenetként használja.

    A generált `apimodel.json` szolgáltatás tartalmazza a bemeneti API-modellben használt egyszerű szolgáltatásnevet, titkos kulcsot és nyilvános SSH-kulcsot. Emellett az összes többi művelet elvégzéséhez szükséges minden egyéb metaadat is az AK-motor számára. Ha elveszíti, az AK-motor nem tudja konfigurálni a fürtöt.

    A titkos kulcsok nem **titkosítottak**. Tárolja a fájlt titkosított, biztonságos helyen. 

## <a name="verify-your-cluster"></a>A fürt ellenőrzése

Győződjön meg arról, hogy a fürt ellenőrzéséhez üzembe helyezi a MySql-t a Helm használatával.

1. Szerezze be az egyik főcsomópont nyilvános IP-címét az Azure Stack hub portál használatával.

2. Egy olyan számítógépről, amely hozzáféréssel rendelkezik a Azure Stack hub-példányhoz, csatlakozzon SSH-n keresztül az új fő csomóponthoz egy ügyfél, például a PuTTY vagy a MobaXterm használatával. 

3. Az SSH-felhasználónévhez használja az "azureuser" nevet, valamint a fürt üzembe helyezéséhez megadott kulcspár titkos kulcsát.

4. A következő parancsok futtatásával hozzon létre egy Redis-főkiszolgáló minta-telepítését (csak a csatlakoztatott bélyegzők esetében):

   ```bash
   kubectl apply -f https://k8s.io/examples/application/guestbook/redis-master-deployment.yaml
   ```

    1. A hüvelyek listájának lekérdezése:

       ```bash
       kubectl get pods
       ```

    2. A válasznak a következőhöz hasonlónak kell lennie:

       ```shell
       NAME                            READY     STATUS    RESTARTS   AGE
       redis-master-1068406935-3lswp   1/1       Running   0          28s
       ```

    3. Az üzembe helyezési naplók megtekintése:

       ```shell
       kubectl logs -f <pod name>
       ```

    A Redis főkiszolgálót tartalmazó minta PHP-alkalmazás teljes üzembe helyezéséhez kövesse [az itt található utasításokat](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/).

5. Leválasztott bélyegző esetén a következő parancsoknak elegendőnek kell lenniük:

    1. Először győződjön meg arról, hogy a fürt végpontjai futnak:

       ```bash
       kubectl cluster-info
       ```

       A kimenetnek a következőképpen kell kinéznie:

       ```shell
       Kubernetes master is running at https://democluster01.location.domain.com
       CoreDNS is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
       kubernetes-dashboard is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy
       Metrics-server is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
       ```

    2. Ezután tekintse át a csomópont-állapotokat:

       ```bash
       kubectl get nodes
       ```

       A kimenet az alábbihoz hasonló lesz:

       ```shell
       k8s-linuxpool-29969128-0   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-1   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-2   Ready      agent    9d    v1.15.5
       k8s-master-29969128-0      Ready      master   9d    v1.15.5
       k8s-master-29969128-1      Ready      master   9d    v1.15.5
       k8s-master-29969128-2      Ready      master   9d    v1.15.5
       ```

6. Az alábbi parancs futtatásával törölheti az Redis POD üzembe helyezését az előző lépésből:

    ```bash
    kubectl delete deployment -l app=redis
    ```

## <a name="rotate-your-service-principle-secret"></a>A szolgáltatás elvének titkának elforgatása

A Kubernetes-fürt az AK-motorral való üzembe helyezését követően az egyszerű szolgáltatásnév (SPN) az Azure Stack hub-példányon Azure Resource Managerekkel folytatott interakciók kezelésére szolgál. Egy adott pillanatban az egyszerű szolgáltatásnév titka lejárhat. Ha a titkos kód lejár, a hitelesítő adatokat az alábbiak szerint frissítheti:

- Az egyes csomópontok frissítése az új egyszerű szolgáltatás titkos kódjával.
- Az API-modell hitelesítő adatainak frissítése és a frissítés futtatása.

### <a name="update-each-node-manually"></a>Az egyes csomópontok manuális frissítése

1. Szerezzen be egy új titkot az egyszerű szolgáltatásnév számára a Felhőbeli operátorral. Az Azure Stack hub használatára vonatkozó utasításokért lásd: [alkalmazás-identitás használata Azure stack hub-erőforrások eléréséhez](../operator/azure-stack-create-service-principals.md).
2. Használja a Felhőbeli kezelő által megadott új hitelesítő adatokat az `/etc/kubernetes/azure.json` egyes csomópontokon való frissítéshez. A frissítés elkészítése után indítsa újra a **kubelet** és a **Kube-Controller-Manager alkalmazást**.

### <a name="update-the-cluster-with-aks-engine-update"></a>A fürt frissítése az AK-Engine Update szolgáltatással

Azt is megteheti, hogy lecseréli a hitelesítő adatokat a alkalmazásban, `apimodel.json` és a frissített JSON-val futtatja a frissítést a Kubernetes azonos vagy újabb verziójára. A modell frissítésével kapcsolatos útmutatásért lásd: [Kubernetes-fürt frissítése Azure stack hub-on](azure-stack-kubernetes-aks-engine-upgrade.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az AK-motor hibáinak megoldása Azure Stack hub-on](azure-stack-kubernetes-aks-engine-troubleshoot.md)