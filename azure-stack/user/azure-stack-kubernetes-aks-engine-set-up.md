---
title: Az AK-motor előfeltételeinek beállítása Azure Stack hub-on
description: Hozza létre az ASK motor Azure Stack hub-on való futtatásának követelményeit.
author: mattbriggs
ms.topic: article
ms.date: 03/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 03/23/2020
ms.openlocfilehash: f89dd7e1036f3c45df184b498c309fe128fe03ba
ms.sourcegitcommit: 961e3b1fae32d7f9567359fa3f7cb13cdc37e28e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80152207"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>Az AK-motor előfeltételeinek beállítása Azure Stack hub-on

Az AK-motort telepítheti egy virtuális gépre (VM) a környezetében, vagy bármely olyan ügyfélszámítógépen, amely hozzáféréssel rendelkezik az Azure Stack hub Resource Manager-végponthoz. A motor futtatása előtt a következőkre lesz szüksége: az AK Base Ubuntu Server és a Linux Custom script bővítmény elérhető az előfizetésben, egy olyan egyszerű szolgáltatásnév, amely hozzá van rendelve egy közreműködő szerepkörhöz, valamint egy magán-és nyilvános kulcspár az Ubuntu-kiszolgálóhoz való SSH-hozzáféréshez. Emellett, ha a Azure Stack Development Kit használja, akkor a számítógépnek meg kell bíznia a megfelelő tanúsítványokban.

Ha rendelkezik az előfeltételekkel, megkezdheti a [fürt definiálását](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Ha Ön a Azure Stack hub Felhőbeli operátora, és az AK-motort szeretné ajánlani, kövesse az [AK-motor hozzáadása az Azure stack hub piactérhez](../operator/azure-stack-aks-engine.md)című témakör útmutatását.

## <a name="prerequisites-for-the-aks-engine"></a>Az AK-motor előfeltételei

Az AK-motor használatához a következő erőforrásokra van szükség. Ne feledje, hogy az AK-motort Azure Stack hub bérlői használják arra, hogy Kubernetes-fürtöket telepítsenek a bérlői előfizetésbe. Az Azure Stack hub-operátor bevonása csak akkor lehetséges, ha a Piactéri elemek letöltésére és egy egyszerű szolgáltatásnév létrehozására van szükség. A részleteket az alábbi táblázatban találja.

A Felhőbeli operátornak a következő elemeket kell megadnia.

| Előfeltételek | Leírás | Kötelező | Utasítások |
| --- | --- | --- | --- | --- |
| Azure Stack hub 1910 vagy újabb | Az KABAi motorhoz Azure Stack hub 1910 vagy újabb rendszer szükséges. | Kötelező | Ha nem biztos abban, hogy Azure Stack hub-verzióját, lépjen kapcsolatba a felhőalapú szolgáltatójával. |
| Egyéni Linux-szkriptek bővítménye | Linux Custom script bővítmény 2,0<br>Ajánlat: egyéni parancsfájl a Linux 2,0-hez<br>Verzió: 2.0.6 (vagy legújabb verzió)<br>Közzétevő: Microsoft Corp | Kötelező | Ha nem rendelkezik ezzel az elemmel az előfizetésében, forduljon a felhő üzemeltetőjéhez. |
| AK Base Ubuntu-rendszerkép | AK-alapú alaprendszerkép<br>Ajánlat: AK<br> 2019.10.24 (vagy újabb verzió)<br>Közzétevő: Microsoft-AK<br>SKU: AK-Ubuntu-1604-201910 | Kötelező | Ha nem rendelkezik ezzel az elemmel az előfizetésében, forduljon a felhő üzemeltetőjéhez. Tekintse meg a verzió függőségével kapcsolatos további információkat a [rendszerképek alapszintű verziójának megkereséséhez](#matching-engine-to-base-image-version).<br> Ha Ön a Azure Stack hub Felhőbeli operátora, és az AK-motort szeretné ajánlani, kövesse az [AK-motor hozzáadása az Azure stack hub piactérhez](../operator/azure-stack-aks-engine.md)című témakör útmutatását. |
| Egyszerű szolgáltatásnév (SPN) |  Egy olyan alkalmazásnak, amelynek az erőforrásait Azure Resource Manager használatával kell telepítenie vagy konfigurálnia, egy egyszerű szolgáltatásnak kell képviselnie. | Kötelező | Előfordulhat, hogy kapcsolatba kell lépnie az adott elemmel kapcsolatos Azure Stack hub-operátorral.<br>Ha egy Azure Active Directory (HRE) egyszerű szolgáltatásnév van használatban, a Kubernetes-fürtben lévő virtuális gépektől internet-hozzáférésre van szükség, hogy az egyszerű szolgáltatás hitelesíthető legyen a HRE. Ha nincs internet-hozzáférés, a Kubernetes-fürt nem fog működni.<br>Útmutatásért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals) |
| (SPN) hozzárendelt **közreműködő** szerepkör | Ahhoz, hogy egy alkalmazás hozzáférhessen az előfizetéséhez tartozó erőforrásokhoz az adott szolgáltatásnév használatával, hozzá kell rendelnie a szolgáltatásnevet egy adott erőforráshoz tartozó szerepkörhöz. | Kötelező | Útmutatásért lásd: [szerepkör társítása](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role) |


Adja hozzá a következőt az "egyszerű szolgáltatásnév" követelmény leírásához: ""



Megadhatja a következő elemeket.

| Előfeltételek | Leírás | Kötelező | Utasítások |
| --- | --- | --- | --- |
| Azure Stack hub-előfizetés | Az ajánlatokat a Azure Stack hub előfizetések segítségével érheti el. Az ajánlat az Ön számára elérhető szolgáltatásokat tartalmazza. | Kötelező | Ahhoz, hogy a bérlői számítási feladatokat üzembe lehessen helyezni Azure Stack központban, először be kell szereznie egy [Azure stack hub-előfizetést](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| Erőforráscsoport | Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Ha nem ad meg egy meglévő erőforráscsoportot, akkor az eszköz létrehoz egyet. | Optional | [Azure Resource Manager erőforráscsoportok kezelése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Titkos nyilvános kulcs | Ha nyílt SSH-kapcsolattal szeretné használni a fejlesztői gépről a webalkalmazást futtató Azure Stack hub-példányban található kiszolgálói virtuális gépre, létre kell hoznia egy Secure Shell (SSH) nyilvános és titkos kulcspárt. | Kötelező | A kulcsok létrehozásával kapcsolatos utasításokért lásd: [SSH-kulcs létrehozása](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).|


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