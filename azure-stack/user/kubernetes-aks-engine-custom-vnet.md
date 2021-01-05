---
title: Kubernetes-fürt üzembe helyezése Azure Stack hub egyéni virtuális hálózatán
description: Megtudhatja, hogyan helyezhet üzembe egy Kubernetes-fürtöt egy egyéni virtuális hálózaton Azure Stack hub-on.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 9/2/2020
ms.openlocfilehash: 417d2cee37bc97f64de9b3d21f81ff60c075e74b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873690"
---
# <a name="deploy-a-kubernetes-cluster-to-a-custom-virtual-network-on-azure-stack-hub"></a>Kubernetes-fürt üzembe helyezése Azure Stack hub egyéni virtuális hálózatán 

Kubernetes-fürtöt az Azure Kubernetes Service (ak) motor használatával helyezhet üzembe egy egyéni virtuális hálózaton. Ez a cikk a virtuális hálózatban szükséges információk megtalálását vizsgálja. A fürt által használt IP-címek kiszámításához, a Vales API-modellben való beállításához, valamint az útválasztási tábla és a hálózati biztonsági csoport beállításához szükséges lépéseket itt találja.

A Azure Stack hub Kubernetes-fürtje az AK-motor használatával a kubenet hálózati beépülő modult használja. Az Azure-beli kubenet hálózati beépülő modul hálózatkezelésével kapcsolatban lásd: [kubenet hálózatkezelés használata saját IP-címtartományok az Azure Kubernetes Service-ben (ak)](/azure/aks/configure-kubenet).

## <a name="constraints-when-creating-a-custom-virtual-network"></a>Egyéni virtuális hálózat létrehozásakor megkötések

