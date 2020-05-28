---
title: Začínáme s nástroji pro vývoj pro úložiště Azure Stack hub
description: Pokyny, jak začít s používáním nástrojů pro vývoj úložiště Azure Stack hub
author: mattbriggs
ms.author: mabrigg
ms.date: 5/27/2020
ms.topic: conceptual
ms.reviewer: xiaofmao
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: bc682604e5c57c3a878c7c5dc17a0bfc18e36e60
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111788"
---
# <a name="get-started-with-azure-stack-hub-storage-development-tools"></a>Začínáme s nástroji pro vývoj pro úložiště Azure Stack hub

Centrum Microsoft Azure Stack poskytuje sadu služeb úložiště, které zahrnují úložiště objektů blob, tabulek a front.

Tento článek vám pomůže začít používat vývojové nástroje pro úložiště Azure Stack hub. Podrobnější informace a ukázkový kód najdete v příslušných kurzech k Azure Storage.

> [!NOTE]  
> Existují rozdíly mezi Azure Stack úložištěm centra a úložištěm Azure, včetně konkrétních požadavků na jednotlivé platformy. K dispozici jsou například konkrétní klientské knihovny a požadavky na příponu koncových bodů pro Azure Stack hub. Další informace najdete v tématu [úložiště centra Azure Stack: rozdíly a požadavky](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Klientské knihovny Azure

Pro klientské knihovny pro úložiště si pamatujte na verzi, která je kompatibilní s REST API. Ve svém kódu musíte taky zadat koncový bod centra Azure Stack.

::: moniker range=">=azs-1811"
### <a name="1811-update-or-newer-versions"></a>1811 aktualizace nebo novější verze

| Klientská knihovna | Verze podporovaného centra Azure Stack | Odkaz | Specifikace koncového bodu |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | Balíček NuGet:<br><https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0><br> <br>Verze GitHubu:<br><https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0> | soubor App. config |
| Java | 7.0.0 | Balíček Maven:<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0><br> <br>Verze GitHubu:<br><https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0> | Nastavení připojovacího řetězce |
| Node.js | 2.8.3 | Odkaz na NPM:<br><https://www.npmjs.com/package/azure-storage><br>(Spustit: `npm install azure-storage@2.8.3` )<br> <br>Verze GitHubu:<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3> | Deklarace instance služby |
| C++ | 5.2.0 | Balíček NuGet:<br><https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0><br> <br>Verze GitHubu:<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0> | Nastavení připojovacího řetězce |
| PHP | 1.2.0 | Verze GitHubu:<br>Obecný<https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common><br>Příznaky<https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob><br>Provedených<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue><br>Stolní<https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table><br> <br>Instalovat přes skladatele (Další informace [najdete v podrobnostech níže](#install-php-client-via-composer---current)) | Nastavení připojovacího řetězce |
| Python | 1.1.0 | Verze GitHubu:<br>Obecný<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common><br>Příznaky<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob><br>Provedených<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue> | Deklarace instance služby |
| Ruby | 1.0.1 | Balíček RubyGems:<br>Obecný<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Příznaky<https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>Provedených<https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Stolní<https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>Verze GitHubu:<br>Obecný<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Příznaky<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>Provedených<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Stolní<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | Nastavení připojovacího řetězce |

#### <a name="install-php-client-via-composer---current"></a>Instalovat klienta PHP prostřednictvím skladatele – aktuální

Instalace prostřednictvím skladatele: (jako příklad Vezměte objekt BLOB).

1. V kořenovém adresáři projektu vytvořte soubor s názvem **skladatel. JSON** s následujícím kódem:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Stáhněte [skladatele. phar](https://getcomposer.org/composer.phar) do kořenového adresáře projektu.
3. Spusťte `php composer.phar install`.
::: moniker-end

::: moniker range=">=azs-1802 <=azs-1809"
### <a name="previous-versions-1802-to-1809-update"></a>Předchozí verze (aktualizace 1802 až 1809)

| Klientská knihovna | Verze podporovaného centra Azure Stack | Odkaz | Specifikace koncového bodu |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Balíček NuGet:<br><https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0><br> <br>Verze GitHubu:<br><https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0> | soubor App. config |
| Java | 6.1.0 | Balíček Maven:<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0><br> <br>Verze GitHubu:<br><https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0> | Nastavení připojovacího řetězce |
| Node.js | 2.7.0 | Odkaz na NPM:<br><https://www.npmjs.com/package/azure-storage><br>(Spustit: `npm install azure-storage@2.7.0` )<br> <br>Verze GitHubu:<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0> | Deklarace instance služby |
| C++ | 3.1.0 | Balíček NuGet:<br><https://www.nuget.org/packages/wastorage.v140/3.1.0><br> <br>Verze GitHubu:<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0> | Nastavení připojovacího řetězce |
| PHP | 1.0.0 | Verze GitHubu:<br>Obecný<https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common><br>Příznaky<https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob><br>Provedených<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue><br>Stolní<https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table><br> <br>Nainstalujte přes skladatele (viz podrobnosti níže).) | Nastavení připojovacího řetězce |
| Python | 1.0.0 | Verze GitHubu:<br>Obecný<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common><br>Příznaky<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob><br>Provedených<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue> | Deklarace instance služby |
| Ruby | 1.0.1 | Balíček RubyGems:<br>Obecný<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Příznaky<https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>Provedených<https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>Stolní<https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>Verze GitHubu:<br>Obecný<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Příznaky<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>Provedených<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>Stolní<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | Nastavení připojovacího řetězce |

#### <a name="install-php-client-via-composer---previous"></a>Instalovat klienta PHP prostřednictvím skladatele – předchozí

Instalace prostřednictvím skladatele: (jako příklad Vezměte objekt BLOB jako příklad).

1. V kořenovém adresáři projektu vytvořte soubor s názvem **skladatel. JSON** s následujícím kódem:

   ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
   ```

2. Stáhněte [skladatele. phar](https://getcomposer.org/composer.phar) do kořenového adresáře projektu.
3. Spusťte `php composer.phar install`.
:::moniker-end

## <a name="endpoint-declaration"></a>Deklarace koncového bodu

Koncový bod centra Azure Stack zahrnuje dvě části: název oblasti a doménu Azure Stack centra.
V Azure Stack Development Kit je výchozím koncovým bodem **místní. azurestack. external**.
Pokud si nejste jisti vaším koncovým bodem, obraťte se na správce cloudu.

## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

V případě Azure Stackového centra je přípona koncového bodu určená v souboru App. config:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

V případě Azure Stackového centra je přípona koncového bodu určená v nastavení připojovacího řetězce:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

V případě Azure Stackového centra je přípona koncového bodu zadaná v instanci deklarace:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

V případě Azure Stackového centra je přípona koncového bodu určená v nastavení připojovacího řetězce:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

V případě Azure Stackového centra je přípona koncového bodu určená v nastavení připojovacího řetězce:

```php
$connectionString = 'BlobEndpoint=https://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=https:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=https:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

V případě Azure Stackového centra je přípona koncového bodu zadaná v instanci deklarace:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

V případě Azure Stackového centra je přípona koncového bodu určená v nastavení připojovacího řetězce:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Následující kurzy k úložišti objektů BLOB v Azure se vztahují na centrum Azure Stack. Poznamenejte si konkrétní požadavek na příponu koncového bodu pro Azure Stack hub popsaný v části předchozí [Příklady](#examples) .

* [Začínáme s úložištěm Azure Blob pomocí rozhraní .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)
* [Používání úložiště Blob z Javy](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Používání úložiště Blob z Node.js](/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage)
* [Používání úložiště BLOB z C++](/azure/storage/blobs/storage-c-plus-plus-how-to-use-blobs)
* [Používání úložiště Blob z PHP](/azure/storage/blobs/storage-php-how-to-use-blobs)
* [Jak používat úložiště objektů BLOB v Azure z Pythonu](/azure/storage/blobs/storage-python-how-to-use-blob-storage)
* [Používání úložiště Blob z Ruby](/azure/storage/blobs/storage-ruby-how-to-use-blob-storage)

## <a name="queue-storage"></a>Queue Storage

Následující kurzy k Azure Queue Storage se vztahují na centra Azure Stack. Poznamenejte si konkrétní požadavek na příponu koncového bodu pro Azure Stack hub popsaný v části předchozí [Příklady](#examples) .

* [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](/azure/storage/queues/storage-dotnet-how-to-use-queues)
* [Používání úložiště Queue z Javy](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Používání úložiště Queue z Node.js](/azure/storage/queues/storage-nodejs-how-to-use-queues)
* [Používání úložiště Queue z C++](/azure/storage/queues/storage-c-plus-plus-how-to-use-queues)
* [Používání úložiště Queue z PHP](/azure/storage/queues/storage-php-how-to-use-queues)
* [Používání úložiště Queue z Pythonu](/azure/storage/queues/storage-python-how-to-use-queue-storage)
* [Používání úložiště Queue z Ruby](/azure/storage/queues/storage-ruby-how-to-use-queue-storage)

## <a name="table-storage"></a>Úložiště Table

Následující kurzy k Azure Table Storage se vztahují na centra Azure Stack. Poznamenejte si konkrétní požadavek na příponu koncového bodu pro Azure Stack hub popsaný v části předchozí [Příklady](#examples) .

* [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](/azure/cosmos-db/table-storage-how-to-use-dotnet)
* [Používání úložiště Table z Javy](/azure/cosmos-db/table-storage-how-to-use-java)
* [Používání tabulkového úložiště Azure z Node.js](/azure/cosmos-db/table-storage-how-to-use-nodejs)
* [Používání úložiště Table z C++](/azure/cosmos-db/table-storage-how-to-use-c-plus)
* [Používání úložiště Table z PHP](/azure/cosmos-db/table-storage-how-to-use-php)
* [Používání úložiště Table v Pythonu](/azure/cosmos-db/table-storage-how-to-use-python)
* [Používání úložiště Table z Ruby](/azure/cosmos-db/table-storage-how-to-use-ruby)

## <a name="next-steps"></a>Další kroky

* [Seznámení s Microsoft Azure Storage](/azure/storage/common/storage-introduction)
