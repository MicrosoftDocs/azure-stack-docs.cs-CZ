---
title: Přidání image virtuálního počítače do Azure stacku | Dokumentace Microsoftu
description: Přidat image virtuálního počítače nebo odebrat obrázek, který se vaše organizace vlastní Windows nebo image virtuálního počítače s Linuxem pro klienty použít.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 440e68b54b99139b85ad53fd67bc43c409d658a8
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985632"
---
# <a name="add-a-vm-image-to-offer-in-azure-stack"></a>Přidání image virtuálního počítače ve službě Azure Stack nabízí

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Ve službě Azure Stack můžete přidat image virtuálního počítače (VM) na webu marketplace zpřístupnit uživatelům. Image virtuálních počítačů můžete přidat pomocí šablon Azure Resource Manageru pro službu Azure Stack. Image virtuálních počítačů do uživatelského rozhraní služby Azure Marketplace můžete také přidat jako položky Marketplace. Použijte image na Azure markeplace globální nebo vlastní image virtuálního počítače. Můžete přidat image virtuálního počítače pomocí portálu pro správu nebo prostředí Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Přidání image virtuálního počítače pomocí portálu

> [!NOTE]  
> S touto metodou musíte vytvořit položku Marketplace samostatně.

Image musí být možné odkazovat identifikátor URI úložiště objektů blob. Příprava image operačního systému Windows nebo Linuxem ve formátu virtuálního pevného disku (VHDX není) a pak ji nahrát do účtu úložiště v Azure nebo ve službě Azure Stack. Pokud vaše image je již nahrána do úložiště objektů blob v Azure nebo ve službě Azure Stack, můžete přeskočit krok 1.

1. [Nahrání image virtuálního počítače Windows Azure pro nasazení Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) nebo image Linuxu, postupujte podle pokynů v tématu [nasaďte Linuxové virtuální počítače v Azure stacku](azure-stack-linux.md). Před nahráním image, je důležité vzít v úvahu následující faktory:

   - Azure Stack jenom podporuje generování jedno (1) virtuální počítač ve virtuální pevný disk pevný disk naformátovat. Oprava format struktury logický disk lineárně v rámci souboru, takže posun disku o X je uložen v objektu blob s posunem X. Malé zápatí na konci objektu blob popisuje vlastnosti virtuálního pevného disku. Pokud chcete potvrdit, pokud je oprava disku, použijte [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) příkaz prostředí PowerShell.  

     > [!IMPORTANT]  
     >  Azure Stack nepodporuje dynamického disku VHD. Změna velikosti dynamický disk, který je připojen k virtuálnímu počítači ponechá virtuální počítač ve stavu selhání. Chcete-li tento problém zmírnit, odstraňte virtuální počítač bez odstranění disku Virtuálního počítače, objekt blob VHD v účtu úložiště. Převést virtuální pevný disk z dynamický disk na pevný disk a znovu vytvořte virtuální počítač.

   - To je mnohem efektivnější k nahrání obrázku do úložiště objektů blob v Azure stacku, než se do Azure blob storage vzhledem k tomu, že bude trvat kratší dobu nasdílejte image do úložiště imagí Azure Stack.

   - Po odeslání [image virtuálního počítače Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), ujistěte se, že k nahrazení **přihlášení k Azure** krok s [konfigurace prostředí PowerShell pro operátory Azure stacku](azure-stack-powershell-configure-admin.md) kroku.  

   - Poznamenejte si úložiště objektů blob v identifikátoru URI, kde jste tuto image odeslat. Identifikátor URI objektu blob úložiště má následující formát: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* VHD.

   - Pokud chcete zpřístupnit tento objekt blob anonymně, přejděte na kontejner objektů blob v účtu úložiště kde virtuálního pevného disku image virtuálního počítače na server nebyl nahrán. Vyberte **Blob**a pak vyberte **zásada přístupu**. Můžete volitelně vygenerovat sdílený přístupový podpis kontejneru a zahrnout ho jako součást identifikátor URI objektu blob. Tento krok zajistí, že se že objekt blob je možné použít pro přidání tohoto jako obrázek. Pokud se objekt blob není dostupné anonymně, image virtuálního počítače bude vytvořena ve stavu selhání.

     ![Přejít na objektech blob na účtu úložiště](./media/azure-stack-add-vm-image/image1.png)

     ![Sada objektů blob přístup k veřejné](./media/azure-stack-add-vm-image/image2.png)

2. Přihlaste se ke službě Azure Stack jako operátor. V nabídce vyberte **všechny služby** > **image** pod **Compute** > **přidat**.

3. V části **vytvoření image**, zadejte název, předplatné, skupinu prostředků, umístění, disk s operačním systémem, typ operačního systému, identifikátor URI objektu blob úložiště, typ účtu a hostujte ukládání do mezipaměti. Vyberte **vytvořit** můžete začít vytvářet Image virtuálního počítače.

   ![Zahájit vytvoření bitové kopie](./media/azure-stack-add-vm-image/image4.png)

   Po úspěšném vytvoření image, image stav virtuálního počítače se změní na **Succeeded**.

