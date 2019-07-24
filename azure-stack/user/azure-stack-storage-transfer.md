---
title: Použití nástrojů pro přenos dat v Azure Stack Storage | Microsoft Docs
description: Přečtěte si o Azure Stack nástrojů pro přenos dat úložiště.
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
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418464"
---
# <a name="use-data-transfer-tools-in-azure-stack-storage"></a>Použití nástrojů pro přenos dat v Azure Stack Storage

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack poskytuje sadu služeb úložiště pro disky, objekty blob, tabulky, fronty a funkce správy účtů. Některé nástroje Azure Storage jsou k dispozici, pokud chcete spravovat nebo přesouvat data do nebo z úložiště Azure Stack. Tento článek poskytuje přehled dostupných nástrojů.

Vaše požadavky určují, které z následujících nástrojů vám nejlépe vyhovuje:

* [AzCopy](#azcopy)

    Nástroj příkazového řádku specifický pro úložiště, který můžete stáhnout pro kopírování dat z jednoho objektu do jiného objektu v rámci nebo mezi účty úložiště.

* [Azure PowerShell](#azure-powershell)

    Prostředí příkazového řádku založené na úlohách a skriptovací jazyk navržený hlavně pro správu systému.

* [Azure CLI](#azure-cli)

    Open source nástroj pro více platforem, který poskytuje sadu příkazů pro práci s platformami Azure a Azure Stack.

* [Průzkumník služby Microsoft Storage](#microsoft-azure-storage-explorer)

    Snadno použitelné samostatné aplikace s uživatelským rozhraním.

* [Blobfuse](#blobfuse)

    Ovladač virtuálního systému souborů pro Azure Blob Storage, který umožňuje přístup k existujícím datům objektů blob bloku v účtu úložiště prostřednictvím systému souborů Linux.

Vzhledem k tomu, že mezi Azure a Azure Stack jsou rozdíly v rámci služby Storage, můžou existovat určité konkrétní požadavky na jednotlivé nástroje popsané v následujících částech. Srovnání mezi Azure Stack Storage a Azure Storage najdete v části [Azure Stack Storage: Rozdíly a požadavky](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy je nástroj příkazového řádku určený ke kopírování dat z Microsoft Azure objektů BLOB a Table do úložiště pomocí jednoduchých příkazů s optimálním výkonem. Data můžete kopírovat z jednoho objektu do druhého v rámci nebo mezi účty úložiště.

### <a name="download-and-install-azcopy"></a>Stažení a instalace AzCopy

* V případě aktualizace 1811 nebo novější verze [Stáhněte AzCopy v10 za účelem +](/azure/storage/common/storage-use-azcopy-v10#download-azcopy).
* Pro předchozí verze (1802 až 1809 aktualizace) [Stáhněte AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).

### <a name="azcopy-101-configuration-and-limits"></a>Konfigurace a omezení AzCopy 10,1

AzCopy 10,1 je teď možné nakonfigurovat tak, aby používala starší verze rozhraní API. Tato možnost povoluje (omezená) podporu pro Azure Stack.
Chcete-li nakonfigurovat rozhraní API verze AzCopy na podporu Azure Stack, nastavte `AZCOPY_DEFAULT_SERVICE_API_VERSION` proměnnou prostředí na `2017-11-09`.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | V příkazovém řádku použijte:`set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`<br> V prostředí PowerShell použijte:`$env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"`|
| **Linux** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |
| **MacOS** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |

V AzCopy 10,1 jsou podporovány následující funkce Azure Stack:

| Funkce | Podporované akce |
| --- | --- |
|Spravovat kontejner|Vytvoření kontejneru<br>Vypsat obsah kontejnerů
|Spravovat úlohu|Zobrazit úlohy<br>Pokračování úlohy
|Odebrat objekt BLOB|Odebrání jednoho objektu BLOB<br>Odebrat celý nebo částečný virtuální adresář
|Nahrát soubor|Nahrát soubor<br>Odeslat adresář<br>Nahrajte obsah adresáře.
|Stáhnout soubor|Stáhnout soubor<br>Stáhnout adresář<br>Stažení obsahu adresáře
|Synchronizovat soubor|Synchronizace kontejneru do místního systému souborů<br>Synchronizace místního systému souborů do kontejneru

   > [!NOTE]
   > * Azure Stack nepodporuje poskytování autorizačních přihlašovacích údajů AzCopy pomocí Azure Active Directory (AD). K objektům úložiště v Azure Stack musíte přistupovat pomocí tokenu sdíleného přístupového podpisu (SAS).
   > * Azure Stack nepodporuje synchronní přenos dat mezi dvěma umístěními objektů blob Azure Stack a mezi Azure Storage a Azure Stack. Nemůžete použít "AzCopy CP" k přesunu dat z Azure Stack do Azure Storage (nebo jiným způsobem) přímo pomocí AzCopy 10,1.

### <a name="azcopy-command-examples-for-data-transfer"></a>Příklady příkazů AzCopy pro přenos dat

Následující příklady následují při kopírování dat do a z Azure Stack objektů BLOB v rámci typických scénářů. Další informace najdete v tématu Začínáme [s AzCopy](/azure/storage/common/storage-use-azcopy-v10).

### <a name="download-all-blobs-to-a-local-disk"></a>Stažení všech objektů blob na místní disk

```
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

### <a name="upload-single-file-to-virtual-directory"></a>Odeslat jeden soubor do virtuálního adresáře

```
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

### <a name="azcopy-known-issues"></a>AzCopy známé problémy

 - Žádná operace AzCopy v úložišti souborů není k dispozici, protože úložiště souborů ještě není v Azure Stack k dispozici.
 - Pokud chcete přenášet data mezi dvěma umístěními Azure Stack objektů BLOB (nebo mezi Azure Stack a Azure Storage pomocí AzCopy 10.1), musíte nejdřív stáhnout data do místního umístění a pak je znovu nahrát do cílového adresáře na Azure Stack nebo Azure Storage. Případně můžete použít AzCopy 7,1 a zadat přenos s možností **/SyncCopy** pro kopírování dat.  
 - Verze systému Linux AzCopy podporuje pouze aktualizaci 1802 nebo novější, a nepodporuje Table service.
 
## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell je modul, který poskytuje rutiny pro správu služeb v Azure i v Azure Stack. Je to prostředí příkazového řádku založené na úlohách a skriptovací jazyk, který je určený hlavně pro správu systému.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalace a konfigurace PowerShellu pro Azure Stack

Pro práci s Azure Stack jsou vyžadovány Azure PowerShell moduly kompatibilní s Azure Stack. Další informace najdete v tématu [instalace PowerShellu pro Azure Stack](../operator/azure-stack-powershell-install.md) a [Konfigurace prostředí powershellu pro Azure Stack uživatele](azure-stack-powershell-configure-user.md).

### <a name="powershell-sample-script-for-azure-stack"></a>Ukázkový skript PowerShellu pro Azure Stack 

Tato ukázka předpokládá, že jste úspěšně [nainstalovali PowerShell pro Azure Stack](../operator/azure-stack-powershell-install.md). Tento skript vám pomůže dokončit konfiguraci a požádat Azure Stack přihlašovací údaje tenanta, aby se Váš účet přidal do místního prostředí PowerShell. Skript pak nastaví výchozí předplatné Azure, vytvoří v Azure nový účet úložiště, vytvoří nový kontejner v tomto novém účtu úložiště a nahraje do tohoto kontejneru existující soubor obrázku (BLOB). Jakmile skript vypíše všechny objekty BLOB v tomto kontejneru, vytvoří nový cílový adresář v místním počítači a stáhne soubor bitové kopie.

1. Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](../operator/azure-stack-powershell-install.md).
2. Ve službě [Azure Stack development Kit by měl být blobEndpoint](../operator/azure-stack-powershell-download.md) .
3. Otevřete **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** a **Spusťte jako správce**a pak kliknutím na **soubor** > **Nový** vytvořte nový soubor skriptu.
4. Zkopírujte skript níže a vložte ho do nového souboru skriptu.
5. Aktualizujte proměnné skriptu na základě nastavení konfigurace.
   > [!NOTE]
   > Tento skript musí být spuštěn v kořenovém adresáři pro **AzureStack_Tools**.

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

### <a name="powershell-known-issues"></a>Známé problémy v PowerShellu

Aktuální kompatibilní verze modulu Azure PowerShell pro Azure Stack je 1.2.11 pro operace uživatele. Liší se od nejnovější verze Azure PowerShell. Tento rozdíl ovlivňuje operaci služby Storage následujícím způsobem:

Formát `Get-AzureRmStorageAccountKey` návratové hodnoty ve verzi 1.2.11 má dvě vlastnosti: `Key1` a `Key2`, zatímco aktuální verze Azure vrací pole obsahující všechny klíče účtu.

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

Další informace najdete v tématu [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey).

## <a name="azure-cli"></a>Azure CLI

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete ho nainstalovat na macOS, Linux a Windows a spustit ho z příkazového řádku.

Azure CLI je optimalizované pro správu a správu prostředků Azure z příkazového řádku a pro vytváření skriptů pro automatizaci, které pracují s Azure Resource Manager. Poskytuje řadu stejných funkcí, které najdete na portálu Azure Stack, včetně přístupu k datům.

Azure Stack vyžaduje Azure CLI verze 2,0 nebo novější. Další informace o instalaci a konfiguraci Azure CLI pomocí Azure Stack najdete v tématu [instalace a konfigurace Azure Stack CLI](azure-stack-version-profiles-azurecli2.md). Další informace o použití rozhraní příkazového řádku Azure k provedení několika úloh, které pracují s prostředky ve vašem účtu úložiště Azure Stack, najdete v tématu [použití Azure CLI s Azure Storage](/azure/storage/storage-azure-cli).

### <a name="azure-cli-sample-script-for-azure-stack"></a>Ukázkový skript Azure CLI pro Azure Stack

Po dokončení instalace a konfigurace rozhraní příkazového řádku můžete vyzkoušet následující postup, který vám umožní pracovat s Azure Stack prostředky úložiště s malým prostředím. Skript dokončí následující akce:

* Vytvoří nový kontejner v účtu úložiště.
* Nahraje existující soubor (jako objekt BLOB) do kontejneru.
* Zobrazí seznam všech objektů BLOB v kontejneru.
* Stáhne soubor do cílového umístění v místním počítači, který zadáte.

Před spuštěním tohoto skriptu se ujistěte, že se můžete úspěšně připojit k cílovému Azure Stack a přihlásit se k němu.

1. Otevřete oblíbený textový editor a potom zkopírujte a vložte předchozí skript do editoru.
2. Aktualizujte proměnné skriptu tak, aby odrážely nastavení konfigurace.
3. Po aktualizaci nezbytných proměnných uložte skript a ukončete Editor. V dalších krocích se předpokládá, že jste najmenovali skript **my_storage_sample. sh**.
4. Označte skript jako spustitelný soubor, pokud je to nutné:`chmod +x my_storage_sample.sh`
5. Spusťte skript. Například v bash:`./my_storage_sample.sh`

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

Průzkumník služby Azure Storage je samostatná aplikace od Microsoftu. Umožňuje snadno pracovat s daty úložiště Azure Storage i Azure Stack na počítačích s Windows, macOS a Linux. Pokud potřebujete snadný způsob, jak spravovat data úložiště Azure Stack, zvažte použití Průzkumník služby Microsoft Azure Storage.

* Další informace o konfiguraci Průzkumník služby Azure Storage pro práci s Azure Stack najdete v tématu [připojení Průzkumník služby Storage k předplatnému Azure Stack](azure-stack-storage-connect-se.md).
* Další informace o Průzkumník služby Microsoft Azure Storage najdete v tématu Začínáme [s Průzkumníkem služby Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer) .

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) je ovladač virtuálního systému souborů pro Azure Blob Storage, který umožňuje přístup k existujícím datům objektů blob bloku v účtu úložiště prostřednictvím systému souborů Linux. Azure Blob Storage je služba úložiště objektů, a proto nemá hierarchický obor názvů. Blobfuse poskytuje tento obor názvů pomocí schématu virtuálního adresáře s použitím lomítka-lomítka `/` jako oddělovače. Blobfuse funguje jak pro Azure, tak pro Azure Stack. 

Další informace o připojení úložiště objektů BLOB jako systému souborů s Blobfuse v systému Linux najdete v tématu [Postup připojení úložiště objektů BLOB jako systému souborů s Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

Pro Azure Stack je potřeba zadat *blobEndpoint* při konfiguraci přihlašovacích údajů k účtu úložiště společně s parametrem Account, AccountKey/sasToken a ContainerName.

V Azure Stack Development Kit (ASDK) by měl `myaccount.blob.local.azurestack.external`být *blobEndpoint* . V Azure Stack integrovaném systému kontaktujte správce cloudu, pokud si nejste jisti vaším koncovým bodem.

*accountKey* a *sasToken* lze nakonfigurovat pouze jednou. Když je zadaný klíč účtu úložiště, konfigurační soubor přihlašovacích údajů má tento formát:

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Pokud je zadaný sdílený přístupový token, konfigurační soubor přihlašovacích údajů má následující formát:

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Další postup

* [Připojení Průzkumníka služby Storage k předplatnému Azure Stack](azure-stack-storage-connect-se.md)
* [Začínáme s Průzkumníkem služby Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Úložiště konzistentní s Azure: rozdíly a požadavky](azure-stack-acs-differences.md)
* [Seznámení s Microsoft Azure Storage](/azure/storage/common/storage-introduction)
