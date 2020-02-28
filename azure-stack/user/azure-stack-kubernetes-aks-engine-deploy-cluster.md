---
title: Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on
description: Kubernetes-fürt üzembe helyezése Azure Stack hub-on az AK-motort futtató ügyfél virtuális gépről.
author: mattbriggs
ms.topic: article
ms.date: 01/10/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: b8826fc929c571e39d36139bf724861ae9cc7fbd
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702703"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on

A Kubernetes-fürtöt Azure Stack hubhoz telepítheti egy olyan ügyfél virtuális gépről, amely az AK-motort futtatja. Ebben a cikkben bemutatjuk a fürt specifikációjának írását, a fürt üzembe helyezését a `apimodel.json` fájllal, valamint a fürt ellenőrzésével, amely a MySQL-t a Helm használatával helyezi üzembe.

## <a name="define-a-cluster-specification"></a>Fürt specifikációjának megadása

A fürt specifikációját megadhatja egy dokumentum-fájlban az [API-modell](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram)nevű JSON-formátum használatával. Az KABAi motor a fürt létrehozásához az API-modellben egy fürt-specifikációt használ. 

### <a name="update-the-api-model"></a>Az API-modell frissítése

Ez a szakasz a fürthöz tartozó API-modell létrehozását vizsgálja.

1.  Kezdje egy Azure Stack hub [példa](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) API-modellel, és készítsen helyi másolatot a telepítéshez. A gépről telepítette az AK motort:

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > Ha le van választva, letöltheti a fájlt, és manuálisan másolhatja azt a leválasztott gépre, ahol szerkeszteni szeretné. A fájlt a Linux rendszerű gépre másolhatja, például a [Putty vagy a megnyerő](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html)eszközzel.

2.  A szerkesztőben való megnyitásához használhatja a nanot:

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > Ha nincs telepítve a nano, a nanot telepítheti Ubuntu-re: `sudo apt-get install nano`.

3.  A kubernetes-azurestack. JSON fájlban keresse meg a `orchestratorRelease`. Válasszon egy támogatott Kubernetes-verziót. Például 1,14, 1,15. A verziók gyakran frissülnek. A verziót x. xx helyett x. xx. x értékkel kell megadni. Az aktuális verziók listáját lásd: [támogatott Kubernetes-verziók](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). A támogatott verziót a következő AK Engine-parancs futtatásával tekintheti meg:

    ```bash
    aks-engine get-versions
    ```

4.  Keresse meg `customCloudProfile`, és adja meg a bérlői portál URL-címét. Például: `https://portal.local.azurestack.external`. 

5. Ha AD FSt használ, vegye fel `"identitySystem":"adfs"`. Például:

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!Note]  
    > Ha az Azure AD-t használja az identitásrendszer számára, nem kell felvennie a **identitySystem** mezőt.

6. Keresse meg `portalURL`, és adja meg a bérlői portál URL-címét. Például: `https://portal.local.azurestack.external`.

7.  A tömb `masterProfile`állítsa be a következő mezőket:

    | Mező | Leírás |
    | --- | --- |
    | dnsPrefix | Adjon meg egy egyedi karakterláncot, amely a virtuális gépek állomásneve azonosítására szolgál majd. Például egy név az erőforráscsoport neve alapján. |
    | count |  Adja meg a központi telepítéshez használni kívánt főkiszolgálók számát. HA egy HA üzemelő példány esetében a minimum a 3, az 1 érték nem lehet üzemelő példányokhoz. |
    | vmSize |  Adja meg [Azure stack hub által támogatott méretet](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)(például `Standard_D2_v2`). |
    | disztribúció | Írja be a `aks-ubuntu-16.04` (igen) kifejezést. |

8.  A tömb `agentPoolProfiles` frissítésében:

    | Mező | Leírás |
    | --- | --- |
    | count | Adja meg az üzemelő példányhoz használni kívánt ügynökök számát. |
    | vmSize | Adja meg [Azure stack hub által támogatott méretet](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)(például `Standard_D2_v2`). |
    | disztribúció | Írja be a `aks-ubuntu-16.04` (igen) kifejezést. |