4. Chcete-li snadněji k dispozici pro potřeby koncových uživatelů image virtuálního počítače v uživatelském rozhraní, je vhodné [vytvořit položku Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Odeberte image virtuálního počítače pomocí portálu

1. Otevřít na portálu pro správu na [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Vyberte **Marketplace správu**a potom vyberte virtuální počítač, který chcete odstranit.

3. Klikněte na **Odstranit**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Přidání image virtuálního počítače na webu Marketplace pomocí Powershellu

> [!Note]  
> Když přidáte šablony a nasazení prostředí PowerShell založený na bitovou kopii pouze bude k dispozici pro správce prostředků Azure. Nedělejte obrázky k dispozici vaši uživatelé jako položky marketplace publikování položky marketplace pomocí kroků v článku [vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)

1. [Instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).  

2. Přihlaste se ke službě Azure Stack jako operátor. Pokyny najdete v tématu [přihlásit ke službě Azure Stack jako operátor](azure-stack-powershell-configure-admin.md).

3. Otevřete prostředí PowerShell s řádku se zvýšenými oprávněními a spusťte:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>" `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   **Přidat AzsPlatformimage** rutiny určuje hodnoty slouží k odkazování image virtuálního počítače pomocí šablony Azure Resource Manageru. Mezi hodnoty patří:
   - **publisher**  
     Příklad: `Canonical`  
     Segment názvu publisher z image virtuálního počítače, který uživatelé používají při jejich nasazení bitové kopie. Příkladem je **Microsoft**. V tomto poli nezahrnují mezera nebo speciální znaky.  
   - **nabídka**  
     Příklad: `UbuntuServer`  
     Segment název nabídky image virtuálního počítače, který uživatelé použít, když nasadí image virtuálního počítače. Příkladem je **WindowsServer**. V tomto poli nezahrnují mezera nebo speciální znaky.  
   - **sku**  
     Příklad: `14.04.3-LTS`  
     Název segmentu skladovou Položku Image virtuálního počítače, který uživatelé použít, když nasadí image virtuálního počítače. Příkladem je **Datacenter2016**. V tomto poli nezahrnují mezera nebo speciální znaky.  
   - **Verze**  
     Příklad: `1.0.0`  
     Verze Image virtuálního počítače, který uživatelé použít, když nasadí image virtuálního počítače. Tato verze je ve formátu  *\#.\#. \#*. Příkladem je **1.0.0**. V tomto poli nezahrnují mezera nebo speciální znaky.  
   - **osType**  
     Příklad: `Linux`  
     OsType obrázku musí být buď **Windows** nebo **Linux**.  
   - **OSUri**  
     Příklad: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Můžete určit identifikátor URI úložiště objektů blob pro `osDisk`.  

     Další informace najdete v tématu referenční informace prostředí PowerShell pro [přidat AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) rutiny a [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) rutiny.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Přidání vlastní image virtuálního počítače na webu Marketplace pomocí Powershellu
 
1. [Instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).

   ```powershell
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. Pokud používáte **Active Directory Federation Services**, použijte následující rutinu:

   ```powershell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external
    ```

3. Přihlaste se ke službě Azure Stack jako operátor. Pokyny najdete v tématu [přihlásit ke službě Azure Stack jako operátor](azure-stack-powershell-configure-admin.md).

4. Vytvoření účtu úložiště v globálního Azure nebo Azure Stack a ukládat vlastní image virtuálního počítače. Pokyny najdete v tématu [rychlý start: Nahrávání, stahování a výpis objektů BLOB pomocí webu Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Příprava image operačního systému Windows nebo Linuxem ve formátu virtuálního pevného disku (VHDX není), odešlete image do účtu úložiště a získejte identifikátor URI, kde je možné načíst image virtuálního počítače pomocí Powershellu.  

   ```powershell
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

6. (Volitelně) Můžete nahrát celou řadu datových disků jako součást image virtuálního počítače. Vytvoření vaší datové disky pomocí rutiny New-DataDiskObject. Otevřete PowerShell řádku se zvýšenými oprávněními a spusťte:

   ```powershell
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. Otevřete prostředí PowerShell s řádku se zvýšenými oprávněními a spusťte:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>" -OSType "<ostype>" -OSUri "<osuri>"
   ```

    Další informace o rutinu Add-AzsPlatformimage a rutiny New-DataDiskObject, najdete v článku Microsoft Powershellu [dokumentace ke službě Azure Stack operátor modulu](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Odstranit image virtuálního počítače pomocí Powershellu

Pokud už nepotřebujete image virtuálního počítače, který jste nahráli, můžete ho odstranit z webu Marketplace pomocí následující rutiny:

1. [Instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).

2. Přihlaste se ke službě Azure Stack jako operátor.

3. Otevřete prostředí PowerShell s řádku se zvýšenými oprávněními a spusťte:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   **Odebrat AzsPlatformImage** rutiny určuje hodnoty slouží k odkazování image virtuálního počítače pomocí šablony Azure Resource Manageru. Mezi hodnoty patří:
   - **publisher**  
     Příklad: `Canonical`  
     Segment názvu publisher z image virtuálního počítače, který uživatelé používají při jejich nasazení bitové kopie. Příkladem je **Microsoft**. V tomto poli nezahrnují mezera nebo speciální znaky.  
   - **nabídka**  
     Příklad: `UbuntuServer`  
     Segment název nabídky image virtuálního počítače, který uživatelé použít, když nasadí image virtuálního počítače. Příkladem je **WindowsServer**. V tomto poli nezahrnují mezera nebo speciální znaky.  
   - **sku**  
     Příklad: `14.04.3-LTS`  
     Název segmentu skladovou Položku Image virtuálního počítače, který uživatelé použít, když nasadí image virtuálního počítače. Příkladem je **Datacenter2016**. V tomto poli nezahrnují mezera nebo speciální znaky.  
   - **Verze**  
     Příklad: `1.0.0`  
     Verze Image virtuálního počítače, který uživatelé použít, když nasadí image virtuálního počítače. Tato verze je ve formátu  *\#.\#. \#*. Příkladem je **1.0.0**. V tomto poli nezahrnují mezera nebo speciální znaky.  
    
     Další informace o rutině Remove-AzsPlatformImage, najdete v článku Microsoft Powershellu [dokumentace ke službě Azure Stack operátor modulu](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Další postup

[Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)
