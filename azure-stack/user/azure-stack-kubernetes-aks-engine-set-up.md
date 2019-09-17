---
title: Az AK-motor előfeltételeinek beállítása Azure Stackon | Microsoft Docs
description: Határozza meg a kérés motorjának a Azure Stack való futtatásának követelményeit.
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
ms.openlocfilehash: a65449d39f114bd0ff5eb859bd49ec36d4bb4281
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019226"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack"></a>Az AK-motor előfeltételeinek beállítása Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az AK-motort telepítheti egy virtuális gépre a környezetében, vagy bármely olyan ügyfélszámítógépet, amely hozzáféréssel rendelkezik a Azure Stack Resource Manager-végponthoz. A motor futtatása előtt a következőkre lesz szüksége: az AK Base Ubuntu Server és a Linux Custom script bővítmény elérhető az előfizetésben, egy egyszerű szolgáltatásnév, amely hozzá van rendelve egy közreműködő szerepkörhöz, valamint egy magán-vagy nyilvános kulcspár SSH-hozzáférés az Ubuntu-kiszolgálóhoz. Emellett, ha a Azure Stack Development Kit használja, akkor a számítógépnek meg kell bíznia a megfelelő tanúsítványokban.

Ha rendelkezik az előfeltételekkel, megkezdheti a [fürt definiálását](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

## <a name="prerequisites-for-the-aks-engine"></a>Az AK-motor előfeltételei

Az AK-motor használatához a következő erőforrásokkal kell rendelkeznie. Ne feledje, hogy az AK-motort Azure Stack bérlői használják, hogy Kubernetes-fürtöket telepítsenek a bérlői előfizetésbe. Az egyetlen rész, ahol a Azure Stack operátor bevonása szükséges lehet a Piactéri elemek letöltéséhez és egy egyszerű szolgáltatásnév létrehozásához. A részleteket az alábbi táblázatban találja.

| Előfeltétel | Leírás | Kötelező | Útmutatás |
| --- | --- | --- | --- |
| Egyéni Linux-szkriptek bővítménye | Linux Custom script bővítmény 2,0<br>Ajánlat Egyéni parancsfájl a Linux 2,0-hez<br>Verziója 2.0.6 (vagy a legújabb verzió)<br>Publisher Microsoft Corp | Kötelező | Ha nem rendelkezik ezzel az elemmel az előfizetésében, forduljon a felhő üzemeltetőjéhez. |
| AK Base Ubuntu-rendszerkép | AK-alapú alaprendszerkép<br>Ajánlat: AK<br>Verziója 2019.07.30 (vagy újabb verzió)<br>Közzétevő: Microsoft-AK<br>SKU: AK-Ubuntu-1604-201907 | Kötelező | Ha nem rendelkezik ezzel az elemmel az előfizetésében, forduljon a felhő üzemeltetőjéhez. Tekintse meg a verzió függőségével kapcsolatos további információkat a [rendszerképek alapszintű verziójának megkereséséhez](#matching-engine-to-base-image-version). |
| Előfizetés Azure Stack | A Azure Stack előfizetések segítségével érheti el az ajánlatokat. Az ajánlat az Ön számára elérhető szolgáltatásokat tartalmazza. | Kötelező | Ahhoz, hogy a bérlői számítási feladatokat Azure Stackban lehessen üzembe helyezni, először egy Azure Stack- [előfizetést](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services)kell beszereznie. |
| Egyszerű szolgáltatásnév (SPN) |  Egy olyan alkalmazásnak, amelynek az erőforrásait Azure Resource Manager használatával kell telepítenie vagy konfigurálnia, egy egyszerű szolgáltatásnak kell képviselnie. | Kötelező | Előfordulhat, hogy kapcsolatba kell lépnie a Azure Stack operátorral az adott elemmel kapcsolatban.  Útmutatásért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals) |
| (SPN) hozzárendelt **közreműködő** szerepkör | Ahhoz, hogy egy alkalmazás hozzáférhessen az előfizetéséhez tartozó erőforrásokhoz az adott szolgáltatásnév használatával, hozzá kell rendelnie a szolgáltatásnevet egy adott erőforráshoz tartozó szerepkörhöz. | Kötelező | Útmutatásért lásd: [szerepkör társítása](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role) |
| Resource group | Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Ha nem ad meg egy meglévő erőforráscsoportot, akkor az eszköz létrehoz egyet. | Választható | [Azure Resource Manager erőforráscsoportok kezelése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Titkos nyilvános kulcs | Ha a fejlesztői gépről nyitott SSH-kapcsolattal szeretné használni a webalkalmazást futtató Azure Stack-példányban található kiszolgálói virtuális gépet, létre kell hoznia egy Secure Shell (SSH) nyilvános és titkos kulcspárt. | Kötelező | A kulcsok létrehozásával kapcsolatos utasításokért lásd: [SSH-kulcs létrehozása](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).|

> [!Note]  
> Az AK-motor előfeltételeit az [Azure CLI](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) -vel is létrehozhatja Azure Stack vagy [Azure stack powershellhez](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install).

## <a name="matching-engine-to-base-image-version"></a>Illeszkedő motor az alaprendszerkép verziójához

Az AK-motor egy létrehozott rendszerképet, az **AK-alapú alapképet**használja. Az egyes alrendszerek motorjának verziója függ egy adott rendszerkép-verziótól, amelyet a Azure Stack operátora a Azure Stack elérhetővé tett. A [támogatott Kubernetes-verziókban](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)található egy táblázat, amely az AK-motor verziószámait és a megfelelő támogatott Kubernetes-verziókat tartalmazza. Az AK-motor verziója `v0.40.0` például az AK-alapú alaprendszerkép verziójától `2019.08.21` függ. Kérje meg Azure Stack kezelőjét, hogy töltse le az adott rendszerkép verzióját az Azure Marketplace-ről a Azure Stack Marketplace-re.

A rendszer elindítja és hibát jelez, ha a rendszerkép nem érhető el a Azure Stack piactéren. Ha például jelenleg a (z) 0.39.1 és az AK-alapú alaprendszerkép verziója `2019.08.09` nem érhető el, a következő hibaüzenet jelenik meg az AK-motor futtatásakor: 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-201908:2019.08.09' is not available. 
Verify that all fields in the storage profile are correct.
```

A következő parancs futtatásával tekintheti meg az AK-motor aktuális verzióját:

```bash  
$ aks-engine version
Version: v0.39.1
GitCommit: 6fff62731
GitTreeState: clean
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az AK-motor üzembe helyezése Windows rendszeren Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Az AK-motor üzembe helyezése Linux rendszeren Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)