---
title: Použití nástroje pro ověření šablony v Azure Stack hub
description: Pomocí nástroje pro ověření šablony vyhledejte šablony pro nasazení do centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 10/01/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: 35fe7fbd2a3d7004d70e343ca763ea49563f2597
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546561"
---
# <a name="use-the-template-validation-tool-in-azure-stack-hub"></a>Použití nástroje pro ověření šablony v Azure Stack hub

Pomocí nástroje pro ověření šablony ověřte, jestli jsou [šablony](azure-stack-arm-templates.md) Azure Resource Manager připravené k nasazení do centra Azure Stack. Nástroj pro ověření šablony je k dispozici v úložišti GitHub nástroje Azure Stack hub. Stáhněte si nástroje Azure Stack hub pomocí postupu popsaného v tématu [Stažení nástrojů z GitHubu](../operator/azure-stack-powershell-download.md).

## <a name="overview"></a>Přehled

Chcete-li ověřit šablonu, je nutné nejprve sestavit soubor možností cloudu a poté spustit nástroj pro ověření. Použijte následující moduly PowerShellu z Azure Stack nástrojů centra:

- Ve složce **CloudCapabilities** : **AZ. CloudCapabilities. psm1** vytvoří soubor JSON možností cloudu, který představuje služby a verze v cloudu centra Azure Stack.
- Ve složce **TemplateValidator** : **AZ. TemplateValidator. psm1** používá soubor JSON možností cloudu k testování šablon pro nasazení v centru Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Sestavení souboru možností cloudu

Než použijete validátor šablon, spusťte modul příkaz **AZ. CloudCapabilities** PowerShell k sestavení souboru JSON.

> [!NOTE]
> Pokud aktualizujete integrovaný systém nebo přidáte jakékoli nové služby nebo virtuální rozšíření, měli byste tento modul spustit znovu.

1. Ujistěte se, že máte připojení k rozbočovači Azure Stack. Tyto kroky se dají provést z hostitele Azure Stack Development Kit (ASDK), nebo můžete k připojení z pracovní stanice použít [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) .
2. Importujte modul PowerShell **AZ. CloudCapabilities** :

    ```powershell
    Import-Module .\CloudCapabilities\Az.CloudCapabilities.psm1
    ```

3. Pomocí rutiny **Get-CloudCapabilities** načtěte verze služby a vytvořte soubor JSON cloudové možnosti. Pokud nezadáte `-OutputPath` , soubor **AzureCloudCapabilities.jsv** je vytvořen v aktuálním adresáři. Použijte své skutečné umístění v Azure:

    ```powershell
    Get-AzCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Ověřování šablon

Pomocí těchto kroků můžete ověřit šablony pomocí modulu PowerShell **AZ. TemplateValidator** . Můžete použít vlastní šablony nebo [šablony rychlý Start centra Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importujte modul PowerShell **AZ. TemplateValidator. psm1** :

    ```powershell
    cd "c:\AzureStack-Tools-az\TemplateValidator"
    Import-Module .\Az.TemplateValidator.psm1
    ```

2. Spusťte validátor šablon:

    ```powershell
    Test-AzTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

V konzole PowerShellu se zobrazují upozornění a chyby ověřování šablony a zapisují se do souboru HTML ve zdrojovém adresáři. Následující snímek obrazovky je příkladem sestavy ověření:

![Sestava ověření šablony](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametry

Rutina pro validátor šablon podporuje následující parametry.

| Parametr | Popis | Povinné |
| ----- | -----| ----- |
| `TemplatePath` | Určuje cestu k rekurzivnímu nalezení Azure Resource Manager šablon. | Yes |
| `TemplatePattern` | Určuje názvy souborů šablon, které se mají spárovat. | Ne |
| `CapabilitiesPath` | Určuje cestu k souboru JSON pro cloudové možnosti. | Yes |
| `IncludeComputeCapabilities` | Zahrnuje vyhodnocení prostředků IaaS, jako jsou velikosti virtuálních počítačů a rozšíření virtuálních počítačů. | Ne |
| `IncludeStorageCapabilities` | Zahrnuje vyhodnocení prostředků úložiště, například typů SKU. | Ne |
| `Report` | Určuje název vygenerované sestavy jazyka HTML. | Ne |
| `Verbose` | Zapisuje do konzoly chyby a upozornění. | Ne|

### <a name="examples"></a>Příklady

Tento příklad ověřuje všechny [šablony rychlého startu centra Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) stažené do místního úložiště. V tomto příkladu se taky ověřují velikosti a rozšíření virtuálních počítačů proti funkcím ASDK:

```powershell
test-AzTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Další kroky

- [Nasazení šablon do centra Azure Stack](azure-stack-arm-templates.md)
- [Vývoj šablon pro centrum Azure Stack](azure-stack-develop-templates.md)
