---
title: Az Azure Stackben Python API-verzióprofilok használata |} A Microsoft Docs
description: Ismerje meg az Azure Stackben Python API-verzióprofilok használatával.
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
ms.openlocfilehash: 18884a4570f5c90d00b8e17e42fe8bc1b3c073c2
ms.sourcegitcommit: c4507a100eadd9073aed0d537d054e394b34f530
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67198598"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>API-verzióprofilok használata az Azure Stackben Pythonnal

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="python-and-api-version-profiles"></a>Python- és API-verzióprofilok

A Python SDK API-verzióprofilok célozhat meg például az Azure Stack és a globális Azure más felhőalapú platformot támogatja. Használhat API-t egy hibrid felhőalapú megoldások létrehozásához. A Python SDK támogatja a következő API-profilokat:

- **latest**  
    Ezt a profilt a legújabb API-verziók célozza meg benne az Azure platform minden szolgáltató számára.
- **2019-03-01-hybrid**  
    Ez a profil a legújabb API-verziók célozza meg benne az összes erőforrás-szolgáltató az Azure Stack-platformon blokk-verziók 1904-es vagy újabb.
- **2018-03-01-hybrid**  
    Ezt a profilt a leginkább kompatibilis API-verziók célozza meg benne az összes erőforrás-szolgáltató az Azure Stack-platformokon.
- **2017-03-09-profile**  
    Ez a profil célozza meg benne az Azure Stack által támogatott erőforrás-szolgáltatók a leginkább kompatibilis API-verziók.

   API-profilok és az Azure Stack kapcsolatos további információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Az Azure Python SDK telepítése

