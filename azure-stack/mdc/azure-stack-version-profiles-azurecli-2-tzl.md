---
title: Azure Stack hub kezelése az Azure CLI-vel – MDC | Microsoft Docs
description: Ismerje meg, hogyan kezelheti és helyezheti üzembe az erőforrásokat az Azure Stack hub platformon egy moduláris adatközpont (MDC) számára a platformfüggetlen parancssori felület (CLI) használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 2296d1228819d32f77ccc0dd35e4f2228908e969
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910261"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli---modular-data-center-mdc"></a>Erőforrások kezelése és üzembe helyezése Azure Stack hubhoz az Azure CLI-moduláris adatközpont (MDC) használatával

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

A cikk lépéseit követve állítsa be az Azure Command-Line Interface (CLI) eszközt a Azure Stack hub-erőforrások Linux, Mac és Windows rendszerű ügyfélszámítógépeken való kezeléséhez.

## <a name="prepare-for-azure-cli"></a>Felkészülés az Azure CLI-re

**A virtuális gép aliasok végpontja** egy aliast (például "UbuntuLTS" vagy "Win2012Datacenter") biztosít. Ez az alias a virtuális gépek telepítésekor egyetlen paraméterként hivatkozik a lemezkép-közzétevőre, az ajánlatra, az SKU-ra és a verzióra.  

A következő szakasz a virtuális gép aliasok végpontjának beállítását ismerteti.

### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>A virtuális gép aliasnevei végpont beállítása

Beállíthat egy nyilvánosan elérhető végpontot, amely egy virtuálisgép-alias fájlt üzemeltet. A VM-alias fájl egy olyan JSON-fájl, amely a rendszerkép köznapi nevét adja meg. A nevet akkor használja, ha a virtuális gépet Azure CLI-paraméterként telepíti.

1. Ha egyéni rendszerképet tesz közzé, jegyezze fel a közzététel során megadott kiadói, ajánlati, SKU-és verziószám-információkat. Ha ez egy rendszerkép a piactéren, megtekintheti az adatokat a ```Get-AzureVMImage``` parancsmag használatával.  

2. Töltse le a [mintát](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a githubról.

3. Hozzon létre egy Storage-fiókot Azure Stack központban. Ha elkészült, hozzon létre egy BLOB-tárolót. Állítsa be a hozzáférési házirendet a "Public" értékre.  

4. Töltse fel a JSON-fájlt az új tárolóba. Ha elkészült, megtekintheti a blob URL-címét. Jelölje ki a blob nevét, majd válassza ki az URL-címet a blob tulajdonságai között.

### <a name="install-or-upgrade-cli"></a>PARANCSSORI felület telepítése vagy frissítése

Jelentkezzen be a fejlesztői munkaállomásra, és telepítse a CLI-t. Azure Stack hub esetében az Azure CLI 2,0-es vagy újabb verziójára van szükség. Az API-profilok legújabb verziójának a parancssori felület aktuális verzióját kell megadnia. A CLI-t az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) című cikkben ismertetett lépések segítségével telepítheti. 

Annak ellenőrzéséhez, hogy a telepítés sikeres volt-e, nyisson meg egy terminált vagy egy parancssorablakot, és futtassa a következő parancsot:

```shell
az --version
```

Ekkor meg kell jelennie az Azure CLI és a számítógépre telepített egyéb függő kódtárak verziójának.
    
![Azure CLI Azure Stack hub Python-helyen](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)


## <a name="windowslinux-azure-ad"></a>Windows/Linux (Azure AD)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha az Azure AD-t használja Identity Management szolgáltatásként, és Windows/Linux rendszerű számítógépen használja a CLI-t.

### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

1. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

2. Regisztrálja a környezetét. A következő paraméterek használata a futtatásakor `az cloud register` :

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> Az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszer-végponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [set up the VM aliass Endpoint](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Állítsa be az aktív környezetet az alábbi parancsok használatával.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
1. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Jelentkezzen be az Azure Stack hub-környezetbe felhasználóként vagy egyszerű szolgáltatásnévként. 

   - Bejelentkezés *felhasználóként*: 

     Megadhatja a felhasználónevet és a jelszót közvetlenül a `az login` parancson belül, vagy egy böngészőben végezheti el a hitelesítést. Ha a fiókjában engedélyezve van a többtényezős hitelesítés, az utóbbit el kell végeznie:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Ha a felhasználói fiókja engedélyezte a többtényezős hitelesítést, a `az login` paraméter megadása nélkül használja a parancsot `-u` . A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.

   - Bejelentkezés *egyszerű szolgáltatásként*: 
    
     A bejelentkezés előtt [hozzon létre egy egyszerű szolgáltatásnevet a Azure Portal vagy a](../operator/azure-stack-create-service-principals.md) parancssori felület használatával, és rendeljen hozzá egy szerepkört. Most jelentkezzen be a következő parancs használatával:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/output.png)

## <a name="windowslinux-ad-fs"></a>Windows/Linux (AD FS)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha Active Directory összevont szolgáltatásokat (AD FS) használja az identitáskezelési szolgáltatásként, és Windows/Linux rendszerű számítógépen használja a CLI-t.


### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

1. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

2. Regisztrálja a környezetét. A következő paraméterek használata a futtatásakor `az cloud register` :

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> Az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszer-végponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [set up the VM aliass Endpoint](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Állítsa be az aktív környezetet az alábbi parancsok használatával.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

1. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Bejelentkezhet a Azure Stack hub-környezetbe felhasználóként vagy egyszerű szolgáltatásnévként. 

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

### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/output.png)


## <a name="known-issues"></a>Ismert problémák

Ismert problémák léptek fel a CLI Azure Stack hub-ban való használatakor:

 - A CLI interaktív mód. A parancs például az `az interactive` Azure stack hub még nem támogatja.
 - Azure Stack hub-ban elérhető virtuálisgép-rendszerképek listájának lekéréséhez használja a parancsot a parancs `az vm image list --all` helyett `az vm image list` . A beállítás megadásával `--all` biztosíthatja, hogy a válasz csak az Azure stack hub-környezetben elérhető lemezképeket adja vissza.
 - Előfordulhat, hogy az Azure-ban elérhető virtuálisgép-rendszerkép-aliasok nem alkalmazhatók Azure Stack hubhoz. Virtuálisgép-lemezképek használata esetén a rendszerkép aliasa helyett a teljes URN paramétert (Canonical: UbuntuServer: 14.04.3-LTS: 1.0.0) kell használnia. Ennek az URN-nek meg kell egyeznie a parancsból származtatott rendszerkép-specifikációkkal `az vm images list` .

## <a name="next-steps"></a>Következő lépések

- [Sablonok üzembe helyezése az Azure CLI-vel](../user/azure-stack-deploy-template-command-line.md)
- [Az Azure CLI engedélyezése Azure Stack hub-felhasználók számára (operátor)](../operator/azure-stack-cli-admin.md)
- [Felhasználói engedélyek kezelése](../operator/azure-stack-manage-permissions.md) 