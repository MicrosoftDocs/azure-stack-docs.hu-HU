---
title: Az AK-motor előfeltételeinek beállítása Azure Stack hub-on | Microsoft Docs
description: Hozza létre az ASK motor Azure Stack hub-on való futtatásának követelményeit.
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 6c9f90444c814f0580f9596ceb06b8d3db6e2821
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820117"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>Az AK-motor előfeltételeinek beállítása Azure Stack hub-on

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

Az AK-motort telepítheti a környezetében lévő virtuális gépre, vagy bármely, az Azure Stack hub Resource Manager-végponthoz hozzáféréssel rendelkező ügyfélszámítógépre. A motor futtatása előtt a következőkre lesz szüksége: az AK Base Ubuntu Server és a Linux Custom script bővítmény elérhető az előfizetésben, egy olyan egyszerű szolgáltatásnév, amely hozzá van rendelve egy közreműködő szerepkörhöz, valamint egy magán-és nyilvános kulcspár az Ubuntu-kiszolgálóhoz való SSH-hozzáféréshez. Emellett, ha a Azure Stack Development Kit használja, akkor a számítógépnek meg kell bíznia a megfelelő tanúsítványokban.

Ha rendelkezik az előfeltételekkel, megkezdheti a [fürt definiálását](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Ha Ön a Azure Stack hub Felhőbeli operátora, és az AK-motort szeretné ajánlani, kövesse az [AK-motor hozzáadása az Azure stack hub piactérhez](../operator/azure-stack-aks-engine.md)című témakör útmutatását.

## <a name="prerequisites-for-the-aks-engine"></a>Az AK-motor előfeltételei

Az AK-motor használatához a következő erőforrásokkal kell rendelkeznie. Ne feledje, hogy az AK-motort Azure Stack hub bérlői használják arra, hogy Kubernetes-fürtöket telepítsenek a bérlői előfizetésbe. Az Azure Stack hub-operátor bevonása csak akkor lehetséges, ha a Piactéri elemek letöltésére és egy egyszerű szolgáltatásnév létrehozására van szükség. A részleteket az alábbi táblázatban találja.

| Előfeltétel | Leírás | Szükséges | Utasítások |
| --- | --- | --- | --- |
| Egyéni Linux-szkriptek bővítménye | Linux Custom script bővítmény 2,0<br>Ajánlat: egyéni parancsfájl a Linux 2,0-hez<br>Verzió: 2.0.6 (vagy legújabb verzió)<br>Közzétevő: Microsoft Corp | Szükséges | Ha nem rendelkezik ezzel az elemmel az előfizetésében, forduljon a felhő üzemeltetőjéhez. |
| AK Base Ubuntu-rendszerkép | AK-alapú alaprendszerkép<br>Ajánlat: AK<br> 2019.10.24 (vagy újabb verzió)<br>Közzétevő: Microsoft-AK<br>SKU: AK-Ubuntu-1604-201910 | Szükséges | Ha nem rendelkezik ezzel az elemmel az előfizetésében, forduljon a felhő üzemeltetőjéhez. Tekintse meg a verzió függőségével kapcsolatos további információkat a [rendszerképek alapszintű verziójának megkereséséhez](#matching-engine-to-base-image-version).<br> Ha Ön a Azure Stack hub Felhőbeli operátora, és az AK-motort szeretné ajánlani, kövesse az [AK-motor hozzáadása az Azure stack hub piactérhez](../operator/azure-stack-aks-engine.md)című témakör útmutatását. |
| Azure Stack hub-előfizetés | Az ajánlatokat a Azure Stack hub előfizetések segítségével érheti el. Az ajánlat az Ön számára elérhető szolgáltatásokat tartalmazza. | Szükséges | Ahhoz, hogy a bérlői számítási feladatokat üzembe lehessen helyezni Azure Stack központban, először be kell szereznie egy [Azure stack hub-előfizetést](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| Egyszerű szolgáltatásnév (SPN) |  Egy olyan alkalmazásnak, amelynek az erőforrásait Azure Resource Manager használatával kell telepítenie vagy konfigurálnia, egy egyszerű szolgáltatásnak kell képviselnie. | Szükséges | Előfordulhat, hogy kapcsolatba kell lépnie az adott elemmel kapcsolatos Azure Stack hub-operátorral.  Útmutatásért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals) |
| (SPN) hozzárendelt **közreműködő** szerepkör | Ahhoz, hogy egy alkalmazás hozzáférhessen az előfizetéséhez tartozó erőforrásokhoz az adott szolgáltatásnév használatával, hozzá kell rendelnie a szolgáltatásnevet egy adott erőforráshoz tartozó szerepkörhöz. | Szükséges | Útmutatásért lásd: [szerepkör társítása](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role) |
| Erőforráscsoport | Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Ha nem ad meg egy meglévő erőforráscsoportot, akkor az eszköz létrehoz egyet. | Választható | [Azure Resource Manager erőforráscsoportok kezelése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Titkos nyilvános kulcs | Ha nyílt SSH-kapcsolattal szeretné használni a fejlesztői gépről a webalkalmazást futtató Azure Stack hub-példányban található kiszolgálói virtuális gépre, létre kell hoznia egy Secure Shell (SSH) nyilvános és titkos kulcspárt. | Szükséges | A kulcsok létrehozásával kapcsolatos utasításokért lásd: [SSH-kulcs létrehozása](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).|

> [!Note]  
> Az AK-motor előfeltételeit az Azure CLI-vel is létrehozhatja [Azure stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) vagy [Azure stack hub PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install)számára.

## <a name="matching-engine-to-base-image-version"></a>Illeszkedő motor az alaprendszerkép verziójához

Az AK-motor egy létrehozott rendszerképet, az **AK-alapú alapképet**használja. Az egyes alrendszerek motorjának verziója az Azure Stack hub-kezelő által az Azure Stack hub-ben elérhetővé tett rendszerkép-verziótól függ. A [támogatott Kubernetes-verziókban](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)található egy táblázat, amely az AK-motor verziószámait és a megfelelő támogatott Kubernetes-verziókat tartalmazza. Például az KABAi motor verziója `v0.43.0` az AK alaprendszerképének `2019.10.24`tól függ. Kérje meg a Azure Stack hub-kezelőt, hogy töltse le az adott rendszerkép verzióját az Azure Marketplace-ről a Azure Stack hub piactérre.

A rendszer elindítja és hibát jelez, ha a rendszerkép nem érhető el a Azure Stack hub piactéren. Ha például jelenleg a 0.43.0 és az AK-alapú alaprendszerkép-verziót használja `2019.10.24` nem érhető el, a következő hibaüzenet jelenik meg az KABAi motor futtatásakor: 

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az AK-motor üzembe helyezése Windows rendszeren Azure Stack hub-ban](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Az AK-motor üzembe helyezése Linux rendszeren Azure Stack hub-ban](azure-stack-kubernetes-aks-engine-deploy-linux.md)