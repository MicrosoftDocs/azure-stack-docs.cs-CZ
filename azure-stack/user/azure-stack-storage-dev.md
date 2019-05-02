---
title: Začínáme s Azure Stack nástroje pro vývoj úložišť | Dokumentace Microsoftu
description: Pokyny, které vám umožní začít pomocí nástroje pro vývoj úložišť Azure Stack
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 02/27/2019
ms.topic: conceptual
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 28fc182404107b100c9b69ecd73d961d83bd3039
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985624"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Začínáme s Azure Stack nástroje pro vývoj úložišť

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Microsoft Azure Stack nabízí sadu služeb úložiště, který obsahuje objekt blob, table a queue storage.

Použijte tento článek jako vodítko a začněte využívat nástroje pro vývoj úložišť Azure Stack. Podrobnější informace a ukázky kódu najdete v odpovídající kurzy služby Azure storage.

> [!NOTE]  
> Jsou známy rozdíly mezi úložiště služby Azure Stack a Azure storage, včetně specifické požadavky pro jednotlivé platformy. Například existují konkrétní klientské knihovny a určitého koncového bodu přípona požadavky pro Azure Stack. Další informace najdete v tématu [úložiště služby Azure Stack: Rozdíly a aspekty](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Klientské knihovny Azure

Pro klientské knihovny pro úložiště mějte na paměti, která je kompatibilní s rozhraním REST API verze. Ve vašem kódu, musíte zadat také koncový bod služby Azure Stack.

### <a name="1811-update-or-newer-versions"></a>1811 update nebo novější verze

| Klientská knihovna | Podporovaná verze služby Azure Stack | Odkaz | Koncový bod specifikace |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | Balíček Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0 | souboru app.config |
| Java | 7.0.0 | Maven balíček:<br>https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0 | Nastavení připojovací řetězce |
| Node.js | 2.8.3 | Odkaz na NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Spuštění: `npm install azure-storage@2.8.3`)<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3 | Deklarace instance služby |
| C++ | 5.2.0 | Balíček Nuget:<br>https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0 | Nastavení připojovací řetězce |
| PHP | 1.2.0 | Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common<br>Objekt BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob<br>Fronta:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue<br>Tabulka: https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table<br> <br>Instalace přes Composer (Další informace, [viz podrobnosti níže](#install-php-client-via-composer---current).) | Nastavení připojovací řetězce |
| Python | 1.1.0 | Verze Githubu:<br>Běžné:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common<br>Objekt BLOB:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob<br>Fronta:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue | Deklarace instance služby |
| Ruby | 1.0.1 | Balíček RubyGems:<br>Běžné:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Objekt BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Fronta: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabulka: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Objekt BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Fronta: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabulka: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Nastavení připojovací řetězce |

#### <a name="install-php-client-via-composer---current"></a>Instalace klienta PHP prostřednictvím autora – aktuální

Chcete-li nainstalovat prostřednictvím autora: (trvat objektů blob jako příklad).

1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři projektu s následujícím kódem:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Stáhněte si [composer.phar](https://getcomposer.org/composer.phar) do kořenového adresáře projektu.
3. Spustit: `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Předchozí verze (aktualizace 1802 k 1809)

| Klientská knihovna | Podporovaná verze služby Azure Stack | Odkaz | Koncový bod specifikace |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Balíček Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | souboru app.config |
| Java | 6.1.0 | Maven balíček:<br>https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Nastavení připojovací řetězce |
| Node.js | 2.7.0 | Odkaz na NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Spuštění: `npm install azure-storage@2.7.0`)<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Deklarace instance služby |
| C++ | 3.1.0 | Balíček Nuget:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Nastavení připojovací řetězce |
| PHP | 1.0.0 | Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Objekt BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Fronta:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabulka: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Instalace přes Composer (viz podrobnosti níže).) | Nastavení připojovací řetězce |
| Python | 1.0.0 | Verze Githubu:<br>Běžné:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Objekt BLOB:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Fronta:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Deklarace instance služby |
| Ruby | 1.0.1 | Balíček RubyGems:<br>Běžné:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Objekt BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Fronta: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabulka: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Objekt BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Fronta: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabulka: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Nastavení připojovací řetězce |

#### <a name="install-php-client-via-composer---previous"></a>Instalace klienta PHP prostřednictvím autora - předchozí

Chcete-li nainstalovat prostřednictvím autora: (take blob jako příklad).

1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři projektu s následujícím kódem:

   ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
   ```

2. Stáhněte si [composer.phar](https://getcomposer.org/composer.phar) do kořenového adresáře projektu.
3. Spustit: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Koncový bod deklarace

Koncový bod služby Azure Stack zahrnuje dvě části: název oblasti a doméně služby Azure Stack.
V Azure Stack Development Kit, je výchozí koncový bod **local.azurestack.external**.
Pokud si nejste jisti o váš koncový bod, obraťte se na správce cloudu.

## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

Pro Azure Stack, která je zadaná přípona koncového bodu v souboru app.config:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Pro službu Azure Stack přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Pro službu Azure Stack je přípona koncového bodu podle instance deklarace:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Pro službu Azure Stack přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Pro službu Azure Stack přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```php
$connectionString = 'BlobEndpoint=https://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=https:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=https:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Pro službu Azure Stack je přípona koncového bodu podle instance deklarace:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Pro službu Azure Stack přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

V následujících kurzech úložiště objektů Blob v Azure se vztahují na služby Azure Stack. Poznámka: požadavek přípona určitého koncového bodu pro službu Azure Stack je popsáno v předchozí [příklady](#examples) oddílu.

* [Začínáme s úložištěm Azure Blob pomocí rozhraní .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)
* [Používání úložiště Blob z Javy](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Používání úložiště Blob z Node.js](/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage)
* [Používání úložiště Blob z jazyka C++](/azure/storage/blobs/storage-c-plus-plus-how-to-use-blobs)
* [Používání úložiště Blob z PHP](/azure/storage/blobs/storage-php-how-to-use-blobs)
* [Jak používat Azure Blob storage z Pythonu](/azure/storage/blobs/storage-python-how-to-use-blob-storage)
* [Používání úložiště Blob z Ruby](/azure/storage/blobs/storage-ruby-how-to-use-blob-storage)

## <a name="queue-storage"></a>Queue Storage

Následující kurzy Azure Queue storage se vztahují na služby Azure Stack. Poznámka: požadavek přípona určitého koncového bodu pro službu Azure Stack je popsáno v předchozí [příklady](#examples) oddílu.

* [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](/azure/storage/queues/storage-dotnet-how-to-use-queues)
* [Používání úložiště Queue z Javy](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Používání úložiště Queue z Node.js](/azure/storage/queues/storage-nodejs-how-to-use-queues)
* [Používání úložiště Queue z jazyka C++](/azure/storage/queues/storage-c-plus-plus-how-to-use-queues)
* [Používání úložiště Queue z PHP](/azure/storage/queues/storage-php-how-to-use-queues)
* [Používání úložiště Queue z Pythonu](/azure/storage/queues/storage-python-how-to-use-queue-storage)
* [Používání úložiště Queue z Ruby](/azure/storage/queues/storage-ruby-how-to-use-queue-storage)

## <a name="table-storage"></a>Úložiště Table

Následující kurzy Azure Table storage se vztahují na služby Azure Stack. Poznámka: požadavek přípona určitého koncového bodu pro službu Azure Stack je popsáno v předchozí [příklady](#examples) oddílu.

* [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](/azure/cosmos-db/table-storage-how-to-use-dotnet)
* [Používání úložiště Table z Javy](/azure/cosmos-db/table-storage-how-to-use-java)
* [Používání tabulkového úložiště Azure z Node.js](/azure/cosmos-db/table-storage-how-to-use-nodejs)
* [Používání úložiště Table z jazyka C++](/azure/cosmos-db/table-storage-how-to-use-c-plus)
* [Používání úložiště Table z PHP](/azure/cosmos-db/table-storage-how-to-use-php)
* [Jak používat úložiště tabulek v Pythonu](/azure/cosmos-db/table-storage-how-to-use-python)
* [Používání úložiště Table z Ruby](/azure/cosmos-db/table-storage-how-to-use-ruby)

## <a name="next-steps"></a>Další postup

* [Úvod do Microsoft Azure storage](/azure/storage/common/storage-introduction)
