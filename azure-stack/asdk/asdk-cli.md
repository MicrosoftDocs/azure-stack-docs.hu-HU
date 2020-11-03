---
title: Az Azure CLI tanúsítványának beállítása a Azure Stack Development Kiton (ASDK)? | Microsoft Docs
description: További információ az Azure CLI tanúsítványának beállításáról a Azure Stack Development Kit Azure Stack Development Kit.
author: mattbriggs
ms.topic: how-to
ms.date: 10/26/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 8b55fea8ed9692da34234e2633bdb43e92de46eb
ms.sourcegitcommit: b960df16e84ec9fbccfce772102b91f0b7ae7060
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294405"
---
# <a name="setting-up-certificates-for-azure-cli-on-azure-stack-development-kit"></a>Tanúsítványok beállítása az Azure CLI-hez Azure Stack Development Kit

A cikk lépéseit követve állítsa be az Azure Command-Line felületét (CLI) a Linux-, Mac-és Windows-ügyféloldali platformok Azure Stack Development Kit (ASDK) erőforrásainak kezelésére.

Ez a cikk a tanúsítványok beszerzésével és a távoli felügyeleti gépen való megbízhatóságával foglalkozik. Az Azure CLI telepítéséhez és a környezethez való kapcsolódáshoz lásd: az [Azure CLI telepítése Azure stack hubhoz](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="prepare-for-azure-cli"></a>Felkészülés az Azure CLI-re

A ASDK esetében szüksége lesz a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványára, hogy az Azure CLI-t használja a fejlesztői gépen. A tanúsítvány segítségével kezelheti az erőforrásokat a parancssori felületről.

 - **Az Azure stack hub hitelesítésszolgáltatói főtanúsítványra** akkor van szükség, ha a parancssori FELÜLETET a ASDK kívüli munkaállomásról használja.  

 - **A virtuális gép aliasok végpontja** egy aliast (például "UbuntuLTS" vagy "Win2012Datacenter") biztosít. Ez az alias a virtuális gépek telepítésekor egyetlen paraméterként hivatkozik a lemezkép-közzétevőre, az ajánlatra, az SKU-ra és a verzióra.  

Az alábbi szakaszok azt ismertetik, hogyan kérheti le ezeket az értékeket.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának exportálása

Ha az Azure CLI-t a ASDK szeretné használni, exportálja a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványt.

A ASDK legfelső szintű tanúsítványának exportálása PEM formátumban:

1. Adja meg az Azure Stack hub legfelső szintű tanúsítványának nevét:
    1. Jelentkezzen be az Azure Stack hub felhasználói vagy felügyeleti portálján.
    2. A címsor közelében válassza a **biztonságos** lehetőséget.
    3. Az előugró ablakban válassza az **érvényes** lehetőséget.
    4. A tanúsítvány ablakban válassza a tanúsítvány **elérési útja** fület.
    5. Jegyezze fel az Azure Stack hub legfelső szintű tanúsítványának nevét.

    ![Azure Stack hub főtanúsítványa](../user/media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Hozzon létre egy Windows rendszerű virtuális gépet Azure stack hub-on](../user/azure-stack-quick-windows-portal.md).

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

## <a name="set-up-the-virtual-machine-alias-endpoint"></a>A virtuális gép alias-végpontjának beállítása

Beállíthat egy nyilvánosan elérhető végpontot, amely egy virtuálisgép-alias fájlt üzemeltet. A VM-alias fájl egy olyan JSON-fájl, amely a rendszerkép köznapi nevét adja meg. A nevet akkor használja, ha a virtuális gépet Azure CLI-paraméterként telepíti.

1. Ha egyéni rendszerképet tesz közzé, jegyezze fel a közzététel során megadott kiadói, ajánlati, SKU-és verziószám-információkat. Ha ez egy rendszerkép a piactéren, megtekintheti az adatokat a ```Get-AzureVMImage``` parancsmag használatával.  

2. Töltse le a [mintát](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a githubról.

3. Hozzon létre egy Storage-fiókot Azure Stack központban. Ha elkészült, hozzon létre egy BLOB-tárolót. Állítsa be a hozzáférési házirendet a "Public" értékre.  

4. Töltse fel a JSON-fájlt az új tárolóba. Ha elkészült, megtekintheti a blob URL-címét. Jelölje ki a blob nevét, majd válassza ki az URL-címet a blob tulajdonságai között.


## <a name="trust-the-certificate"></a>A tanúsítvány megbízhatósága

Ha az Azure CLI-t a aSDK szeretné használni, meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban.

### <a name="windows"></a>[Windows](#tab/win)

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

Az Azure CLI telepítésével és csatlakozásával kapcsolatos utasításokért lásd: az [Azure CLI telepítése Azure stack hub-ra](/azure-stack/user/azure-stack-version-profiles-azurecli2).

### <a name="linux"></a>[Linux](#tab/lin)

A CLI beállításakor bízza meg a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát úgy, hogy hozzáfűzi a meglévő Python-tanúsítványhoz.

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

Az Azure CLI telepítésével és csatlakozásával kapcsolatos utasításokért lásd: az [Azure CLI telepítése Azure stack hub-ra](/azure-stack/user/azure-stack-version-profiles-azurecli2).

---

## <a name="next-steps"></a>Következő lépések

[Az Azure CLI telepítése Azure Stack hubhoz](../user/azure-stack-version-profiles-azurecli2.md)