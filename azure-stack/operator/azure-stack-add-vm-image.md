---
title: Přidání vlastní image virtuálního počítače do Azure Stack | Microsoft Docs
description: Přečtěte si, jak přidat nebo odebrat vlastní image virtuálního počítače pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 9dc5039a2c8b74b14da59573758a4cf8d1a3657a
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282653"
---
# <a name="add-a-custom-vm-to-azure-stack"></a>Přidání vlastního virtuálního počítače do Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

V Azure Stack můžete přidat vlastní image virtuálního počítače (VM) do Marketplace a zpřístupnit ji uživatelům. Obrázky se přidávají pomocí Azure Resource Manager šablon pro Azure Stack. Image virtuálních počítačů můžete také přidat do uživatelského rozhraní Azure Marketplace jako položku Marketplace pomocí portálu pro správu nebo Windows PowerShellu. Použijte buď obrázek z globální Azure Marketplace, nebo vlastní image virtuálního počítače.

## <a name="generalize-the-vm-image"></a>Generalizace image virtuálního počítače

### <a name="windows"></a>Windows

Vytvořte vlastní zobecněný virtuální pevný disk. Pokud je virtuální pevný disk z vnějšku mimo Azure, postupujte podle kroků v části [nahrání generalizace virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů v Azure](/azure/virtual-machines/windows/upload-generalized-managed) , aby byl váš virtuální pevný disk správně **Sysprep** a bylo možné ho zobecnit.

