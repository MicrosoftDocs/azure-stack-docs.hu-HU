---
title: Az API-verziók profiljainak használata a Python használatával Azure Stack hub-ban
description: Ismerje meg, hogyan használhatók az API-verziók profiljai a Python használatával Azure Stack hub-ban.
author: sethmanheim
ms.topic: article
ms.date: 12/2/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 676cd4fe93a464df0cd0a4cddeb3e12ddee1f229
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96526033"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack-hub"></a>Az API-verziók profiljainak használata a Python használatával Azure Stack hub-ban

A Python SDK támogatja az API-verziók profiljait különböző felhőalapú platformok, például az Azure Stack hub és a globális Azure megcélzásához. Az API-profilok használatával megoldásokat hozhat létre hibrid felhőhöz.

A cikkben szereplő utasítások Microsoft Azure előfizetést igényelnek. Ha még nem rendelkezik ilyennel, [ingyenes próbaverziós fiókot](https://go.microsoft.com/fwlink/?LinkId=330212)is kaphat.

## <a name="python-and-api-version-profiles"></a>Python-és API-verziók profiljai

A Python SDK a következő API-profilokat támogatja:

- **legújabb**  
    Ez a profil az Azure platform összes szolgáltatójának legújabb API-verzióit célozza meg.
- **2019-03-01 – hibrid**  
    Ez a profil a 1904-es vagy újabb verziójú Azure Stack hub-platform összes erőforrás-szolgáltatójának legújabb API-verzióit célozza meg.
- **2018-03-01 – hibrid**  
    Ez a profil az Azure Stack hub platform összes erőforrás-szolgáltatójának legkompatibilisebb API-verzióit célozza meg.
- **2017-03-09 – profil**  
    Ez a profil az Azure Stack hub által támogatott erőforrás-szolgáltatók legkompatibilisebb API-verzióit célozza meg.

   További információ az API-profilokról és az Azure Stack hub-ról: [az API-verziók profiljainak kezelése Azure stack központban](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Az Azure Python SDK telepítése

1. [Telepítse a git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)-t.
2. A Python SDK telepítésével kapcsolatos útmutatásért lásd: az [Azure Python-fejlesztőknek](/python/azure/python-sdk-azure-install?view=azure-python).
3. Ha nem érhető el, hozzon létre egy előfizetést, és mentse az előfizetés-azonosítót későbbi használatra. Az előfizetések létrehozásával kapcsolatos utasításokért lásd: [előfizetések létrehozása Azure stack hub-beli ajánlatokhoz](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Hozzon létre egy egyszerű szolgáltatásnevet, és mentse az azonosítót és a titkos kulcsot. Az Azure Stack hub szolgáltatáshoz való létrehozásával kapcsolatos utasításokért lásd: [alkalmazások Azure stack hub elérésének biztosítása](../operator/azure-stack-create-service-principals.md).
5. Győződjön meg arról, hogy a szolgáltatásnév közreműködői/tulajdonosi szerepkörrel rendelkezik az előfizetésében. A szerepkörnek az egyszerű szolgáltatáshoz való hozzárendelésével kapcsolatos utasításokért lásd: [alkalmazások Azure stack hubhoz való hozzáférésének biztosítása](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Előfeltételek

Ha Azure Stack hubhoz szeretné használni a Python Azure SDK-t, a következő értékeket kell megadnia, majd értékeket kell beállítania környezeti változókkal. A környezeti változók megadásához tekintse meg az adott operációs rendszerre vonatkozó utasításokat az alábbi táblázatban leírtak szerint.

| Érték | Környezeti változók | Leírás |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító | `AZURE_TENANT_ID` | Az Azure Stack hub- [bérlő azonosítója](../operator/azure-stack-identity-overview.md). |
| Ügyfél-azonosító | `AZURE_CLIENT_ID` | Az egyszerű szolgáltatásnév a jelen cikk előző szakaszában a szolgáltatásnév létrehozásakor mentve. |
| Előfizetés azonosítója | `AZURE_SUBSCRIPTION_ID` | Az előfizetés- [azonosítóval](../operator/service-plan-offer-subscription-overview.md#subscriptions) érheti el az ajánlatokat az Azure stack hub szolgáltatásban. |
| Titkos ügyfélkulcs | `AZURE_CLIENT_SECRET` | A szolgáltatás egyszerű alkalmazásának titkos kulcsa a szolgáltatásnév létrehozásakor mentve. |
| Resource Manager-végpont | `ARM_ENDPOINT` | Lásd a [Azure stack hub Resource Manager-végpontot](azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint) ismertető cikket. |
| Erőforrás helye | `AZURE_RESOURCE_LOCATION` | Az Azure Stack hub-környezet erőforrásának helye.

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának megbízhatósága

Ha a ASDK használja, explicit módon meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Nem kell megbíznia a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban Azure Stack hub integrált rendszerekkel.

#### <a name="windows"></a>Windows

1. Keresse meg a Python tanúsítványtároló helyét a gépen. A hely változhat, attól függően, hogy hol telepítette a Pythont. Nyisson meg egy parancssort vagy egy rendszergazda jogú PowerShell-parancssort, és írja be a következő parancsot:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítványtároló helyét; például: **~/lib/python3.5/site-Packages/Certifi/CAcert.PEM**. A megadott elérési út az operációs rendszertől és a telepített Python-verziótól függ.

2. Bízza a Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát úgy, hogy hozzáfűzi a meglévő Python-tanúsítványhoz:

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate; for ex: C:\Users\user1\Downloads\root.pem>"

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
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

> [!NOTE]  
> Ha a **virtualenv** -t használja a Python SDK-hoz való fejlesztéshez, ahogy azt a [Python minta futtatása](#run-the-python-sample) szakaszban is említettük, az előző tanúsítványt hozzá kell adnia a virtuális környezet tanúsítványtárolóhoz. Az elérési út a következőhöz hasonló lehet: `..\mytestenv\Lib\site-packages\certifi\cacert.pem` .

## <a name="python-samples-for-azure-stack-hub"></a>Python-minták az Azure Stack hub-hoz

A Python SDK-val Azure Stack hub számára elérhető mintakód-minták a következők:

- [Erőforrások és erőforráscsoportok kezelése](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [Storage-fiók kezelése](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [Virtuális gépek kezelése](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/): Ez a minta **2019-03-01-hibrid** profilt használ, amely az Azure stack hub által támogatott legújabb API-verziókat célozza meg.

## <a name="manage-virtual-machine-sample"></a>Virtuálisgép-minta kezelése

A következő Python-mintakód használatával általános felügyeleti feladatokat hajthat végre a virtuális gépekhez (VM) az Azure Stack hub-ban. A kód mintája a következőket mutatja be:

- Virtuális gépek létrehozása:
  - Linux rendszerű virtuális gép létrehozása
  - Windows rendszerű virtuális gép létrehozása
- Virtuális gép frissítése:
  - Meghajtó kibontása
  - Virtuális gép címkézése
  - Adatlemezek csatolása
  - Adatlemezek leválasztása
- Virtuális gép üzemeltetése:
  - Virtuális gép elindítása
  - Virtuális gép leállítása
  - Virtuális gép újraindítása
- Virtuális gépek felsorolása
- Virtuális gép törlése

A műveleteket végrehajtó kód áttekintéséhez tekintse meg a Python parancsfájl **example.py** a **run_example ()** függvényt a GitHub [-adattár Hybrid-számítás-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM)című részében.

Az egyes műveletek egyértelműen egy megjegyzéssel és egy Print függvénnyel vannak megjelölve. A példák nem feltétlenül szerepelnek a listában látható sorrendben.

## <a name="run-the-python-sample"></a>A Python-minta futtatása

1. [Telepítse a Pythont](https://www.python.org/downloads/) , ha még nincs telepítve. Ez a minta (és az SDK) kompatibilis a Python 2,7, 3,4, 3,5 és 3,6 rendszerrel.

2. A Python-fejlesztés általános javaslata a virtuális környezet használata. További információt a [Python dokumentációjában](https://docs.python.org/3/tutorial/venv.html)talál.

3. Telepítse és inicializálja a virtuális környezetet a **venv** modullal a Python 3 rendszeren (telepítenie kell a [virtualenv](https://pypi.python.org/pypi/virtualenv) for Python 2,7):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. A tárház klónozása:

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. A függőségek telepítése a **pip** használatával:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Hozzon létre egy [egyszerű szolgáltatásnevet](../operator/azure-stack-create-service-principals.md) Azure stack hub használatával való együttműködéshez. Győződjön meg arról, hogy a szolgáltatásnév [közreműködői/tulajdonosi szerepkörrel](../operator/azure-stack-create-service-principals.md#assign-a-role) rendelkezik az előfizetésében.

7. Állítsa be a következő változókat, és exportálja ezeket a környezeti változókat a jelenlegi rendszerhéjba:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. A minta futtatásához az Ubuntu 16,04-LTS és a WindowsServer 2012-R2-DataCenter lemezképeknek jelen kell lenniük az Azure Stack hub piactéren. Ezek a lemezképek [letölthetők az Azure-ból](../operator/azure-stack-download-azure-marketplace-item.md), vagy hozzáadhatók a [platform lemezkép-tárházához](../operator/azure-stack-add-vm-image.md).

9. Minta futtatása:

    ```python
    python example.py
    ```

## <a name="next-steps"></a>További lépések

- [Azure Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
- [Az Azure Virtual Machines dokumentációja](https://azure.microsoft.com/services/virtual-machines/)
- [Képzési terv Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