1. A Git telepítése [a hivatalos hely](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Útmutatást nyújt a Python SDK telepítése, lásd: [Python-fejlesztőknek az Azure](/python/azure/python-sdk-azure-install?view=azure-python).
3. Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi használat céljából. Előfizetés létrehozásával kapcsolatos útmutatóért lásd: [ajánlatok, előfizetések létrehozása az Azure Stackben](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Hozzon létre egy egyszerű szolgáltatást, és mentse a azonosítója és kulcsa. Az Azure stack-beli szolgáltatásnév létrehozása az utasításokért lásd: [alkalmazások elérést biztosíthat az Azure Stack](../operator/azure-stack-create-service-principals.md).
5. Ellenőrizze, hogy az egyszerű szolgáltatást a közreműködői és tulajdonosi szerepkör-előfizetésében. Szerepkör hozzárendelése egyszerű szolgáltatást, lásd: [alkalmazások elérést biztosíthat az Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack az Azure Python SDK használatához adja meg a következő értékeket, és adja meg az értékeket a környezeti változókat. Az utasításokat lásd a táblázat az operációs rendszerének a a környezeti változók beállítása után.

| Érték | Környezeti változók | Leírás |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító | AZURE_TENANT_ID | Az Azure Stack értékét [bérlőazonosító](../operator/azure-stack-identity-overview.md). |
| Ügyfél-azonosító | AZURE_CLIENT_ID | A szolgáltatás egyszerű Alkalmazásazonosító egyszerű szolgáltatás létrehozásakor ez a cikk az előző szakaszban mentett. |
| Előfizetés azonosítója | AZURE_SUBSCRIPTION_ID | A [előfizetés-azonosító](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben. |
| Titkos ügyfélkulcs | AZURE_CLIENT_SECRET | A szolgáltatás egyszerű alkalmazás titkos kulcs mentése az egyszerű szolgáltatás létrehozásakor. |
| Resource Manager-végpont | ARM_ENDPOINT | Tekintse meg a [Azure Stack Resource Manager-végpontot](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Resource Location (Erőforrás helye) | AZURE_RESOURCE_LOCATION | Az Azure Stack környezettel erőforrás helye.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató főtanúsítványát a megbízható

A ASDK használja, ha megbízik a legfelső szintű hitelesítésszolgáltató tanúsítványát a távoli gépen kell. Nem kell ehhez az integrált rendszerekkel.

#### <a name="windows"></a>Windows

1. Keresse meg a python tanúsítványtár helye a gépen. A hely eltérőek lehetnek attól függően, hol telepítette a Python. Nyisson meg egy parancssort, vagy egy rendszergazda jogú PowerShell-parancssort, és írja be a következő parancsot:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Jegyezze fel a tanúsítványt tároló helye. Ha például *~/lib/python3.5/site-packages/certifi/cacert.pem*. Az operációs rendszer és a Python telepített verzióját az adott elérési út függ.

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
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"

    ```

> [!NOTE]  
> Ha virtualenv említettek szerint a Python SDK-val fejlesztéséhez használ, szüksége lesz a fenti tanúsítvány hozzáadása a virtuális környezet tanúsítványtár is. Az elérési út hasonlóan néznének ki: ".. \mytestenv\Lib\site-packages\certifi\cacert.PEM"



## <a name="python-samples-for-azure-stack"></a>Python-minták az Azure Stackben

Az Azure stack-Python SDK-val érhető el Kódminták a következők:

- [Erőforrások és -erőforráscsoportok kezelése](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/).
- [Storage-fiók kezelése](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/).
- [Virtuális gépek kezelése](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/). (A 2019-03-01-hibrid, amely az Azure Stack által támogatott legújabb API-verziókat célzó használó minta)

## <a name="python-manage-virtual-machine-sample"></a>Python virtuális gép minta kezelése

A következő mintakód segítségével gyakori felügyeleti feladatok végrehajtása a virtuális gépek az Azure stack. A mintakód bemutatja, hogy:

- Virtuális gépek létrehozása:
  - Linuxos virtuális gép létrehozása
  - Windows rendszerű virtuális gép létrehozása
- A virtuális gép frissítése:
  - Bontsa ki a meghajtó
  - Virtuális gép címkézése
  - Adatlemez csatolása
  - Adatlemezek leválasztása
- Virtuális gép működtetéséhez:
  - A virtuális gép elindítása
  - Virtuális gép leállítása
  - Indítsa újra a virtuális gép
- Virtuális gépek listája
- A virtuális gép törlése

Tekintse át a kódot, amely végrehajtja ezeket a műveleteket, tekintse meg a **run_example()** függvény a Python-szkriptet a **example.py** a GitHub-adattárat a [hibrid számítási Python-kezelése – virtuális gépek és](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Minden művelet egyértelműen célállapotba megjegyzést, és a egy nyomtatási funkció. A példák nem feltétlenül az itt látható a listában szereplő sorrendben.

## <a name="run-the-python-sample"></a>A Python-minta futtatása

1. Ha még nem tette meg [telepítik a Pythont](https://www.python.org/downloads/). Ez a minta (és az SDK-t) a rendszer kompatibilis a Python 2.7-es, 3.4-es, 3.5-ös és 3.6-os.

2. A Python-fejlesztés általános javaslat, hogy egy virtuális környezethez. További információkért lásd: a [dokumentace Pro Python](https://docs.python.org/3/tutorial/venv.html).

3. Telepítse, és a virtuális környezet a Python 3 "venv" modul inicializálása (telepítenie kell a [virtualenv](https://pypi.python.org/pypi/virtualenv) Python 2.7):

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

5. A pip használatával függőségek telepítése:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Hozzon létre egy [szolgáltatásnév](../operator/azure-stack-create-service-principals.md) működjön az Azure Stack használatával. Győződjön meg arról, hogy rendelkezik az egyszerű szolgáltatás [közreműködői vagy tulajdonosi szerepkör](../operator/azure-stack-create-service-principals.md#assign-a-role) az előfizetésén.

7. Állítsa be a következő változókat, és exportálhatja ezeket a környezeti változókat az aktuális felületen:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Ez a minta futtatásához, Ubuntu 16.04-LTS és lemezképek WindowsServer-2012-R2-DataCenter jelen kell lennie az Azure Stack piactéren. Ezek lehetnek, vagy [az Azure-ból letöltött](../operator/azure-stack-download-azure-marketplace-item.md), vagy hozzáadhatja a [Platformlemezképtárhoz](../operator/azure-stack-add-vm-image.md).

9. Minta futtatása:

    ```python
    python example.py
    ```


## <a name="next-steps"></a>További lépések

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Az Azure Virtual Machines – dokumentáció](https://azure.microsoft.com/services/virtual-machines/)
- [Képzési terv a virtuális gépek](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Ha nem rendelkezik Microsoft Azure-előfizetési, beszerezheti a egy ingyenes próbafiókot [Itt](https://go.microsoft.com/fwlink/?LinkId=330212).
