---
title: Uživatelská příručka k nástroji Azure Site Recovery navrácení služeb po obnovení
description: Naučte se používat nástroj Azure Site Recovery navrácení služeb po obnovení k ochraně virtuálních počítačů (VM).
author: sethmanheim
ms.author: sethm
ms.date: 11/19/2020
ms.topic: how-to
ms.reviewer: rtiberiu
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: 0cb3bccab11d337a8a8804578233edb95ac02dc6
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517221"
---
# <a name="azure-site-recovery-failback-tool"></a>Nástroj pro navrácení služeb po obnovení Azure Site Recovery

V připojeném prostředí můžete použít Azure Site Recovery k ochraně virtuálních počítačů spuštěných v centru Azure Stack. [Tento článek](/azure/site-recovery/azure-stack-site-recovery) popisuje, jak nastavit prostředí a jak Site Recovery pomáhají přispívat k celkové provozní kontinuitě a strategii zotavení po havárii pro tyto úlohy.

V případě výpadku se operátor centra Azure Stack projde postupem *převzetí služeb při selhání* . Jakmile se centrum Azure Stack znovu spustí a znovu spustí, projde se procesem *navrácení služeb po obnovení* . Tento postup převzetí služeb při selhání je popsaný v [tomto Site Recovery článku](/azure/site-recovery/azure-stack-site-recovery), ale proces navrácení služeb po obnovení zahrnuje několik ručních kroků:

1. Zastavte virtuální počítač běžící v Azure.
2. Stáhněte si virtuální pevné disky.
3. Nahrajte virtuální pevné disky do centra Azure Stack.
4. Vytvořte znovu virtuální počítače.
5. Nakonec spusťte tento virtuální počítač běžící v Azure Stackovém centru. 

Vzhledem k tomu, že tento proces může být náchylný k chybám a časově náročný, jsme vytvořili skripty, které vám pomůžou tento proces urychlit a automatizovat.

> [!Note]  
> Nástroj Azure Site Recovery vyžaduje centrum Azure Stack AZ Modules. Pokud používáte moduly AzureRM centra Azure Stack, budete muset upgradovat pracovní stanici nebo použít nástroj Azure Site Recovery navrácení služeb po obnovení v izolovaném prostředí pomocí modulů AZ Modules. Další informace najdete v tématu [instalace prostředí PowerShell AZ Module for Azure Stack hub](powershell-install-az-module.md).

## <a name="failback-procedure"></a>Procedura navrácení služeb po obnovení

Automatizovaný proces navrácení služeb po obnovení obsahuje tři hlavní části:

- **Copy-AzSiteRecoveryVmVHD**:
  - Ukončí virtuální počítač Azure.
  - Připraví export disku.
  - Zkopíruje disk buď prostřednictvím AzCopy, nebo StorageBlobCopy.
  - Nahraje disk do účtu úložiště Azure Stack hub.

- Po zkopírování disku existují dva scénáře, které jsou pokryté prostřednictvím **přípravy-AzSiteRecoveryVMFailBack**:
  - Původní centrum Azure Stack se obnovilo. Původní virtuální počítač stále existuje a stačí změnit pouze jeho virtuální pevné disky.
  - Pokud dojde ke ztrátě původních virtuálních počítačů, musíte v případě havárie znovu sestavit celý virtuální počítač.

  Tento postup se zabývá oběma scénáři vytvořením šablony a požadovaného souboru parametrů.

- Skutečné nasazení Azure Resource Manager šablony pomocí souboru parametrů a nasazení/vytvoření virtuálního počítače v centru Azure Stack.

### <a name="prerequisites"></a>Předpoklady

K provedení tohoto postupu navrácení služeb po obnovení se vyžadují tyto požadavky:

- Zkopírujte [nástroj Azure Site Recovery navrácení služeb po obnovení](https://aka.ms/azshasr).

- Importujte modul FailbackTool. psm1 do PowerShellu.

- Postupujte podle pokynů v [tomto článku a nainstalujte si modul AZ Module for Azure Stack hub](powershell-install-az-module.md).

- volitelné [Stáhněte si AzCopy verze 10](/azure/storage/common/storage-use-azcopy-v10).

  - Kopírování objektu BLOB pomocí **AzCopy** je rychlejší, ale vyžaduje další místo na místním disku pro dočasné uložení souboru objektu BLOB.
  - Pokud se **AzCopy** nepoužívá, kopie VHD se provádí pomocí **AzStorageBlobCopy**. To znamená, že není vyžadováno žádné místní úložiště, ale proces trvá déle.

- Přístup k prostředkům na Azure Portal a přístup k vytváření těchto prostředků v centru Azure Stack.

## <a name="step-1-copy-blob-from-azure-to-azure-stack-hub"></a>Krok 1: zkopírování objektu BLOB z Azure do centra Azure Stack

Voláním rutiny prostředí PowerShell **copy-AzSiteRecoveryVmVHD** zastavte virtuální počítač Azure, Stáhněte si virtuální pevné disky z Azure a nahrajte je do centra Azure Stack. Například:

```powershell
$uris = Copy-AzSiteRecoveryVmVHD `
        -SourceVM $vmOnAzure `
        -TargetStorageAccountName "targetaccountName" `
        -TargetStorageEndpoint "redmond.ext-v.masd.stbtest.microsoft.com" `
        -TargetStorageAccountKey $accountKey `
        -AzCopyPath "C:\azcopy_v10\azcopy.exe" `
        -VhdLocalFolder "C:\tempfolder"
```

Vezměte na vědomí následující skutečnosti:

- Tento příklad používá `$uris` pro uchování `SourceDiskVhdUris` hodnoty používané v kroku 2.

- `-SourceVM`Parametr je objekt virtuálního počítače načtený nástrojem `Get-AzVM` .
  - Toto je chráněný virtuální počítač z centra Azure Stack, u kterého došlo k převzetí služeb při selhání v Azure.
  - Nezáleží na tom, jestli je virtuální počítač spuštěný, protože skript vypne virtuální počítač. Doporučuje se, abyste ho explicitně vypnuli a zastavili služby uvnitř virtuálního počítače.

- Na straně Azure Stack centra můžete zadat buď klíč účtu (pomocí `TargetStorageAccountKey` ), nebo token SAS (pomocí `TargetStorageAccountSasToken` ) účtu úložiště. Token SAS se musí vytvořit na úrovni účtu úložiště s minimálně následujícími oprávněními:

   :::image type="content" source="media/site-recovery-failback/sasperms.png" alt-text="Oprávnění tokenu SAS":::

- Můžete zadat koncový bod úložiště, který zahrnuje oblast a plně kvalifikovaný název domény. například `regionname.azurestack.microsoft.com` , nebo název prostředí centra Azure Stack, například `AzureStackTenant` . Pokud se použije název prostředí, měl by být uveden pomocí **Get-AzEnvironment**.

- Pomocí **AzCopy** nebo **AZSTORAGEBLOBCOPY** můžete zkopírovat VHD z Azure do centra Azure Stack. **AzCopy** je rychlejší, ale musí nejdřív stahovat soubory VHD do místní složky:
  - Pokud chcete použít **AzCopy**, zadejte parametry `-AzCopyPath` a `-VhdLocalFolder` (cestu, do které se virtuální pevné disky zkopírují).
  - Pokud není k dispozici dostatek místa v místním prostředí, můžete zvolit kopírování VHD přímo bez **AzCopy**, a to vynecháním parametrů `-AzCopyPath` a `-VhdLocalFolder` . Ve výchozím nastavení tento příkaz používá **AzStorageBlobCopy** ke zkopírování přímo do Azure Stack účtu úložiště centra.

## <a name="step-2-generate-resource-manager-templates"></a>Krok 2: generování šablon Správce prostředků

Po zkopírování disku použijte rutinu **Prepare-AzSiteRecoveryVMFailBack** k vytvoření `$templateFile` a `$parameterFile` požadované k nasazení virtuálního počítače do centra Azure Stack:

```powershell
$templateFile, $parameterFile = Prepare-AzSiteRecoveryVMFailBack `
                                -SourceContextName "PublicAzure" `
                                -SourceVM $vmOnAzure `
                                -SourceDiskVhdUris $uris `
                                -TargetResourceLocation "redmond" `
                                -ArmTemplateDestinationPath "C:\ARMtemplates" `
                                -TargetVM $vmOnHub `
                                -TargetContextName "AzureStack"

```

Vezměte na vědomí následující skutečnosti:

- Tento příklad používá `-SourceDiskVhdUris` jako návratovou hodnotu z kroku 1 (pomocí `$uris` ).

- Tato rutina podporuje dva scénáře:
  - Když zadáte `-TargetVM` , předpokládá se, že virtuální počítač je aktivní na straně centra Azure Stack a chcete nahradit jeho disky nejnovějšími zkopírovanými z Azure.
  - Skript vygeneruje šablonu Správce prostředků pro nasazení tohoto virtuálního počítače a odstraní existující virtuální počítač z centra Azure Stack.
  
  > [!NOTE]
  > Odstranění samotného virtuálního počítače centra Azure Stack neodebere ostatní objekty (například virtuální síť, skupinu prostředků, skupin zabezpečení sítě). Odstraní jenom samotný prostředek virtuálního počítače a šablona se pak nasadí s `-incremental` parametrem.

  - Pokud tento parametr nezadáte `-TargetVM` , skript předpokládá, že virtuální počítač už na straně centra Azure Stack neexistuje, takže skript vytvoří šablonu správce prostředků pro nasazení zcela nového virtuálního počítače.

- Vygenerované soubory šablon Správce prostředků jsou umístěny pod `-ArmTemplateDestinationPath` a úplná cesta k souboru šablony nebo souboru parametrů je vrácena.

- Pokud `-TargetVM` je zadán parametr, rutina odstraní virtuální počítač, takže můžete pokračovat v následujících krocích.

## <a name="step-3-deploy-the-resource-manager-template"></a>Krok 3: nasazení šablony Správce prostředků

V tomto okamžiku se VHD nahraje do centra Azure Stack a vytvoří se šablona Správce prostředků a příslušné soubory parametrů. To je všechno, co zbývá k nasazení virtuálního počítače do centra Azure Stack.

V některých scénářích můžete chtít upravit tuto šablonu a přidat, odebrat nebo změnit některé názvy nebo prostředky. Tato možnost je povolená, protože šablonu můžete podle potřeby upravovat a upravovat.

Po dokončení a po potvrzení prostředků v šabloně Správce prostředků podle očekávání můžete zavolat rutinu **New-AzResourceGroupDeployment** k nasazení prostředků. Například:

```powershell
New-AzResourceGroupDeployment `
  -Name "Failback" `
  -ResourceGroupName "failbackrg" `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile `
  -Mode Incremental
```

Vezměte na vědomí následující skutečnosti:

- `-ResourceGroupName`Parametr by měl být existující skupina prostředků.
- `-TemplateFile`Parametry a `-TemplateParameterFile` přicházejí z vrácených hodnot v kroku 2.

## <a name="next-steps"></a>Další kroky

- [Funkce virtuálního počítače centra Azure Stack](../user/azure-stack-vm-considerations.md)
- [Přidání a odebrání vlastní image virtuálního počítače do centra Azure Stack](azure-stack-add-vm-image.md)
- [Vytvoření virtuálního počítače s Windows pomocí PowerShellu v centru Azure Stack](../user/azure-stack-quick-create-vm-windows-powershell.md)