-  Az egyéni VNET ugyanabban az előfizetésben kell lennie, mint a Kubernetes-fürt összes többi összetevőjével.
-  A főcsomópontok készletének és az ügynök-csomópontok készletének ugyanabban a virtuális hálózatban kell lennie. A csomópontjait különböző alhálózatokra is telepítheti ugyanazon a virtuális hálózaton belül.
-  A Kubernetes-fürt alhálózatának IP-címtartományt kell használnia az egyéni virtuális hálózat IP-tartományának az IP-címtartomány [beolvasása](#get-the-ip-address-block)című témakörben.

## <a name="create-custom-virtual-network"></a>Egyéni virtuális hálózat létrehozása

Az Azure Stack hub-példányban egyéni virtuális hálózattal kell rendelkeznie. További információ: gyors útmutató [: virtuális hálózat létrehozása a Azure Portal használatával](/azure/virtual-network/quick-create-portal).

Hozzon létre egy új alhálózatot a virtuális hálózaton. Az alhálózati erőforrás-azonosító és az IP-címtartomány beolvasása szükséges. A fürt üzembe helyezésekor az API-modell erőforrás-AZONOSÍTÓját és tartományát fogja használni.

1. Nyissa meg az Azure Stack hub felhasználói portált a Azure Stack hub-példányban.
2. Válassza a **Minden erőforrás** elemet.
3. Adja meg a virtuális hálózat nevét a keresőmezőbe.
4. Alhálózat hozzáadásához válassza az **alhálózatok**  >  és **alhálózatok** lehetőséget.
5. Adjon hozzá egy **nevet** és egy **címtartományt** a CIDR-jelölés használatával. Válassza az **OK** lehetőséget.
4. Válassza a **Tulajdonságok** lehetőséget a **virtuális hálózatok** panelen. Másolja ki az **erőforrás-azonosítót**, majd adja hozzá a t `/subnets/<nameofyoursubnect>` . Ezt az értéket fogja használni a `vnetSubnetId` fürthöz tartozó API-modell kulcsának értékeként. Az alhálózat erőforrás-azonosítója a következő formátumot használja:<br>`/subscriptions/SUB_ID/resourceGroups/RG_NAME/providers/Microsoft.Network/virtualNetworks/VNET_NAME/subnets/SUBNET_NAME`

    ![virtuális hálózati erőforrás azonosítója](media/kubernetes-aks-engine-custom-vnet/virtual-network-id.png)

5. Válassza ki az **alhálózatok** elemet a **virtuális hálózatok** panelen. Válassza ki az alhálózat nevét, például: default.
    
    ![virtuális hálózati CIDR blokk](media/kubernetes-aks-engine-custom-vnet/virtual-network-cidr-block.png)
    
6. Az alhálózat panelen jegyezze fel a címtartomány és a virtuális hálózat CIDR blokkját, például: `10.1.0.0 - 10.1.0.255 (256 addresses)` és `10.1.0.0/24` .

## <a name="considerations-for-selecting-an-address-space"></a>Címterület kiválasztásának szempontjai

Egyéni virtuális hálózat létrehozásakor meg kell adnia a hálózat IP-címét és egy IP-címtartományt minden alhálózathoz. Vegye figyelembe a következő tényezőket, amikor kiválasztja a Kubernetes-fürtben használandó címeket és tartományokat:
-  Az átfedésben lévő címtartomány az IP-címek összeütközését vagy kommunikációs hibákat eredményezhet. Az átfedésben lévő IP-címek kockázatának csökkentése érdekében válasszon ki egy egyedi címtartományt az új virtuális hálózat számára.
-  A és a tartományban lévő címtartomány `10.` `172.` gyakran a magánhálózatok esetében használatos, és a meglévő Datacenter-infrastruktúra is használhatja őket. Ha a Kubernetes-alkalmazásai erőforrásokat használnak az adatközpontban, csökkentse az összeütközések kockázatát úgy, hogy az Ön adatközpontjának címterület-beli egyéni virtuális hálózata számára kiválaszt egy címtartományt.
-  Javasoljuk, hogy használjon dedikált alhálózatot a Kubernetes-fürthöz.

## <a name="get-the-ip-address-block"></a>Az IP-cím blokkjának beolvasása

Az AK-motor támogatja a üzembe helyezést egy meglévő virtuális hálózaton. Meglévő alhálózatra történő üzembe helyezéskor a fürt egymást követő IP-címeket fog használni az ügynökökhöz, a másikat pedig a főkiszolgálók számára.

Két értéket kell megadnia. Ismernie kell a fürt számára fenntartott IP-címek számát, valamint az alhálózat IP-címén belüli első egymást követő statikus IP-címet.

Az AK-motor több főcsomópont használata esetén legfeljebb 16 nem használt IP-címet igényel. A fürt minden egyes főkiszolgáló esetében egy IP-címet fog használni, legfeljebb öt főkiszolgálóval. A következő 10 IP-címet is meg kell adni az AK-os motor számára a teljes belmagasság IP-címének lefoglalása után. Végezetül a terheléselosztó a főkiszolgálók és a belmagasság lefoglalása után egy további IP-címet is használ, összesen 16.

Az IP-címek blokkjának elhelyezésekor az alhálózatnak a meglévő IP-címek következő foglalásait kell megadnia:
 - Az első négy IP-cím és az utolsó IP-cím le van foglalva, és nem használható egyetlen Azure-alhálózatban sem
 - A 16 IP-cím pufferének nyitva kell maradnia.
 - A fürt első IP-címének a címtartomány vége felé kell esnie az IP-ütközések elkerülése érdekében. Ha lehetséges, rendelje hozzá a `firstConsecutiveStaticIP` tulajdonságot egy IP-címhez az alhálózat elérhető IP-címtartomány *vége* közelében.

A következő példában láthatja, hogy a különböző szempontok hogyan töltik ki az alhálózat IP-tartományát. Ez három főkiszolgálón érhető el. Ha 256-címmel rendelkező alhálózatot használ, például 10.1.0.0/24, akkor az első egymást követő statikus IP-címet kell megadnia a 207-nél. A következő táblázat a címeket és szempontokat tartalmazza:

| Tartomány/24 alhálózat | Szám | Megjegyzés |
| --- | --- | --- |
| 10.1.0.0 – 10.1.03 | 4 | Az Azure-alhálózaton van fenntartva. |
| **10.1.0.224**– 10.1.0.238 | 14 | Egy AK-motor által meghatározott fürt IP-címeinek száma.<br><br> 3 főkiszolgálói IP-cím<br>10 IP-cím a belmagassághoz<br>1 a terheléselosztó IP-címe |
| 10.1.0.239 - 10.1.0.255 | 16 | 16 IP-cím puffere. |
| 10.1.0.256 | 1 | Az Azure-alhálózaton van fenntartva. |

Ebben a példában a tulajdonság a következő `firstConsecutiveStaticIP` lesz: `10.1.0.224` .

Nagyobb alhálózatok esetén, például a 60000-nál több címmel rendelkező/16 esetében előfordulhat, hogy nem találja, hogy a statikus IP-hozzárendeléseket a hálózati terület végére állítsa be. Állítsa be a fürt statikus IP-címét az IP-terület első 24 címéről, hogy a fürt rugalmas legyen a címek igénylése során.

## <a name="update-the-api-model"></a>Az API-modell frissítése

Frissítse a fürt üzembe helyezéséhez használt API-modellt az AK-motorról az egyéni virtuális hálózatra.

A **masterProfile** állítsa be a következő értékeket:

| Mező | Példa | Leírás |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Határozza meg az alhálózat erőforrás-AZONOSÍTÓját.  |
| firstConsecutiveStaticIP | 10.1.0.224 | Rendeljen hozzá `firstConsecutiveStaticIP` egy olyan IP-címet a konfigurációs tulajdonsághoz, amely a kívánt alhálózaton elérhető IP-címtartomány *vége* közelében van. `firstConsecutiveStaticIP` csak a fő készletre vonatkozik. |

A **agentPoolProfiles** állítsa be a következő értékeket:

| Mező | Példa | Leírás |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | A Azure Resource Manager útvonal AZONOSÍTÓjának megadása az alhálózathoz.  |

A **orchestratorProfile**-ben keresse meg a **kubernetesConfig** , és állítsa be a következő értéket:

| Mező | Példa | Leírás |
| --- | --- | --- |
| clusterSubnet | `172.16.244.0/24` | A fürt alhálózatának (POD Network) IP-tartományának IP-címtartományt kell használnia a definiált egyéni IP-VNET. |

Például:

```json
"masterProfile": {
  ...
  "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
  "firstConsecutiveStaticIP": "10.1.0.224",
  ...
},
...
"agentPoolProfiles": [
  {
    ...
    "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
    ...
  },
    ...
"kubernetesConfig": [
  {
    ...
    "clusterSubnet": "172.16.244.0/24",
    ...
  },

```

## <a name="deploy-your-cluster"></a>A fürt üzembe helyezése

Miután hozzáadta az értékeket az API-modellhez, üzembe helyezheti a fürtöt az ügyfélszámítógépről a `deploy` paranccsal az AK-motor használatával. Útmutatásért lásd: [Kubernetes-fürt üzembe helyezése](azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster).

## <a name="set-the-route-table"></a>Az útválasztási táblázat beállítása

A fürt üzembe helyezése után térjen vissza a virtuális hálózathoz a Azure Stack felhasználói portálon. Állítsa be az útválasztási táblázatot és a hálózati biztonsági csoportot (NSG) az alhálózat paneljén. Ha nem az Azure CNI használja, például `networkPlugin` : az `kubenet` `kubernetesConfig` API-modell konfigurációs objektumában. Miután sikeresen telepített egy fürtöt az egyéni virtuális hálózatra, szerezze be az útválasztási tábla erőforrásának AZONOSÍTÓját a fürt erőforráscsoport **hálózat** paneljéről.

1. Nyissa meg az Azure Stack hub felhasználói portált a Azure Stack hub-példányban.
2. Válassza a **Minden erőforrás** elemet.
3. Adja meg a virtuális hálózat nevét a keresőmezőbe.
4. Válassza ki az **alhálózatok** elemet, majd válassza ki a fürtöt tartalmazó alhálózat nevét.
    
    ![útválasztási táblázat és hálózati biztonsági csoport](media/kubernetes-aks-engine-custom-vnet/virtual-network-route-table.png)
    
5. Válassza az **útválasztási táblázat** lehetőséget, majd válassza ki a fürt útválasztási tábláját.

> [!NOTE]  
> A Kubernetes Windows-fürthöz tartozó egyéni virtuális hálózat [ismert hibával](https://github.com/Azure/aks-engine/issues/371)rendelkezik.

## <a name="next-steps"></a>További lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)  
- További információ a [tárolók Azure monitoráról – áttekintés](/azure/azure-monitor/insights/container-insights-overview)
