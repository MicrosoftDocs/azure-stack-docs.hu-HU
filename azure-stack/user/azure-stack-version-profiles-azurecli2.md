---
title: Csatlakozás az Azure Stack parancssori felülettel |} A Microsoft Docs
description: Az Azure Stacken erőforrásokat üzembe helyezheti és kezelheti a többplatformos parancssori felület (CLI) használata
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 7dba3d791a2ce19429506699ae538c747ed37dbd
ms.sourcegitcommit: 7ceddf8b67f920d5a5eef4a84e157079a53b3374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2019
ms.locfileid: "66821785"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>API-verzióprofilok használata az Azure CLI-vel az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A lépésekkel állíthatja be az Azure parancssori felület (CLI) Azure Stack Development Kit (ASDK) erőforrások kezelése a Linux, Mac és Windows-ügyfélplatformokon ebben a cikkben.

## <a name="prepare-for-azure-cli"></a>Készítse elő az Azure CLI-hez

Szüksége lesz a legfelső szintű hitelesítésszolgáltató tanúsítványát az Azure Stackhez az Azure CLI használata a fejlesztési számítógépén. A CLI-n keresztül-erőforrások kezeléséhez használhatja a tanúsítványt.

 - **Az Azure Stack hitelesítésszolgáltató főtanúsítványát** szükség, ha a parancssori felület kívül a ASDK munkaállomásokon használja.  

 - **A virtuális gép aliasok végpontjának** alias, mint a "UbuntuLTS" vagy "Win2012Datacenter," által hivatkozott egy rendszerkép közzétevője, ajánlat, Termékváltozat és verzió egyetlen paramétert, ha a virtuális gépek üzembe helyezéséhez biztosít.  

A következő szakaszok ismertetik ezeket az értékeket beszerzése.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató legfelső szintű tanúsítványának exportálása

Ha egy integrált rendszer használ, nem kell a legfelső szintű hitelesítésszolgáltató tanúsítványának exportálása. Szüksége lesz egy ASDK a legfelső szintű hitelesítésszolgáltató tanúsítványának exportálása.

PEM-formátumú ASDK legfelső szintű tanúsítványának exportálásához:

1. Kérje le az Azure Stack legfelső szintű tanúsítvány neve:
    - Jelentkezzen be az Azure Stack-bérlő vagy a felügyeleti portálra.
    - Kattintson a "Biztonságos" közel címet a böngésző címsorába.
    - Az előugró ablakban kattintson az "Érvényes".
    - A tanúsítvány ablakban kattintson a "Tanúsítványláncba" fülre. 
    - Jegyezze fel az Azure Stack legfelső szintű tanúsítvány nevére.

    ![Az Azure Stack főtanúsítványának](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Egy Windows virtuális gép létrehozása az Azure Stackben](azure-stack-quick-windows-portal.md).

3. Jelentkezzen be a gépre, nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő szkriptet:

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

4. Másolja a tanúsítványt a helyi gépen.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>A virtuális gép aliasok végpontjának beállítása

Beállíthat egy nyilvánosan elérhető végponton, amelyen egy virtuális gép alias fájlt. A virtuális gép alias fájl nem egy JSON-fájl által biztosított rendszerképek köznapi neve. A nevet fogja használni, ha telepít egy virtuális Gépet az Azure CLI-vel paraméterként.

1. Ha egy egyéni rendszerkép közzététele, jegyezze fel a közzétevő, ajánlat, Termékváltozat és verzió közzététele során megadott információk. Ha egy lemezképet a marketplace webhelyről, az információk használatával megtekintheti a ```Get-AzureVMImage``` parancsmagot.  

