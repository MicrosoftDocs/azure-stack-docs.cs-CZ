---
title: Přesunutí virtuálního počítače z Azure do centra Azure Stack
description: Naučte se, jak přesunout virtuální počítač z Azure do centra Azure Stack do centra Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: c8c68a64f7a05e03c70e138cb4d8c95da7417ec9
ms.sourcegitcommit: 3e225b30a54159b6b8dbeb2f843a2e5a721b746e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91519407"
---
# <a name="move-a-vm-from-azure-to-azure-stack-hub"></a>Přesunutí virtuálního počítače z Azure do centra Azure Stack

Virtuální pevný disk (VHD) můžete nahrát z virtuálního počítače vytvořeného v Azure do vaší instance centra Azure Stack.

## <a name="prepare-and-download-your-vhd-from-azure"></a>Příprava a stažení virtuálního pevného disku z Azure

Najděte si část, která je specifická pro vaše potřeby při přípravě virtuálního pevného disku.

#### <a name="windows---specialized"></a>[Systém Windows – specializované](#tab/win-spec)

- Postupujte podle kroků v článku [Vytvoření virtuálního počítače s Windows z specializovaného disku pomocí prostředí PowerShell](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) k přípravě virtuálního pevného disku.
- Pokud chcete nasadit rozšíření virtuálních počítačů, ujistěte se, že je k dispozici soubor Agent virtuálního počítače. msi.  
  Informace a postupy najdete v tématu [Přehled agenta virtuálního počítače Azure](/azure/virtual-machines/extensions/agent-windows). Ujistěte se, že je rozšíření nainstalované na VIRTUÁLNÍm počítači před přesunutím virtuálního počítače. Pokud se agent virtuálního počítače nenachází v VHD, nasazení rozšíření se nezdaří. Při zřizování není nutné nastavovat profil operačního systému, nebo ho nastavit `$vm.OSProfile.AllowExtensionOperations = $true` .

#### <a name="windows---generalized"></a>[Windows – generalizovaná](#tab/win-gen)

::: moniker range="<=azs-1910"
- Postupujte podle pokynů v části [stažení virtuálního pevného disku s Windows z Azure](/azure/virtual-machines/windows/download-vhd) pro správnou generalizaci a stažení virtuálního pevného disku před jeho přesunutím do centra Azure Stack.
- Při zřizování virtuálního počítače v Azure použijte PowerShell. Připravte ho bez `-ProvisionVMAgent` příznaku.
- Před zobecněním virtuálního počítače v Azure odeberte všechna rozšíření virtuálních počítačů pomocí rutiny **Remove-AzureRmVMExtension** z virtuálního počítače. Rozšíření virtuálních počítačů, která se instalují, můžete zjistit tak, že na `Windows (C:) > WindowsAzure > Logs > Plugins` .

```powershell  
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```
::: moniker-end
::: moniker range=">=azs-2002"

Postupujte podle pokynů v části [stažení virtuálního pevného disku s Windows z Azure](/azure/virtual-machines/windows/download-vhd) pro správnou generalizaci a stažení virtuálního pevného disku před jeho přesunutím do centra Azure Stack.
::: moniker-end

#### <a name="linux---specialized"></a>[Linux – specializované](#tab/lin-spec)

- Před stažením virtuálního počítače se systémem Linux postupujte podle pokynů v části Příprava virtuálního počítače v článku [Vytvoření virtuálního počítače se systémem Linux z vlastního disku pomocí Azure CLI](/azure/virtual-machines/linux/upload-vhd#prepare-the-vm) .
- Při přípravě a stažení virtuálního pevného disku použijte postup v článku [stažení virtuálního pevného disku se systémem Linux z Azure](/azure//virtual-machines/windows/download-vhd) .
- V případě specializovaného virtuálního pevného disku se ujistěte, že používáte sémantiku "připojit" `-CreateOption Attach` . Příklad najdete v článku [Vytvoření virtuálního počítače pomocí existujícího spravovaného disku s operačním systémem pomocí prostředí PowerShell (Windows)](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks).

#### <a name="linux---generalized"></a>[Linux – generalizované](#tab/lin-gen)

1. Zastavte službu **waagent** :

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Verze agenta Azure Linux, které pracují s centrem Azure Stack, [jsou popsané tady](../operator/azure-stack-linux.md#azure-linux-agent). Ujistěte se, že bitová kopie, na které jste spustili nástroj Sysprep, má verzi agenta Azure Linux, která je kompatibilní s Azure Stack hub.

2. Ukončí zrušení přidělení virtuálního počítače.

3. Stáhněte si VHD.

   1. Pokud chcete stáhnout soubor VHD, vygenerujte adresu URL sdíleného přístupového podpisu (SAS). Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

   1. V nabídce okna pro virtuální počítač vyberte **disky**.

   1. Vyberte disk s operačním systémem pro virtuální počítač a pak vyberte **exportovat disk**.

   1. Nastavte čas vypršení platnosti adresy URL na 36000.

   1. Vyberte **generovat adresu URL**.

   1. Vygenerujte adresu URL.

   1. V části vygenerovaná adresa URL vyberte **Stáhnout soubor VHD**.

   1. Možná budete muset vybrat **Uložit** v prohlížeči a zahájit stahování. Výchozí název souboru VHD je **abcd**.

   1. Nyní můžete tento virtuální pevný disk přesunout do centra Azure Stack.

> [!IMPORTANT]  
> Skript najdete v [ukázkovém skriptu článku, kde můžete nahrát virtuální pevný disk do Azure a vytvořit nový virtuální počítač](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script) pro nahrání virtuálního pevného disku do účtu úložiště uživatele centra Azure Stack a vytvoření virtuálního počítače. Ujistěte se, že jste zadali `$urlOfUploadedImageVhd` účet úložiště centra Azure Stack + adresa URL kontejneru. Pro zobecněný virtuální pevný disk nezapomeňte použít `FromImage` hodnotu při nastavení `-CreateOption FromImage` .

---

## <a name="verify-your-vhd"></a>Ověření virtuálního pevného disku

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Odeslat do účtu úložiště

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-vm"></a>Vytvoření virtuálního počítače

Vlastní image se přidávají ve dvou formách: **specializované** a **zobecněné**.

### <a name="specialized"></a>[Specializovaná](#tab/create-vm-spec)

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

### <a name="generalized"></a>[Generalizovaná](#tab/create-vm-gen)

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]
---
## <a name="next-steps"></a>Další kroky

[Přesunutí virtuálního počítače do centra Azure Stack – přehled](vm-move-overview.md)
