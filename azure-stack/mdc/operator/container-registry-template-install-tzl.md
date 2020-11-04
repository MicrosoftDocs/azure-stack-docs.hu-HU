---
title: Tároló-beállításjegyzék hozzáadása Azure Stack hubhoz | Microsoft Docs
titleSuffix: Azure Stack
description: Megtudhatja, hogyan adhat hozzá egy tároló-beállításjegyzéket Azure Stack hub Marketplace-hez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: d3f3c0b746049dfb5a34af17f4dc1d24d1b2246e
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329098"
---
# <a name="add-a-container-registry-to-azure-stack-hub"></a>Tároló-beállításjegyzék hozzáadása Azure Stack hubhoz

A tároló-beállításjegyzéket hozzáadhatja a Azure Stack hub piactérhez, így a felhasználók üzembe helyezhetik és kezelhetik saját tároló-beállításjegyzéket. Ez a megoldási sablon telepíti és konfigurálja a nyílt forráskódú Docker Container Registryt egy olyan felhasználói előfizetésben, amely az AK Base Ubuntu 16,04-LTS-rendszerképben fut. A sablon támogatja a csatlakoztatott és a leválasztott (gapped) központi telepítéseket is, és támogatja a Azure Active Directory (Azure AD) és a Active Directory összevont szolgáltatások (AD FS) Azure Stack hubokat.

## <a name="get-the-marketplace-item"></a>Piactér-elemek beszerzése

A **Container Registry sablon** Marketplace-eleme a következő GitHub-tárházban található: https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/registry-v1.0.2/Microsoft.AzureStackContainerRegistry.1.0.2.azpkg . A Piactéri elem elérhető a portálon a Select előfizetések területen.

Az elem (oldalsó betöltés) a piactéren a Microsoft. AzureStackDockerContainerRegistry. 1.0.2. azpkg használatával is hozzáadható. A cikkben található parancsfájlok a git-tárház (zip) csomagként való letöltésével https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip és a fájlok kibontásával érhetők el. A parancsfájlt a `azurestack-gallery-master\registry\scripts` mappában találja.

## <a name="prerequisites"></a>Előfeltételek

A Container Registry Marketplace-elem Azure Stack hub-beli hozzáadása előtt a következő elemeket kell megadnia.

