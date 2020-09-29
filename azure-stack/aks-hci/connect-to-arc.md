---
title: Azure Kubernetes-szolgáltatás összekapcsolása Azure Stack HCI-fürtön az Azure arc for Kubernetes
description: Azure Kubernetes-szolgáltatás összekapcsolása Azure Stack HCI-fürtön az Azure arc for Kubernetes
author: abha
ms.topic: how-to
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 24dc2efdc591404db1bbfc30cf9c1bc83e2ed356
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948890"
---
# <a name="connect-an-azure-kubernetes-service-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes"></a>Azure Kubernetes-szolgáltatás összekapcsolása Azure Stack HCI-fürtön az Azure arc for Kubernetes

Ha Azure Stack HCI-fürtön lévő Azure Kubernetes szolgáltatás csatlakozik az Azure arc-hoz, akkor az a Azure Portal jelenik meg. Azure Resource Manager azonosítója és felügyelt identitása lesz. A fürtök szabványos Azure-előfizetésekhez vannak csatolva, és egy erőforráscsoport alatt találhatók, és ugyanúgy fogadhatnak címkéket, mint bármely más Azure-erőforrást.

Kubernetes-fürt Azure-hoz való összekapcsolásához a fürt rendszergazdájának ügynököket kell telepítenie. Ezek az ügynökök egy nevű Kubernetes-névtérben futnak `azure-arc` , és szabványos Kubernetes üzemelő példányok. Az ügynökök felelősek az Azure-hoz való csatlakozásért, az Azure arc-naplók és-metrikák összegyűjtéséhez, valamint a konfigurációs kérések figyeléséhez.

Az Azure arc-kompatibilis Kubernetes támogatja az iparági szabványnak megfelelő SSL-t az adatátvitel biztonságossá tételéhez. Az adatok titkosságának biztosítása érdekében a rendszer a Azure Cosmos DB adatbázisában tárolja az adatok titkosítását is.

Az alábbi lépések útmutatást nyújtanak az Azure Kubernetes szolgáltatás bevezetéséhez Azure Stack HCI-fürtökön az Azure arc-ba. **Ezeket a lépéseket kihagyhatja, ha már előkészítette a Kubernetes-fürtöt az Azure arc-ba a Windows felügyeleti központban.**

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy készen áll-e az alábbi követelményekre:

* Egy Azure Kubernetes szolgáltatás Azure Stack HCI-fürtön, és legalább egy Linux-feldolgozói csomópont működik. 

* Szüksége lesz egy kubeconfig-fájlra a fürt és a fürt rendszergazdai szerepkörének eléréséhez a fürtön az arc-kompatibilis Kubernetes-ügynökök telepítéséhez.
* Telepítse az Azure Kubernetes szolgáltatást Azure Stack HCI PowerShell-modulon.
* Az Azure CLI-hez készült Kubernetes CLI-bővítmények telepítéséhez az Azure CLI 2.3-s verziója szükséges. [Telepítse az Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)-t. A legújabb verzióra is frissítheti, hogy az Azure CLI 2.3 + verziója legyen.
* Egy Azure-előfizetés, amelynek Ön a tulajdonosa vagy közreműködője. 
* Futtassa a jelen dokumentumban található parancsokat egy PowerShell felügyeleti ablakban.


## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure arc-ügynökök a következő protokollok/portok/kimenő URL-címek működéséhez szükségesek.

* TCP a 443-es porton – > `https://:443`
* TCP a 9418-es porton – > `git://:9418`

| Végpont (DNS)                                                                                               | Leírás                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Ahhoz szükséges, hogy az ügynök csatlakozhasson az Azure-hoz, és regisztrálja a fürtöt                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Adatsík végpontja az ügynök számára az állapot leküldéséhez és a konfigurációs adatok beolvasásához                                      |
| `https://docker.io`                                                                                            | A tároló lemezképének lekéréséhez szükséges                                                                                         |
| `https://github.com`, git://github.com                                                                         | Például a GitOps repók a GitHubon futnak. A konfigurációs ügynöknek a megadott git-végponthoz való kapcsolódásra van szüksége. |
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager tokenek beolvasásához és frissítéséhez szükséges                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | A tároló lemezképének lekéréséhez szükséges az Azure arc-ügynökökhöz                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  A rendszer által hozzárendelt felügyelt azonosító tanúsítványok lekéréséhez szükséges                                                                  |

