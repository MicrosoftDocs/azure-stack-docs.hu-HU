---
title: Azure Stack hub kezelése az Azure CLI-vel
description: Ismerje meg, hogyan kezelheti és helyezheti üzembe az erőforrásokat az Azure Stack hub platformon a platformfüggetlen parancssori felületen (CLI).
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 5cd1c1b7dac9e05925488b3543461f3fbd8dd9e5
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525880"
---
# <a name="install-azure-cli-on-azure-stack-hub"></a>Az Azure CLI telepítése Azure Stack hubhoz

Az Azure CLI-t telepítheti Azure Stack hub Windows vagy Linux rendszerű gépekkel való kezeléséhez. Ez a cikk végigvezeti az Azure CLI telepítésének és beállításának lépésein.

## <a name="install-azure-cli"></a>Telepítse az Azure CLI-t

1. Jelentkezzen be a fejlesztői munkaállomásra, és telepítse a CLI-t. Azure Stack hub esetében az Azure CLI 2,0-es vagy újabb verziójára van szükség. 

2. A CLI-t az [Azure CLI telepítése](/cli/azure/install-azure-cli) című cikkben ismertetett lépések segítségével telepítheti. 

3. Annak ellenőrzéséhez, hogy a telepítés sikeres volt-e, nyisson meg egy terminált vagy egy parancssorablakot, és futtassa a következő parancsot:

    ```shell
    az --version
    ```

    Ekkor meg kell jelennie az Azure CLI és a számítógépre telepített egyéb függő kódtárak verziójának.

    ![Azure CLI Azure Stack hub Python-helyen](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Jegyezze fel a parancssori felület Python-helyét. Ha a ASDK futtatja, ezt a helyet kell használnia a tanúsítvány hozzáadásához. A parancssori felület ASDK való telepítéséhez szükséges tanúsítványok beállításával kapcsolatos utasításokért lásd: [tanúsítványok beállítása az Azure CLI-hez a Azure stack Development Kit](../asdk/asdk-cli.md).

## <a name="set-up-azure-cli"></a>Az Azure CLI beállítása

### <a name="azure-ad-on-windows"></a>[Azure AD Windows rendszeren](#tab/ad-win)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha az Azure AD-t használja Identity Management szolgáltatásként, és a CLI-t használja a Windows rendszerű gépen.

#### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

1. Ha a ASDK használja, akkor bízza a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát. Útmutatásért lásd: [a tanúsítvány megbízhatósága](../asdk/asdk-cli.md#trust-the-certificate).

2. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

3. Regisztrálja a környezetét. A következő paraméterek használata a futtatásakor `az cloud register` :

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | A ASDK található **ResourceManagerUrl** : `https://management.local.azurestack.external/` az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszervégponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | `local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [a virtuálisgép-alias végpontjának beállítása](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Állítsa be az aktív környezetet az alábbi parancsok használatával.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack hub verzióját, akkor az API-verzió Profile **2019-03-01-Hybrid** helyett a **2017-03-09-profil API-** profilt kell használnia. Az Azure CLI legújabb verzióját is használni kell.
 
6. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Jelentkezzen be az Azure Stack hub-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Bejelentkezés *felhasználóként*: 

     Megadhatja a felhasználónevet és a jelszót közvetlenül a `az login` parancson belül, vagy egy böngészőben végezheti el a hitelesítést. Ha a fiókjában engedélyezve van a többtényezős hitelesítés, az utóbbit el kell végeznie:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Ha a felhasználói fiókja engedélyezte a többtényezős hitelesítést, a `az login` paraméter megadása nélkül használja a parancsot `-u` . A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.

   - Bejelentkezés *egyszerű szolgáltatásként*: 
    
     A bejelentkezés előtt [hozzon létre egy egyszerű szolgáltatásnevet a Azure Portal vagy a](../operator/azure-stack-create-service-principals.md?view=azs-2002) parancssori felület használatával, és rendeljen hozzá egy szerepkört. Most jelentkezzen be a következő parancs használatával:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-on-windows"></a>[AD FS Windows rendszeren](#tab/adfs-win)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha Active Directory összevont szolgáltatásokat (AD FS) használja az Identitáskezelő szolgáltatásként, és a parancssori felületet használja a Windows rendszerű gépen.

#### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz


1. Ha a ASDK használja, akkor bízza a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát. Útmutatásért lásd: [a tanúsítvány megbízhatósága](../asdk/asdk-cli.md#trust-the-certificate).

2. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

3. Regisztrálja a környezetét. A következő paraméterek használata a futtatásakor `az cloud register` :

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | A ASDK található **ResourceManagerUrl** : `https://management.local.azurestack.external/` az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszervégponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | `local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [a virtuálisgép-alias végpontjának beállítása](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Állítsa be az aktív környezetet az alábbi parancsok használatával.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack hub verzióját, akkor az API-verzió Profile **2019-03-01-Hybrid** helyett a **2017-03-09-profil API-** profilt kell használnia. Az Azure CLI legújabb verzióját is használni kell.

6. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Bejelentkezhet a Azure Stack hub-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Bejelentkezés *felhasználóként*:

     Megadhatja a felhasználónevet és a jelszót közvetlenül a `az login` parancson belül, vagy egy böngészőben végezheti el a hitelesítést. Ha a fiókjában engedélyezve van a többtényezős hitelesítés, az utóbbit el kell végeznie:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Ha a felhasználói fiókja engedélyezte a többtényezős hitelesítést, a `az login` paraméter megadása nélkül használja a parancsot `-u` . A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.

   - Bejelentkezés *egyszerű szolgáltatásként*: 
    
     Készítse elő a. PEM-fájlt, amelyet a szolgáltatás egyszerű bejelentkezéséhez kíván használni.

     A rendszerbiztonsági tag létrehozására szolgáló ügyfélszámítógépen exportálja az egyszerű szolgáltatás tanúsítványát pfx-ként a következő helyen található titkos kulccsal `cert:\CurrentUser\My` . A tanúsítvány neve megegyezik a rendszerbiztonsági tag nevével.

     Alakítsa át a pfx-t PEM-ra (használja az OpenSSL segédprogramot).

     Jelentkezzen be a CLI-be:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

#### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

### <a name="azure-ad-on-linux"></a>[Azure AD Linuxon](#tab/ad-lin)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha az Azure AD-t használja Identity Management szolgáltatásként, és egy Linux rendszerű számítógépen használja a CLI-t.

#### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

A következő lépésekkel csatlakozhat Azure Stack hubhoz:


1. Ha a ASDK használja, akkor bízza a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát. Útmutatásért lásd: [a tanúsítvány megbízhatósága](../asdk/asdk-cli.md#trust-the-certificate).

2. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

3. Regisztrálja a környezetét. A következő paraméterek használata a futtatásakor `az cloud register` :

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | A ASDK található **ResourceManagerUrl** : `https://management.local.azurestack.external/` az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszervégponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | `local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [a virtuálisgép-alias végpontjának beállítása](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Állítsa be az aktív környezetet. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack hub verzióját, akkor az API-verzió Profile **2019-03-01-Hybrid** helyett a **2017-03-09-profil API-** profilt kell használnia. Az Azure CLI legújabb verzióját is használni kell.

6. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Bejelentkezhet a Azure Stack hub-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Bejelentkezés *felhasználóként*:

     Megadhatja a felhasználónevet és a jelszót közvetlenül a `az login` parancson belül, vagy egy böngészőben végezheti el a hitelesítést. Ha a fiókjában engedélyezve van a többtényezős hitelesítés, az utóbbit el kell végeznie:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Ha a felhasználói fiókjában engedélyezve van a többtényezős hitelesítés, a `az login` parancsot a paraméter megadása nélkül is használhatja `-u` . A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.
   
   * Bejelentkezés *egyszerű szolgáltatásként*
    
     A bejelentkezés előtt [hozzon létre egy egyszerű szolgáltatásnevet a Azure Portal vagy a](../operator/azure-stack-create-service-principals.md?view=azs-2002) parancssori felület használatával, és rendeljen hozzá egy szerepkört. Most jelentkezzen be a következő parancs használatával:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
    az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-linux"></a>[Linux AD FS](#tab/adfs-lin)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha Active Directory összevont szolgáltatásokat (AD FS) használja felügyeleti szolgáltatásként, és a CLI-t használja egy Linux rendszerű gépen.

#### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

A következő lépésekkel csatlakozhat Azure Stack hubhoz:

1. Ha a ASDK használja, akkor bízza a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát. Útmutatásért lásd: [a tanúsítvány megbízhatósága](../asdk/asdk-cli.md#trust-the-certificate).

2. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

3. Regisztrálja a környezetét. A futtatásakor használja a következő paramétereket `az cloud register` .

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | A ASDK található **ResourceManagerUrl** : `https://management.local.azurestack.external/` az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszervégponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | `local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [a virtuálisgép-alias végpontjának beállítása](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Állítsa be az aktív környezetet. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack hub verzióját, akkor az API-verzió Profile **2019-03-01-Hybrid** helyett a **2017-03-09-profil API-** profilt kell használnia. Az Azure CLI legújabb verzióját is használni kell.

6. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Bejelentkezhet a Azure Stack hub-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

7. bejelentkezés: 

   *  **Felhasználóként** egy, az eszköz kódját használó webböngésző használatával:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.

   * Egyszerű szolgáltatásnév:
        
     Készítse elő a. PEM-fájlt, amelyet a szolgáltatás egyszerű bejelentkezéséhez kíván használni.

      * A rendszerbiztonsági tag létrehozására szolgáló ügyfélszámítógépen exportálja az egyszerű szolgáltatás tanúsítványát pfx-ként a következő helyen található titkos kulccsal `cert:\CurrentUser\My` . A tanúsítvány neve megegyezik a rendszerbiztonsági tag nevével.
  
      * Alakítsa át a pfx-t PEM-ra (használja az OpenSSL segédprogramot).

     Jelentkezzen be a CLI-be:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

#### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
  az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

### <a name="known-issues"></a>Ismert problémák

Ismert problémák léptek fel a CLI Azure Stack hub-ban való használatakor:

 - A CLI interaktív mód. A parancs például az `az interactive` Azure stack hub még nem támogatja.
 - Azure Stack hub-ban elérhető virtuálisgép-rendszerképek listájának lekéréséhez használja a parancsot a parancs `az vm image list --all` helyett `az vm image list` . A beállítás megadásával `--all` biztosíthatja, hogy a válasz csak az Azure stack hub-környezetben elérhető lemezképeket adja vissza.
 - Előfordulhat, hogy az Azure-ban elérhető virtuálisgép-rendszerkép-aliasok nem alkalmazhatók Azure Stack hubhoz. Virtuálisgép-lemezképek használata esetén a rendszerkép aliasa helyett a teljes URN paramétert (Canonical: UbuntuServer: 14.04.3-LTS: 1.0.0) kell használnia. Ennek az URN-nek meg kell egyeznie a parancsból származtatott rendszerkép-specifikációkkal `az vm images list` .

---

## <a name="next-steps"></a>További lépések

- [Sablonok üzembe helyezése az Azure CLI-vel](azure-stack-deploy-template-command-line.md)
- [Az Azure CLI engedélyezése Azure Stack hub-felhasználók számára (operátor)](../operator/azure-stack-cli-admin.md)
- [Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md) 
