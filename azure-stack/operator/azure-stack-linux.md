---
title: Linuxos rendszerképek hozzáadása a Azure Stack Marketplace-hez | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá linuxos lemezképeket Azure Stack Marketplace-hez.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 208e632634c59be0338c70020e7fc0fdae846797
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74299005"
---
# <a name="add-linux-images-to-azure-stack-marketplace"></a>Linux-lemezképek hozzáadása Azure Stack piactérhez

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Linux rendszerű virtuális gépeket (VM-ket) telepíthet Azure Stack egy Linux-alapú rendszerkép a Azure Stack piactéren való hozzáadásával. A leggyorsabban a Piactérkezelőn keresztül adhat hozzá Linux rendszerképet az Azure Stackhez. Ezek a rendszerképek elő lettek készítve az Azure Stackben történő használathoz, és ellenőrizve lett a kompatibilitásuk.

## <a name="marketplace-management"></a>Piactér-kezelés

Az Azure Marketplace-ről származó linuxos lemezképek letöltéséhez lásd: [Marketplace-elemek letöltése az Azure-ból Azure stack](azure-stack-download-azure-marketplace-item.md). Válassza ki azokat a Linux-lemezképeket, amelyeken a felhasználók számára biztosítani szeretné a Azure Stack.

Ezek a lemezképek gyakran frissülnek, ezért a piactér-felügyeletet gyakran érdemes naprakészen tartani.

## <a name="prepare-your-own-image"></a>Saját rendszerkép előkészítése

Amikor lehetséges, a Marketplace-kezelőből töltse le az elérhető rendszerképeket. Ezek a rendszerképek elő lettek készítve az Azure Stackben történő használathoz, és tesztelésen estek át.

### <a name="azure-linux-agent"></a>Azure Linux-ügynök

Az Azure Linux-ügynököt (általában **WALinuxAgent** vagy **WALinuxAgent**) kötelező megadni, és az ügynök nem minden verziója működik Azure stackon. A 2.2.21 és a 2.2.34 (inclusive) közötti verziók nem támogatottak Azure Stackon. A legújabb ügynök-verziók 2.2.35 való használatához alkalmazza a 1901-es gyorsjavítást/1902-es gyorsjavítást, vagy frissítse a Azure Stack a 1903-es kiadásra (vagy újabb verzióra). Vegye figyelembe, hogy a [Cloud-init](https://cloud-init.io/) Azure Stack a 1910-nál újabb kiadásokban támogatott.

| Azure Stack Build | Azure Linux-ügynök létrehozása |
| ------------- | ------------- |
| 1.1901.0.99 vagy korábbi | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 vagy újabb |
| 1.1902.2.73  | 2.2.35 vagy újabb |
| 1.1903.0.35  | 2.2.35 vagy újabb |
| 1903 utáni buildek | 2.2.35 vagy újabb |
| Nem támogatott | 2.2.21-2.2.34 |
| 1910 utáni buildek | Minden Azure WALA-ügynök verziója|

Készítse elő saját linuxos rendszerképét a következő utasítások használatával:

* [CentOS-alapú disztribúciók](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES és openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>Cloud-init

A [Cloud-init](https://cloud-init.io/) Azure Stack a 1910-nál újabb kiadásokban támogatott. A Linux rendszerű virtuális gép testreszabásához a Cloud-init használatával a következő PowerShell-utasításokat használhatja: 

### <a name="step-1-create-a-cloud-inittxt-file-with-your-cloud-config"></a>1\. lépés: hozzon létre egy Cloud-init. txt fájlt a Cloud-config

Hozzon létre egy Cloud-init. txt nevű fájlt, és illessze be a következő Felhőbeli konfigurációt:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
  ```
  
### <a name="step-2-reference-the-cloud-inittxt-during-the-linux-vm-deployment"></a>2\. lépés: a Cloud-init. txt fájlra való hivatkozás a linuxos virtuális gép üzembe helyezése során

Töltse fel a fájlt egy Azure Storage-fiókba, Azure Stack Storage-fiókba vagy a GitHub-tárházba, amelyet a Azure Stack Linux rendszerű virtuális gép elérhet.
Jelenleg a Cloud-init használata a virtuálisgép-telepítéshez csak a REST, a PowerShell és a parancssori felületeken támogatott, és nem rendelkezik társított portál kezelőfelülettel a Azure Stack.

[Ezeket](../user/azure-stack-quick-create-vm-linux-powershell.md) az utasításokat követve hozhatja létre a linuxos virtuális gépet a PowerShell használatával, de ügyeljen arra, hogy a Cloud-init. txt fájlra hivatkozzon a `-CustomData` jelző részeként:

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>Rendszerkép hozzáadása a piactérhez

Kövesse a [rendszerkép hozzáadása a piactérhez](azure-stack-add-vm-image.md)című témakört. Győződjön meg arról, hogy a `OSType` paraméter `Linux`értékre van beállítva.

Miután hozzáadta a lemezképet a piactérhez, létrejön egy Marketplace-elem, és a felhasználók telepíthetnek egy Linux rendszerű virtuális gépet.

## <a name="next-steps"></a>További lépések

* [Azure Marketplace-elemek letöltése az Azure-ból az Azure Stackbe](azure-stack-download-azure-marketplace-item.md)
* [Azure Stack Marketplace – áttekintés](azure-stack-marketplace.md)
