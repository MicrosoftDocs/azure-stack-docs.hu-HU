---
title: Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stackon | Microsoft Docs
description: Kubernetes-fürt üzembe helyezése Azure Stack rendszerű ügyfélről az AK-motort futtató virtuális gépről.
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
ms.openlocfilehash: 8891ac7a1fb4b5b5af110e800f49c0d8fa873f66
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019325"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack"></a>Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Kubernetes-fürtöt Azure Stack is telepítheti egy olyan ügyfél virtuális gépről, amely az AK-motort futtatja. Ebben a cikkben a fürt specifikációjának írását, a fürt és a `apimodel.json` fájllal történő üzembe helyezését, valamint a fürt ellenőrzésével foglalkozunk a MySQL és a Helm használatával.

## <a name="define-a-cluster-specification"></a>Fürt specifikációjának megadása

A fürt specifikációját megadhatja egy dokumentum-fájlban az [API-modell](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram)nevű JSON-formátum használatával. Az KABAi motor a fürt létrehozásához az API-modellben egy fürt-specifikációt használ. 

### <a name="update-the-api-model"></a>Az API-modell frissítése

Ez a szakasz a fürthöz tartozó API-modell létrehozását vizsgálja.

1.  Kezdje egy Azure Stack [példa](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) API-modellel, és készítsen helyi másolatot a telepítéshez. A gépről telepítette az AK motort:

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
    > Ha nincs telepítve a nano, akkor telepítheti a nanot Ubuntu- `sudo apt-get install nano`re:.

3.  A kubernetes-azurestack. JSON fájlban keresse meg `orchestratorRelease`a fájlt. Válasszon egy támogatott Kubernetes-verziót. Például: 1,11, 1,12, 1,13, 1,14. A verziók gyakran frissülnek. A verziót x. xx helyett x. xx. x értékkel kell megadni. Az aktuális verziók listáját lásd: [támogatott Kubernetes-verziók](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). A támogatott verziót a következő AK Engine-parancs futtatásával tekintheti meg:

    ```bash
    aks-engine get-versions
    ```

4.  Keresse `portalURL` meg és adja meg a bérlői portál URL-címét. Például: `https://portal.local.azurestack.external`.

5.  A tömbben `masterProfile`állítsa be a következő mezőket:

    | Mező | Leírás |
    | --- | --- |
    | dnsPrefix | Adjon meg egy egyedi karakterláncot, amely a virtuális gépek állomásneve azonosítására szolgál majd. Például egy név az erőforráscsoport neve alapján. |
    | count |  Adja meg a központi telepítéshez használni kívánt főkiszolgálók számát. HA egy HA üzemelő példány esetében a minimum a 3, az 1 érték nem lehet üzemelő példányokhoz. |
    | vmSize |  Adja meg [a Azure stack által támogatott méretet](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)(példa `Standard_D2_v2`). |
    | disztribúció | Írja be a `aks-ubuntu-16.04` (igen) kifejezést. |

6.  A tömb `agentPoolProfiles` frissítése:

    | Mező | Leírás |
    | --- | --- |
    | count | Adja meg az üzemelő példányhoz használni kívánt ügynökök számát. |
    | vmSize | Adja meg [a Azure stack által támogatott méretet](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)(példa `Standard_D2_v2`). |
    | disztribúció | Írja be a `aks-ubuntu-16.04` (igen) kifejezést. |

7.  A tömb `linuxProfile` frissítése:

    | Mező | Leírás |
    | --- | --- |
    | adminUsername | Adja meg a virtuális gép rendszergazdájának felhasználónevét. |
    | ssh | Adja meg azt a nyilvános kulcsot, amelyet a virtuális gépekkel való SSH-hitelesítéshez kíván használni. |

### <a name="more-information-about-the-api-model"></a>További információ az API-modellről

