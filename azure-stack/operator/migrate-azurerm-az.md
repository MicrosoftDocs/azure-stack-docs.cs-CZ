---
title: Migrace skriptů Azure PowerShell z AzureRM na az in Azure Stack hub
description: Seznamte se s kroky a nástroji pro migraci skriptů z modulu AzureRM do nového modulu AZ Module v Azure Stack hub.
author: mattbriggs
ms.author: mabrigg
ms.topic: conceptual
ms.date: 10/05/2020
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: bfb385e29a89ad8d47ace3b15776bd368e0cf521
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546167"
---
# <a name="migrate-from-azurerm-to-azure-powershell-az-in-azure-stack-hub"></a>Migrace z AzureRM na Azure PowerShell AZ in Azure Stack hub

Modul Az zajišťuje paritu funkcí s AzureRM, ale používá kratší a konzistentnější názvy rutin.
Skripty napsané pro AzureRM nebudou automaticky fungovat s novým modulem. Pro usnadnění tohoto přechodu Az nabízí nástroje, které umožňují spouštět stávající skripty s využitím AzureRM. Migrace na novou sadu příkazů není nikdy nic příjemného, ale tento článek vám pomůže začít s přechodem na nový modul.

Úplný seznam všech zásadních změn mezi AzureRM a Az najdete v tématu [Průvodce migrací pro Az 1.0.0](/powershell/azure/migrate-az-1.0.0).

## <a name="check-for-installed-versions-of-azurerm"></a>Kontrola nainstalovaných verzí AzureRM

Než začnete provádět jakékoli kroky migrace, zkontrolujte, jaké verze AzureRM jsou ve vašem systému nainstalované. Získáte tak jistotu, že se skripty již spouštějí v nejnovější verzi, a zjistíte, jestli můžete povolit aliasy příkazů bez nutnosti odinstalovat AzureRM.

Pokud chcete zkontrolovat, jaké verze AzureRM máte nainstalované, spusťte následující příkaz:

```powershell-interactive
Get-InstalledModule -Name AzureRM -AllVersions
```

## <a name="check-current-scripts-work-with-azurerm"></a>Kontrolovat aktuální skripty fungují s AzureRM

To je nejdůležitější krok! Spusťte stávající skripty a ujistěte se, že fungují s _nejnovější_ verzí AzureRM ( __2.5.0__ ). Pokud skripty nefungují, nezapomeňte si přečíst [průvodce migrací AzureRM](/powershell/azure/azurerm/migration-guide.6.0.0).

## <a name="install-the-azure-powershell-az-module"></a>Instalace modulu Az Azure PowerShellu

Prvním krokem je instalace modulu Az na vaší platformě. Pokud instalujete Az, doporučuje se odinstalovat AzureRM. V následujícím postupu se naučíte, jak dál spouštět stávající skripty a zajistit kompatibilitu pro staré názvy rutin.

Při instalaci modulu Az Azure PowerShellu postupujte takto:

* __Doporučené__ : [odinstalujte modul AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module).
  Ujistěte se, že jste odebrali _všechny_ nainstalované verze AzureRM, ne jenom nejnovější verzi.
* [Instalace modulu Az](/powershell/azure/install-az-ps)

## <a name="enable-azurerm-compatibility-aliases"></a>Povolení aliasů kompatibility s AzureRM 

> [!IMPORTANT]
>
> Režim kompatibility povolte pouze v případě, že jste odinstalovali _všechny_ verze AzureRM. Povolení režimu kompatibility se stále dostupnými rutinami AzureRM může způsobit neočekávané chování. Pokud jste se rozhodli ponechat nainstalovaný modul AzureRM, tento krok přeskočte, ale mějte na paměti, že všechny rutiny AzureRM budou využívat starší moduly a nebudou volat žádné rutiny Az.

Když je modul AzureRM odinstalovaný a vaše skripty fungují s nejnovější verzí AzureRM, dalším krokem je povolit režim kompatibility pro modul Az. Kompatibilita se povoluje pomocí tohoto příkazu:

```powershell-interactive
Enable-AzureRmAlias -Scope CurrentUser
```

Aliasy umožňují používat staré názvy rutin i s nainstalovaným modulem Az. Tyto aliasy se zapisují do profilu uživatele pro vybraný obor. Pokud žádný profil uživatele neexistuje, vytvoří se.

