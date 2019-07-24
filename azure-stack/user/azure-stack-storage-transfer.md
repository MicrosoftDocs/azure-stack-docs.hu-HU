---
title: Adatátviteli eszközök használata Azure Stack Storage szolgáltatásban | Microsoft Docs
description: További információ a Azure Stack Storage adatátviteli eszközeiről.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 6b6ab8ac8292f8d548b49331cf5d2e2ae100e68f
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418448"
---
# <a name="use-data-transfer-tools-in-azure-stack-storage"></a>Adatátviteli eszközök használata Azure Stack Storage szolgáltatásban

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack lemezek, blobok, táblák, várólisták és fiókkezelés függvények számára biztosít tárolási szolgáltatásokat. Egyes Azure Storage-eszközök akkor érhetők el, ha Azure Stack tárolóból vagy rendszerből kívánja felügyelni vagy áthelyezni az adatátvitelt. Ez a cikk áttekintést nyújt az elérhető eszközökről.

A követelmények határozzák meg, hogy a következő eszközök közül melyik működik a legmegfelelőbben:

* [AzCopy](#azcopy)

    Egy tároló-specifikus parancssori segédprogram, amelyet letöltheti az adatoknak az egyik objektumból a másikba való másolásához a Storage-fiókok között vagy azok között.

* [Azure PowerShell](#azure-powershell)

    Feladat-alapú, parancssori rendszerhéj és parancsnyelv, amely kifejezetten a rendszerfelügyelethez készült.

* [Azure CLI](#azure-cli)

    Nyílt forráskódú, platformfüggetlen eszköz, amely parancsokat biztosít az Azure és a Azure Stack platformmal való munkavégzéshez.

* [Microsoft Storage Explorer](#microsoft-azure-storage-explorer)

    Egy könnyen használható önálló alkalmazás felhasználói felülettel.

* [Blobfuse](#blobfuse)

    Egy virtuális fájlrendszer-illesztőprogram az Azure Blob Storagehoz, amely lehetővé teszi a meglévő blokk blob-adatai elérését a Storage-fiókban a Linux fájlrendszer használatával.

Az Azure és a Azure Stack közötti tárolási szolgáltatások közötti különbségek miatt előfordulhat, hogy az alábbi szakaszokban ismertetett eszközökhöz bizonyos követelmények vonatkoznak. A Azure stack Storage és az Azure Storage közötti összehasonlításért lásd [: Azure stack Storage: Különbségek és szempontok](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

A AzCopy egy parancssori segédprogram, amely az adatok másolását Microsoft Azure Blob és Table Storage szolgáltatásból, az optimális teljesítményt biztosító egyszerű parancsokkal. Az adatok az egyik objektumból a másikba másolhatók a Storage-fiókok között vagy azok között.

### <a name="download-and-install-azcopy"></a>A AzCopy letöltése és telepítése

* Az 1811-es vagy újabb verziójának frissítéséhez [töltse le a AzCopy v10 +](/azure/storage/common/storage-use-azcopy-v10#download-azcopy)verziót.
* Az előző verziókhoz (1802 – 1809 frissítés) [töltse le a AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).

### <a name="azcopy-101-configuration-and-limits"></a>AzCopy 10,1 konfiguráció és korlátok

A AzCopy 10,1 mostantól a régebbi API-verziók használatára is konfigurálható. Ez lehetővé teszi (korlátozott) a Azure Stack támogatását.
A AzCopy API-verziójának a Azure stack támogatásához való konfigurálásához `AZCOPY_DEFAULT_SERVICE_API_VERSION` állítsa a környezeti `2017-11-09`változót a következőre:.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | A parancssorban használja a következőket:`set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`<br> A PowerShellben használja a következőket:`$env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"`|
| **Linux** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |
| **MacOS** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |

A AzCopy 10,1-ben a következő funkciók támogatottak Azure Stack esetén:

| Funkció | Támogatott műveletek |
| --- | --- |
|Tároló kezelése|Tároló létrehozása<br>Tárolók tartalmának listázása
|Feladatok kezelése|Feladatok megjelenítése<br>Feladatok folytatása
|BLOB eltávolítása|Egyetlen blob eltávolítása<br>Teljes vagy részleges virtuális könyvtár eltávolítása
|Fájl feltöltése|Fájl feltöltése<br>Könyvtár feltöltése<br>Könyvtár tartalmának feltöltése
|Fájl letöltése|Fájl letöltése<br>Könyvtár letöltése<br>Könyvtár tartalmának letöltése
|Fájl szinkronizálása|Tároló szinkronizálása helyi fájlrendszerrel<br>Helyi fájlrendszer szinkronizálása egy tárolóval

   > [!NOTE]
   > * A Azure Stack nem támogatja a Azure Active Directory (AD) használatával történő AzCopy engedélyezési hitelesítő adatok megadását. A Storage-objektumokat megosztott hozzáférési aláírás (SAS) token használatával kell elérni Azure Stack.
   > * Azure Stack nem támogatja a szinkron adatátvitelt két Azure Stack blob-helye, valamint az Azure Storage és a Azure Stack között. A "azcopy CP" nem használható az adatok Azure Stackból az Azure Storage-ba való áthelyezésére (vagy a másik felé) közvetlenül a AzCopy 10,1-mel.

### <a name="azcopy-command-examples-for-data-transfer"></a>AzCopy-példák adatátvitel esetén

Az alábbi példák az adatok Azure Stack blobokra történő másolásának tipikus forgatókönyveit követik. További információ: Ismerkedés [a AzCopy szolgáltatással](/azure/storage/common/storage-use-azcopy-v10).

### <a name="download-all-blobs-to-a-local-disk"></a>Összes blob letöltése helyi lemezre

```
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

### <a name="upload-single-file-to-virtual-directory"></a>Egyetlen fájl feltöltése a virtuális könyvtárba

```
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

### <a name="azcopy-known-issues"></a>AzCopy ismert problémák

 - Nem érhető el bármely AzCopy művelet, mert a file Storage még nem érhető el Azure Stackban.
 - Ha az adatok átvitele két Azure Stack blob-hely között, illetve Azure Stack és az Azure Storage között a AzCopy 10.1 használatával végezhető el, először le kell töltenie az adatok egy helyi helyre, majd újra fel kell töltenie a célkiszolgálóra a Azure Stack vagy az Azure Storage szolgáltatásban. Vagy használhatja a AzCopy 7,1-es verzióját, és az adatok másolásához adja meg az átvitelt a **/SyncCopy** kapcsolóval.  
 - A AzCopy Linux-verziója csak a 1802-es vagy újabb verziót támogatja, és nem támogatja a Table service.
 
## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell egy modul, amely parancsmagokat biztosít a szolgáltatások Azure-és Azure Stack-beli kezeléséhez. Ez egy feladat-alapú, parancssori rendszerhéj és programozási nyelv, amely kifejezetten a rendszerfelügyelethez készült.

### <a name="install-and-configure-powershell-for-azure-stack"></a>A PowerShell telepítése és konfigurálása Azure Stack

Azure Stack kompatibilis Azure PowerShell modulok szükségesek a Azure Stack használatához. További információ: a [PowerShell telepítése Azure Stackhez](../operator/azure-stack-powershell-install.md) és [a Azure stack felhasználó PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-user.md).

### <a name="powershell-sample-script-for-azure-stack"></a>PowerShell-minta parancsfájl a Azure Stackhoz 

Ez a példa feltételezi, hogy sikeresen [telepítette a PowerShellt a Azure Stackhoz](../operator/azure-stack-powershell-install.md). Ez a szkript segítséget nyújt a konfiguráció végrehajtásában, és felkéri a Azure Stack bérlői hitelesítő adatait, hogy vegye fel a fiókját a helyi PowerShell-környezetbe. A szkript ezután beállítja az alapértelmezett Azure-előfizetést, létrehoz egy új Storage-fiókot az Azure-ban, létrehoz egy új tárolót az új Storage-fiókban, és feltölt egy meglévő lemezképfájlt (blobot) a tárolóba. Miután a parancsfájl felsorolja az adott tárolóban lévő összes blobot, a rendszer létrehoz egy új célhelyet a helyi számítógépen, és letölti a lemezképfájlt.

1. Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](../operator/azure-stack-powershell-install.md).
2. Töltse le a [az Azure Stack működéséhez szükséges eszközök](../operator/azure-stack-powershell-download.md).
3. Nyissa meg a **Windows PowerShell integrált parancsprogram-kezelési környezet** és a **Futtatás rendszergazdaként**lehetőséget, majd kattintson az**új** **fájl** > elemre egy új parancsfájl létrehozásához.
4. Másolja az alábbi szkriptet, és illessze be az új parancsfájlba.
5. Frissítse a parancsfájl-változókat a konfigurációs beállítások alapján.
   > [!NOTE]
   > Ezt a szkriptet a **AzureStack_Tools**gyökérkönyvtárában kell futtatni.

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>PowerShell – ismert problémák

A felhasználói műveletekhez a Azure Stack aktuálisan kompatibilis Azure PowerShell moduljának verziója 1.2.11. Eltér a Azure PowerShell legújabb verziójától. Ez a különbség a tárolási szolgáltatások működését befolyásolja a következő módon:

A 1.2.11 verziójának visszatérési értékének formátuma `Get-AzureRmStorageAccountKey` két tulajdonsággal rendelkezik: `Key1` és `Key2`, míg az aktuális Azure-verzió az összes fiók kulcsát tartalmazó tömböt ad vissza.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

További információ: [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey).

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. Telepítheti macOS, Linux és Windows rendszeren, és futtathatja a parancssorból.

Az Azure CLI az Azure-erőforrások parancssorból történő kezeléséhez és felügyeletéhez, valamint a Azure Resource Manageron dolgozó automatizálási parancsfájlok létrehozásához van optimalizálva. Számos, a Azure Stack-portálon található funkciót tartalmaz, beleértve a gazdag adatelérést is.

Azure Stack az Azure CLI 2,0-es vagy újabb verziójára van szükség. Az Azure CLI Azure Stack használatával történő telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure stack parancssori felület telepítése és konfigurálása](azure-stack-version-profiles-azurecli2.md). További információ arról, hogyan használhatja az Azure CLI-t több, a Azure Stack Storage-fiók erőforrásaival kapcsolatos feladat elvégzésére: [Az Azure CLI használata az Azure Storage](/azure/storage/storage-azure-cli)-ban.

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure CLI-minta parancsfájl a Azure Stack

A CLI telepítésének és konfigurálásának befejezése után kipróbálhatja a következő lépéseket a kisebb rendszerhéj-parancsfájlokkal való együttműködéshez a Azure Stack Storage-erőforrások használatához. A parancsfájl a következő műveleteket hajtja végre:

* Létrehoz egy új tárolót a Storage-fiókban.
* Feltölt egy meglévő fájlt (blobként) a tárolóba.
* Felsorolja a tárolóban lévő összes blobot.
* Letölti a fájlt a megadott helyi számítógépen lévő célhelyre.

A szkript futtatása előtt győződjön meg arról, hogy sikeresen tud csatlakozni, és bejelentkezik a cél Azure Stackba.

1. Nyissa meg a kedvenc szövegszerkesztőjét, majd másolja és illessze be az előző szkriptet a szerkesztőbe.
2. Frissítse a szkript változóit a konfigurációs beállításoknak megfelelően.
3. A szükséges változók frissítése után mentse a parancsfájlt, és lépjen ki a szerkesztőből. A következő lépések feltételezik, hogy elnevezte a **my_storage_sample. sh**parancsfájlt.
4. A parancsfájlt végrehajthatóként kell megjelölnie, ha szükséges:`chmod +x my_storage_sample.sh`
5. Futtassa a szkriptet. Például a Bashben:`./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

A Azure Storage Explorer egy önálló alkalmazás a Microsofttól. Lehetővé teszi az Azure Storage és a Azure Stack Storage-alapú adattárolási funkcióinak egyszerű használatát Windows, macOS és Linux rendszerű számítógépeken. Ha egyszerűen szeretné kezelni a Azure Stack Storage-adatait, érdemes lehet Microsoft Azure Storage Explorert használni.

* Ha többet szeretne megtudni arról, hogyan konfigurálhatja a Azure Storage Explorert a Azure Stack való együttműködésre, tekintse meg a [Storage Explorer Azure stack-előfizetéshez való kapcsolódás](azure-stack-storage-connect-se.md)
* További információ a Microsoft Azure Storage Explorerról: Ismerkedés [a Storage Explorerrel](/azure/vs-azure-tools-storage-manage-with-storage-explorer)

## <a name="blobfuse"></a>Blobfuse 

A [Blobfuse](https://github.com/Azure/azure-storage-fuse) az Azure Blob Storage virtuális fájlrendszer-illesztőprogramja, amely lehetővé teszi a meglévő blokk blob-adatai elérését a Storage-fiókban a Linux fájlrendszerén keresztül. Az Azure Blob Storage egy objektum-tárolási szolgáltatás, ezért nem rendelkezik hierarchikus névtérrel. A Blobfuse a virtuális könyvtár sémájának használatával biztosítja ezt a névteret a továbbítási `/` perjelek elválasztóként való használatával. A Blobfuse az Azure-on és a Azure Stackon is működik. 

Ha többet szeretne megtudni arról, hogyan csatlakoztatható a blob Storage fájlrendszerrel a Blobfuse Linux rendszeren, tekintse meg a [blob Storage csatlakoztatása](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)fájlrendszerként a Blobfuse-mel című témakört. 

Azure Stack esetében meg kell adni a *blobEndpoint* a Storage-fiók hitelesítő adatainak konfigurálásakor a accountName, a AccountKey/sasToken és a containerName mellett.

A Azure Stack Development Kitban (ASDK) a *blobEndpoint* kell lennie `myaccount.blob.local.azurestack.external`. Azure Stack integrált rendszeren lépjen kapcsolatba a felhő rendszergazdájával, ha nem biztos benne, hogy a végpontról van szó.

a *accountKey* és a *sasToken* egyszerre csak egy konfigurálható. Ha meg van adva egy Storage-fiók kulcsa, a hitelesítő adatok konfigurációs fájljának formátuma a következő:

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Megosztott hozzáférési jogkivonat megadása esetén a hitelesítő adatok konfigurációs fájljának formátuma a következő:

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>További lépések

* [A Storage Explorer összekötése Azure Stack-előfizetéssel](azure-stack-storage-connect-se.md)
* [Ismerkedés a Storage Explorerrel](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure-konzisztens tárolás: különbségek és megfontolások](azure-stack-acs-differences.md)
* [A Microsoft Azure Storage bemutatása](/azure/storage/common/storage-introduction)