| Item | Típus | Részletek |
| --- | --- | --- |
| Azure Stack hub PowerShell-modulok (AZS. Gallery. admin) | PowerShell-modulok | Csak akkor szükséges, ha a tároló beállításjegyzék-sablonjának katalógusa elemet betölti, a Azure Stack hub PowerShell-modulok a katalógus elemeinek hozzáadására és eltávolítására szolgálnak.<br>[Azure Stack PowerShell-modulok telepítése](../../operator/azure-stack-powershell-install.md) |
| Container Registry sablon | Marketplace-tétel | Ahhoz, hogy a tároló-beállításjegyzéket Azure Stack hub-felhasználóként lehessen üzembe helyezni, a tároló beállításjegyzék-sablon Marketplace-elemének elérhetőnek kell lennie az előfizetésében, vagy manuálisan kell hozzáadnia (betöltve) az Azure Stack hub piactéren. Ha az oldal betöltését végzi, kövesse az utasításokat, hogy betöltse a csomagot a `readme.md` [GitHub-adattárba](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/registry-v1.0.1). |
| AK Base Ubuntu 16,04-LTS-rendszerkép, szeptember 2019 minimális kiadási verzió | Marketplace-tétel | Ahhoz, hogy a Azure Stack hub-felhasználók üzembe helyezzük a tároló-beállításjegyzéket, a piactéren elérhetővé kell tennie az AK-alapú alaplemezképet. A Container Registry sablon a rendszerképet használja, ha egy Ubuntu virtuális gép, amely a Docker-tároló beállításjegyzékének bináris fájljait tárolja. |
| Linux Custom script bővítmény 2,0 | Marketplace-tétel | Ahhoz, hogy a Azure Stack hub-felhasználók üzembe helyezzük a tároló-beállításjegyzéket, a piactéren elérhetővé kell tennie a Linux egyéni parancsfájl-bővítményt. A tároló beállításjegyzék-sablonjának központi telepítése a bővítmény használatával konfigurálja a beállításjegyzéket. |
| SSL-tanúsítvány | Tanúsítvány | A tároló beállításjegyzék-sablonját telepítő felhasználóknak meg kell adniuk egy PFX-tanúsítványt, amelyet a beállításjegyzék szolgáltatás SSL-titkosításának konfigurálásakor használ. Ha a parancsfájlt használja, a PowerShell-munkamenetet emelt szintű parancssorból kell futtatnia. Ez nem futtatható a DVM vagy a HLH.<br>A nyilvános vagy privát/vállalati tanúsítványokkal rendelkező Azure Stack hub PKI-tanúsítványokra vonatkozó követelményeivel kapcsolatos általános útmutatásért tekintse meg ezt a dokumentációt: [Azure stack hub nyilvánoskulcs-infrastruktúra (PKI) tanúsítványára vonatkozó követelmények](../../operator/azure-stack-pki-certs.md)<br>A tanúsítvány teljes tartománynevének ezt a mintát kell követnie `<vmname>.<location>.cloudapp.<fqdn>` , kivéve, ha egyéni tartományt/DNS-bejegyzést használ a végponthoz. A névnek betűvel kell kezdődnie, és legalább két betűt kell tartalmaznia, és csak kisbetűket és legalább három karaktert kell használnia. |
| Szolgáltatási elv (SPN) | Alkalmazás regisztrálása | A tároló beállításjegyzékének üzembe helyezéséhez és konfigurálásához létre kell hozni egy alkalmazás-regisztrációt, más néven az egyszerű szolgáltatásnevet (SPN). Ez az egyszerű szolgáltatásnév a virtuális gép és a beállításjegyzék konfigurálása során használatos a Marketplace-elemek üzembe helyezése előtt létrehozott Microsoft Azure Key Vault és a Storage-fiók erőforrásainak eléréséhez.<br>Az egyszerű szolgáltatásnevet az Azure AD-ben kell létrehozni az Azure Stack hub felhasználói portálján bejelentkezett bérlőn belül. AD FS használata esetén a rendszer a helyi könyvtárban hozza létre.<br>Az [alábbi útmutatást követve](../../operator/azure-stack-create-service-principals.md)részletesen tájékozódhat arról, hogyan hozhat létre egyszerű szolgáltatásnevet az Azure ad-hez és a AD FS hitelesítési módszerekhez.<br> **Fontos** : a frissítések telepítéséhez mentenie kell az SPN-alkalmazás azonosítóját és a titkos kulcsot.<br> |
| Beállításjegyzékbeli Felhasználónév és jelszó | Hitelesítő adatok | A nyílt forráskódú Docker-tároló beállításjegyzékének telepítése és konfigurálása engedélyezve van az alapszintű hitelesítéssel. Ha a beállításjegyzéket Docker-parancsokkal szeretné elérni a képek leküldéséhez és lekéréséhez, a felhasználónevet és a jelszót kötelező megadni. A Felhasználónév és a jelszó biztonságosan tárolódik egy Key Vault tárolóban.<br>**Fontos** : mentse a beállításjegyzék felhasználónevét és jelszavát, és jelentkezzen be a beállításjegyzékbe és a leküldéses/lekérési képekbe. |
| Nyilvános SSH/titkos kulcs | Hitelesítő adatok | A virtuális géppel való üzembe helyezéssel vagy futásidejű problémákkal kapcsolatos problémák elhárításához meg kell adni egy nyilvános SSH-kulcsot az üzembe helyezéshez és a megfelelő titkos kulcshoz. Azt javasoljuk, hogy az OpenSSH-formátumot, az ssh-keygen-t, a privát/nyilvános kulcspár létrehozásához használja, mivel a naplók gyűjtéséhez szükséges diagnosztikai parancsfájlok ezt a formátumot igénylik.<br>**Fontos** : ahhoz, hogy az üzembe helyezett virtuális gép hibaelhárításhoz hozzáférhessen, hozzáféréssel kell rendelkeznie a nyilvános és a titkos kulcsokhoz. |
| Hozzáférés rendszergazdai és felhasználói portálokhoz és felügyeleti végpontokhoz | Kapcsolatok | Ez az útmutató feltételezi, hogy a beállításjegyzéket olyan rendszerről telepíti és konfigurálja, amely az Azure Stack hub rendszerhez kapcsolódik. |

