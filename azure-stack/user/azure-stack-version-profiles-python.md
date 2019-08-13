---
title: API-verziók profiljainak használata a Python használatával Azure Stackban | Microsoft Docs
description: Ismerje meg, hogyan használhatók az API-verziók profiljai a Python használatával Azure Stackban.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
<!-- dev: viananth -->
ms.openlocfilehash: 35ce331c29e89af3a81396a9658cf8a0f29018d3
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959419"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Az API-verziók profiljainak használata a Python használatával Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Python-és API-verziók profiljai

A Python SDK támogatja az API-verziók profiljait különböző felhőalapú platformok, például a Azure Stack és a globális Azure megcélzásához. Az API-profilok használatával megoldásokat hozhat létre hibrid felhőhöz. A Python SDK a következő API-profilokat támogatja:

- **latest**  
    Ez a profil az Azure platform összes szolgáltatójának legújabb API-verzióit célozza meg.
- **2019-03-01-hybrid**  
    Ez a profil a Azure Stack platform összes erőforrás-szolgáltatójának legújabb API-verzióit célozza meg a Stamp 1904-es vagy újabb verzióihoz.
- **2018-03-01-hybrid**  
    Ez a profil a Azure Stack platform összes erőforrás-szolgáltatójának legkompatibilisebb API-verzióit célozza meg.