Pokud virtuální pevný disk pochází z Azure, postupujte podle pokynů v části [generalize zdrojového virtuálního počítače pomocí nástroje Sysprep](/azure/virtual-machines/windows/upload-generalized-managed#generalize-the-source-vm-by-using-sysprep) , abyste ho mohli Azure Stack.

### <a name="linux"></a>Linux

Pokud je virtuální pevný disk mimo Azure, postupujte podle příslušných pokynů k generalizaci VHD:

- [Distribuce na základě CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES nebo openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Server Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pokud virtuální pevný disk pochází z Azure, postupujte podle těchto pokynů pro generalizaci VHD:

1. Zastavte službu **waagent** :

   ```bash
   # sudo waagent -force -deprovision
   # export HISTSIZE=0
   # logout
   ```

2. Vypněte virtuální počítač a stáhněte VHD. Pokud předáváte virtuální pevný disk z Azure, můžete to udělat pomocí exportu disku, jak je znázorněno v [stažení virtuálního pevného disku s Windows z Azure](/azure/virtual-machines/windows/download-vhd).

### <a name="common-steps-for-both-windows-and-linux"></a>Běžné kroky pro Windows i Linux

Než obrázek nahrajete, je důležité vzít v úvahu následující:

- Azure Stack podporuje generování jednoho (1) virtuálního počítače ve formátu VHD s pevným diskem. Pevně daný logický disk se strukturuje v rámci souboru, takže posun disku *x* je uložený na posunu objektu BLOB *x*. Malé zápatí na konci objektu BLOB popisuje vlastnosti VHD. Pokud chcete ověřit, jestli je disk pevný, použijte rutinu **Get-VHD** PowerShellu.

- Azure Stack nepodporuje virtuální pevné disky (VHD) dynamického disku. Změna velikosti dynamického disku připojeného k virtuálnímu počítači způsobí opuštění virtuálního počítače ve stavu selhání. Pokud chcete tento problém zmírnit, odstraňte virtuální počítač, aniž byste odstranili disk virtuálního pevného disku v účtu úložiště. Pak převeďte virtuální pevný disk z dynamického disku na pevný disk a znovu vytvořte virtuální počítač.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Přidání image virtuálního počítače jako operátoru Azure Stack pomocí portálu

1. Na image musí být odkazováno pomocí identifikátoru URI úložiště objektů BLOB. Připravte bitovou kopii operačního systému Windows nebo Linux ve formátu VHD (ne VHDX) a pak obrázek nahrajte do účtu úložiště v Azure nebo Azure Stack.

   - Pokud je virtuální pevný disk v Azure, můžete k přímému přenosu VHD mezi Azure a Azure Stack účtu úložiště použít nástroj, jako je [AzCopy](/azure/storage/common/storage-use-azcopy) , pokud používáte na připojeném Azure Stack.

   - Pokud máte na odpojeném Azure Stack virtuální pevný disk v Azure, budete muset stáhnout virtuální pevný disk do počítače, který má připojení k Azure i Azure Stack. Pak zkopírujete VHD do tohoto počítače z Azure před tím, než přenesete virtuální pevný disk do Azure Stack pomocí některého z běžných [nástrojů pro přenos dat úložiště](../user/azure-stack-storage-transfer.md) , které se dají používat v Azure a Azure Stack.

2. Podle [tohoto příkladu](/powershell/module/azurerm.compute/add-azurermvhd?view=azurermps-6.13.0) můžete nahrát VHD do účtu úložiště na portálu pro správu Azure Stack.

   - Nahrávání obrázku do úložiště objektů blob Azure Stack než do úložiště objektů BLOB v Azure je efektivnější, protože image se nahrává do úložiště Azure Stack imagí kratší dobu.

   - Po nahrání [image virtuálního počítače s Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)se ujistěte, že jste přepnuli krok **přihlášení do Azure** pomocí kroku [Konfigurace prostředí PowerShellu Azure Stack operator](azure-stack-powershell-configure-admin.md) .  

3. Poznamenejte si identifikátor URI úložiště objektů blob, do kterého nahráváte obrázek. Identifikátor URI úložiště objektů BLOB má následující formát: *&lt;storageAccount @ no__t-2 @ no__t-3 @ no__t-4blobContainer @ no__t-5 @ no__t-6 @ no__t-7targetVHDName @ no__t-8*. VHD.

4. Pokud chcete objekt BLOB anonymně zpřístupnit, přečtěte si kontejner objektů BLOB účtu úložiště, ve kterém se nahrál virtuální pevný disk image virtuálního počítače. Vyberte **objekt BLOB**a potom vyberte **zásady přístupu**. Volitelně můžete pro kontejner vygenerovat sdílený přístupový podpis a zahrnout ho jako součást identifikátoru URI objektu BLOB. Tento krok zajistí, že je objekt BLOB dostupný pro použití. Pokud objekt BLOB není anonymně přístupný, vytvoří se image virtuálního počítače ve stavu selhání.

   ![Přejít na objekty blob účtu úložiště](./media/azure-stack-add-vm-image/image1.png)

   ![Nastavit přístup k objektu BLOB na veřejné](./media/azure-stack-add-vm-image/image2.png)

5. Přihlaste se k Azure Stack jako operátor. V nabídce vyberte **všechny služby** > **Image** pod položkou **COMPUTE** > **Přidat**.

6. V části **vytvořit image**zadejte název, předplatné, skupinu prostředků, umístění, disk s operačním systémem, typ operačního systému, identifikátor URI objektu BLOB úložiště, typ účtu a mezipaměť hostitele. Pak vyberte **vytvořit** a začněte vytvářet image virtuálního počítače.

   ![Začněte vytvářet image.](./media/azure-stack-add-vm-image/image4.png)

   Po úspěšném vytvoření image se stav image virtuálního počítače změní na **úspěch**.

7. Když přidáte obrázek, je k dispozici pouze pro šablony založené na Azure Resource Manager a nasazení prostředí PowerShell. Pokud chcete uživatelům zpřístupnit Image jako položku Marketplace, publikujte položku Marketplace pomocí kroků v článku [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md). Nezapomeňte si poznamenat hodnoty **vydavatele**, **nabídky**, **SKU**a **verze** . Budete je potřebovat při úpravách šablony Resource Manageru a manifestu. JSON ve vašem vlastním. azpkg.

## <a name="remove-the-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Odebrání image virtuálního počítače jako operátoru Azure Stack pomocí portálu

1. Otevřete portál Azure Stack [správce](https://adminportal.local.azurestack.external).

2. Pokud má image virtuálního počítače přidruženou položku Marketplace, vyberte **Správa Marketplace**a pak vyberte položku virtuálního počítače, kterou chcete odstranit.

3. Pokud image virtuálního počítače nemá přidruženou položku Marketplace, přejděte do části **všechny služby > compute > image virtuálních počítačů**a potom vyberte tři tečky ( **...** ) vedle image virtuálního počítače.

4. Vyberte **Odstranit**.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Přidání image virtuálního počítače jako operátoru Azure Stack pomocí prostředí PowerShell

1. [Nainstalujte PowerShell pro Azure Stack](azure-stack-powershell-install.md).  

2. Přihlaste se k Azure Stack jako operátor. Pokyny najdete v tématu [přihlášení k Azure Stack jako operátor](azure-stack-powershell-configure-admin.md).

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
     Například: `Canonical`  
     Název **vydavatele** image virtuálního počítače, který uživatelé používají při nasazování bitové kopie. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **dodání**  
     Například: `UbuntuServer`  
     Název **nabídky** image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **skladové**  
     Například: `14.04.3-LTS`  
     Segment názvu **SKU** image virtuálního počítače, který uživatelé používají při nasazování image virtuálního počítače. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **version**  
     Například: `1.0.0`  
     Verze image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače Tato verze je ve formátu *\#. \#. \#* . Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **osType**  
     Například: `Linux`  
     **OsType** obrázku musí být buď **Windows** , nebo **Linux**.  
   - **OSUri**  
     Například: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Identifikátor URI úložiště objektů blob můžete zadat pro `osDisk`.  

     Další informace najdete v referenčních informacích k prostředí PowerShell pro rutiny [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) a [New-DataDiskObject](/powershell/module/Azs.Compute.Admin/New-DataDiskObject) .

## <a name="remove-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Odebrání image virtuálního počítače jako operátoru Azure Stack pomocí prostředí PowerShell

Pokud už bitovou kopii virtuálního počítače, kterou jste nahráli, nepotřebujete, můžete ji odstranit z webu Marketplace pomocí následující rutiny:

1. [Nainstalujte PowerShell pro Azure Stack](azure-stack-powershell-install.md).

2. Přihlaste se k Azure Stack jako operátor.

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
     Například: `Canonical`  
     Název **vydavatele** image virtuálního počítače, který uživatelé používají při nasazování bitové kopie. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **dodání**  
     Například: `UbuntuServer`  
     Název **nabídky** image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **skladové**  
     Například: `14.04.3-LTS`  
     Segment názvu **SKU** image virtuálního počítače, který uživatelé používají při nasazování image virtuálního počítače. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **version**  
     Například: `1.0.0`  
     Verze image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače Tato verze je ve formátu *\#. \#. \#* . Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  

     Další informace o rutině **Remove-AzsPlatformImage** najdete v dokumentaci modulu Microsoft PowerShell [Azure Stack operator](/powershell/module/).

## <a name="next-steps"></a>Další kroky

- [Vytvoření a publikování vlastní položky Azure Stack Marketplace](azure-stack-create-and-publish-marketplace-item.md)
- [Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)
