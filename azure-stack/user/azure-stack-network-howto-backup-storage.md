---
title: A Storage-fiókok biztonsági mentése a Azure Stack-on | Microsoft Docs
description: Megtudhatja, hogyan készíthet biztonsági másolatot a Storage-fiókjairól Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 3f6ed5604bd2d32d9d030ceb4b5f67567362cc34
ms.sourcegitcommit: 4d7611d81da6f2f8ef50adab3c09f9122a75bc9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73145761"
---
# <a name="back-up-your-storage-accounts-on-azure-stack"></a>A Storage-fiókok biztonsági mentése Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk a Storage-fiókok védelmét és helyreállítását vizsgálja Azure Stack Azure Storage-fiókokon belül.

## <a name="elements-of-the-solution"></a>A megoldás elemei

Ez a szakasz a megoldás és a főbb részek általános szerkezetét vizsgálja.

![Azure Stack tároló biztonsági mentése](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>Alkalmazás rétege

Az adatok replikálása a Storage-fiókok között külön Azure Stack skálázási egységeken történik több [put blob](https://docs.microsoft.com/rest/api/storageservices/put-blob) kiállításával, vagy a [Letiltás](https://docs.microsoft.com/rest/api/storageservices/put-block) műveletekkel több helynek is írhat objektumokat. Azt is megteheti, hogy az alkalmazás kiállíthatja a [blob másolása](https://docs.microsoft.com/rest/api/storageservices/copy-blob) műveletet úgy, hogy a blobot egy külön méretezési egységben üzemeltetett Storage-fiókba másolja, miután a Put művelet befejeződik az elsődleges fiókban.

### <a name="scheduled-copy-task"></a>Ütemezett másolási feladat

A AzCopy egy kiváló eszköz, amellyel helyi fájlrendszerekből, Azure Cloud Storage-ból, Azure Stack Tárterületből és S3-ból másolhatók az adatok. A AzCopy jelenleg két Azure Stack Storage-fiók között nem tud Adatmásolást készíteni. Az objektumok forrásból Azure Stack Storage-fiókba való másolása egy célként megadott Azure Stack Storage-fiókhoz egy közvetítő helyi fájlrendszer szükséges.

További információkért tekintse meg a AzCopy az [adatátviteli eszközök használata Azure stack Storage](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer?view=azs-1908#azcopy) -ban című cikket.

### <a name="azure-stack-source"></a>Azure Stack (forrás)

Ez annak a Storage-fióknak a forrása, amelyre biztonsági másolatot szeretne készíteni.

Szüksége lesz a forrás Storage-fiók URL-címére és az SAS-jogkivonatra. A Storage-fiókokkal való munkavégzéssel kapcsolatos útmutatásért lásd: [Azure stack Storage fejlesztői eszközök használatának első lépései](azure-stack-storage-dev.md).

### <a name="azure-stack-target"></a>Azure Stack (cél)

Ez az a cél, amely a biztonsági mentéshez használni kívánt fiókadatokat tárolja. A célként megadott Azure Stack példánynak a célként megadott Azure Stacktól eltérő helyen kell lennie. A forrásnak pedig csatlakoznia kell a célhoz.

Szüksége lesz a forrás Storage-fiók URL-címére és az SAS-jogkivonatra. A Storage-fiókokkal való munkavégzéssel kapcsolatos útmutatásért lásd: [Azure stack Storage fejlesztői eszközök használatának első lépései](azure-stack-storage-dev.md).

### <a name="intermediary-local-filesystem"></a>Közvetítő helyi fájlrendszer

Szüksége lesz egy helyre a AzCopy futtatásához és az adatok tárolásához a forrásból való másoláskor, majd a cél Azure Stack írásakor. Ez egy köztes kiszolgáló a forrás Azure Stack.

Létrehozhat egy Linux-vagy Windows Server-kiszolgálót közbenső kiszolgálóként. A kiszolgálónak elegendő lemezterülettel kell rendelkeznie a forrás Storage-fiók tárolójában lévő összes objektum tárolásához.
- A Linux-kiszolgálók beállításával kapcsolatos útmutatásért tekintse meg a [Linux Server rendszerű virtuális gép létrehozása a Azure stack portál használatával](azure-stack-quick-linux-portal.md)című témakört.  
- A Windows Server beállításával kapcsolatos útmutatásért tekintse meg a [Windows Server rendszerű virtuális gép létrehozása a Azure stack-portálon](azure-stack-quick-windows-portal.md)című témakört.  

Miután beállította a Windows Servert, telepítenie kell [Azure stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) -t és [Azure stack eszközöket](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json).

## <a name="set-up-backup-for-storage-accounts"></a>Biztonsági mentés beállítása a Storage-fiókokhoz

1. A forrás és a cél Storage-fiókok blob-végpontjának beolvasása.

    ![Azure Stack tároló biztonsági mentése](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. SAS-jogkivonatok létrehozása és rögzítése a forrás és a cél Storage-fiókok számára.

    ![Azure Stack tároló biztonsági mentése](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. Telepítse a [AzCopy](https://github.com/Azure/azure-storage-azcopy) -t a közbenső kiszolgálóra, és állítsa be az API-verziót Azure stack Storage-fiókokhoz.

    - Windows Server esetén:

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - Linux (Ubuntu) kiszolgáló esetén:

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. A köztes kiszolgálón hozzon létre parancsfájlt. Frissítse ezt a parancsot a **Storage-fiókjával**, az **sas-kulccsal**és a **helyi könyvtár elérési útjával**. Az adatok növekményes másolásához a **forrás** Storage-fiókból futtassa a parancsfájlt.

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  Adja meg a **Storage-fiókot**, a * * sas-kulcs * * és a * * helyi könyvtár elérési útját.  Ezzel a megoldással növekményes Adatmásolást fog használni a **cél** Storage-fiókba
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  A cron vagy a Windows Feladatütemező használatával ütemezze a másolást a forrás Azure Stack Storage-fiókból a köztes kiszolgáló helyi tárolójába. Ezután másolja a helyi tárterületet a köztes kiszolgálóról a cél Azure Stack Storage-fiókba.

    Ennek a megoldásnak a RPO a/MO paraméter értéke, valamint a forrás-és a köztes kiszolgáló és a célként megadott fiók közötti hálózati sávszélesség határozza meg.

    - Linux (Ubuntu) kiszolgáló esetén:

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | Paraméter | Megjegyzés | 
    | ---- | ---- |
    | /SC | Használjon perces ütemtervet. |
    | /MO | Egy *XX* perces intervallum. |
    | /TN | A feladat neve. |
    | /TR | A `script.bat` fájl elérési útja. |


    - Windows Server esetén:

    A Windows-feladatok ütemezésének használatáról további információt a Feladatütemező [fejlesztők](https://docs.microsoft.com/windows/win32/taskschd/task-scheduler-start-page) számára című témakörben talál.
    

## <a name="use-your-storage-account-in-a-disaster"></a>A Storage-fiók használata katasztrófa esetén

Minden Azure Stack Storage-fiók rendelkezik egy egyedi DNS-névvel, amely maga a Azure Stack régió nevéből származik, például `https://krsource.blob.east.asicdc.com/`. A DNS-névvel írt és onnan beolvasott alkalmazásoknak meg kell adni a Storage-fiók DNS-nevének változását, ha a célként megadott fiók esetében például `https://krtarget.blob.west.asicdc.com/` kell használni a katasztrófák során.

Az alkalmazás-kapcsolódási karakterláncok akkor módosíthatók, ha az objektumok áttelepítéséhez meg kell adni egy adott vészhelyzetet, vagy ha CNAME rekord van használatban a forrás-és a cél Storage-fiókok előtt, a terheléselosztó konfigurálható manuális feladatátvételi algoritmussal, amely lehetővé teszi, hogy a rendszergazda deklarálja a célt

Ha az alkalmazás az HRE vagy a AD FS helyett a SAS-t használja, a fenti metódus nem fog működni, és az alkalmazás-kapcsolódási karakterláncokat frissíteni kell a célként megadott Storage-fiók URL-címével és a cél Storage-fiókhoz generált SAS-kulcsokkal.

## <a name="next-steps"></a>Következő lépések

[Ismerkedés a Azure Stack Storage fejlesztői eszközeivel](azure-stack-storage-dev.md)