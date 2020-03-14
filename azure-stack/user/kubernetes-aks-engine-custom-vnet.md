---
title: Kubernetes-fürt üzembe helyezése Azure Stack hub egyéni virtuális hálózatán
description: Megtudhatja, hogyan helyezhet üzembe egy Kubernetes-fürtöt egy egyéni virtuális hálózaton Azure Stack hub-on.
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: 91b70c490f2771510a5bde202484837dd170166f
ms.sourcegitcommit: 4301e8dee16b4db32b392f5979dfec01ab6566c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79318058"
---
# <a name="deploy-a-kubernetes-cluster-to-a-custom-virtual-network-on-azure-stack-hub"></a>Kubernetes-fürt üzembe helyezése Azure Stack hub egyéni virtuális hálózatán 

Kubernetes-fürtöt az Azure Kubernetes Service (ak) motor használatával helyezhet üzembe egy egyéni virtuális hálózaton. Ez a cikk a virtuális hálózatban szükséges információk megtalálását vizsgálja. A fürt által használt IP-címek kiszámításához, a Vales API-modellben való beállításához, valamint az útválasztási tábla és a hálózati biztonsági csoport beállításához szükséges lépéseket itt találja.

A Azure Stack hub Kubernetes-fürtje az AK-motor használatával a kubenet hálózati beépülő modult használja. Az Azure-beli kubenet hálózati beépülő modul hálózatkezelésével kapcsolatban lásd: [kubenet hálózatkezelés használata saját IP-címtartományok az Azure Kubernetes Service-ben (ak)](https://docs.microsoft.com/azure/aks/configure-kubenet).

## <a name="create-custom-virtual-network"></a>Egyéni virtuális hálózat létrehozása

Az Azure Stack hub-példányban egyéni virtuális hálózattal kell rendelkeznie. További információ: gyors útmutató [: virtuális hálózat létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

Hozzon létre egy új alhálózatot a virtuális hálózaton. Az alhálózati erőforrás-azonosító és az IP-címtartomány beolvasása szükséges. A fürt üzembe helyezésekor az API-modell erőforrás-AZONOSÍTÓját és tartományát fogja használni.

1. Nyissa meg az Azure Stack hub felhasználói portált a Azure Stack hub-példányban.
2. Válassza az **Összes erőforrás** elemet.
3. Adja meg a virtuális hálózat nevét a keresőmezőbe.
4. Alhálózat **hozzáadásához válassza az alhálózatok** >  **+ alhálózatok** lehetőséget.
5. Adjon hozzá egy **nevet** és egy **címtartományt** a CIDR-jelölés használatával. Kattintson az **OK** gombra.
4. Válassza a **Tulajdonságok** lehetőséget a **virtuális hálózatok** panelen. Másolja ki az **erőforrás-azonosítót**, majd adja hozzá a `/subnets/<nameofyoursubnect>`. Ezt az értéket fogja használni a fürthöz tartozó API-modell `vnetSubnetId` kulcsának értékeként. Az alhálózat erőforrás-azonosítója a következő formátumot használja:<br>`/subscriptions/SUB_ID/resourceGroups/RG_NAME/providers/Microsoft.Network/virtualNetworks/VNET_NAME/subnets/SUBNET_NAME`

    ![virtuális hálózati erőforrás azonosítója](media/kubernetes-aks-engine-custom-vnet/virtual-network-id.png)

5. Válassza ki az **alhálózatok** elemet a **virtuális hálózatok** panelen. Válassza ki az alhálózat nevét, például: default.
    
    ![virtuális hálózati CIDR blokk](media/kubernetes-aks-engine-custom-vnet/virtual-network-cidr-block.png)
    
6. Az alhálózat panelen jegyezze fel a címtartományt és a virtuális hálózati CIDR blokkot, például: `10.1.0.0 - 10.1.0.255 (256 addresses)` és `10.1.0.0/24`.



## <a name="get-the-ip-address-block"></a>Az IP-cím blokkjának beolvasása

Az AK-motor támogatja a üzembe helyezést egy meglévő virtuális hálózaton. Meglévő alhálózatra történő üzembe helyezéskor a fürt egymást követő IP-címeket fog használni az ügynökökhöz, a másikat pedig a főkiszolgálók számára.

Két értéket kell megadnia. Ismernie kell a fürt számára fenntartott IP-címek számát, valamint az alhálózat IP-címén belüli első egymást követő statikus IP-címet.

Az AK-motor több főcsomópont használata esetén legfeljebb 16 nem használt IP-címet igényel. A fürt minden egyes főkiszolgáló esetében egy IP-címet fog használni, legfeljebb öt főkiszolgálóval. A következő 10 IP-címet is meg kell adni az AK-os motor számára a teljes belmagasság IP-címének lefoglalása után. Végezetül a terheléselosztó a főkiszolgálók és a belmagasság lefoglalása után egy további IP-címet is használ, összesen 16.

Az IP-címek blokkjának elhelyezésekor az alhálózatnak a meglévő IP-címek következő foglalásait kell megadnia:
 - Az első négy IP-cím és az utolsó IP-cím le van foglalva, és nem használható egyetlen Azure-alhálózatban sem
 - A 16 IP-cím pufferének nyitva kell maradnia.
 - A fürt első IP-címének a címtartomány vége felé kell esnie az IP-ütközések elkerülése érdekében. Ha lehetséges, rendelje hozzá a `firstConsecutiveStaticIP` tulajdonságot egy IP-címhez az alhálózaton elérhető IP-címtartomány *vége* közelében.

A következő példában láthatja, hogy a különböző szempontok hogyan töltik ki az alhálózat IP-tartományát. Ez három főkiszolgálón érhető el. Ha 256-címmel rendelkező alhálózatot használ, például 10.1.0.0/24, akkor az első egymást követő statikus IP-címet kell megadnia a 207-nél. A következő táblázat a címeket és szempontokat tartalmazza:

| Tartomány/24 alhálózat | Szám | Megjegyzés |
| --- | --- | --- |
| 10.1.0.0 – 10.1.03 | 4 | Az Azure-alhálózaton van fenntartva. |
| **10.1.0.224**– 10.1.0.238 | 14 | Egy AK-motor által meghatározott fürt IP-címeinek száma.<br><br> 3 főkiszolgálói IP-cím<br>10 IP-cím a belmagassághoz<br>1 a terheléselosztó IP-címe |
| 10.1.0.239 - 10.1.0.255 | 16 | 16 IP-cím puffere. |
| 10.1.0.256 | 1 | Az Azure-alhálózaton van fenntartva. |

Ebben a példában a `firstConsecutiveStaticIP` tulajdonságot `10.1.0.224`.

Nagyobb alhálózatok esetén, például a 60000-nál több címmel rendelkező/16 esetében előfordulhat, hogy nem találja, hogy a statikus IP-hozzárendeléseket a hálózati terület végére állítsa be. Állítsa be a fürt statikus IP-címét az IP-terület első 24 címéről, hogy a fürt rugalmas legyen a címek igénylése során.


## <a name="update-the-api-model"></a>Az API-modell frissítése

Frissítse a fürt üzembe helyezéséhez használt API-modellt az AK-motorról az egyéni virtuális hálózatra.

A **masterProfile** állítsa be a következő értékeket:

| Mező | Példa | Leírás |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Határozza meg az alhálózat erőforrás-AZONOSÍTÓját.  |
| firstConsecutiveStaticIP | 10.1.0.224 | Rendeljen hozzá egy olyan IP-címet a `firstConsecutiveStaticIP` konfigurációs tulajdonsághoz, amely a kívánt alhálózaton elérhető IP-címtartomány *vége* közelében van. `firstConsecutiveStaticIP` csak a fő készletre vonatkozik. |

A **agentPoolProfiles** állítsa be a következő értékeket:

| Mező | Példa | Leírás |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | A Azure Resource Manager útvonal AZONOSÍTÓjának megadása az alhálózathoz.  |

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

```

## <a name="deploy-your-cluster"></a>A fürt üzembe helyezése

Miután hozzáadta az értékeket az API-modellhez, a `deploy` parancs használatával üzembe helyezheti a fürtöt az ügyfélgépről az AK-motor használatával. Útmutatásért lásd: [Kubernetes-fürt üzembe helyezése](azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster).

## <a name="set-the-route-table-and-network-security-group"></a>Az útválasztási tábla és a hálózati biztonsági csoport beállítása

A fürt üzembe helyezése után térjen vissza a virtuális hálózathoz a Azure Stack felhasználói portálon. Állítsa be az útválasztási táblázatot és a hálózati biztonsági csoportot (NSG) az alhálózat paneljén. Ha nem használja az Azure CNI-t, például `networkPlugin`: `kubenet` az `kubernetesConfig` API-modell konfigurációs objektumában. Miután sikeresen telepített egy fürtöt az egyéni virtuális hálózatra, szerezze be az útválasztási tábla erőforrásának AZONOSÍTÓját a fürt erőforráscsoport **hálózat** paneljéről.

1. Nyissa meg az Azure Stack hub felhasználói portált a Azure Stack hub-példányban.
2. Válassza az **Összes erőforrás** elemet.
3. Adja meg a virtuális hálózat nevét a keresőmezőbe.
4. Válassza ki az **alhálózatok** elemet, majd válassza ki a fürtöt tartalmazó alhálózat nevét.
    
    ![útválasztási táblázat és hálózati biztonsági csoport](media/kubernetes-aks-engine-custom-vnet/virtual-network-rt-nsg.png)
    
5. Válassza az **útválasztási táblázat** lehetőséget, majd válassza ki a fürt útválasztási tábláját.
6. Válassza a **hálózati biztonsági csoport** lehetőséget, majd válassza ki a fürthöz tartozó NSG.

> [!Note]  
> A Kubernetes Windows-fürthöz tartozó egyéni virtuális hálózat [ismert hibával](https://github.com/Azure/aks-engine/issues/371)rendelkezik.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)  
- További információ a [tárolók Azure monitoráról – áttekintés](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)