A parancsfájl `Pre-reqs` létrehozza a piactér-elemek telepítéséhez szükséges egyéb bemeneteket.

## <a name="installation-steps"></a>A telepítés lépései

A tároló-beállításjegyzék sablonjának telepítéséhez több erőforrást kell létrehozni az üzembe helyezés előtt.

1. Kapcsolódjon Azure Stack hubhoz felhasználóként a PowerShell használatával, és válasszon egy előfizetést a parancsmag használatával `Select-AzureRmSubscription –Subscription <subscription guid>` . A Azure Stack hub PowerShell-hez való csatlakozással kapcsolatos további információkért lásd: [Csatlakozás a Azure Stackhoz a PowerShell felhasználóként](../../user/azure-stack-powershell-configure-user.md).

2. Futtassa `Import-Modules .\\pre-reqs.ps1` a parancsot a modulok parancsfájlban történő importálásához `pre-reqs.ps1` . A szkript létrehoz egy erőforráscsoportot, egy Storage-fiókot, egy BLOB-tárolót, Key Vault tárolót, hozzáférési engedélyeket rendel az egyszerű szolgáltatásnév számára, és a beállításjegyzék tanúsítványait, felhasználónevét és jelszavát másolja Key Vault tárolóba.

3. Futtassa az alábbi parancsmagot egy emelt szintű parancssorból a következő paraméterekhez tartozó környezet értékeinek használatával:

    ```powershell  
         Set-ContainerRegistryPrerequisites -Location Shanghai `
         -ServicePrincipalId <spn app id> `
         -ResourceGroupName newregreq1 `
         -StorageAccountName newregsa1 `
         -StorageAccountBlobContainer newregct1 `
         -KeyVaultName newregkv1 `
         -CertificateSecretName containersecret2 `
         -CertificateFilePath C:\crinstall\shanghairegcert.pfx `
         -CertificatePassword <cert password> `
         -RegistryUserName admin `
         -RegistryUserPassword <password> 
    ```

    | Paraméter | Részletek |
    | --- | --- |
    | $Location | Ez más néven a régió neve. |
    | $ResourceGroupName | Adja meg a Storage-fiók és a Key Vault-tároló létrehozásához használni kívánt erőforráscsoport nevét. A Piactéri elemek telepítésekor egy másik erőforráscsoportot kell megadnia. |
    | $StorageAccountName | Adja meg annak a Storage-fióknak a nevét, amelyet a rendszer a tároló-beállításjegyzékhez hoz létre, amelyet a rendszer leküldett képek tárolására használ. |
    | $StorageAccountBlobContainer | Adja meg annak a blob-tárolónak a nevét, amelyet a rendszerkép-tárolóhoz kíván létrehozni. |
    | $KeyVaultName | Adja meg annak a Key Vault-tárolónak a nevét, amelyet a tanúsítvány és a Felhasználónév és a jelszó értékének tárolására kíván létrehozni. |
    | $CertificateSecretName | Adja meg a PFX-tanúsítvány tárolásához Key Vaultban létrehozott titok nevét. |
    | $CertificateFilePath | Adja meg a PFX-tanúsítvány elérési útját. |
    | $CertificatePassword | Adja meg a PFX-tanúsítvány jelszavát. |
    | $ServicePrincipalId | Adja meg az egyszerű szolgáltatásnév AppID. |
    | $RegistryUserName | Adja meg azt a felhasználónevet, amely az alapszintű hitelesítés használatával éri el a beállításjegyzék szolgáltatás elérését. |
    | $RegistryUserPassword | Adja meg a beállításjegyzék felhasználójának jelszavát. |