> [!WARNING]
>
> Pro tento příkaz můžete použít jiný obor `-Scope`, ale nedoporučuje se to. Aliasy se zapisují do profilu uživatele pro vybraný obor, proto je povolte v co neomezenějším oboru. Povolení aliasů na úrovni celého systému by také mohlo způsobit potíže pro ostatní uživatele, kteří mají modul AzureRM nainstalovaný ve svém místním oboru.

Po povolení režimu aliasů spusťte vaše skripty znovu a potvrďte, že i dál fungují podle očekávání. 

## <a name="change-module-and-cmdlet-names"></a>Změnit název modulu a rutiny

Obecně se názvy modulů změnily tak, aby se z `AzureRM` a `Azure` stalo `Az`. Totéž platí i pro rutiny.
Například modul `AzureRM.Compute` se přejmenoval na `Az.Compute`. Z `New-AzureRMVM` se stalo `New-AzVM` a `Get-AzureStorageBlob` je teď `Get-AzStorageBlob`.

Pro tuto změnu pojmenování existují výjimky, o kterých byste měli vědět. Některé moduly se přejmenovaly nebo sloučily se stávajícími moduly, aniž by to mělo vliv na příponu příslušných rutin, kromě změny `AzureRM` nebo `Azure` na `Az`. Jinak se změnila celá přípona rutiny, aby odrážela nový název modulu.

| Modul AzureRM | Modul Az | Změnila se přípona rutiny? |
|----------------|-----------|------------------------|
| AzureRM.Profile | Az.Accounts | Yes |
| AzureRM.Insights | Az.Monitor | Yes |
| AzureRM.Tags | Az.Resources | Ne |
| AzureRM.UsageAggregates | Az.Billing | Ne |
| AzureRM.Consumption | Az.Billing | Ne |

## <a name="summary"></a>Shrnutí

Pomocí tohoto postupu můžete aktualizovat všechny vaše stávající skripty tak, aby používaly nový modul. Pokud máte jakékoli dotazy nebo problémy s těmito kroky, které vaši migraci ztížily, přidejte k tomuto článku komentáře, abychom mohli uvedené pokyny vylepšit.

## <a name="breaking-changes-for-az-100"></a>Zásadní změny v Az 1.0.0

Tento dokument obsahuje podrobné informace o změnách mezi AzureRM 6.x a novým modulem Az verze 1.x nebo novější. Obsah vám pomůže zorientovat se v celé cestě k migraci, včetně změn specifických pro jednotlivé moduly, které můžou mít vliv na vaše skripty.

## <a name="general-breaking-changes"></a>Obecné zásadní změny

Tato část podrobně popisuje obecné zásadní změny v souvislosti s přepracováním modulu Az.

### <a name="cmdlet-noun-prefix-changes"></a>Změny předpony podstatného názvu rutiny

V modulu AzureRM rutiny jako předponu používaly `AzureRM` nebo `Azure`.  Az názvy rutin zjednodušuje a normalizuje, aby všechny rutiny jako předponu používaly Az. Příklad:

```powershell  
Get-AzureRMVM
Get-AzureKeyVaultSecret
```

Po změně:

```powershell  
Get-AzVM
Get-AzKeyVaultSecret
```

Pro zjednodušení přechodu na tyto nové názvy rutin Az zavádí dvě nové rutiny – [Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias) a [Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias).  `Enable-AzureRmAlias` vytvoří aliasy pro starší názvy rutin v AzureRM, které se mapují na novější názvy rutin Az. Pomocí argumentu `-Scope` v rutině `Enable-AzureRmAlias` můžete zvolit, kde se mají aliasy povolit.

Například následující skript v AzureRM:

```powershell  
#Requires -Modules AzureRM.Storage
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

je možné spustit s minimálními změnami s využitím rutiny `Enable-AzureRmAlias`:

```powershell  
#Requires -Modules Az.Storage
Enable-AzureRmAlias -Scope Process
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Spuštěním rutiny `Enable-AzureRmAlias -Scope CurrentUser` se povolí aliasy pro všechny relace PowerShellu, které otevřete, aby nebylo nutné nijak měnit podobné skripty:

```powershell  
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Úplné podrobnosti o používání aliasů rutin najdete v [referenčních informacích k rutině Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias).

Jakmile budete připraveni zakázat aliasy, rutinou `Disable-AzureRmAlias` se odeberou vytvořené aliasy. Úplné podrobnosti najdete v [referenčních informacích k rutině Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias).

> [!IMPORTANT]
> Při zakazování aliasů se ujistěte, že se zakáží pro _všechny_ obory, ve kterých byly povolené.

### <a name="module-name-changes"></a>Změny názvů modulů

Názvy modulů se změnily z `AzureRM.*` na `Az.*` s výjimkou následujících modulů:

| Modul AzureRM | Modul Az |
|----------------|-----------|
| Azure.Storage | Az.Storage |
| Azure.AnalysisServices | Az.AnalysisServices |
| AzureRM.Profile | Az.Accounts |
| AzureRM.Insights | Az.Monitor |
| AzureRM.RecoveryServices.Backup | Az.RecoveryServices |
| AzureRM.RecoveryServices.SiteRecovery | Az.RecoveryServices |
| AzureRM.Tags | Az.Resources |
| AzureRM.MachineLearningCompute | Az.MachineLearning |
| AzureRM.UsageAggregates | Az.Billing |
| AzureRM.Consumption | Az.Billing |

Změny v názvech modulů znamenají, že všechny skripty, které k načtení konkrétních modulů používají příkazy `#Requires` a `Import-Module`, bude potřeba změnit tak, aby místo toho používaly nové názvy modulů. Pro moduly, ve kterých se přípona rutiny nezměnila, to znamená, že i když se změnil název modulu, přípona označující pracovní prostor se _nezměnila_.

#### <a name="migrating-requires-and-import-module-statements"></a>Migrace vyžaduje a importovat příkazy modulu

Skripty, které pomocí příkazu `#Requires` nebo `Import-Module` deklarují závislost na modulech AzureRM, je potřeba aktualizovat tak, aby používaly nové názvy modulů. Příklad:

```powershell  
#Requires -Module AzureRM.Compute
```

je potřeba změnit na:

```powershell  
#Requires -Module Az.Compute
```

Příkaz `Import-Module`:

```powershell  
Import-Module -Name AzureRM.Compute
```

je potřeba změnit na:

```powershell  
Import-Module -Name Az.Compute
```

### <a name="migrating-fully-qualified-cmdlet-invocations"></a>Migrace plně kvalifikovaných vyvolání rutin

Skripty, které používají plně kvalifikovaná volání rutin, jako například:

```powershell  
AzureRM.Compute\Get-AzureRmVM
```

je potřeba změnit tak, aby používaly nové názvy modulů a rutin:

```powershell  
Az.Compute\Get-AzVM
```

### <a name="migrating-module-manifest-dependencies"></a>Migrace závislostí manifestu modulu

V modulech, které vyjadřují závislosti na modulech AzureRM prostřednictvím souboru manifestu modulu (.psd1), bude potřeba aktualizovat názvy modulů v části `RequiredModules`:

```powershell
RequiredModules = @(@{ModuleName="AzureRM.Profile"; ModuleVersion="5.8.2"})
```

je potřeba změnit na:

```powershell
RequiredModules = @(@{ModuleName="Az.Accounts"; ModuleVersion="1.0.0"})
```

### <a name="removed-modules"></a>Odebrané moduly

Následující moduly se odebraly:

- `AzureRM.Backup`
- `AzureRM.Compute.ManagedService`
- `AzureRM.Scheduler`

Nástroje pro tyto služby se už aktivně nepodporují.  Doporučujeme zákazníkům co nejdříve přejít na alternativní služby.

### <a name="windows-powershell-51-and-net-472"></a>Windows PowerShell 5.1 a .NET 4.7.2

K používání Az s PowerShellem 5.1 pro Windows se vyžaduje instalace rozhraní .NET Framework 4.7.2. K používání PowerShellu Core 6.x nebo novějšího se rozhraní .NET Framework nevyžaduje.

### <a name="temporary-removal-of-user-login-using-pscredential"></a>Dočasné odebrání přihlášení uživatele pomocí PSCredential

