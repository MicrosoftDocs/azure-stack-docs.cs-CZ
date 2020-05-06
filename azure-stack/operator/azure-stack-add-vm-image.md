---
title: Přidání vlastní image virtuálního počítače do centra Azure Stack
description: Přečtěte si, jak přidat nebo odebrat vlastní image virtuálního počítače do centra Azure Stack.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2019
ms.openlocfilehash: 167a9c9d46be7ac77e59e2be7ede1c09e43fda70
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847924"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>Přidání vlastní image virtuálního počítače do centra Azure Stack

V centru Azure Stack můžete přidat vlastní image virtuálního počítače (VM) do Marketplace a zpřístupnit ji uživatelům. Image virtuálních počítačů můžete přidat do Marketplace centra Azure Stack pomocí portálu pro správu nebo Windows PowerShellu. Použijte obrázek z globální Azure Marketplace jako základ pro vlastní image nebo vytvořte vlastní pomocí technologie Hyper-V.

## <a name="step-1-create-the-custom-vm-image"></a>Krok 1: Vytvoření vlastní image virtuálního počítače

### <a name="windows"></a>Windows

Vytvořte vlastní zobecněný virtuální pevný disk.

**Pokud je virtuální pevný disk z vnějšku mimo Azure**, postupujte podle kroků v části [nahrání generalizace virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů v Azure](/azure/virtual-machines/windows/upload-generalized-managed) , aby byl váš virtuální pevný disk správně **Sysprep** a bylo možné ho zobecnit.

**Pokud virtuální pevný disk pochází z Azure**, před ZOBECNĚNÍM virtuálního počítače se ujistěte, že máte následující:

- Při zřizování virtuálního počítače v Azure použijte PowerShell a zřiďte ho bez `-ProvisionVMAgent` příznaku.
- Před zobecněním virtuálního počítače v Azure odeberte všechna rozšíření virtuálních počítačů pomocí rutiny **Remove-AzureRmVMExtension** z virtuálního počítače. Rozšíření virtuálních počítačů, která se instalují, můžete zjistit tak `Windows (C:) > WindowsAzure > Logs > Plugins`, že na.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

Podle pokynů v [tomto článku](/azure/virtual-machines/windows/download-vhd) proveďte správnou generalizaci a stažení virtuálního pevného disku, abyste ho mohli přenést do centra Azure Stack.

### <a name="linux"></a>Linux

**Pokud je virtuální pevný disk mimo Azure**, postupujte podle příslušných pokynů k generalizaci VHD:

- [Distribuce založené na CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES nebo openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

**Pokud virtuální pevný disk pochází z Azure**, postupujte podle těchto pokynů a proveďte generalizaci a stažení virtuálního pevného disku:

1. Zastavte službu **waagent** :

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Mějte na paměti, že verze agenta Azure Linux pracují se službou Azure Stack hub, [jak je uvedeno zde](azure-stack-linux.md#azure-linux-agent). Ujistěte se, že image nástroje Sysprep má verzi agenta Azure Linux, která je kompatibilní s Azure Stack hub.

2. Ukončí zrušení přidělení virtuálního počítače.

3. Stáhněte si VHD.

   1. Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL sdíleného přístupového podpisu (SAS). Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

   1. V nabídce okna pro virtuální počítač vyberte **disky**.

   1. Vyberte disk s operačním systémem pro virtuální počítač a pak vyberte **exportovat disk**.

   1. Nastavte čas vypršení platnosti adresy URL na 36000.

   1. Vyberte **generovat adresu URL**.

   1. Vygenerujte adresu URL.

   1. V části vygenerovaná adresa URL vyberte **Stáhnout soubor VHD**.

   1. Možná budete muset vybrat **Uložit** v prohlížeči a zahájit stahování. Výchozí název souboru VHD je _abcd_.

### <a name="considerations"></a>Požadavky

Než obrázek nahrajete, je důležité vzít v úvahu následující:

- Rozbočovač Azure Stack podporuje jenom generaci jednoho virtuálního počítače (1) ve formátu VHD s pevným diskem. Pevně daný logický disk se strukturuje v rámci souboru, takže posun disku *x* je uložený na posunu objektu BLOB *x*. Malé zápatí na konci objektu BLOB popisuje vlastnosti VHD. Pokud chcete ověřit, jestli je disk pevný, použijte rutinu **Get-VHD** PowerShellu.

- Centrum Azure Stack nepodporuje virtuální pevné disky (VHD) dynamického disku.

## <a name="step-2-upload-the-vm-image-to-a-storage-account"></a>Krok 2: nahrání image virtuálního počítače do účtu úložiště

1. [Nainstalujte PowerShell pro centrum Azure Stack](azure-stack-powershell-install.md).  

2. Přihlaste se k Azure Stack centrum jako operátor. Pokyny najdete v tématu [přihlášení do centra Azure Stack jako operátor](azure-stack-powershell-configure-admin.md).

3. Na image musí být odkazováno pomocí identifikátoru URI úložiště objektů BLOB. Připravte bitovou kopii operačního systému Windows nebo Linux ve formátu VHD (ne VHDX) a pak obrázek nahrajte do účtu úložiště v centru Azure Stack.

   - Pokud je virtuální pevný disk v Azure, můžete pomocí nástroje, jako je například [AzCopy](/azure/storage/common/storage-use-azcopy) , přenést přímo virtuální pevný disk mezi Azure a účtem úložiště centra Azure Stack, pokud pracujete v připojeném centru Azure Stack.

   - Pokud je váš virtuální pevný disk v Azure, musíte na odpojeném rozbočovači Azure Stack stáhnout virtuální pevný disk do počítače, který má připojení k rozbočovači Azure i Azure Stack. Pak zkopírujete virtuální pevný disk do tohoto počítače z Azure dřív, než přenesete virtuální pevný disk do centra Azure Stack pomocí některého z běžných [nástrojů pro přenos dat úložiště](../user/azure-stack-storage-transfer.md) , které se dají používat v Azure a centru Azure Stack.

     Jedním z těchto nástrojů, který se používá v tomto příkladu, je příkaz Add-AzureRmVHD, který odešle VHD do účtu úložiště na portálu pro správu centra Azure Stack.  

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd"
     ```

4. Poznamenejte si identifikátor URI úložiště objektů blob, do kterého nahráváte obrázek. Identifikátor URI úložiště objektů BLOB má následující formát: * &lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*. VHD.

5. Pokud chcete objekt BLOB anonymně zpřístupnit, přečtěte si kontejner objektů BLOB účtu úložiště, ve kterém se nahrál virtuální pevný disk image virtuálního počítače. Vyberte **objekt BLOB**a potom vyberte **zásady přístupu**. Volitelně můžete pro kontejner vygenerovat sdílený přístupový podpis a zahrnout ho jako součást identifikátoru URI objektu BLOB. Tento krok zajistí, že je objekt BLOB dostupný pro použití. Pokud objekt BLOB není anonymně přístupný, vytvoří se image virtuálního počítače ve stavu selhání.

   ![Přejít na objekty blob účtu úložiště](./media/azure-stack-add-vm-image/tca1.png)

   ![Nastavit přístup k objektu BLOB na veřejné](./media/azure-stack-add-vm-image/tca2.png)

   ![Nastavit přístup k objektu BLOB na veřejné](./media/azure-stack-add-vm-image/tca3.png)

## <a name="step-3-option-1-add-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>Krok 3, možnost 1: Přidání image virtuálního počítače jako operátora Azure Stackového centra pomocí portálu

1. Přihlaste se k Azure Stack centrum jako operátor. V nabídce vyberte **všechny služby** > **COMPUTE** v > části **image virtuálních počítačů****Přidat**.

   ![Vlastní uživatelské rozhraní pro zkušební načtení image](./media/azure-stack-add-vm-image/tca4.png)

2. V části **vytvořit image**zadejte identifikátor URI objektu BLOB pro vydavatele, nabídku, SKU, verzi a disk s operačním systémem. Pak vyberte **vytvořit** a začněte vytvářet image virtuálního počítače.

   ![Vlastní uživatelské rozhraní pro zkušební načtení image](./media/azure-stack-add-vm-image/tca5.png)

   Po úspěšném vytvoření image se stav image virtuálního počítače změní na **úspěch**.

3. Když přidáte obrázek, je k dispozici pouze pro šablony založené na Azure Resource Manager a nasazení prostředí PowerShell. Pokud chcete uživatelům zpřístupnit Image jako položku Marketplace, publikujte položku Marketplace pomocí kroků v článku [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md). Nezapomeňte si poznamenat hodnoty **vydavatele**, **nabídky**, **SKU**a **verze** . Budete je potřebovat při úpravách šablony Správce prostředků a manifestu. JSON ve vašem vlastním. azpkg.

## <a name="step-3-option-2-add-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>Krok 3, možnost 2: Přidání image virtuálního počítače jako operátora Azure Stackového centra pomocí PowerShellu

1. [Nainstalujte PowerShell pro centrum Azure Stack](azure-stack-powershell-install.md).  

2. Přihlaste se k Azure Stack centrum jako operátor. Pokyny najdete v tématu [přihlášení do centra Azure Stack jako operátor](azure-stack-powershell-configure-admin.md).

3. Otevřete PowerShell s výzvou se zvýšenými oprávněními a spusťte příkaz:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<Offer>" `
      -sku "<SKU>" `
      -version "<#.#.#>" `
      -OSType "<OS type>" `
      -OSUri "<OS URI>"
   ```

   Rutina **Add-AzsPlatformimage** určuje hodnoty, které používají šablony Azure Resource Manager k odkazování na image virtuálního počítače. Mezi tyto hodnoty patří:
   - **Microsoft**  
     Příklad: `Canonical`  
     Název **vydavatele** image virtuálního počítače, který uživatelé používají při nasazování bitové kopie. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **dodání**  
     Příklad: `UbuntuServer`  
     Název **nabídky** image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **skladové**  
     Příklad: `14.04.3-LTS`  
     Segment názvu **SKU** image virtuálního počítače, který uživatelé používají při nasazování image virtuálního počítače. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **znění**  
     Příklad: `1.0.0`  
     Verze image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače Tato verze je ve formátu * \#\#... \#*. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **osType**  
     Příklad: `Linux`  
     **OsType** obrázku musí být buď **Windows** , nebo **Linux**.  
   - **OSUri**  
     Příklad: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Pro můžete zadat identifikátor URI úložiště objektů BLOB `osDisk`.  

     Další informace najdete v referenčních informacích k prostředí PowerShell pro rutinu [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) .

4. Když přidáte obrázek, je k dispozici pouze pro šablony založené na Azure Resource Manager a nasazení prostředí PowerShell. Pokud chcete uživatelům zpřístupnit Image jako položku Marketplace, publikujte položku Marketplace pomocí kroků v článku [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md). Nezapomeňte si poznamenat hodnoty **vydavatele**, **nabídky**, **SKU**a **verze** . Budete je potřebovat při úpravách šablony Resource Manageru a manifestu. JSON ve vašem vlastním. azpkg.

## <a name="remove-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>Odebrání image virtuálního počítače jako operátora centra Azure Stack pomocí portálu

1. Otevřete centrum Azure Stack [portál pro správu] `https://adminportal.local.azurestack.external`.

2. Pokud má image virtuálního počítače přidruženou položku Marketplace, vyberte **Správa Marketplace**a pak vyberte položku virtuálního počítače, kterou chcete odstranit.

3. Pokud image virtuálního počítače nemá přidruženou položku Marketplace, přejděte do části **všechny služby > compute > image virtuálních počítačů**a potom vyberte tři tečky (**...**) vedle image virtuálního počítače.

4. Vyberte **Odstranit**.

## <a name="remove-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>Odebrání image virtuálního počítače jako operátora centra Azure Stack pomocí PowerShellu

Pokud už bitovou kopii virtuálního počítače, kterou jste nahráli, nepotřebujete, můžete ji odstranit z webu Marketplace pomocí následující rutiny:

1. [Nainstalujte PowerShell pro centrum Azure Stack](azure-stack-powershell-install.md).

2. Přihlaste se k Azure Stack centrum jako operátor.

3. Otevřete PowerShell s výzvou se zvýšenými oprávněními a spusťte příkaz:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<Publisher>" `
    -offer "<Offer>" `
    -sku "<SKU>" `
    -version "<Version>" `
   ```

   Rutina **Remove-AzsPlatformImage** určuje hodnoty, které používají šablony Azure Resource Manager k odkazování na image virtuálního počítače. Mezi tyto hodnoty patří:
   - **Microsoft**  
     Příklad: `Canonical`  
     Název **vydavatele** image virtuálního počítače, který uživatelé používají při nasazování bitové kopie. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **dodání**  
     Příklad: `UbuntuServer`  
     Název **nabídky** image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **skladové**  
     Příklad: `14.04.3-LTS`  
     Segment názvu **SKU** image virtuálního počítače, který uživatelé používají při nasazování image virtuálního počítače. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **znění**  
     Příklad: `1.0.0`  
     Verze image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače Tato verze je ve formátu * \#\#... \#*. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  

     Další informace o rutině **Remove-AzsPlatformImage** najdete v [dokumentaci k modulu operátora centra Azure Stack](/powershell/module/)Microsoft PowerShellu.

## <a name="next-steps"></a>Další kroky

- [Vytvoření a publikování položky Marketplace pro vlastní Azure Stack centra](azure-stack-create-and-publish-marketplace-item.md)
- [Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)