9.  A tömb `linuxProfile` frissítésében:

    | Mező | Leírás |
    | --- | --- |
    | adminUsername | Adja meg a virtuális gép rendszergazdájának felhasználónevét. |
    | ssh | Adja meg azt a nyilvános kulcsot, amelyet a virtuális gépekkel való SSH-hitelesítéshez kíván használni. Ha a PuTTY-t használja, nyissa meg a PuTTY Key Generatort a PuTTY titkos kulcs és a nyilvános kulcs betöltéséhez, amely az SSH-RSA-val kezdődik az alábbi példában látható módon. A Linux-ügyfél létrehozásakor generált kulcsot használhatja, de **a nyilvános kulcsot is át kell másolnia, hogy az egyetlen sor szöveg legyen a példában látható módon**.|

    ![Putty Key Generator](media/azure-stack-kubernetes-aks-engine-deploy-cluster/putty-key-generator.png)

### <a name="more-information-about-the-api-model"></a>További információ az API-modellről

- Az API-modell összes elérhető beállításának teljes hivatkozását a [fürt definíciói](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)című témakörben találja.  
- Az Azure Stack hub adott lehetőségeinek kiemeléséhez tekintse meg a [Azure stack hub-fürt definíciójának sajátosságait](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Kubernetes-fürt üzembe helyezése

Miután összegyűjtötte az összes szükséges értéket az API-modellben, létrehozhat egy fürtöt. Ezen a ponton a következőket kell tennie:

Kérdezze meg a Azure Stack hub operátort a következőre:

- Ellenőrizze a rendszer állapotát, és futtassa a `Test-AzureStack` és az OEM-gyártó hardver-figyelési eszközét.
- Ellenőrizze a rendszerkapacitást, beleértve az erőforrásokat, például a memóriát, a tárterületet és a nyilvános IP-címeket.
- Adja meg az előfizetéshez társított kvóta részleteit, így ellenőrizheti, hogy van-e elegendő hely a használni kívánt virtuális gépek számához.

Fürt üzembe helyezésének folytatása:

1.  Tekintse át a Azure Stack hub [CLI-jelzők](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags)számára elérhető paramétereket az AK-motor számára.

    | Paraméter | Példa | Leírás |
    | --- | --- | --- |
    | Azure – env | AzureStackCloud | Annak jelzése, hogy a célként megadott platformot Azure Stack hub használja `AzureStackCloud`. |
    | identitás-rendszerek | ADFS | Választható. Ha Active Directory összevont szolgáltatásokat (AD FS) használ, adja meg a személyazonosság-kezelési megoldást. |
    | location | helyi | Az Azure Stack hub régiójának neve. A ASDK a régió `local`értékre van állítva. |
    | resource-group | Kube – RG | Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket. |
    | API – modell | ./kubernetes-azurestack.json | A fürt konfigurációs fájljának vagy API-modellének elérési útja. |
    | kimenet – könyvtár | Kube – RG | Adja meg annak a könyvtárnak a nevét, amely a kimeneti fájlt `apimodel.json` és egyéb létrehozott fájlokat is tartalmaz. |
    | ügyfél-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatásnév GUID azonosítóját. Az ügyfél-azonosító az alkalmazás AZONOSÍTÓJAként van azonosítva, amikor a Azure Stack hub rendszergazdája létrehozta a szolgáltatásnevet. |
    | ügyfél – titok | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatás titkos kulcsát. Ez a szolgáltatás létrehozásakor beállított ügyfél-titkos kulcs. |
    | előfizetés-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az előfizetés-AZONOSÍTÓját. További információ: [előfizetés az ajánlatra](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) |

    Például:

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

2.  Ha valamilyen oknál fogva a kimeneti könyvtár létrehozása után a végrehajtás meghiúsul, javítsa ki a hibát, és futtassa újra a parancsot. Ha újra futtatja az üzemelő példányt, és ugyanazt a kimeneti könyvtárat használta, az AK-motor hibát jelez, ami azt jelzi, hogy a könyvtár már létezik. A meglévő könyvtárat a Flag: `--force-overwrite`paranccsal írhatja felül.

3.  Mentse az KABAi motor fürtjének konfigurációját biztonságos, titkosított helyen.

    Keresse meg a `apimodel.json`fájlt. Mentse biztonságos helyre. Ezt a fájlt a rendszer az összes többi AK-motor-művelet bemenetként használja.

    A generált `apimodel.json` tartalmazza a bemeneti API-modellben használt egyszerű szolgáltatásnevet, titkos kulcsot és nyilvános SSH-kulcsot. Emellett az összes többi művelet elvégzéséhez szükséges minden egyéb metaadat is az AK-motor számára. Ha elveszíti, az AK-motor nem tudja konfigurálni a fürtöt.

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az AK-motor hibáinak megoldása Azure Stack hub-on](azure-stack-kubernetes-aks-engine-troubleshoot.md)