- Az API-modell összes elérhető beállításának teljes hivatkozását a [fürt definíciói](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)című témakörben találja.  
- A Azure Stack adott lehetőségeinek kiemeléséhez tekintse meg a [Azure stack-fürt definíciójának sajátosságait](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Kubernetes-fürt üzembe helyezése

Miután összegyűjtötte az összes szükséges értéket az API-modellben, létrehozhat egy fürtöt. Ezen a ponton a következőket kell tennie:

Kérdezze meg Azure Stack kezelőjét a következőre:

- Ellenőrizze a rendszer állapotát, és javasolja a futtatását `Test-AzureStack` és az OEM-gyártó hardveres figyelési eszközét.
- Ellenőrizze a rendszerkapacitást, beleértve az erőforrásokat, például a memóriát, a tárterületet és a nyilvános IP-címeket.
- Adja meg az előfizetéshez társított kvóta részleteit, így ellenőrizheti, hogy van-e elegendő hely a használni kívánt virtuális gépek számához.

Fürt üzembe helyezésének folytatása:

1.  Tekintse át az AK-motorhoz elérhető paramétereket Azure Stack [CLI-jelzők](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags)esetében.

    | Paraméter | Példa | Leírás |
    | --- | --- | --- |
    | Azure – env | AzureStackCloud | Annak jelzése, hogy a célként megadott platform Azure Stack használni `AzureStackCloud`kívánt AK-motor. |
    | location | helyi | A Azure Stack régiójának neve. Azon ASDK esetében, amelyekhez a régió `local`van beállítva. |
    | resource-group | Kube – RG | Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket. |
    | API – modell | ./kubernetes-azurestack.json | A fürt konfigurációs fájljának vagy API-modellének elérési útja. |
    | kimenet – könyvtár | Kube – RG | Adja meg annak a könyvtárnak a nevét, amely a `apimodel.json` kimeneti fájlt és más létrehozott fájlokat is tartalmaz. |
    | ügyfél-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatásnév GUID azonosítóját. Az ügyfél-azonosító az alkalmazás-AZONOSÍTÓként van azonosítva, amikor a Azure Stack rendszergazdája létrehozta a szolgáltatásnevet. |
    | ügyfél – titok | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az egyszerű szolgáltatás titkos kulcsát. Ez a szolgáltatás létrehozásakor beállított ügyfél-titkos kulcs. |
    | előfizetés-azonosító | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Adja meg az előfizetés-AZONOSÍTÓját. További információ: [előfizetés az ajánlatra](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) |

    Például:

    ```bash  
    aks-engine deploy \\
    --azure-env AzureStackCloud
    --location <for asdk is local>\\
    --resource-group kube-rg \\
    --api-model ./kubernetes-azurestack.json \\
    --output-directory kube-rg \\
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \\
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \\
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

2.  Ha valamilyen oknál fogva a kimeneti könyvtár létrehozása után a végrehajtás meghiúsul, javítsa ki a hibát, és futtassa újra a parancsot. Ha újra futtatja az üzemelő példányt, és ugyanazt a kimeneti könyvtárat használta, az AK-motor hibát jelez, ami azt jelzi, hogy a könyvtár már létezik. A meglévő könyvtárat a Flag: `--force-overwrite`paranccsal írhatja felül.

3.  Mentse az KABAi motor fürtjének konfigurációját biztonságos, titkosított helyen.

    Keresse meg a `apimodel.json`fájlt. Mentse biztonságos helyre. Ezt a fájlt a rendszer az összes többi AK-motor-művelet bemenetként használja.

    A generált `apimodel.json` szolgáltatás tartalmazza a bemeneti API-modellben használt egyszerű szolgáltatásnevet, titkos kulcsot és nyilvános SSH-kulcsot. Emellett az összes többi művelet elvégzéséhez szükséges minden egyéb metaadat is az AK-motor számára. Ha elveszíti, az AK-motor nem tudja konfigurálni a fürtöt.

    A titkos kulcsok nem **titkosítottak**. Tárolja a fájlt titkosított, biztonságos helyen. 

## <a name="verify-your-cluster"></a>A fürt ellenőrzése

Győződjön meg arról, hogy a fürt ellenőrzéséhez üzembe helyezi a MySQL-t a Helm használatával.

1. Szerezze be az egyik főcsomópont nyilvános IP-címét a Azure Stack-portál használatával.

2. Egy olyan számítógépről, amely hozzáféréssel rendelkezik a Azure Stack-példányhoz, csatlakozzon SSH-n keresztül az új fő csomóponthoz egy ügyfél, például a PuTTY vagy a MobaXterm használatával. 

3. Az SSH-felhasználónévhez használja az "azureuser" nevet, valamint a fürt üzembe helyezéséhez megadott kulcspár titkos kulcsát.

4.  Futtassa az alábbi parancsot:

    ```bash
    sudo snap install helm –classic
    helm repo update
    helm install stable/mysql
    ```

5. Ha a futtatási `install stable/mysql` kísérlet után hibaüzenetet kap, `Error: incompatible versions client[v2.XX.X] server[v2.YY.Y]`például:. Futtassa az alábbi parancsot:

    ```bash 
    helm init --force-upgrade
    and retry:
    helm install stable/mysql
    ```

6.  A teszt tisztításához keresse meg a MySQL-telepítéshez használt nevet. Az alábbi példában a név `wintering-rodent`a következő:. Ezután törölje. 

    Futtassa az alábbi parancsot:

    ```bash
    helm ls
    NAME REVISION UPDATED STATUS CHART APP VERSION NAMESPACE
    wintering-rodent 1 Thu Oct 18 15:06:58 2018 DEPLOYED mysql-0.10.1 5.7.14 default
    helm delete wintering-rodent
    ```

    Ekkor megjelenik a CLI:
    ```bash
    release "wintering-rodent" deleted
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az AK-motor hibáinak megoldása Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)