---
title: Azure Stack hub kezelése az Azure CLI-vel
description: Ismerje meg, hogyan kezelheti és helyezheti üzembe az erőforrásokat az Azure Stack hub platformon a platformfüggetlen parancssori felületen (CLI).
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 1fb17516e5ef0b4e3a670703a34e1e895c847b52
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88818844"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>Erőforrások kezelése és üzembe helyezése Azure Stack hubhoz az Azure CLI-vel

A jelen cikkben ismertetett lépéseket követve állíthatja be az Azure parancssori felületét (CLI) a Linux, Mac és Windows rendszerű ügyféloldali platformok Azure Stack Development Kit (ASDK) erőforrásainak kezeléséhez.

## <a name="prepare-for-azure-cli"></a>Felkészülés az Azure CLI-re

Ha a ASDK használja, szüksége lesz az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványára, hogy az Azure CLI-t használja a fejlesztői gépen. A tanúsítvány segítségével kezelheti az erőforrásokat a parancssori felületről.

 - **Az Azure stack hub hitelesítésszolgáltatói főtanúsítványra** akkor van szükség, ha a parancssori FELÜLETET a ASDK kívüli munkaállomásról használja.  

 - **A virtuális gép aliasok végpontja** egy aliast (például "UbuntuLTS" vagy "Win2012Datacenter") biztosít. Ez az alias a virtuális gépek telepítésekor egyetlen paraméterként hivatkozik a lemezkép-közzétevőre, az ajánlatra, az SKU-ra és a verzióra.  

Az alábbi szakaszok azt ismertetik, hogyan kérheti le ezeket az értékeket.

### <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának exportálása

Ha integrált rendszer használatával dolgozik, nem kell exportálnia a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt. Ha a ASDK használja, exportálja a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt egy ASDK.

A ASDK legfelső szintű tanúsítványának exportálása PEM formátumban:

1. Adja meg az Azure Stack hub legfelső szintű tanúsítványának nevét:
    - Jelentkezzen be az Azure Stack hub felhasználói vagy felügyeleti portálján.
    - A címsor közelében kattintson a **biztonságos** elemre.
    - Az előugró ablakban kattintson az **érvényes**elemre.
    - A tanúsítvány ablakban kattintson a tanúsítvány **elérési útja** fülre.
    - Jegyezze fel az Azure Stack hub legfelső szintű tanúsítványának nevét.

    ![Azure Stack hub főtanúsítványa](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Hozzon létre egy Windows rendszerű virtuális gépet Azure stack hub-on](azure-stack-quick-windows-portal.md).

