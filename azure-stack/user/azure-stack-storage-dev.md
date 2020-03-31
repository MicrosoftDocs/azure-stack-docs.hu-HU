---
title: Ismerkedés a Azure Stack hub Storage fejlesztői eszközeivel
description: Útmutató az Azure Stack hub Storage fejlesztői eszközök használatának megkezdéséhez
author: mattbriggs
ms.author: mabrigg
ms.date: 1/22/2020
ms.topic: conceptual
ms.reviewer: xiaofmao
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: dc89de51c730bdd50000d004555b47d82ab08be6
ms.sourcegitcommit: 8e480803ee0672eea328e4f14a8b75be94c8fc7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80391483"
---
# <a name="get-started-with-azure-stack-hub-storage-development-tools"></a>Ismerkedés a Azure Stack hub Storage fejlesztői eszközeivel

Microsoft Azure Stack hub olyan tárolási szolgáltatásokat biztosít, amelyek blob, Table és üzenetsor-tárolót tartalmaznak.

Ez a cikk útmutatóként szolgál az Azure Stack hub Storage Fejlesztőeszközök használatának megkezdéséhez. Részletesebb információ és mintakód található a megfelelő Azure Storage-oktatóanyagokban.

> [!NOTE]  
> A Azure Stack hub Storage és az Azure Storage között különbségek vannak, beleértve az egyes platformokra vonatkozó egyedi követelményeket is. Előfordulhat például, hogy az Azure Stack hub esetében egyediek az ügyféloldali kódtárak és a végponti utótagok. További információ [: Azure stack hub Storage: különbségek és megfontolások](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azure-ügyfél kódtárai

A Storage ügyféloldali kódtárai esetében vegye figyelembe a REST API kompatibilis verziót. Az Azure Stack hub-végpontot is meg kell adnia a kódban.


::: moniker range=">azs-2002"
### <a name="2002-update-or-newer-versions"></a>2002 frissítés vagy újabb verzió

| Ügyfélkódtár | Azure Stack hub támogatott verziója | Hivatkozás | Végpont specifikációja |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 11.0.0 | Nuget-csomag:<br>Gyakori: <https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/11.0.0><br>BLOB: <https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/11.0.0><br>Üzenetsor: <https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/11.0.0><br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-net/releases/tag/v11.0.0> | app.config file |
| Java | 7.0.0 | Maven-csomag:<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0><br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0> | A kapcsolatok karakterláncának beállítása |
| Node.js | 2.8.3 | NPM hivatkozása:<br><https://www.npmjs.com/package/azure-storage><br>(Futtatás: `npm install azure-storage@2.8.3`)<br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3> | Szolgáltatási példány deklarációja |
| C++ | 7.1.0 | GitHub-kiadás:<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v7.1.0> | A kapcsolatok karakterláncának beállítása |
| PHP | 1.2.0 | GitHub-kiadás:<br>Gyakori: <https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common><br>BLOB: <https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob><br>Üzenetsor: <https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue><br>Tábla: <https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table><br> <br>Telepítés a Zeneszerzőn keresztül (további tudnivalókért [tekintse meg az alábbi részleteket](#install-php-client-via-composer---current).) | A kapcsolatok karakterláncának beállítása |
| Python | 2.1.0 | GitHub-kiadás:<br>Gyakori: <https://github.com/Azure/azure-storage-python/releases/tag/v2.1.0-common><br>BLOB: <https://github.com/Azure/azure-storage-python/releases/tag/v2.1.0-blob><br>Üzenetsor: <https://github.com/Azure/azure-storage-python/releases/tag/v2.1.0-queue> | Szolgáltatási példány deklarációja |
| Ruby | 1.0.1 | RubyGems-csomag:<br>Gyakori: <https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>BLOB: <https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>Üzenetsor: <https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Tábla: <https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub-kiadás:<br>Gyakori: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>BLOB: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>Üzenetsor: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Tábla: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | A kapcsolatok karakterláncának beállítása |

#### <a name="install-php-client-via-composer---current"></a>PHP-ügyfél telepítése a Zeneszerzőn keresztül – aktuális

Telepítés a zeneszerző használatával: (a blobot példaként kell megtennie).

1. Hozzon létre egy **zeneszerző. JSON** nevű fájlt a projekt gyökerében a következő kóddal:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. A [zeneszerző. farmakovigilancia](https://getcomposer.org/composer.phar) letöltése a projekt gyökerébe.
3. Futtatás: `php composer.phar install`.
::: moniker-end

::: moniker range=">=azs-1811 <azs-2002"
### <a name="previous-versions-1811-to-1910-update"></a>Korábbi verziók (1811 – 1910 frissítés)

| Ügyfélkódtár | Azure Stack hub támogatott verziója | Hivatkozás | Végpont specifikációja |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | Nuget-csomag:<br><https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0><br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0> | app.config file |
| Java | 7.0.0 | Maven-csomag:<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0><br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0> | A kapcsolatok karakterláncának beállítása |
| Node.js | 2.8.3 | NPM hivatkozása:<br><https://www.npmjs.com/package/azure-storage><br>(Futtatás: `npm install azure-storage@2.8.3`)<br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3> | Szolgáltatási példány deklarációja |
| C++ | 5.2.0 | Nuget-csomag:<br><https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0><br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0> | A kapcsolatok karakterláncának beállítása |
| PHP | 1.2.0 | GitHub-kiadás:<br>Gyakori: <https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common><br>BLOB: <https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob><br>Várólista<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue><br>Tábla: <https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table><br> <br>Telepítés a Zeneszerzőn keresztül (további tudnivalókért [tekintse meg az alábbi részleteket](#install-php-client-via-composer---current).) | A kapcsolatok karakterláncának beállítása |
| Python | 1.1.0 | GitHub-kiadás:<br>Közös<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common><br>BLOB<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob><br>Várólista<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue> | Szolgáltatási példány deklarációja |
| Ruby | 1.0.1 | RubyGems-csomag:<br>Közös<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>BLOB: <https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>Üzenetsor: <https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Tábla: <https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub-kiadás:<br>Gyakori: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>BLOB: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>Üzenetsor: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Tábla: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | A kapcsolatok karakterláncának beállítása |

#### <a name="install-php-client-via-composer---current"></a>PHP-ügyfél telepítése a Zeneszerzőn keresztül – aktuális

Telepítés a zeneszerző használatával: (a blobot példaként kell megtennie).

1. Hozzon létre egy **zeneszerző. JSON** nevű fájlt a projekt gyökerében a következő kóddal:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. A [zeneszerző. farmakovigilancia](https://getcomposer.org/composer.phar) letöltése a projekt gyökerébe.
3. Futtatás: `php composer.phar install`.
::: moniker-end

::: moniker range=">=azs-1802 <=azs-1809"
### <a name="previous-versions-1802-to-1809-update"></a>Korábbi verziók (1802 – 1809 frissítés)

| Ügyfélkódtár | Azure Stack hub támogatott verziója | Hivatkozás | Végpont specifikációja |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Nuget-csomag:<br><https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0><br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0> | app.config file |
| Java | 6.1.0 | Maven-csomag:<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0><br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0> | A kapcsolatok karakterláncának beállítása |
| Node.js | 2.7.0 | NPM hivatkozása:<br><https://www.npmjs.com/package/azure-storage><br>(Futtatás: `npm install azure-storage@2.7.0`)<br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0> | Szolgáltatási példány deklarációja |
| C++ | 3.1.0 | Nuget-csomag:<br><https://www.nuget.org/packages/wastorage.v140/3.1.0><br> <br>GitHub-kiadás:<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0> | A kapcsolatok karakterláncának beállítása |
| PHP | 1.0.0 | GitHub-kiadás:<br>Gyakori: <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common><br>BLOB: <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob><br>Várólista<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue><br>Tábla: <https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table><br> <br>Telepítés a Zeneszerzőn keresztül (lásd az alábbi adatokat).) | A kapcsolatok karakterláncának beállítása |
| Python | 1.0.0 | GitHub-kiadás:<br>Közös<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common><br>BLOB<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob><br>Várólista<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue> | Szolgáltatási példány deklarációja |
| Ruby | 1.0.1 | RubyGems-csomag:<br>Közös<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>BLOB: <https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>Üzenetsor: <https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Tábla: <https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub-kiadás:<br>Gyakori: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>BLOB: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>Üzenetsor: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Tábla: <https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | A kapcsolatok karakterláncának beállítása |

#### <a name="install-php-client-via-composer---previous"></a>PHP-ügyfél telepítése a Zeneszerzőn keresztül – előző

A következőn keresztül történő telepítéshez: (blob készítése példaként).

1. Hozzon létre egy **zeneszerző. JSON** nevű fájlt a projekt gyökerében a következő kóddal:

   ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
   ```

2. A [zeneszerző. farmakovigilancia](https://getcomposer.org/composer.phar) letöltése a projekt gyökerébe.
3. Futtatás: `php composer.phar install`.
:::moniker-end

## <a name="endpoint-declaration"></a>Végpont deklarációja

Azure Stack hub-végpont két részből áll: egy régió és a Azure Stack hub tartományának nevével.
A Azure Stack Development Kit az alapértelmezett végpont **helyi. azurestack. external**.
Ha nem biztos benne, hogy a végpontról van szó, forduljon a felhő rendszergazdájához.

## <a name="examples"></a>Példák

### <a name="net"></a>.NET

Azure Stack hub esetében a végpont utótagja az app. config fájlban van megadva:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Azure Stack hub esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Azure Stack hub esetében a végpont utótagja a deklarációs példányban van megadva:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Azure Stack hub esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Azure Stack hub esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```php
$connectionString = 'BlobEndpoint=https://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=https:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=https:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Azure Stack hub esetében a végpont utótagja a deklarációs példányban van megadva:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Azure Stack hub esetében a végpont utótagját a rendszer a kapcsolatok karakterláncának beállításában adja meg:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

A következő Azure Blob Storage-oktatóanyagok a Azure Stack hub-ra vonatkoznak. Figyelje meg, hogy az előző [példák](#examples) részben ismertetett Azure stack hub adott végponti utótagra vonatkozó követelménye.

* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)
* [How to use Blob storage from Java (A Blob Storage használata Javával)](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [How to use Blob storage from Node.js (A Blob Storage használata Node.js-sel)](/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage)
* [A blob Storage használata a következővel:C++](/azure/storage/blobs/storage-c-plus-plus-how-to-use-blobs)
* [How to use Blob storage from PHP (A Blob Storage használata PHP-val)](/azure/storage/blobs/storage-php-how-to-use-blobs)
* [Az Azure Blob Storage használata a Pythonból](/azure/storage/blobs/storage-python-how-to-use-blob-storage)
* [How to use Blob storage from Ruby (A Blob Storage használata Rubyval)](/azure/storage/blobs/storage-ruby-how-to-use-blob-storage)

## <a name="queue-storage"></a>Queue Storage

A következő Azure üzenetsor-tárolási oktatóanyagok a Azure Stack hub-ra vonatkoznak. Figyelje meg, hogy az előző [példák](#examples) részben ismertetett Azure stack hub adott végponti utótagra vonatkozó követelménye.

* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](/azure/storage/queues/storage-dotnet-how-to-use-queues)
* [How to use Queue Storage from Java (A Queue Storage használata Javával)](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [How to use Queue storage from Node.js (A Queue Storage használata Node.js-sel)](/azure/storage/queues/storage-nodejs-how-to-use-queues)
* [A várólista-tároló használata a következőből:C++](/azure/storage/queues/storage-c-plus-plus-how-to-use-queues)
* [How to use Queue storage from PHP (A Queue Storage használata PHP-val)](/azure/storage/queues/storage-php-how-to-use-queues)
* [How to use Queue storage from Python (A Queue Storage használata Pythonnal)](/azure/storage/queues/storage-python-how-to-use-queue-storage)
* [How to use Queue storage from Ruby (A Queue Storage használata Rubyval)](/azure/storage/queues/storage-ruby-how-to-use-queue-storage)

## <a name="table-storage"></a>Table Storage

A következő Azure Table Storage-oktatóanyagok a Azure Stack hub-ra vonatkoznak. Figyelje meg, hogy az előző [példák](#examples) részben ismertetett Azure stack hub adott végponti utótagra vonatkozó követelménye.

* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](/azure/cosmos-db/table-storage-how-to-use-dotnet)
* [How to use Table storage from Java (A Table Storage használata Javával)](/azure/cosmos-db/table-storage-how-to-use-java)
* [Az Azure Table Storage használata Node.js-sel](/azure/cosmos-db/table-storage-how-to-use-nodejs)
* [A Table Storage használata a következővel:C++](/azure/cosmos-db/table-storage-how-to-use-c-plus)
* [How to use Table storage from PHP (A Table Storage használata PHP-val)](/azure/cosmos-db/table-storage-how-to-use-php)
* [A Table Storage használata a Pythonban](/azure/cosmos-db/table-storage-how-to-use-python)
* [How to use Table storage from Ruby (A Table Storage használata Rubyval)](/azure/cosmos-db/table-storage-how-to-use-ruby)

## <a name="next-steps"></a>Következő lépések

* [A Microsoft Azure Storage bemutatása](/azure/storage/common/storage-introduction)
