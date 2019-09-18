---
title: Přidání image virtuálního počítače do Azure Stack | Microsoft Docs
description: Přečtěte si, jak přidat nebo odebrat image virtuálního počítače pro Azure Stack.
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
ms.date: 09/17/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: fef815ec23655638bbe4df1bcdccae42aeee13e2
ms.sourcegitcommit: 9f4c6e96f60b4c229316e7a4ab6e0e5ef0a9a232
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71061172"
---
# <a name="add-a-vm-image-to-azure-stack"></a>Přidání image virtuálního počítače do Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

V Azure Stack můžete přidat image virtuálního počítače (VM) do Marketplace a zpřístupnit ji uživatelům. Obrázky se přidávají pomocí Azure Resource Manager šablon pro Azure Stack. Image virtuálních počítačů můžete také přidat do uživatelského rozhraní Azure Marketplace jako položku Marketplace pomocí portálu pro správu nebo Windows PowerShellu. Použijte buď obrázek z globální Azure Marketplace, nebo vlastní image virtuálního počítače.

## <a name="add-a-vm-image-through-the-portal"></a>Přidání image virtuálního počítače přes portál

> [!NOTE]  
> Pomocí této metody musíte položku Marketplace vytvořit samostatně.

Na image musí být odkazováno pomocí identifikátoru URI úložiště objektů BLOB. Připravte bitovou kopii operačního systému Windows nebo Linux ve formátu VHD (ne VHDX) a pak obrázek nahrajte do účtu úložiště v Azure nebo Azure Stack. Pokud je vaše image už nahraná v úložišti objektů BLOB v Azure nebo Azure Stack, můžete přeskočit krok 1.