3. Jelentkezzen be a virtuális gépre, nyisson meg egy rendszergazda jogú PowerShell-parancssort, majd futtassa a következő parancsfájlt:

    ```powershell  
      $label = "<the name of your Azure Stack Hub root cert from Step 1>"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

4. Másolja a tanúsítványt a helyi gépre.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>A virtuális gép aliasnevei végpont beállítása

Beállíthat egy nyilvánosan elérhető végpontot, amely egy virtuálisgép-alias fájlt üzemeltet. A VM-alias fájl egy olyan JSON-fájl, amely a rendszerkép köznapi nevét adja meg. A nevet akkor használja, ha a virtuális gépet Azure CLI-paraméterként telepíti.

1. Ha egyéni rendszerképet tesz közzé, jegyezze fel a közzététel során megadott kiadói, ajánlati, SKU-és verziószám-információkat. Ha ez egy rendszerkép a piactéren, megtekintheti az adatokat a ```Get-AzureVMImage``` parancsmag használatával.  

2. Töltse le a [mintát](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a githubról.

3. Hozzon létre egy Storage-fiókot Azure Stack központban. Ha elkészült, hozzon létre egy BLOB-tárolót. Állítsa be a hozzáférési házirendet a "Public" értékre.  

4. Töltse fel a JSON-fájlt az új tárolóba. Ha elkészült, megtekintheti a blob URL-címét. Jelölje ki a blob nevét, majd válassza ki az URL-címet a blob tulajdonságai között.

### <a name="install-or-upgrade-cli"></a>PARANCSSORI felület telepítése vagy frissítése

Jelentkezzen be a fejlesztői munkaállomásra, és telepítse a CLI-t. Azure Stack hub esetében az Azure CLI 2,0-es vagy újabb verziójára van szükség. Az API-profilok legújabb verziójának a parancssori felület aktuális verzióját kell megadnia. A CLI-t az [Azure CLI telepítése](/cli/azure/install-azure-cli) című cikkben ismertetett lépések segítségével telepítheti. 

1. Annak ellenőrzéséhez, hogy a telepítés sikeres volt-e, nyisson meg egy terminált vagy egy parancssorablakot, és futtassa a következő parancsot:

    ```shell
    az --version
    ```

    Ekkor meg kell jelennie az Azure CLI és a számítógépre telepített egyéb függő kódtárak verziójának.

    ![Azure CLI Azure Stack hub Python-helyen](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Jegyezze fel a parancssori felület Python-helyét. Ha a ASDK futtatja, ezt a helyet kell használnia a tanúsítvány hozzáadásához.


## <a name="windows-azure-ad"></a>Windows (Azure AD)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha az Azure AD-t használja Identity Management szolgáltatásként, és a CLI-t használja a Windows rendszerű gépen.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának megbízhatósága

Ha a ASDK használja, meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Ez a lépés nem szükséges az integrált rendszerekhez.

Ha meg szeretné bízni a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát, fűzze hozzá a meglévő Python-tanúsítványtárolóhoz az Azure CLI-vel telepített Python-verzióhoz. Előfordulhat, hogy a Python saját példányát futtatja. Az Azure CLI a Python saját verzióját tartalmazza.

1. Keresse meg a tanúsítványtároló helyét a gépen.  A helyet a parancs futtatásával találja meg  `az --version` .

2. Navigáljon ahhoz a mappához, amely a CLI Python-alkalmazást tartalmazza. Futtatni kívánja a Python ezen verzióját. Ha a Pythont a rendszerútvonalon állította be, a Python futtatása a Python saját verzióját fogja futtatni. Ehelyett futtatni kívánja a CLI által használt verziót, és hozzá kell adnia a tanúsítványt az adott verzióhoz. Előfordulhat például, hogy a CLI Python a következő helyen található: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\` .

    Az alábbi parancsokat használja:

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helyét. Például: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. A megadott elérési út az operációs rendszertől és a CLI-telepítéstől függ.

2. A meglévő Python-tanúsítványhoz való hozzáfűzéssel bízza meg a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

1. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

2. Regisztrálja a környezetét. A következő paraméterek használata a futtatásakor `az cloud register` :

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | A ASDK található **ResourceManagerUrl** : `https://management.local.azurestack.external/` az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszervégponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | `local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
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

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack hub verzióját, akkor az API-verzió Profile **2019-03-01-Hybrid**helyett a **2017-03-09-profil API-** profilt kell használnia. Az Azure CLI legújabb verzióját is használni kell.
 
1. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Jelentkezzen be az Azure Stack hub-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

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

### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha Active Directory összevont szolgáltatásokat (AD FS) használja az Identitáskezelő szolgáltatásként, és a parancssori felületet használja a Windows rendszerű gépen.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának megbízhatósága

Ha a ASDK használja, meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Ez a lépés nem szükséges az integrált rendszerekhez.

1. Keresse meg a tanúsítvány helyét a gépen. A hely változhat attól függően, hogy hol telepítette a Pythont. Nyisson meg egy parancssort vagy egy rendszergazda jogú PowerShell-parancssort, és írja be a következő parancsot:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helyét. Például: `~/lib/python3.5/site-packages/certifi/cacert.pem`. A megadott elérési út az operációs rendszertől és a telepített Python-verziótól függ.

2. A meglévő Python-tanúsítványhoz való hozzáfűzéssel bízza meg a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

1. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

2. Regisztrálja a környezetét. A következő paraméterek használata a futtatásakor `az cloud register` :

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | A ASDK található **ResourceManagerUrl** : `https://management.local.azurestack.external/` az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszervégponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | `local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
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

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack hub verzióját, akkor az API-verzió Profile **2019-03-01-Hybrid**helyett a **2017-03-09-profil API-** profilt kell használnia. Az Azure CLI legújabb verzióját is használni kell.

1. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Bejelentkezhet a Azure Stack hub-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

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

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha az Azure AD-t használja Identity Management szolgáltatásként, és egy Linux rendszerű számítógépen használja a CLI-t.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának megbízhatósága

Ha a ASDK használja, meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Ez a lépés nem szükséges az integrált rendszerekhez.

A meglévő Python-tanúsítványhoz való hozzáfűzéssel bízza meg a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát.

1. Keresse meg a tanúsítvány helyét a gépen. A hely változhat attól függően, hogy hol telepítette a Pythont. Telepítenie kell a pip-et és az üzembe helyező kiépítés modult. Használja a következő Python-parancsot a bash-parancssorból:

    ```bash  
    az --version
    ```

    Jegyezze fel a tanúsítvány helyét. Például: `~/lib/python3.5/site-packages/certifi/cacert.pem`. A megadott elérési út az operációs rendszertől és a telepített Python-verziótól függ.

2. Futtassa a következő bash-parancsot a tanúsítvány elérési útjával.

   - Távoli Linux rendszerű gépek esetén:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - A Azure Stack hub-környezetben található Linux rendszerű gépek esetén:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

A következő lépésekkel csatlakozhat Azure Stack hubhoz:

1. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

2. Regisztrálja a környezetét. A következő paraméterek használata a futtatásakor `az cloud register` :

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | A ASDK található **ResourceManagerUrl** : `https://management.local.azurestack.external/` az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszervégponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | `local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [set up the VM aliass Endpoint](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Állítsa be az aktív környezetet. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack hub verzióját, akkor az API-verzió Profile **2019-03-01-Hybrid**helyett a **2017-03-09-profil API-** profilt kell használnia. Az Azure CLI legújabb verzióját is használni kell.

5. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Bejelentkezhet a Azure Stack hub-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

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

### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
    az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Ez a szakasz végigvezeti a parancssori felület beállításán, ha Active Directory összevont szolgáltatásokat (AD FS) használja felügyeleti szolgáltatásként, és a CLI-t használja egy Linux rendszerű gépen.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának megbízhatósága

Ha a ASDK használja, meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Ez a lépés nem szükséges az integrált rendszerekhez.

A meglévő Python-tanúsítványhoz való hozzáfűzéssel bízza meg a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát.

1. Keresse meg a tanúsítvány helyét a gépen. A hely változhat attól függően, hogy hol telepítette a Pythont. Telepítenie kell a pip-et és az üzembe helyező kiépítés modult. Használja a következő Python-parancsot a bash-parancssorból:

    ```bash  
    az --version 
    ```

    Jegyezze fel a tanúsítvány helyét. Például: `~/lib/python3.5/site-packages/certifi/cacert.pem`. A megadott elérési út az operációs rendszertől és a telepített Python-verziótól függ.

2. Futtassa a következő bash-parancsot a tanúsítvány elérési útjával.

   - Távoli Linux rendszerű gépek esetén:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - A Azure Stack hub-környezetben található Linux rendszerű gépek esetén:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>Kapcsolódás Azure Stack hubhoz

A következő lépésekkel csatlakozhat Azure Stack hubhoz:

1. Regisztrálja Azure Stack hub-környezetét a `az cloud register` parancs futtatásával.

2. Regisztrálja a környezetét. A futtatásakor használja a következő paramétereket `az cloud register` .

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | `AzureStackUser`A felhasználói környezethez használható. Ha az operátor van megadva, akkor a ( `AzureStackAdmin` ) lehetőséget. |
    | Resource Manager-végpont | `https://management.local.azurestack.external` | A ASDK található **ResourceManagerUrl** : `https://management.local.azurestack.external/` az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/` Ha kérdése van az integrált rendszervégponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | helyi. azurestack. external | `local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external` a ASDK. Integrált rendszer esetén használjon végpontot a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [set up the VM aliass Endpoint](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. Állítsa be az aktív környezetet. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Frissítse környezeti konfigurációját az Azure Stack Hub-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack hub verzióját, akkor az API-verzió Profile **2019-03-01-Hybrid**helyett a **2017-03-09-profil API-** profilt kell használnia. Az Azure CLI legújabb verzióját is használni kell.

5. Jelentkezzen be az Azure Stack hub-környezetbe a `az login` paranccsal. Bejelentkezhet a Azure Stack hub-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

6. bejelentkezés: 

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

### <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot egy alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
  az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Ismert problémák

Ismert problémák léptek fel a CLI Azure Stack hub-ban való használatakor:

 - A CLI interaktív mód. A parancs például az `az interactive` Azure stack hub még nem támogatja.
 - Azure Stack hub-ban elérhető virtuálisgép-rendszerképek listájának lekéréséhez használja a parancsot a parancs `az vm image list --all` helyett `az vm image list` . A beállítás megadásával `--all` biztosíthatja, hogy a válasz csak az Azure stack hub-környezetben elérhető lemezképeket adja vissza.
 - Előfordulhat, hogy az Azure-ban elérhető virtuálisgép-rendszerkép-aliasok nem alkalmazhatók Azure Stack hubhoz. Virtuálisgép-lemezképek használata esetén a rendszerkép aliasa helyett a teljes URN paramétert (Canonical: UbuntuServer: 14.04.3-LTS: 1.0.0) kell használnia. Ennek az URN-nek meg kell egyeznie a parancsból származtatott rendszerkép-specifikációkkal `az vm images list` .

## <a name="next-steps"></a>Következő lépések

- [Sablonok üzembe helyezése az Azure CLI-vel](azure-stack-deploy-template-command-line.md)
- [Az Azure CLI engedélyezése Azure Stack hub-felhasználók számára (operátor)](../operator/azure-stack-cli-admin.md)
- [Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md) 
