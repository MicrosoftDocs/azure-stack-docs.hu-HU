---
title: Kapcsolódás Azure Stack a CLI-vel | Microsoft Docs
description: Ismerje meg, hogyan kezelheti és telepítheti az erőforrásokat a többplatformos parancssori felület (CLI) használatával Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 788d0fd6479ab054568d549af2f7a4306a963d3b
ms.sourcegitcommit: 4139b507d6da98a086929da48e3b4661b70bc4f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68299444"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Az API-verziók profiljainak használata az Azure CLI-vel Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A cikkben ismertetett lépéseket követve állíthatja be az Azure parancssori felületét (CLI) a Linux, Mac és Windows rendszerű ügyféloldali platformok Azure Stack Development Kit (ASDK) erőforrásainak kezeléséhez.

## <a name="prepare-for-azure-cli"></a>Felkészülés az Azure CLI-re

Ha a Azure Stack Development Kit használja, szüksége lesz a HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványára Azure Stack számára, hogy az Azure CLI-t használja a fejlesztői gépen. A tanúsítvány segítségével kezelheti az erőforrásokat a parancssori felületről.

 - **A Azure stack hitelesítésszolgáltatói** főtanúsítványra akkor van szükség, ha a parancssori FELÜLETET a ASDK kívüli munkaállomásról használja.  

 - **A virtuálisgép** -aliasok végpontja olyan aliast biztosít, mint a "UbuntuLTS" vagy a "Win2012Datacenter", amely a lemezkép-közzétevőre, az ajánlatra, az SKU-ra és a verzióra hivatkozik egyetlen paraméterként a virtuális gépek telepítésekor.  

Az alábbi szakaszok azt ismertetik, hogyan kérheti le ezeket az értékeket.

### <a name="export-the-azure-stack-ca-root-certificate"></a>A Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának exportálása

Ha integrált rendszer használatát használja, nem kell exportálnia a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt. A HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt egy ASDK kell exportálnia.

A ASDK legfelső szintű tanúsítványának exportálása PEM formátumban:

1. Szerezze be a Azure Stack legfelső szintű tanúsítványának nevét:
    - Jelentkezzen be a Azure Stack bérlő vagy a felügyeleti portálra.
    - Kattintson a "biztonság" gombra a címsor közelében.
    - Az előugró ablakban kattintson az "érvényes" elemre.
    - A tanúsítvány ablakban kattintson a "tanúsítvány elérési útja" fülre. 
    - Jegyezze fel Azure Stack legfelső szintű tanúsítványának nevét.

    ![Főtanúsítvány Azure Stack](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Hozzon létre egy Windows rendszerű virtuális gépet Azure stack](azure-stack-quick-windows-portal.md).