1. A parancsfájl befejeződése után figyelje meg, hogy a parancsfájl vége a sablon üzembe helyezéséhez használandó paramétereket tartalmaz. Ezeknek az értékeknek a másolásakor és beillesztésekor előfordulhat, hogy a rendszer az érték bepakolásakor helyet helyez el.

    ```powershell  
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/<subcription id>/resourceGroups/newr
    egreg1/providers/Microsoft.KeyVault/vaults/newregkv1
    PFX KeyVaultSecretUrl        : https://newregkv1.vault.shanghai.azurestack.corp.microsoft.com:443/secr
    ets/containersecret1/37cc2f7ea1c44ad7b930e2c237a14949
    PFX Certificate Thumbprint   : 64BD5F3BC41DCBC6495998900ED322D8110DE25E
    ----------------------------------------------------------------
    StorageAccountResourceId     : /subscriptions/<subcription id>/resourcegroups/newr
    egreg1/providers/Microsoft.Storage/storageAccounts/newregsa1
    Blob Container               : newregct1
    ----------------------------------------------------------------
    
    Skus : aks-ubuntu-1604-201909
    
    
    Skus : aks-ubuntu-1604-201910
    
    ---------------------------------------------------------------- 
    
    ```

1. Nyissa meg az Azure Stack hub felhasználói portált.

2. Válassza **Create** a  >  **számítási**  >  **Container Registry sablon** létrehozása lehetőséget.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-install-tzl/template.png)

3. Válassza ki az előfizetést, az erőforráscsoportot és a helyet a tároló beállításjegyzék-sablonjának telepítéséhez.

    ![Előfizetés kiválasztása](./media/container-registry-template-install-tzl/subscription.png)

4. Fejezze be a virtuális gép konfigurációjának részleteit. A rendszerkép SKU alapértelmezett értéke: **AK-Ubuntu-1604-201909** ; a függvény kimenete azonban `Set-ContainerRegistryPrerequisites` tartalmazza a telepítéshez használandó elérhető SKU-ket tartalmazó listát. Ha több SKU is létezik, válassza a legújabb SKU-t az üzembe helyezéshez.

    ![A virtuális gép konfigurációjának részletei](./media/container-registry-template-install-tzl/details.png)

    | Paraméter | Részletek |
    | --- | --- |
    | Felhasználónév | A virtuális géphez való bejelentkezéshez adja meg a felhasználónevet. |
    | Nyilvános SSH-kulcs | Adja meg a virtuális géppel való hitelesítéshez használt nyilvános SSH-kulcsot SSH protokoll használatával. |
    | Méret | Válassza ki a telepítendő virtuális gép méretét. |
    | Nyilvános IP-cím | Adja meg a virtuális gép IP-címének nevét és típusát (dinamikus vagy statikus). A tartománynév érvénytelen. Csak kisbetűket, számokat és kötőjeleket tartalmazhat. Az első karakternek betűnek kell lennie. Az utolsó karakternek betűnek vagy számnak kell lennie. Az értéknek három és 63 karakter közöttinek kell lennie.  |
    | Tartománynévcímke | Adja meg a beállításjegyzékhez tartozó DNS-előtagot. A teljes FQDN-nek meg kell egyeznie a beállításjegyzékhez létrehozott PFX-tanúsítvány CN-értékével. |
    | Replikák | Itt adhatja meg, hogy hány tároló-replika induljon el. |
    | Rendszerkép SKU | A központi telepítéshez használni kívánt rendszerkép-SKU megadása. Az AK alaprendszerképéhez elérhető SKU-ket a szkript sorolja fel `Set-ContainerRegistryPrerequisites` . |
    | SPN ügyfél-azonosító | Az SPN-alkalmazás AZONOSÍTÓjának megadásához. |
    | SPN-jelszó/Jelszó megerősítése | Az SPN-alkalmazás AZONOSÍTÓjának titkát kell megadnia. |

1. Fejezze be a tárolási és Key Vault konfigurációt.

    ![Tárolási és Key Vault konfiguráció](./media/container-registry-template-install-tzl/storage.png)

    | Paraméter | Részletek |
    | --- | --- |
    | Meglévő kiterjesztett Storage-fiók erőforrás-azonosítója | Adja meg a Storage-fiók erőforrás-AZONOSÍTÓját a parancsfájl által visszaadott értékkel `pre-reqs` . |
    | Meglévő háttérbeli blob-tároló | Adja meg a blob-tároló nevét, amely a reqs előtti parancsfájl kimenetében szerepel. |
    | PFX-tanúsítvány Key Vault erőforrás-azonosítója | Adja meg a parancsfájl által visszaadott Key Vault erőforrás-azonosítót `pre-reqs` . |
    | PFX-tanúsítvány Key Vault titkos URL-cím | Adja meg a reqs előtti parancsfájl által visszaadott tanúsítvány URL-címét. |
    | PFX-Tanúsítvány ujjlenyomata | Adja meg a tanúsítvány ujjlenyomatát, amelyet az előzetes reqs parancsfájl adott vissza. |