Kvůli změnám v toku ověřování pro .NET Standard dočasně odebíráme možnost přihlášení uživatele prostřednictvím objektu PSCredential. Tato možnost bude znovu zavedená ve verzi PowerShellu 5.1 pro Windows vydané 15. 1. 2019. Podrobnosti najdete u [tohoto problému na GitHubu](https://github.com/Azure/azure-powershell/issues/7430).

### <a name="default-device-code-login-instead-of-web-browser-prompt"></a>Výchozí přihlášení kódem zařízení místo výzvy ve webovém prohlížeči

Kvůli změnám v toku ověřování pro .NET Standard jako výchozí tok přihlášení při interaktivním přihlašování používáme přihlášení zařízení. Přihlášení ve webovém prohlížeči jako výchozí možnost bude znovu zavedeno ve verzi PowerShellu 5.1 pro Windows vydané 15. 1. 2019. Uživatelé pak budou mít možnost zvolit přihlášení zařízení pomocí přepínacího parametru.

## <a name="module-breaking-changes"></a>Zásadní změny modulů

Tato část podrobně popisuje konkrétní zásadní změny jednotlivých modulů a rutin.

### <a name="azapimanagement-previously-azurermapimanagement"></a>Az.ApiManagement (dříve AzureRM.ApiManagement)

- Odebraly se následující rutiny:
  - New-AzureRmApiManagementHostnameConfiguration
  - Set-AzureRmApiManagementHostnames
  - Update-AzureRmApiManagementDeployment
  - Import-AzureRmApiManagementHostnameCertificate
  - K nastavení těchto vlastností místo toho použijte rutinu **Set-AzApiManagement**.
- Odebraly se následující vlastnosti:
  - Odebrání vlastností `PortalHostnameConfiguration`, `ProxyHostnameConfiguration`, `ManagementHostnameConfiguration` a `ScmHostnameConfiguration` typu `PsApiManagementHostnameConfiguration` z třídy `PsApiManagementContext`. Místo toho použijte `PortalCustomHostnameConfiguration`, `ProxyCustomHostnameConfiguration`, `ManagementCustomHostnameConfiguration` a `ScmCustomHostnameConfiguration` typu `PsApiManagementCustomHostNameConfiguration`.
  - Odebrání vlastnosti `StaticIPs` z třídy PsApiManagementContext. Tato vlastnost se rozdělila na `PublicIPAddresses` a `PrivateIPAddresses`.
  - Odebrání požadované vlastnosti `Location` z rutiny New-AzureApiManagementVirtualNetwork.

### <a name="azbilling-previously-azurermbilling-azurermconsumption-and-azurermusageaggregates"></a>Az.Billing (dříve AzureRM.Billing, AzureRM.Consumption a AzureRM.UsageAggregates)

- Z rutiny `Get-AzConsumptionUsageDetail` se odebral parametr `InvoiceName`.  Skripty budou muset pro práci s fakturou používat jiné parametry identity.

### <a name="azcompute-previously-azurermcompute"></a>Az.Compute (dříve AzureRM.Compute)

- Z vlastnosti `Identity` v objektech `PSVirtualMachine` a `PSVirtualMachineScaleSet` se odebraly hodnoty `IdentityIds`. Skripty už by se při zpracování neměly rozhodovat podle hodnoty tohoto pole.
- Typ vlastnosti `InstanceView` objektu `PSVirtualMachineScaleSetVM` se změnil z `VirtualMachineInstanceView` na `VirtualMachineScaleSetVMInstanceView`.
- Z vlastnosti `UpgradePolicy` se odebraly vlastnosti `AutoOSUpgradePolicy` a `AutomaticOSUpgrade`.
- Typ vlastnosti `Sku` v objektu `PSSnapshotUpdate` se změnil z `DiskSku` na `SnapshotSku`.
- `VmScaleSetVMParameterSet` se odebrala z rutiny `Add-AzVMDataDisk` a už není možné přidávat datové disky do jednotlivých virtuálních počítačů škálovacích sad.

### <a name="azkeyvault-previously-azurermkeyvault"></a>Az.KeyVault (dříve AzureRM.KeyVault)

- Z objektů `PSKeyVaultKeyAttributes`, `PSKeyVaultKeyIdentityItem` a `PSKeyVaultSecretAttributes` se odebrala vlastnost `PurgeDisabled`. Skripty už by se při zpracování neměly rozhodovat podle vlastnosti ```PurgeDisabled```.

### <a name="azmonitor-previously-azurerminsights"></a>Az.Monitor (dříve AzureRM.Insights)

- Odebrání názvů parametrů `Categories` a `Timegrains` v množném čísle z rutiny `Set-AzDiagnosticSetting` a jejich nahrazení názvy parametrů v jednotném čísle. Skripty používající
  ```powershell  
  Set-AzureRmDiagnosticSetting -Timegrains PT1M -Categories Category1, Category2
  ```

  je potřeba změnit na
  ```powershell  
  Set-AzDiagnosticSetting -Timegrain PT1M -Category Category1, Category2
  ```

### <a name="aznetwork-previously-azurermnetwork"></a>Az.Network (dříve AzureRM.Network)

- Odebrání zastaralého parametru `ResourceId` z rutiny `Get-AzServiceEndpointPolicyDefinition`.
- Odebrání zastaralé vlastnosti `EnableVmProtection` z objektu `PSVirtualNetwork`.
- Odebrání zastaralé rutiny `Set-AzVirtualNetworkGatewayVpnClientConfig`.

Skripty už by se při zpracování neměly rozhodovat na základě hodnot v těchto polích.

### <a name="azresources-previously-azurermresources"></a>Az.Resources (dříve AzureRM.Resources)

- Odebrání parametru `Sku` z rutiny `New/Set-AzPolicyAssignment`.
- Odebrání parametru `Password` z rutin `New-AzADServicePrincipal` a `New-AzADSpCredential`. Hesla se generují automaticky. Skripty, u kterých se zadávalo heslo:

  ```powershell  
  New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476 -Password $secPassword
  ```

  by se měly změnit tak, aby heslo načítaly z výstupu:

  ```powershell  
  $credential = New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476
  $secPassword = $credential.Secret
  ```


### <a name="azstorage-previously-azurestorage-and-azurermstorage"></a>Az.Storage (dříve Azure.Storage a AzureRM.Storage)

- Pro zajištění podpory vytvoření kontextu úložiště OAuth pouze s použitím názvu účtu úložiště se výchozí sada parametrů změnila na `OAuthParameterSet`.
  - Příklad: `$ctx = New-AzureStorageContext -StorageAccountName $accountName`
- Parametr `Location` se stal povinným v rutině `Get-AzStorageUsage`.
- Metody rozhraní API služby Storage teď místo synchronních volání rozhraní API používají Asynchronní model založený na úkolech (TAP). Následující příklady ukazují nové asynchronní příkazy:

#### <a name="blob-snapshot"></a>Snímek objektu BLOB

AzureRM:

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$b.ICloudBlob.Snapshot()
```

Az:

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$task = $b.ICloudBlob.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="share-snapshot"></a>Sdílet snímek

AzureRM:

```powershell  
$Share = Get-AzureStorageShare -Name $containerName -Context $ctx
$snapshot = $Share.Snapshot()
```

Az:

```powershell  
$Share = Get-AzStorageShare -Name $containerName -Context $ctx
$task = $Share.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="undelete-soft-deleted-blob"></a>Obnovení obnovitelně odstraněného objektu blob

AzureRM:

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$b.ICloudBlob.Undelete()
```

Az:

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$task = $b.ICloudBlob.UndeleteAsync()
$task.Wait()
```

#### <a name="set-blob-tier"></a>Nastavit úroveň objektu BLOB

AzureRM:

```powershell  
$blockBlob = Get-AzureStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$blockBlob.ICloudBlob.SetStandardBlobTier("hot")

$pageBlob = Get-AzureStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$pageBlob.ICloudBlob.SetPremiumBlobTier("P4")
```

Az:

```powershell  
$blockBlob = Get-AzStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$task = $blockBlob.ICloudBlob.SetStandardBlobTierAsync("hot")
$task.Wait()

$pageBlob = Get-AzStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$task = $pageBlob.ICloudBlob.SetPremiumBlobTierAsync("P4")
$task.Wait()
```

### <a name="azwebsites-previously-azurermwebsites"></a>Az.Websites (dříve AzureRM.Websites)

- Odebrání zastaralých vlastností z objektů `PSAppServicePlan`, `PSCertificate`, `PSCloningInfo` a `PSSite`

## <a name="next-steps"></a>Další kroky

- Další informace o PowerShellu v centru Azure Stack najdete v tématu [Začínáme s PowerShellem v centru Azure Stack](../user/azure-stack-powershell-overview.md) .
- Instalace prostředí PowerShell AZ Module, viz [install PowerShell AZ Module for Azure Stack hub](powershell-install-az-module.md)