1. [Nahrajte image virtuálního počítače s Windows do Azure pro nasazení Správce prostředků](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) nebo pro Image Linux postupujte podle pokynů popsaných v tématu [nasazení virtuálních počítačů se systémem Linux v Azure Stack](azure-stack-linux.md). Než obrázek nahrajete, je důležité vzít v úvahu následující faktory:

   - Azure Stack podporuje jenom generaci jednoho virtuálního počítače (1) ve formátu VHD s pevným diskem. Pevně daný logický disk se strukturuje v rámci souboru, takže posun disku X je uložený na posunu objektu BLOB X. Malé zápatí na konci objektu BLOB popisuje vlastnosti VHD. Pokud chcete ověřit, jestli je disk pevný, použijte příkaz [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell.  

     > [!IMPORTANT]  
     >  Azure Stack nepodporuje virtuální pevné disky s dynamickými disky. Změna velikosti dynamického disku připojeného k virtuálnímu počítači způsobí opuštění virtuálního počítače ve stavu selhání. Pokud chcete tento problém zmírnit, odstraňte virtuální počítač, aniž byste odstranili disk virtuálního pevného disku v účtu úložiště. Pak převeďte virtuální pevný disk z dynamického disku na pevný disk a znovu vytvořte virtuální počítač.

   - Je efektivnější nahrát obrázek do Azure Stack úložiště objektů BLOB než do úložiště objektů BLOB v Azure, protože image se nahrává do úložiště Azure Stack imagí kratší dobu.

   - Po nahrání [image virtuálního počítače s Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)se ujistěte, že jste přepnuli krok **přihlášení do Azure** pomocí kroku [Konfigurace prostředí PowerShellu Azure Stack operator](azure-stack-powershell-configure-admin.md) .  

   - Poznamenejte si identifikátor URI úložiště objektů blob, do kterého nahráváte obrázek. Identifikátor URI úložiště objektů BLOB má následující formát:  *&lt;storageAccount&gt;&lt;&gt;/blobContainer/targetVHDName&gt;.VHD.&lt;*

   - Pokud chcete objekt BLOB anonymně zpřístupnit, přečtěte si kontejner objektů BLOB účtu úložiště, ve kterém se nahrál virtuální pevný disk image virtuálního počítače. Vyberte **objekt BLOB**a potom vyberte **zásady přístupu**. Volitelně můžete pro kontejner vygenerovat sdílený přístupový podpis a zahrnout ho jako součást identifikátoru URI objektu BLOB. Tento krok zajistí, že je objekt BLOB dostupný pro použití. Pokud objekt BLOB není anonymně přístupný, vytvoří se image virtuálního počítače ve stavu selhání.

     ![Přejít na objekty blob účtu úložiště](./media/azure-stack-add-vm-image/image1.png)

     ![Nastavit přístup k objektu BLOB na veřejné](./media/azure-stack-add-vm-image/image2.png)

2. Přihlaste se k Azure Stack jako operátor. V nabídce vyberte **všechny služby** > **Image** v části **výpočetní** > prostředí**Přidat**.

3. V části **vytvořit image**zadejte název, předplatné, skupinu prostředků, umístění, disk s operačním systémem, typ operačního systému, identifikátor URI objektu BLOB úložiště, typ účtu a mezipaměť hostitele. Pak vyberte **vytvořit** a začněte vytvářet image virtuálního počítače.

   ![Začněte vytvářet image.](./media/azure-stack-add-vm-image/image4.png)

   Po úspěšném vytvoření image se stav image virtuálního počítače změní na **úspěch**.

4. Aby se image virtuálního počítače lépe zajistila pro spotřebu uživatelů v uživatelském rozhraní, je vhodné [vytvořit položku Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Odebrání image virtuálního počítače přes portál

1. Otevřete portál správce na adrese [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

2. Vyberte **Správa Marketplace**a potom vyberte virtuální počítač, který chcete odstranit.

3. Klikněte na tlačítko **odstranit**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Přidání image virtuálního počítače do Marketplace pomocí PowerShellu

> [!Note]  
> Přidáte-li obrázek, bude k dispozici pouze pro šablony založené na Azure Resource Manager a nasazení prostředí PowerShell. Pokud chcete uživatelům zpřístupnit Image jako položku Marketplace, publikujte položku Marketplace pomocí kroků v tomto článku: [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)

1. [Nainstalujte PowerShell pro Azure Stack](azure-stack-powershell-install.md).  

2. Přihlaste se k Azure Stack jako operátor. Pokyny najdete v tématu [přihlášení k Azure Stack jako operátor](azure-stack-powershell-configure-admin.md).

3. Otevřete PowerShell s výzvou se zvýšenými oprávněními a spusťte příkaz:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>" `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   Rutina **Add-AzsPlatformimage** určuje hodnoty, které používají šablony Azure Resource Manager k odkazování na image virtuálního počítače. Mezi tyto hodnoty patří:
   - **publisher**  
     Příklad: `Canonical`  
     Název vydavatele image virtuálního počítače, který uživatelé používají při nasazování bitové kopie. Příkladem může být **Microsoft**. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **dodání**  
     Příklad: `UbuntuServer`  
     Název nabídky image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače. Příkladem je **windowsserver**. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **sku**  
     Příklad: `14.04.3-LTS`  
     Segment názvu SKU image virtuálního počítače, který uživatelé používají při nasazování image virtuálního počítače. Příkladem je **Datacenter2016**. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **znění**  
     Příklad: `1.0.0`  
     Verze image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače Tato verze je ve formátu  *\#\#... \#* . Příkladem je **1.0.0**. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **osType**  
     Příklad: `Linux`  
     OsType obrázku musí být buď **Windows** , nebo **Linux**.  
   - **OSUri**  
     Příklad: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Pro můžete zadat identifikátor URI úložiště objektů BLOB `osDisk`.  

     Další informace najdete v referenčních informacích k prostředí PowerShell pro rutinu [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) a rutinu [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) .

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Přidání vlastní image virtuálního počítače do Marketplace pomocí PowerShellu
 
1. [Nainstalujte PowerShell pro Azure Stack](azure-stack-powershell-install.md).

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

2. Pokud používáte **Active Directory Federation Services (AD FS) (AD FS)** , použijte následující rutinu:

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

3. Přihlaste se k Azure Stack jako operátor. Pokyny najdete v tématu [přihlášení k Azure Stack jako operátor](azure-stack-powershell-configure-admin.md).

4. Vytvořte účet úložiště v globálním Azure nebo Azure Stack k uložení vlastní image virtuálního počítače. Pokyny najdete v [tématu rychlý Start: Nahrávání, stahování a výpis objektů BLOB pomocí Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Připravte bitovou kopii operačního systému Windows nebo Linux ve formátu VHD (ne VHDX), nahrajte image do svého účtu úložiště a získejte identifikátor URI, kde se dá image virtuálního počítače načíst pomocí PowerShellu.  

   ```powershell
   Add-AzureRmAccount 
   -EnvironmentName "AzureStackAdmin" 
   -TenantId $TenantID
   ```
  
   >[!Note]
   > Pokud vaše relace vyprší, vaše heslo se změnilo nebo chcete jednoduše přepnout účty, spusťte následující rutinu ještě před přihlášením pomocí rutiny Add-AzureRmAccount:`Remove-AzureRmAccount-Scope Process`

6. Volitelně Pole datových disků můžete nahrát jako součást image virtuálního počítače. Pomocí rutiny New-DataDiskObject vytvořte datové disky. Otevřete PowerShell z příkazového řádku se zvýšenými oprávněními a spusťte příkaz:

   ```powershell
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. Otevřete PowerShell s výzvou se zvýšenými oprávněními a spusťte příkaz:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>" -OSType "<ostype>" -OSUri "<osuri>"
   ```

    Další informace o rutině Add-AzsPlatformimage a rutině New-DataDiskObject najdete v [dokumentaci modulu Microsoft PowerShell Azure Stack operator](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Odebrání image virtuálního počítače pomocí PowerShellu

Pokud už bitovou kopii virtuálního počítače, kterou jste nahráli, nepotřebujete, můžete ji odstranit z webu Marketplace pomocí následující rutiny:

1. [Nainstalujte PowerShell pro Azure Stack](azure-stack-powershell-install.md).

2. Přihlaste se k Azure Stack jako operátor.

3. Otevřete PowerShell s výzvou se zvýšenými oprávněními a spusťte příkaz:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   Rutina **Remove-AzsPlatformImage** určuje hodnoty, které používají šablony Azure Resource Manager k odkazování na image virtuálního počítače. Mezi tyto hodnoty patří:
   - **publisher**  
     Příklad: `Canonical`  
     Název vydavatele image virtuálního počítače, který uživatelé používají při nasazování bitové kopie. Příkladem může být **Microsoft**. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **dodání**  
     Příklad: `UbuntuServer`  
     Název nabídky image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače. Příkladem je **windowsserver**. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **sku**  
     Příklad: `14.04.3-LTS`  
     Segment názvu SKU image virtuálního počítače, který uživatelé používají při nasazování image virtuálního počítače. Příkladem je **Datacenter2016**. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **znění**  
     Příklad: `1.0.0`  
     Verze image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače Tato verze je ve formátu  *\#\#... \#* . Příkladem je **1.0.0**. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
    
     Další informace o rutině Remove-AzsPlatformImage najdete v dokumentaci modulu Microsoft PowerShell [Azure Stack operator](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Další postup

[Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)