1. Ha az összes értéket megadja, és a megoldás sablonjának üzembe helyezése megkezdődik, 10-15 percet vesz igénybe, amíg a virtuális gép üzembe helyezi és konfigurálja a beállításjegyzék-szolgáltatást.

    ![Virtuális gép üzembe helyezése](./media/container-registry-template-install-tzl/deploy.png)

2. A beállításjegyzék teszteléséhez nyisson meg egy Docker CLI-példányt egy gépről/virtuális gépre, amely hozzáfér a beállításjegyzék URL-címéhez.

    > [!Note]
    >  Ha olyan önaláírt tanúsítványt vagy tanúsítványt használt, amelyet a használt virtuális géphez nem ismert, akkor telepítenie kell a tanúsítványt a virtuális gépre, és újra kell indítania a Docker-t.

## <a name="pushing-and-pulling-images-from-container-registry"></a>Képek leküldése és húzása a tároló-beállításjegyzékből

1. Jelentkezzen be a használatával `docker login –u \<username> -p \<password>` .
2. Lekéréses és rendszerképek egy ismert beállításjegyzékből.
3. Címkézze fel a lemezképet az újonnan telepített Docker-tároló beállításjegyzékének megcélzásához.
4. Küldje le a rendszerképet az új cél beállításjegyzékbe.

Például:

```powershell  
PS C:\> docker pull mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
1.0: Pulling from azureiotedge-simulated-temperature-sensor
5d20c808ce19: Already exists
656de8e592c3: Already exists
1e1868d1f676: Already exists
f3fb1b0d620f: Pulling fs layer
26224c4fc11a: Pulling fs layer
c459a69d65b2: Pulling fs layer
c459a69d65b2: Verifying Checksum
c459a69d65b2: Download complete
f3fb1b0d620f: Download complete
f3fb1b0d620f: Pull complete
26224c4fc11a: Verifying Checksum
26224c4fc11a: Pull complete
c459a69d65b2: Pull complete
Digest: sha256:dd64ff0918459184574e840ee97aa9f1bacd40aa37c972984ea10f0ecd719d5f
Status: Downloaded newer image for mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker tag mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0    myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp.microsoft.com
docker : WARNING! Using --password via the CLI is insecure. Use --password-stdin.
At line:1 char:1
+ docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (WARNING! Using ...password-stdin.:String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
 
Login Succeeded

PS C:\> docker push myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
The push refers to repository [myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor]
d377c212e567: Preparing
0481685b758f: Preparing
15474c03a0b6: Preparing
8cdec5be5964: Preparing
79116d3fb0bf: Preparing
3fc64803ca2d: Preparing
3fc64803ca2d: Waiting
79116d3fb0bf: Mounted from azureiotedge-agent
8cdec5be5964: Mounted from azureiotedge-agent
15474c03a0b6: Pushed
d377c212e567: Pushed
3fc64803ca2d: Mounted from azureiotedge-agent
0481685b758f: Pushed
1.0: digest: sha256:f5fbc4a5c6806e12cafe1c363fea2b6cbd98a211b8153c5b19aca1386bfa6ecb size: 1576 

```

## <a name="known-issues"></a>Ismert problémák

A sablon által üzembe helyezett Docker Container Registry-szolgáltatás verziója 2,7. Ez a verzió olyan ismert problémát tartalmaz, amely megakadályozza a Windows-tárolók rendszerképeinek leküldését és húzását. A probléma követése a következő GitHub-elemmel történik [https://github.com/docker/distribution-library-image/issues/89](https://github.com/docker/distribution-library-image/issues/89) .

## <a name="next-steps"></a>Következő lépések

[Azure Stack Marketplace – áttekintés](../../operator/azure-stack-marketplace.md)