3. Jelentkezzen be a gépre, nyisson meg egy rendszergazda jogú PowerShell-parancssort, majd futtassa a következő parancsfájlt:

    ```powershell  
      $label = "<the name of your azure stack root cert from Step 1>"
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

Beállíthat egy nyilvánosan elérhető végpontot, amely egy virtuális gép alias-fájlját tárolja. A virtuális gép alias fájlja egy olyan JSON-fájl, amely a rendszerkép köznapi nevét adja meg. A nevet akkor fogja használni, ha a virtuális gépet Azure CLI-paraméterként telepíti.

1. Ha egyéni rendszerképet tesz közzé, jegyezze fel a közzététel során megadott kiadói, ajánlati, SKU-és verziószám-információkat. Ha ez egy rendszerkép a piactéren, megtekintheti az adatokat a ```Get-AzureVMImage``` parancsmag használatával.  

2. Töltse le a [mintát](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a githubról.

3. Hozzon létre egy Storage-fiókot Azure Stack. Ha elkészült, hozzon létre egy BLOB-tárolót. Állítsa be a hozzáférési házirendet a "Public" értékre.  

4. Töltse fel a JSON-fájlt az új tárolóba. Ha elkészült, megtekintheti a blob URL-címét. Jelölje ki a blob nevét, majd válassza ki az URL-címet a blob tulajdonságai között.

### <a name="install-or-upgrade-cli"></a>PARANCSSORI felület telepítése vagy frissítése

Jelentkezzen be a fejlesztői munkaállomásra, és telepítse a CLI-t. Azure Stack az Azure CLI 2,0-es vagy újabb verziójára van szükség. Az API-profilok legújabb verziójának a parancssori felület aktuális verzióját kell megadnia.  A CLI-t az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) című cikkben ismertetett lépések segítségével telepítheti. 

1. Annak ellenőrzéséhez, hogy a telepítés sikeres volt-e, nyisson meg egy terminált vagy egy parancssorablakot, és futtassa a következő parancsot:

    ```shell
    az --version
    ```

    Ekkor meg kell jelennie az Azure CLI és a számítógépre telepített egyéb függő kódtárak verziójának.

    ![Azure CLI Azure Stack Python-helyen](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Jegyezze fel a parancssori felület Python-helyét. Ha a ASDK futtatja, ezt a helyet kell használnia a tanúsítvány hozzáadásához.


## <a name="windows-azure-ad"></a>Windows (Azure AD)

Ebből a szakaszból megtudhatja, hogyan állíthatja be a CLI-t, ha az Azure AD-t használja Identity Management szolgáltatásként, és a CLI-t használja a Windows rendszerű gépen.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>A Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvány megbízhatóságának megtartása

Ha a ASDK használja, akkor meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Ezt nem kell megtennie az integrált rendszerekkel.

Ha meg szeretné bízni a Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt, fűzze hozzá a meglévő Python tanúsítványtárolóhoz az Azure CLI-vel telepített Python verzióhoz. Előfordulhat, hogy a Python saját példányát futtatja. Az Azure CLI a Python saját verzióját tartalmazza.

1. Keresse meg a tanúsítványtároló helyét a gépen.  A helyet a parancs `az --version`futtatásával találja meg.

2. Navigáljon ahhoz a mappához, amely a CLI Python-alkalmazást tartalmazza. Futtatnia kell a Python ezen verzióját. Ha a Pythont beállította a rendszer elérési útjában, a Python futtatása a Python saját verzióját fogja futtatni. Ehelyett futtatnia kell a CLI által használt verziót, és hozzá kell adnia a tanúsítványt az adott verzióhoz. Előfordulhat például, hogy a CLI Python a következő helyen `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`található:.

    Használja az alábbi parancsokat:

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helyét. Például: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. Az adott útvonal az operációs rendszertől és a CLI-telepítéstől függ.

2. Bízza a Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát úgy, hogy hozzáfűzi a meglévő Python-tanúsítványhoz.

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

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

1. Regisztrálja Azure stack-környezetét a `az cloud register` parancs futtatásával.

    Bizonyos helyzetekben a közvetlen kimenő internetkapcsolatot egy proxyn vagy tűzfalon keresztül irányítjuk, amely kikényszeríti az SSL-elfogást. Ezekben az esetekben a `az cloud register` parancs sikertelen lehet, például "nem sikerült beolvasni a végpontokat a felhőből." A hiba megkerüléséhez a következő környezeti változókat állíthatja be:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Regisztrálja a környezetét. A futtatásakor `az cloud register`használja a következő paramétereket.

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | A `AzureStackUser` felhasználói környezethez használható. Ha Ön az operátor, akkor `AzureStackAdmin`válassza a lehetőséget. |
    | Resource Manager-végpont | https://management.local.azurestack.external | A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external/`Az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/`A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`Ha kérdése van az integrált rendszer-végponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | local.azurestack.external | `local.azurestack.external`a ASDK. Integrált rendszer esetén végpontot kíván használni a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external`a ASDK. Integrált rendszer esetén végpontot kíván használni a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [# # # állítsa be a virtuális gép aliasok végpontját](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Állítsa be az aktív környezetet az alábbi parancsok használatával.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Frissítse környezeti konfigurációját a Azure Stack-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack verzióját, akkor az API Version Profile **2017-03-09-profilt** kell használnia, és nem az API **-verzió profilt 2019-03-01-Hybrid**. Az Azure CLI legújabb verzióját kell használnia.
 
1. Jelentkezzen be a Azure stack-környezetbe a `az login` parancs használatával. Bejelentkezhet a Azure Stack-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Bejelentkezés felhasználóként: 

     Megadhatja a felhasználónevet és a jelszót közvetlenül a `az login` parancson belül, vagy egy böngészőben végezheti el a hitelesítést. Ha a fiókjában engedélyezve van a többtényezős hitelesítés, az utóbbit el kell végeznie:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Ha a felhasználói fiókjában engedélyezve van a többtényezős hitelesítés, a `az login` parancsot a `-u` paraméter megadása nélkül is használhatja. A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.

   - Bejelentkezés *egyszerű szolgáltatásként*: 
    
     A bejelentkezés előtt hozzon [létre egy egyszerű szolgáltatásnevet a Azure Portal vagy a](azure-stack-create-service-principals.md) parancssori felület használatával, és rendeljen hozzá egy szerepkört. Most jelentkezzen be a következő parancs használatával:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stackon belül. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Ebből a szakaszból megtudhatja, hogyan állíthatja be a CLI-t, ha Active Directory összevont szolgáltatásokat (AD FS) használ az Identitáskezelő szolgáltatásként, és a parancssori felületet használja a Windows rendszerű gépen.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>A Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvány megbízhatóságának megtartása

Ha a ASDK használja, akkor meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Ezt nem kell megtennie az integrált rendszerekkel.

1. Keresse meg a tanúsítvány helyét a gépen. A hely változhat attól függően, hogy hol telepítette a Pythont. Nyisson meg egy parancssort vagy egy rendszergazda jogú PowerShell-parancssort, és írja be a következő parancsot:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helyét. Például: `~/lib/python3.5/site-packages/certifi/cacert.pem`. A megadott elérési út az operációs rendszertől és a telepített Python-verziótól függ.

2. Bízza a Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát úgy, hogy hozzáfűzi a meglévő Python-tanúsítványhoz.

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

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

1. Regisztrálja Azure stack-környezetét a `az cloud register` parancs futtatásával.

    Bizonyos helyzetekben a közvetlen kimenő internetkapcsolatot egy proxyn vagy tűzfalon keresztül irányítjuk, amely kikényszeríti az SSL-elfogást. Ezekben az esetekben a `az cloud register` parancs sikertelen lehet, például "nem sikerült beolvasni a végpontokat a felhőből." A hiba megkerüléséhez a következő környezeti változókat állíthatja be:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Regisztrálja a környezetét. A futtatásakor `az cloud register`használja a következő paramétereket.

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | A `AzureStackUser` felhasználói környezethez használható. Ha Ön az operátor, akkor `AzureStackAdmin`válassza a lehetőséget. |
    | Resource Manager-végpont | https://management.local.azurestack.external | A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external/`Az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/`A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`Ha kérdése van az integrált rendszer-végponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | local.azurestack.external | `local.azurestack.external`a ASDK. Integrált rendszer esetén végpontot kíván használni a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external`a ASDK. Integrált rendszer esetén végpontot kíván használni a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [# # # állítsa be a virtuális gép aliasok végpontját](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Állítsa be az aktív környezetet az alábbi parancsok használatával.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Frissítse környezeti konfigurációját a Azure Stack-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack verzióját, akkor az API Version Profile **2017-03-09-profilt** kell használnia, és nem az API **-verzió profilt 2019-03-01-Hybrid**. Az Azure CLI legújabb verzióját kell használnia.

1. Jelentkezzen be a Azure stack-környezetbe a `az login` parancs használatával. Bejelentkezhet a Azure Stack-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Bejelentkezés felhasználóként:

     Megadhatja a felhasználónevet és a jelszót közvetlenül a `az login` parancson belül, vagy egy böngészőben végezheti el a hitelesítést. Ha a fiókjában engedélyezve van a többtényezős hitelesítés, az utóbbit el kell végeznie:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Ha a felhasználói fiókjában engedélyezve van a többtényezős hitelesítés, a `az login` parancsot a `-u` paraméter megadása nélkül is használhatja. A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.

   - Bejelentkezés *egyszerű szolgáltatásként*: 
    
     Készítse elő a. PEM-fájlt, amelyet a szolgáltatás egyszerű bejelentkezéséhez kíván használni.

     Azon az ügyfélszámítógépen, ahol a rendszerbiztonsági tag létrejött, exportálja az egyszerű szolgáltatás tanúsítványát pfx-ként a következő helyen `cert:\CurrentUser\My`található titkos kulccsal: a tanúsítvány neve megegyezik a rendszerbiztonsági tag nevével.

     Alakítsa át a pfx-t PEM-ra (használja az OpenSSL segédprogramot).

     Jelentkezzen be a CLI-be:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stackon belül. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Ebből a szakaszból megtudhatja, hogyan állíthatja be a CLI-t, ha az Azure AD-t használja Identity Management szolgáltatásként, és a CLI-t használja egy Linux rendszerű gépen.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>A Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvány megbízhatóságának megtartása

Ha a ASDK használja, akkor meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Ezt nem kell megtennie az integrált rendszerekkel.

Bízza a Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát úgy, hogy hozzáfűzi a meglévő Python-tanúsítványhoz.

1. Keresse meg a tanúsítvány helyét a gépen. A hely változhat attól függően, hogy hol telepítette a Pythont. Telepítenie kell a pip-et és az üzembe helyező kisegítő modult. A bash parancssorból a következő Python-parancsot használhatja:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helyét; például `~/lib/python3.5/site-packages/certifi/cacert.pem`:. A megadott elérési út az operációs rendszertől és a telepített Python-verziótól függ.

2. Futtassa a következő bash-parancsot a tanúsítvány elérési útjával.

   - Távoli Linux rendszerű gépek esetén:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - A Azure Stack környezetben található Linux rendszerű gépek esetén:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

A következő lépésekkel csatlakozhat a Azure Stackhoz:

1. Regisztrálja Azure stack-környezetét a `az cloud register` parancs futtatásával. Bizonyos helyzetekben a közvetlen kimenő internetkapcsolatot egy proxyn vagy tűzfalon keresztül irányítjuk, amely kikényszeríti az SSL-elfogást. Ezekben az esetekben a `az cloud register` parancs sikertelen lehet, például "nem sikerült beolvasni a végpontokat a felhőből." A hiba megkerüléséhez a következő környezeti változókat állíthatja be:

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Regisztrálja a környezetét. A futtatásakor `az cloud register`használja a következő paramétereket.

    | Value | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | A `AzureStackUser` felhasználói környezethez használható. Ha Ön az operátor, akkor `AzureStackAdmin`válassza a lehetőséget. |
    | Resource Manager-végpont | https://management.local.azurestack.external | A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external/`Az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/`A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`Ha kérdése van az integrált rendszer-végponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | local.azurestack.external | `local.azurestack.external`a ASDK. Integrált rendszer esetén végpontot kíván használni a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external`a ASDK. Integrált rendszer esetén végpontot kíván használni a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [# # # állítsa be a virtuális gép aliasok végpontját](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Állítsa be az aktív környezetet. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Frissítse környezeti konfigurációját a Azure Stack-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack verzióját, akkor az API Version Profile **2017-03-09-profilt** kell használnia, és nem az API **-verzió profilt 2019-03-01-Hybrid**. Az Azure CLI legújabb verzióját kell használnia.

5. Jelentkezzen be a Azure stack-környezetbe a `az login` parancs használatával. Bejelentkezhet a Azure Stack-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Bejelentkezés felhasználóként:

     Megadhatja a felhasználónevet és a jelszót közvetlenül a `az login` parancson belül, vagy egy böngészőben végezheti el a hitelesítést. Ha a fiókjában engedélyezve van a többtényezős hitelesítés, az utóbbit el kell végeznie:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Ha a felhasználói fiókjában engedélyezve van a többtényezős hitelesítés, a `az login` parancsot a `-u` paraméter megadása nélkül is használhatja. A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.
   
   * Bejelentkezés *egyszerű szolgáltatásként*
    
     A bejelentkezés előtt hozzon [létre egy egyszerű szolgáltatásnevet a Azure Portal vagy a](azure-stack-create-service-principals.md) parancssori felület használatával, és rendeljen hozzá egy szerepkört. Most jelentkezzen be a következő parancs használatával:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stackon belül. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
    az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Ebből a szakaszból megtudhatja, hogyan állíthatja be a CLI-t, ha Active Directory összevont szolgáltatásokat (AD FS) használ a felügyeleti szolgáltatásként, és egy Linux rendszerű számítógépen használja a CLI-t.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>A Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvány megbízhatóságának megtartása

Ha a ASDK használja, akkor meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Ezt nem kell megtennie az integrált rendszerekkel.

Bízza a Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát úgy, hogy hozzáfűzi a meglévő Python-tanúsítványhoz.

1. Keresse meg a tanúsítvány helyét a gépen. A hely változhat attól függően, hogy hol telepítette a Pythont. Telepítenie kell a pip-et és az üzembe helyező kisegítő modult. A bash parancssorból a következő Python-parancsot használhatja:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helyét; például `~/lib/python3.5/site-packages/certifi/cacert.pem`:. A megadott elérési út az operációs rendszertől és a telepített Python-verziótól függ.

2. Futtassa a következő bash-parancsot a tanúsítvány elérési útjával.

   - Távoli Linux rendszerű gépek esetén:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - A Azure Stack környezetben található Linux rendszerű gépek esetén:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

A következő lépésekkel csatlakozhat a Azure Stackhoz:

1. Regisztrálja Azure stack-környezetét a `az cloud register` parancs futtatásával. Bizonyos helyzetekben a közvetlen kimenő internetkapcsolatot egy proxyn vagy tűzfalon keresztül irányítjuk, amely kikényszeríti az SSL-elfogást. Ezekben az esetekben a `az cloud register` parancs sikertelen lehet, például "nem sikerült beolvasni a végpontokat a felhőből." A hiba megkerüléséhez a következő környezeti változókat állíthatja be:

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Regisztrálja a környezetét. A futtatásakor `az cloud register`használja a következő paramétereket.

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | A `AzureStackUser` felhasználói környezethez használható. Ha Ön az operátor, akkor `AzureStackAdmin`válassza a lehetőséget. |
    | Resource Manager-végpont | https://management.local.azurestack.external | A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external/`Az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/`A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`Ha kérdése van az integrált rendszer-végponttal kapcsolatban, forduljon a felhő üzemeltetőjéhez. |
    | Tárolási végpont | local.azurestack.external | `local.azurestack.external`a ASDK. Integrált rendszer esetén végpontot kíván használni a rendszer számára.  |
    | Kulcstartó utótagja | . Vault. local. azurestack. external | `.vault.local.azurestack.external`a ASDK. Integrált rendszer esetén végpontot kíván használni a rendszer számára.  |
    | VM-rendszerkép aliasa doc-végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuális gép rendszerképének aliasait tartalmazó dokumentum URI azonosítója. További információ: [# # # állítsa be a virtuális gép aliasok végpontját](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Állítsa be az aktív környezetet. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Frissítse környezeti konfigurációját a Azure Stack-specifikus API-verzió profiljának használatára. A konfiguráció frissítéséhez futtassa a következő parancsot:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha a 1808-es verzió előtt futtatja Azure Stack verzióját, akkor az API Version Profile **2017-03-09-profilt** kell használnia, és nem az API **-verzió profilt 2019-03-01-Hybrid**. Az Azure CLI legújabb verzióját kell használnia.

5. Jelentkezzen be a Azure stack-környezetbe a `az login` parancs használatával. Bejelentkezhet a Azure Stack-környezetbe felhasználóként vagy [egyszerű szolgáltatásnévként](/azure/active-directory/develop/app-objects-and-service-principals). 

6. bejelentkezés: 

   *  Felhasználóként  egy, az eszköz kódját használó webböngésző használatával:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >A parancs futtatásával egy URL-címet és egy kódot kell használnia a hitelesítéshez.

   * Egyszerű szolgáltatásnév:
        
     Készítse elő a. PEM-fájlt, amelyet a szolgáltatás egyszerű bejelentkezéséhez kíván használni.

      * Azon az ügyfélszámítógépen, ahol a rendszerbiztonsági tag létrejött, exportálja az egyszerű szolgáltatás tanúsítványát pfx-ként a következő helyen `cert:\CurrentUser\My`található titkos kulccsal: a tanúsítvány neve megegyezik a rendszerbiztonsági tag nevével.
  
      * Alakítsa át a pfx-t PEM-ra (használja az OpenSSL segédprogramot).

     Jelentkezzen be a CLI-be:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Minden beállításnál a CLI használatával hozhat létre erőforrásokat Azure Stackon belül. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő parancs használatával hozzon létre egy "MyResourceGroup" nevű erőforráscsoportot:

```azurecli
  az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport sikeresen létrejött, az előző parancs kimenete az újonnan létrehozott erőforrás következő tulajdonságait eredményezi:

![Erőforráscsoport kimenet létrehozása](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Ismert problémák

Ismert problémák merültek fel a parancssori felület használatakor Azure Stackban:

 - A CLI interaktív mód; a Azure stack például a `az interactive` parancs még nem támogatott.
 - A Azure Stackban elérhető virtuálisgép-rendszerképek listájának lekéréséhez használja `az vm image list --all` a parancsot a parancs `az vm image list` helyett. A `--all` beállítás megadásával biztosíthatja, hogy a válasz csak a Azure stack-környezetben elérhető lemezképeket adja vissza.
 - Előfordulhat, hogy az Azure-ban elérhető virtuálisgép-rendszerkép-aliasok nem alkalmazhatók Azure Stackra. Virtuálisgép-lemezképek használata esetén a rendszerkép aliasa helyett a teljes URN paramétert (Canonical: UbuntuServer: 14.04.3-LTS: 1.0.0) kell használnia. Ennek az urn-nek meg kell egyeznie a `az vm images list` parancsból származtatott rendszerkép-specifikációkkal.

## <a name="next-steps"></a>További lépések

- [Sablonok üzembe helyezése az Azure CLI-vel](azure-stack-deploy-template-command-line.md)
- [Az Azure CLI engedélyezése Azure Stack felhasználók számára (operátor)](../operator/azure-stack-cli-admin.md)
- [Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md) 