2. Töltse le a [mintafájl](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a Githubról.

3. Hozzon létre egy tárfiókot az Azure Stackben. Ha ez megtörtént, hozzon létre egy blobtárolót. A hozzáférési szabályzat a "nyilvános".  

4. Töltse fel az új tároló a JSON-fájlt. Ha ez megtörtént, megtekintheti a blob URL-CÍMÉT. Válassza ki a blob nevét, és válassza az URL-címet a blob tulajdonságai közül.

### <a name="install-or-upgrade-cli"></a>Telepítse vagy frissítse a parancssori felület

Jelentkezzen be a fejlesztő munkaállomás, és telepítse a parancssori felület. Az Azure Stack 2.0 vagy az Azure CLI-vel újabb verziója szükséges. Az API-profilok legújabb verzióját a CLI aktuális verziója szükséges.  Ismertetett lépéseket követve telepítheti a parancssori felület a [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) cikk. 

1. Annak ellenőrzéséhez, hogy a telepítés sikeres volt-e, nyisson meg egy terminált vagy parancssort, és futtassa a következő parancsot:

    ```shell
    az --version
    ```

    Az Azure CLI és a számítógépen telepített függő könyvtárak verzióját kell megjelennie.

    ![Az Azure CLI az Azure Stack Python-hely](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Jegyezze meg a parancssori felület Python-hely. A ASDK futtatásakor meg kell ezen a helyen a tanúsítványt.


## <a name="windows-azure-ad"></a>Windows (az Azure AD)

Ez a szakasz végigvezeti beállítása CLI Ha használja az Azure AD, az identity management-szolgáltatás, és a parancssori felület használata a Windows-gépen.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató főtanúsítványát a megbízható

A ASDK használja, ha megbízik a legfelső szintű hitelesítésszolgáltató tanúsítványát a távoli gépen kell. Nem kell ehhez az integrált rendszerekkel.

Az Azure Stack hitelesítésszolgáltató főtanúsítványát megbízhatóként hozzáfűzése a meglévő Python tanúsítványtárolójába a Python-verzió telepítve van az Azure CLI használatával. Előfordulhat, hogy a saját Python-példányt futtató. Az Azure parancssori felület Python saját verzióját tartalmazza.

1. Keresse meg a gépén a tanúsítványtár helye.  A hely találhatja meg a következő parancs futtatásával `az --version`.

2. Lépjen abba a mappába, amely tartalmazza azt a parancssori felület Python-alkalmazás használ. Érdemes a python verzióját futtatják. Ha a rendszer elérési ÚTJA állította be a Python, a Python futtatásának végrehajtja Python saját verzióját. Ehelyett érdemes a CLI által használt verziója fut, és adja hozzá a tanúsítvány azt a verziót. A parancssori felület Python lehet például: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`.

    A következő parancsokat használja:

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helye. Például: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. Az adott elérési út az operációs rendszer és a CLI telepítés függ.

2. Az Azure Stack hitelesítésszolgáltató főtanúsítványát megbízhatósági a meglévő Python-tanúsítvány hozzáfűzésével.

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

1. Regisztrálja az Azure Stack környezettel futtatásával a `az cloud register` parancsot.

    Bizonyos esetekben közvetlen kimenő internetkapcsolattal áthalad egy proxy vagy tűzfal, amely érvényesíti az SSL-hozzáférés. Ezekben az esetekben a `az cloud register` parancs például a "Nem lehet lekérni végpontok a felhőben." hiba miatt sikertelen lehet Ez a hiba elkerüléséhez állíthatja be az alábbi környezeti változókat:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Regisztrálja a környezetben. Használja az alábbi paramétereket futtatásakor `az cloud register`.

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | Használat `AzureStackUser` a felhasználói környezet. Ha Ön operátor, adja meg `AzureStackAdmin`. |
    | Resource Manager-végpont | https://management.local.azurestack.external | A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` A **ResourceManagerUrl** integrált rendszerek van: `https://management.<region>.<fqdn>/` A szükséges metaadatokat lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Ha a rendszer integrált végpont kérdése van, lépjen kapcsolatba a felhő üzemeltetője. |
    | Storage-végpont | local.azurestack.external | `local.azurestack.external` a ASDK szól. Egy integrált rendszer érdemes a rendszer egy végpontot használja.  |
    | Keyvault-utótag | .vault.local.azurestack.external | `.vault.local.azurestack.external` a ASDK szól. Egy integrált rendszer érdemes a rendszer egy végpontot használja.  |
    | Virtuálisgép-lemezkép alias doc végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuálisgép-lemezkép aliasok tartalmazó dokumentum URI-t. További információkért lásd: [### állítsa be a virtuális gép aliasok végpontjának](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Állítsa be az aktív környezetet az alábbi parancsok használatával.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Frissítés az Ön környezetének konfigurációját az Azure Stack meghatározott API verzió profil használatára. Frissítse a konfigurációt, futtassa a következő parancsot:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha egy Azure Stack verziója előtt a 1808 build futtat, az API-verzióprofil kell használnia **2017-03-09-profile** ahelyett, hogy az API-verzióprofil **2019-03-01-hibrid**. Szüksége lesz egy az Azure CLI legújabb verzióját használja.
 
1. Jelentkezzen be az Azure Stack-környezet használatával a `az login` parancsot. Bejelentkezhet az Azure Stack-környezet vagy egy felhasználó vagy egy [szolgáltatásnév](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Jelentkezzen be egy *felhasználói*: 

     Megadhatja a felhasználónevet és jelszót közvetlenül belül a `az login` parancsot, vagy hitelesíteni tudja a webböngésző használatával. Az utóbbi kell tennie, ha a fiók rendelkezik-e többtényezős hitelesítés engedélyezve:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Ha a felhasználói fiók rendelkezik a többtényezős hitelesítés engedélyezve van, használhatja a `az login` parancsot anélkül, hogy a `-u` paraméter. A következő parancs futtatásával lehetővé teszi egy URL-CÍMÉT és a egy kódot, amely hitelesítést kell használnia.

   - Jelentkezzen be egy *szolgáltatásnév*: 
    
     Amikor bejelentkezik, [hozzon létre egy egyszerű szolgáltatást az Azure Portalon keresztül](azure-stack-create-service-principals.md) vagy a parancssori felület, és rendelje hozzá egy szerepkörhöz. Most jelentkezzen be a következő paranccsal:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Mindent, a belül az Azure Stack-erőforrások létrehozása a CLI használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport létrehozása sikeresen megtörtént, az előző parancs megjeleníti az újonnan létrehozott erőforrás következő tulajdonságai:

![Erőforráscsoport létrehozása a kimenet](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Ez a szakasz végigvezeti beállítása a parancssori felület, ha az Active Directory összevonási szolgáltatásokban (AD FS) használ, az identity management-szolgáltatás, és a parancssori felület használata a Windows-gépen.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató főtanúsítványát a megbízható

A ASDK használja, ha megbízik a legfelső szintű hitelesítésszolgáltató tanúsítványát a távoli gépen kell. Nem kell ehhez az integrált rendszerekkel.

1. Keresse meg a tanúsítvány helye a gépen. A hely eltérőek lehetnek attól függően, hol telepítette a Python. Nyisson meg egy parancssort, vagy egy rendszergazda jogú PowerShell-parancssort, és írja be a következő parancsot:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helye. Például: `~/lib/python3.5/site-packages/certifi/cacert.pem`. Az operációs rendszer és a Python telepített verzióját az adott elérési út függ.

2. Az Azure Stack hitelesítésszolgáltató főtanúsítványát megbízhatósági a meglévő Python-tanúsítvány hozzáfűzésével.

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

1. Regisztrálja az Azure Stack környezettel futtatásával a `az cloud register` parancsot.

    Bizonyos esetekben közvetlen kimenő internetkapcsolattal áthalad egy proxy vagy tűzfal, amely érvényesíti az SSL-hozzáférés. Ezekben az esetekben a `az cloud register` parancs például a "Nem lehet lekérni végpontok a felhőben." hiba miatt sikertelen lehet Ez a hiba elkerüléséhez állíthatja be az alábbi környezeti változókat:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Regisztrálja a környezetben. Használja az alábbi paramétereket futtatásakor `az cloud register`.

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | Használat `AzureStackUser` a felhasználói környezet. Ha Ön operátor, adja meg `AzureStackAdmin`. |
    | Resource Manager-végpont | https://management.local.azurestack.external | A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` A **ResourceManagerUrl** integrált rendszerek van: `https://management.<region>.<fqdn>/` A szükséges metaadatokat lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Ha a rendszer integrált végpont kérdése van, lépjen kapcsolatba a felhő üzemeltetője. |
    | Storage-végpont | local.azurestack.external | `local.azurestack.external` a ASDK szól. Egy integrált rendszer érdemes a rendszer egy végpontot használja.  |
    | Keyvault-utótag | .vault.local.azurestack.external | `.vault.local.azurestack.external` a ASDK szól. Egy integrált rendszer érdemes a rendszer egy végpontot használja.  |
    | Virtuálisgép-lemezkép alias doc végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuálisgép-lemezkép aliasok tartalmazó dokumentum URI-t. További információkért lásd: [### állítsa be a virtuális gép aliasok végpontjának](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Állítsa be az aktív környezetet az alábbi parancsok használatával.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Frissítés az Ön környezetének konfigurációját az Azure Stack meghatározott API verzió profil használatára. Frissítse a konfigurációt, futtassa a következő parancsot:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha egy Azure Stack verziója előtt a 1808 build futtat, az API-verzióprofil kell használnia **2017-03-09-profile** ahelyett, hogy az API-verzióprofil **2019-03-01-hibrid**. Szüksége lesz egy az Azure CLI legújabb verzióját használja.

1. Jelentkezzen be az Azure Stack-környezet használatával a `az login` parancsot. Bejelentkezhet az Azure Stack-környezet vagy egy felhasználó vagy egy [szolgáltatásnév](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Jelentkezzen be egy *felhasználói*:

     Megadhatja a felhasználónevet és jelszót közvetlenül belül a `az login` parancsot, vagy hitelesíteni tudja a webböngésző használatával. Az utóbbi kell tennie, ha a fiók rendelkezik-e többtényezős hitelesítés engedélyezve:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Ha a felhasználói fiók rendelkezik a többtényezős hitelesítés engedélyezve van, használhatja a `az login` parancsot anélkül, hogy a `-u` paraméter. A következő parancs futtatásával lehetővé teszi egy URL-CÍMÉT és a egy kódot, amely hitelesítést kell használnia.

   - Jelentkezzen be egy *szolgáltatásnév*: 
    
     Készítse elő a .pem-fájlt a szolgáltatás egyszerű bejelentkezéshez használandó.

     Az ügyfélszámítógépen, ahol a rendszerbiztonsági tag lett létrehozva, exportálja a szolgáltatásnév helyén található a pfx titkos kulccsal `cert:\CurrentUser\My`; a tanúsítvány neve rendelkezik az egyszerű azonos néven.

     A pfx konvertálható pem (az OpenSSL segédprogramot használja).

     Jelentkezzen be a parancssori felület:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Mindent, a belül az Azure Stack-erőforrások létrehozása a CLI használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```azurecli
az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport létrehozása sikeresen megtörtént, az előző parancs megjeleníti az újonnan létrehozott erőforrás következő tulajdonságai:

![Erőforráscsoport létrehozása a kimenet](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Ez a szakasz végigvezeti beállítása CLI Ha használja az Azure AD, az identity management-szolgáltatás, és a parancssori felület használata Linux rendszerű gépen.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató főtanúsítványát a megbízható

A ASDK használja, ha megbízik a legfelső szintű hitelesítésszolgáltató tanúsítványát a távoli gépen kell. Nem kell ehhez az integrált rendszerekkel.

Az Azure Stack hitelesítésszolgáltató főtanúsítványát megbízhatósági a meglévő Python-tanúsítvány hozzáfűzésével.

1. Keresse meg a tanúsítvány helye a gépen. A hely eltérőek lehetnek attól függően, hol telepítette a Python. Szüksége lesz a pipet és a certifi modul telepítve van. A bash használatával a következő Python-parancsot használhatja:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helye; Ha például `~/lib/python3.5/site-packages/certifi/cacert.pem`. A megadott elérési út attól függ, az operációs rendszer és a Python telepített verzióját.

2. Futtassa az alábbi bash-parancs az elérési útját a tanúsítványhoz.

   - Egy távoli Linux gépen:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Linuxos gépek az Azure Stack-környezetben:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

A következő lépések segítségével csatlakozhat az Azure Stack:

1. Regisztrálja az Azure Stack környezettel futtatásával a `az cloud register` parancsot. Bizonyos esetekben közvetlen kimenő internetkapcsolattal áthalad egy proxy vagy tűzfal, amely érvényesíti az SSL-hozzáférés. Ezekben az esetekben a `az cloud register` parancs például a "Nem lehet lekérni végpontok a felhőben." hiba miatt sikertelen lehet Ez a hiba elkerüléséhez állíthatja be az alábbi környezeti változókat:

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Regisztrálja a környezetben. Használja az alábbi paramétereket futtatásakor `az cloud register`.

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | Használat `AzureStackUser` a felhasználói környezet. Ha Ön operátor, adja meg `AzureStackAdmin`. |
    | Resource Manager-végpont | https://management.local.azurestack.external | A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` A **ResourceManagerUrl** integrált rendszerek van: `https://management.<region>.<fqdn>/` A szükséges metaadatokat lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Ha a rendszer integrált végpont kérdése van, lépjen kapcsolatba a felhő üzemeltetője. |
    | Storage-végpont | local.azurestack.external | `local.azurestack.external` a ASDK szól. Egy integrált rendszer érdemes a rendszer egy végpontot használja.  |
    | Keyvault-utótag | .vault.local.azurestack.external | `.vault.local.azurestack.external` a ASDK szól. Egy integrált rendszer érdemes a rendszer egy végpontot használja.  |
    | Virtuálisgép-lemezkép alias doc végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuálisgép-lemezkép aliasok tartalmazó dokumentum URI-t. További információkért lásd: [### állítsa be a virtuális gép aliasok végpontjának](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Állítsa be az aktív környezetet. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Frissítés az Ön környezetének konfigurációját az Azure Stack meghatározott API verzió profil használatára. Frissítse a konfigurációt, futtassa a következő parancsot:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha egy Azure Stack verziója előtt a 1808 build futtat, az API-verzióprofil kell használnia **2017-03-09-profile** ahelyett, hogy az API-verzióprofil **2019-03-01-hibrid**. Szüksége lesz egy az Azure CLI legújabb verzióját használja.

5. Jelentkezzen be az Azure Stack-környezet használatával a `az login` parancsot. Bejelentkezhet az Azure Stack-környezet vagy egy felhasználó vagy egy [szolgáltatásnév](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Jelentkezzen be egy *felhasználói*:

     Megadhatja a felhasználónevet és jelszót közvetlenül belül a `az login` parancsot, vagy hitelesíteni tudja a webböngésző használatával. Az utóbbi kell tennie, ha a fiók rendelkezik-e többtényezős hitelesítés engedélyezve:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Ha a felhasználói fiók rendelkezik a többtényezős hitelesítés engedélyezve van, használhatja a `az login` parancsot anélkül, hogy a `-u` paraméter. A következő parancs futtatásával lehetővé teszi egy URL-CÍMÉT és a egy kódot, amely hitelesítést kell használnia.
   
   * Jelentkezzen be egy *egyszerű szolgáltatás*
    
     Amikor bejelentkezik, [hozzon létre egy egyszerű szolgáltatást az Azure Portalon keresztül](azure-stack-create-service-principals.md) vagy a parancssori felület, és rendelje hozzá egy szerepkörhöz. Most jelentkezzen be a következő paranccsal:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Mindent, a belül az Azure Stack-erőforrások létrehozása a CLI használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```azurecli
    az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport létrehozása sikeresen megtörtént, az előző parancs megjeleníti az újonnan létrehozott erőforrás következő tulajdonságai:

![Erőforráscsoport létrehozása a kimenet](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Ez a szakasz végigvezeti beállítása a parancssori felület, ha az Active Directory összevonási szolgáltatásokban (AD FS) használ a felügyeleti szolgáltatás, és a parancssori felület használata Linux rendszerű gépen.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató főtanúsítványát a megbízható

A ASDK használja, ha megbízik a legfelső szintű hitelesítésszolgáltató tanúsítványát a távoli gépen kell. Nem kell ehhez az integrált rendszerekkel.

Az Azure Stack hitelesítésszolgáltató főtanúsítványát megbízhatósági a meglévő Python-tanúsítvány hozzáfűzésével.

1. Keresse meg a tanúsítvány helye a gépen. A hely eltérőek lehetnek attól függően, hol telepítette a Python. Szüksége lesz a pipet és a certifi modul telepítve van. A bash használatával a következő Python-parancsot használhatja:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítvány helye; Ha például `~/lib/python3.5/site-packages/certifi/cacert.pem`. A megadott elérési út attól függ, az operációs rendszer és a Python telepített verzióját.

2. Futtassa az alábbi bash-parancs az elérési útját a tanúsítványhoz.

   - Egy távoli Linux gépen:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Linuxos gépek az Azure Stack-környezetben:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Csatlakozás az Azure Stackhez

A következő lépések segítségével csatlakozhat az Azure Stack:

1. Regisztrálja az Azure Stack környezettel futtatásával a `az cloud register` parancsot. Bizonyos esetekben közvetlen kimenő internetkapcsolattal áthalad egy proxy vagy tűzfal, amely érvényesíti az SSL-hozzáférés. Ezekben az esetekben a `az cloud register` parancs például a "Nem lehet lekérni végpontok a felhőben." hiba miatt sikertelen lehet Ez a hiba elkerüléséhez állíthatja be az alábbi környezeti változókat:

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Regisztrálja a környezetben. Használja az alábbi paramétereket futtatásakor `az cloud register`.

    | Érték | Példa | Leírás |
    | --- | --- | --- |
    | Környezet neve | AzureStackUser | Használat `AzureStackUser` a felhasználói környezet. Ha Ön operátor, adja meg `AzureStackAdmin`. |
    | Resource Manager-végpont | https://management.local.azurestack.external | A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` A **ResourceManagerUrl** integrált rendszerek van: `https://management.<region>.<fqdn>/` A szükséges metaadatokat lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Ha a rendszer integrált végpont kérdése van, lépjen kapcsolatba a felhő üzemeltetője. |
    | Storage-végpont | local.azurestack.external | `local.azurestack.external` a ASDK szól. Egy integrált rendszer érdemes a rendszer egy végpontot használja.  |
    | Keyvault-utótag | .vault.local.azurestack.external | `.vault.local.azurestack.external` a ASDK szól. Egy integrált rendszer érdemes a rendszer egy végpontot használja.  |
    | Virtuálisgép-lemezkép alias doc végpont – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | A virtuálisgép-lemezkép aliasok tartalmazó dokumentum URI-t. További információkért lásd: [### állítsa be a virtuális gép aliasok végpontjának](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Állítsa be az aktív környezetet. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Frissítés az Ön környezetének konfigurációját az Azure Stack meghatározott API verzió profil használatára. Frissítse a konfigurációt, futtassa a következő parancsot:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Ha egy Azure Stack verziója előtt a 1808 build futtat, az API-verzióprofil kell használnia **2017-03-09-profile** ahelyett, hogy az API-verzióprofil **2019-03-01-hibrid**. Szüksége lesz egy az Azure CLI legújabb verzióját használja.

5. Jelentkezzen be az Azure Stack-környezet használatával a `az login` parancsot. Bejelentkezhet az Azure Stack-környezet vagy egy felhasználó vagy egy [szolgáltatásnév](/azure/active-directory/develop/app-objects-and-service-principals). 

6. bejelentkezés: 

   *  Mint egy **felhasználói** webböngészővel rendelkező eszköz:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >A következő parancs futtatásával lehetővé teszi egy URL-CÍMÉT és a egy kódot, amely hitelesítést kell használnia.

   * Mint egy egyszerű szolgáltatást:
        
     Készítse elő a .pem-fájlt a szolgáltatás egyszerű bejelentkezéshez használandó.

      * Az ügyfélszámítógépen, ahol a rendszerbiztonsági tag lett létrehozva, exportálja a szolgáltatásnév helyén található a pfx titkos kulccsal `cert:\CurrentUser\My`; a tanúsítvány neve rendelkezik az egyszerű azonos néven.
  
      * A pfx konvertálható pem (az OpenSSL segédprogramot használja).

     Jelentkezzen be a parancssori felület:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Mindent, a belül az Azure Stack-erőforrások létrehozása a CLI használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```azurecli
  az group create -n MyResourceGroup -l local
```

Ha az erőforráscsoport létrehozása sikeresen megtörtént, az előző parancs megjeleníti az újonnan létrehozott erőforrás következő tulajdonságai:

![Erőforráscsoport létrehozása a kimenet](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Ismert problémák

Vannak ismert problémák az Azure Stack parancssori felület használata esetén:

 - A CLI interaktív módban Ha például a `az interactive` paranccsal, az Azure Stackben még nem támogatott.
 - Az Azure Stackben elérhető virtuálisgép-rendszerképek listájának lekéréséhez használja a `az vm image list --all` parancs helyett a `az vm image list` parancsot. Adja meg a `--all` beállítással biztosíthatja, hogy a választ adja vissza az érhetők el az Azure Stack környezettel lemezképeket.
 - Az Azure-ban rendelkezésre álló virtuális gép rendszerkép aliasok nem vonatkoznak az Azure Stackhez. Virtuálisgép-lemezképek használata esetén a teljes URN paramétert kell használnia (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) a rendszerkép alias helyett. Ez URN egyeznie kell a lemezkép előírásoknak, származtatva a `az vm images list` parancsot.

## <a name="next-steps"></a>További lépések

- [Az Azure CLI-vel sablonok üzembe helyezése](azure-stack-deploy-template-command-line.md)
- [Azure CLI-vel engedélyezése az Azure Stack felhasználói (operátor)](../operator/azure-stack-cli-admin.md)
- [Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md) 
