---
title: Linux-lemezképek hozzáadása az Azure Stack hub piactérhez
description: Ismerje meg, hogyan adhat hozzá linuxos lemezképeket az Azure Stack hub Marketplace-hez.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2019
ms.openlocfilehash: 532f61b2b306dab833c35dab403226e70950d43a
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364086"
---
# <a name="add-linux-images-to-the-azure-stack-hub-marketplace"></a>Linux-lemezképek hozzáadása az Azure Stack hub piactérhez

A Linux rendszerű virtuális gépeket (VM-ket) telepítheti Azure Stack hubhoz egy Linux-alapú rendszerkép hozzáadásával Azure Stack hub piactéren. A Linux-rendszerképek Azure Stack hubhoz való hozzáadásának legegyszerűbb módja a Piactéri felügyelet. Ezeket a lemezképeket az Azure Stack hub-vel való kompatibilitás érdekében előkészített és tesztelték.

## <a name="marketplace-management"></a>Piactér-kezelés

Linuxos lemezképek letöltéséhez az Azure Marketplace-ről lásd: [Marketplace-elemek letöltése az Azure-ból Azure stack hubhoz](azure-stack-download-azure-marketplace-item.md). Válassza ki azokat a Linux-rendszerképeket, amelyekhez felhasználókat szeretne biztosítani az Azure Stack hub-ban.

Ezek a lemezképek gyakran frissülnek, ezért a piactér-felügyeletet gyakran érdemes naprakészen tartani.

## <a name="prepare-your-own-image"></a>Saját rendszerkép előkészítése

Amikor lehetséges, a Marketplace-kezelőből töltse le az elérhető rendszerképeket. Ezeket a lemezképeket Azure Stack hubhoz készítettük és tesztelték.

### <a name="azure-linux-agent"></a>Azure Linux-ügynök

Az Azure Linux-ügynököt (általában **WALinuxAgent** vagy **WALinuxAgent**) kötelező megadni, és az ügynök nem minden verziója működik Azure stack hub-on. A 2.2.21 és a 2.2.34 (inclusive) közötti verziók nem támogatottak Azure Stack hub-on. A legújabb ügynök-verziók 2.2.35 való használatához alkalmazza a 1901 gyorsjavítást/1902-es gyorsjavítást, vagy frissítse Azure Stack hub verzióját a 1903-es kiadásra (vagy újabb verzióra). Vegye figyelembe, hogy a [Cloud-init](https://cloud-init.io/) a 1910-nál nagyobb méretű Azure stack hub-kiadások esetén támogatott.

| Azure Stack hub-Build | Azure Linux-ügynök létrehozása |
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

A [Cloud-init](https://cloud-init.io/) a 1910-nál újabb verziókban Azure stack hub-kiadásokon is támogatott. A Linux rendszerű virtuális gép testreszabásához a Cloud-init használatával a következő PowerShell-utasításokat használhatja.

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

Töltse fel a fájlt egy Azure Storage-fiókba, Azure Stack hub Storage-fiókba vagy a GitHub-tárházba, amelyet a Azure Stack hub Linux rendszerű virtuális gép elérhet.
Jelenleg a Cloud-init használata a virtuálisgép-telepítéshez csak a REST, a PowerShell és a parancssori felületeken támogatott, és nem rendelkezik társított portál kezelőfelülettel Azure Stack hub-on.

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

## <a name="next-steps"></a>Következő lépések

* [Marketplace-elemek letöltése az Azure-ból Azure Stack hubhoz](azure-stack-download-azure-marketplace-item.md)
* [Azure Stack hub Marketplace – áttekintés](azure-stack-marketplace.md)
