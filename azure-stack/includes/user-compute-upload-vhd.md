---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: b8c5c7f439c4526b44299e6e3b33b343b11b61ed
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867304"
---
Feltöltheti a virtuális merevlemezt a portálon, vagy a portálon létrehozott tárolóval, a AzCopy használatával.

### <a name="portal-to-generate-sas-url-and-upload-vhd"></a>Az SAS URL-cím létrehozásához és a virtuális merevlemez feltöltéséhez használható portál

1. Jelentkezzen be az Azure Stack hub felhasználói portálra.

2. Válassza a **Storage-fiókok** lehetőséget, válasszon ki egy meglévő Storage-fiókot, vagy hozzon létre egy új Storage-fiókot.

3. A Storage-fiókhoz tartozó Storage-fiók panelen válassza a **Blobok** lehetőséget. Válassza ki a tárolót egy új tároló létrehozásához.

4. Írja be a tároló nevét, majd válassza a **blob (névtelen olvasási hozzáférés csak blobokhoz)** lehetőséget.

5. Ha a AzCopy használatával szeretné feltölteni a rendszerképet a portál helyett, hozzon létre egy SAS-tokent. Válassza a **megosztott elérési aláírás** lehetőséget a Storage-fiókban, majd válassza az **sas és a kapcsolati karakterlánc létrehozása** lehetőséget. Másolja ki és jegyezze fel a **blob Service sas URL-címét**. Ezt az URL-címet fogja használni, ha a AzCopy-t használja a virtuális merevlemez feltöltéséhez.

6. Válassza ki a tárolót, majd válassza a **feltöltés** lehetőséget. Töltse fel a VHD-t.

### <a name="azcopy-vhd"></a>AzCopy VHD

A Azure Storage Explorer vagy a AzCopy használatával csökkentheti az esélyét, hogy a VHD-fájl sérült legyen a feltöltési folyamat során, és a feltöltés gyorsabb lesz. A következő lépések a AzCopy Windows 10-es gépen való használatát jelentik. Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között.

1. Ha nincs telepítve a AzCopy, telepítse a AzCopy. Az útmutató a letöltéshez és az első lépések a AzCopy című cikkben talál útmutatást a [AzCopy megismeréséhez](/azure/storage/common/storage-use-azcopy-v10). Jegyezze fel, hogy hol tárolja a bináris fájlt. Az [elérési úthoz AzCopy adhat hozzá](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/) a PowerShell parancssorból.

2. Nyissa meg a PowerShellt a AzCopy a rendszerhéjból való használatához.

3. A AzCopy használatával töltse fel a virtuális merevlemezt a tárolóba a Storage-fiókba.

    ```powershell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    azcopy cp "/path/to/file.vhd" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS] --blob-type=PageBlob
    ```

> [!NOTE]  
> Töltse fel a VHD-t úgy, hogy az egy fájlnak a virtuális könyvtárba való feltöltéséhez hasonló szintaxist használjon. A Hozzáadás `--blob-type=PageBlob` gombra kattintva győződjön meg róla, hogy a VHD-t **oldal blobként** töltötte fel, nem **pedig** alapértelmezés szerint.

További információ a AzCopy és az egyéb tárolási eszközök használatáról: az [adatátviteli eszközök használata Azure stack hub Storage-ban](../user/azure-stack-storage-transfer.md).