## <a name="step-1-log-in-to-azure"></a>1. lépés: bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és a bejelentkezés után állítson be egy Azure-előfizetést, amelynek Ön a tulajdonosa vagy közreműködője az alapértelmezett előfizetésként.

```console
az login
az account set --subscription "00000000-aaaa-bbbb-cccc-000000000000"
```

## <a name="step-2-register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>2. lépés: a két szolgáltató regisztrálása az Azure arc-kompatibilis Kubernetes:

Ezt a lépést kihagyhatja, ha már regisztrálta a két szolgáltatót az Azure arc-kompatibilis Kubernetes szolgáltatáshoz az előfizetésében. A regisztráció aszinkron folyamat, és előfizetés esetén egyszer kell lennie. A regisztráció körülbelül 10 percet is igénybe vehet. 

```console
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.KubernetesConfiguration
```

A következő parancsokkal ellenőrizze, hogy regisztrálva van-e:

```console
az provider show -n Microsoft.Kubernetes -o table
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="step-3-create-a-resource-group"></a>3. lépés: erőforráscsoport létrehozása

A csatlakoztatott fürterőforrás tárolásához erőforráscsoport szükséges. Használhat egy meglévő erőforráscsoportot az USA keleti régiójában vagy Nyugat-Európában. Ha nem rendelkezik meglévő erőforráscsoporthoz az USA keleti vagy nyugati régiójában, akkor az alábbi paranccsal hozzon létre egy új erőforráscsoportot:

```console
az group create --name AzureArcTest -l EastUS -o table
```

## <a name="step-4-create-a-new-service-principal"></a>4. lépés: új egyszerű szolgáltatás létrehozása


Ezt a lépést kihagyhatja, ha már létrehozott egy `contributor` szerepkörrel rendelkező szolgáltatásnevet, és ismeri a szolgáltatásnév appID, jelszavát és bérlői értékeit.

Hozzon létre egy új szolgáltatásnevet egy tájékoztató névvel. Ennek a névnek egyedinek kell lennie a Azure Active Directory bérlő számára. Az egyszerű szolgáltatásnév alapértelmezett szerepköre: `Contributor` . Ez a szerepkör teljes körű engedélyekkel rendelkezik az Azure-fiókba való olvasásra, illetve írásra. Ezt a szolgáltatásnevet is újra felhasználhatja a fedélzeti több fürtön az Azure-ív használatával. Állítsa be az egyszerű szolgáltatásnév hatókörét az *előfizetések/erőforrás-csoport*számára. *Győződjön meg arról, hogy menti a szolgáltatásnév appID, jelszavát és bérlői értékeit, mivel ezekre az adatokra a következő lépésekben szüksége lesz.*

```console
az ad sp create-for-RBAC --name "azure-arc-for-k8s" --scope /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}
```

**Kimeneti**

```
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-arc-for-k8s",
  "name": "https://azure-arc-for-k8s",
  "password": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
  "tenant": "ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj"
}
```
## <a name="step-5-save-service-principal-details"></a>5. lépés: az egyszerű szolgáltatásnév részleteinek mentése
Mentse a létrehozott szolgáltatásnevet a appId, a jelszót és a bérlői értékeket, valamint a fürt nevét, az Azure-előfizetés AZONOSÍTÓját, az erőforráscsoport nevét és helyét a PowerShell-változók között. Ezzel biztosíthatja, hogy a részleteket más oktatóanyagokban is újra felhasználhassa. Ügyeljen arra, hogy ezeket az értékeket egy Jegyzettömbben is mentse, ha be szeretné állítani a powerShell-munkamenetet.

```PowerShell
$clusterName = #<name of your Kubernetes cluster>
$resourceGroup = #<Azure resource group to store your connected Kubernetes cluster in Azure Arc>
$location = #<Azure resource group location. This can only be eastus or westeurope for Azure Arc for Kubernetes>
$subscriptionId = #<Azure subscription Id>
$appId = #<appID from the service principal created above>
$password = #<password from the service principal created above>
$tenant = #<tenant from the service principal created above>
```
Győződjön meg arról, hogy a megfelelő értékeket rendeli hozzá a változókhoz a következő futtatásával:

```PowerShell
echo $clusterName 
echo $resourceGroup
echo $location 
echo $subscriptionId 
echo $appId 
echo $password 
echo $tenant 
```

## <a name="step-6-connect-to-azure-arc-using-service-principal-and-the-aks-hci-powershell-module"></a>6. lépés: az Azure arc-hoz való kapcsolódás az egyszerű szolgáltatásnév és az AK-HCI PowerShell-modul használatával

Ezután a Kubernetes-fürtöt az Azure-ba a szolgáltatásnév és az AK-HCI PowerShell-modul használatával fogjuk használni. Ez a lépés üzembe helyezi az Azure arc-ügynököket a Kubernetes a `azure-arc` névtérben.

Hivatkozzon az újonnan létrehozott egyszerű szolgáltatásra, és futtassa a `Install-AksHciArcOnboarding` parancsot az AK-HCI PowerShell-modulban.

```PowerShell
Install-AksHciArcOnboarding -clusterName $clusterName -resourcegroup $resourceGroup -location $location -subscriptionid $subscriptionId -clientid $appId -clientsecret $password -tenantid $tenant
```
## <a name="verify-connected-cluster"></a>Csatlakoztatott fürt ellenőrzése

A Kubernetes-fürterőforrás a [Azure Portalon](https://portal.azure.com/)tekinthető meg. Miután megnyitotta a portált a böngészőben, navigáljon az erőforráscsoport és az Azure arc-kompatibilis Kubernetes erőforrásra a PowerShell-parancsban korábban használt erőforrás neve és erőforráscsoport-név bemenete alapján `Install-AksHciArcOnboarding` .

> [!NOTE]
> A fürt bevezetését követően 5 – 10 percet vesz igénybe, hogy a fürt metaadatai (a fürt verziója, az ügynök verziója, a csomópontok száma) az Azure arc-kompatibilis Kubernetes-erőforrás áttekintés lapján legyenek felszínre Azure Portal.

A fürt törléséhez vagy a fürt csatlakozásához, ha az egy kimenő proxykiszolgáló mögött található, látogasson el [Az Azure arc-kompatibilis Kubernetes-fürthöz](/azure/azure-arc/kubernetes/connect-cluster)való csatlakozásra.

## <a name="azure-arc-agents-for-kubernetes"></a>Azure arc-ügynökök a Kubernetes

Az Azure arc-kompatibilis Kubernetes üzembe helyez néhány operátort a `azure-arc` névtérben. Ezeket a központi telepítéseket és hüvelyeket itt tekintheti meg:

```console
kubectl -n azure-arc get deployments,pods
```

Az Azure arc-kompatibilis Kubernetes néhány ügynököt (operátort) tartalmaz, amelyek a névtérben üzembe helyezett fürtön futnak `azure-arc` .

* `deployment.apps/config-agent`: a fürtön alkalmazott forrás-vezérlési konfigurációs erőforrások, valamint a frissítések megfelelőségi állapotának figyeli a csatlakoztatott fürtöt
* `deployment.apps/controller-manager`: az operátorok és az Azure arc-összetevők közötti interakciók
* `deployment.apps/metrics-agent`: más ív-ügynökök metrikáinak gyűjtésével biztosíthatja, hogy ezek az ügynökök optimális teljesítményt mutassanak
* `deployment.apps/cluster-metadata-operator`: a fürt metaadatait – a fürt verzióját, a csomópontok darabszámát és az Azure arc-ügynök verzióját gyűjti.
* `deployment.apps/resource-sync-agent`: szinkronizálja a fent említett fürt metaadatait az Azure-ba
* `deployment.apps/clusteridentityoperator`: Az Azure arc-kompatibilis Kubernetes jelenleg támogatja a rendszerhez rendelt identitást. a clusteridentityoperator fenntartja a más ügynökök által az Azure-nal folytatott kommunikációhoz használt felügyelt szolgáltatás-identitás (MSI) tanúsítványát.
* `deployment.apps/flux-logs-agent`: naplók gyűjtése a verziókövetés konfigurációjának részeként üzembe helyezett Flux-kezelők számára

## <a name="next-steps"></a>Következő lépések

* [GitOps használata csatlakoztatott fürtben](/azure/azure-arc/kubernetes/use-gitops-connected-cluster)
* [A fürt konfigurációjának szabályozása Azure Policy használatával](/azure/azure-arc/kubernetes/use-azure-policy)
* [Azure Monitor engedélyezése Azure arc-kompatibilis Kubernetes-fürtön](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)