- **2017-03-09 – profil**  
    Ez a profil a Azure Stack által támogatott erőforrás-szolgáltatók legkompatibilisebb API-verzióit célozza meg.

   További információ az API-profilokról és a Azure Stackekről: [az API-verziók profiljainak kezelése a Azure Stackban](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Az Azure Python SDK telepítése

1. Telepítse a git-t [a hivatalos](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)webhelyről.
2. A Python SDK telepítésével kapcsolatos útmutatásért lásd: az [Azure Python](/python/azure/python-sdk-azure-install?view=azure-python)-fejlesztőknek.
3. Ha nem érhető el, hozzon létre egy előfizetést, és mentse az előfizetés-azonosítót későbbi használatra. Az előfizetések létrehozásával kapcsolatos utasításokért lásd: előfizetések [létrehozása az ajánlatokhoz Azure stack](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Hozzon létre egy egyszerű szolgáltatásnevet, és mentse az azonosítót és a titkos kulcsot. Az Azure stack-beli szolgáltatásnév létrehozása az utasításokért lásd: [alkalmazások elérést biztosíthat az Azure Stack](../operator/azure-stack-create-service-principals.md).
5. Ellenőrizze, hogy az egyszerű szolgáltatást a közreműködői és tulajdonosi szerepkör-előfizetésében. A szerepkörnek az egyszerű szolgáltatáshoz való hozzárendelésével kapcsolatos útmutatásért lásd: [alkalmazások Azure Stackhoz való hozzáférésének biztosítása](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Előfeltételek

Ha a Python Azure SDK-t Azure Stack használatával szeretné használni, a következő értékeket kell megadnia, majd értékeket kell beállítania környezeti változókkal. A környezeti változók megadásához tekintse meg az adott operációs rendszer táblázatát követő utasításokat.

| Value | Környezeti változók | Leírás |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító | `AZURE_TENANT_ID` | A Azure Stack [bérlő azonosítójának](../operator/azure-stack-identity-overview.md)értéke. |
| Ügyfél-azonosító | `AZURE_CLIENT_ID` | A jelen cikk előző szakaszában a szolgáltatásnév létrehozásakor mentett szolgáltatásnév-alkalmazás azonosítója. |
| Előfizetés azonosítója | `AZURE_SUBSCRIPTION_ID` | Az [előfizetés-azonosító](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) az Azure stack-ban elérhető ajánlatok elérési módja. |
| Titkos ügyfélkulcs | `AZURE_CLIENT_SECRET` | A szolgáltatás egyszerű alkalmazásának titkos kulcsa a szolgáltatásnév létrehozásakor mentve. |
| Resource Manager-végpont | `ARM_ENDPOINT` | Lásd a [Azure stack Resource Manager](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)-végpontot. |
| Erőforrás helye | `AZURE_RESOURCE_LOCATION` | A Azure Stack-környezet erőforrásának helye.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>A Azure Stack HITELESÍTÉSSZOLGÁLTATÓI főtanúsítvány megbízhatóságának megtartása

Ha a ASDK használja, meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Nem kell megbíznia a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban az integrált rendszerekkel.

#### <a name="windows"></a>Windows

1. Keresse meg a Python tanúsítványtároló helyét a gépen. A hely változhat attól függően, hogy hol telepítette a Pythont. Nyisson meg egy parancssort vagy egy rendszergazda jogú PowerShell-parancssort, és írja be a következő parancsot:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítványtároló helyét. Például: *~/lib/python3.5/site-Packages/Certifi/CAcert.PEM*. A megadott elérési út az operációs rendszertől és a telepített Python-verziótól függ.

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
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"

    ```

> [!NOTE]  
> Ha a virtualenv segítségével fejleszti a Python SDK-t az alább leírtak szerint, akkor hozzá kell adnia a fenti tanúsítványt a virtuális környezet tanúsítvány-tárolójához. Az elérési út a következőhöz hasonló lehet: ".. \mytestenv\Lib\site-packages\certifi\cacert.pem"



## <a name="python-samples-for-azure-stack"></a>Python-minták a Azure Stackhoz

A Python SDK-val Azure Stack számára elérhető mintakód-minták a következők:

- [Erőforrások és erőforráscsoportok kezelése](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [Storage-fiók kezelése](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [Virtual Machines kezelése](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/) (Ez a példa a 2019-03-01-Hybrid változatot használja, amely a Azure Stack által támogatott legújabb API-verziókat célozza meg)

## <a name="python-manage-virtual-machine-sample"></a>Python – virtuális gép kezelése – minta

A következő mintakód használatával általános felügyeleti feladatokat hajthat végre a Azure Stack virtuális gépei számára. A kód mintája a következőket mutatja be:

- Virtuális gépek létrehozása:
  - Linux rendszerű virtuális gép készítése
  - Windowsos virtuális gép létrehozása
- Virtuális gép frissítése:
  - Meghajtó kibontása
  - Virtuális gép címkézése
  - Adatlemezek csatolása
  - Adatlemezek leválasztása
- Virtuális gép üzemeltetése:
  - Virtuális gép elindítása
  - Virtuális gép leállítása
  - Virtuális gép újraindítása
- Virtuális gépek listázása
- Virtuális gép törlése

A műveleteket végrehajtó kód áttekintéséhez tekintse meg a Python parancsfájl **example.py** a **run_example ()** függvényt a GitHub [-adattár Hybrid-számítás-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM)című részében.

Az egyes műveletek egyértelműen egy megjegyzéssel és egy Print függvénnyel vannak megjelölve. A példák nem feltétlenül szerepelnek a listában látható sorrendben.

## <a name="run-the-python-sample"></a>A Python-minta futtatása

1. [Telepítse](https://www.python.org/downloads/) a Pythont, ha még nincs telepítve. Ez a minta (és az SDK) kompatibilis a Python 2,7, 3,4, 3,5 és 3,6 rendszerrel.

2. A Python-fejlesztés általános javaslata virtuális környezet használata. További információt a [Python dokumentációjában](https://docs.python.org/3/tutorial/venv.html)talál.

3. Telepítse és inicializálja a virtuális környezetet a "venv" modullal a Python 3 rendszeren (telepítenie kell a [virtualenv](https://pypi.python.org/pypi/virtualenv) for Python 2,7):

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

5. A függőségek telepítése a pip használatával:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Hozzon létre egy [egyszerű szolgáltatásnevet](../operator/azure-stack-create-service-principals.md) , hogy működjön a Azure stack. Győződjön meg arról, hogy az egyszerű szolgáltatás [közreműködői/tulajdonosi szerepkörrel](../operator/azure-stack-create-service-principals.md#assign-a-role) rendelkezik az előfizetésében.

7. Állítsa be a következő változókat, és exportálja ezeket a környezeti változókat a jelenlegi rendszerhéjba:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. A minta futtatásához az Ubuntu 16,04-LTS és a WindowsServer 2012-R2-DataCenter lemezképeknek jelen kell lenniük a Azure Stack piactéren. Ezek a lemezképek letölthetők az [Azure-ból](../operator/azure-stack-download-azure-marketplace-item.md), vagy hozzáadhatók a [platform lemezkép](../operator/azure-stack-add-vm-image.md)-tárházához.

9. Minta futtatása:

    ```python
    python example.py
    ```


## <a name="next-steps"></a>További lépések

- [Az Azure Python fejlesztői központja](https://azure.microsoft.com/develop/python/)
- [Az Azure Virtual Machines dokumentációja](https://azure.microsoft.com/services/virtual-machines/)
- [Képzési terv Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/)

Ha nem rendelkezik Microsoft Azure-előfizetéssel, [itt](https://go.microsoft.com/fwlink/?LinkId=330212)kérhet ingyenes próbaverziós fiókot.
