---
title: Přidání vlastní image virtuálního počítače do centra Azure Stack
description: Přečtěte si, jak přidat nebo odebrat vlastní image virtuálního počítače do centra Azure Stack.
author: sethmanheim
ms.topic: how-to
ms.date: 10/09/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 5266766786cd359446e8313ec19e07746b227fb2
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899478"
---
# <a name="add-and-remove-a-custom-vm-image-to-azure-stack-hub"></a>Přidání a odebrání vlastní image virtuálního počítače do centra Azure Stack

V centru Azure Stack můžete jako operátor přidat vlastní image virtuálního počítače (VM) do Marketplace a zpřístupnit ji uživatelům. Image virtuálních počítačů můžete na marketplace služby Azure Stack Hub přidávat prostřednictvím portálu správce nebo Windows PowerShellu. Použijte obrázek z globální Microsoft Azure Marketplace jako základ pro vlastní image nebo vytvořte vlastní pomocí technologie Hyper-V.

## <a name="add-an-image"></a>Přidání obrázku

Pokyny pro přidání zobecněných a specializovaných imagí najdete v části **COMPUTE** v uživatelské příručce. Než začnete image nabídnout vašim uživatelům, budete chtít vytvořit zobecněnou image. Pokyny najdete v tématu [přesunutí virtuálního počítače do centra Azure Stack – přehled](../user/vm-move-overview.md). Při vytváření imagí, které jsou k dispozici pro klienty, použijte portál pro správu centra Azure Stack nebo koncové body správce místo koncového bodu portálu User Portal nebo adresáře tenanta.

Máte dvě možnosti, jak zpřístupnit image uživatelům:

- **Nabídka Image je dostupná jenom přes Azure Resource Manager**  
  Pokud přidáte obrázek prostřednictvím portálu pro správu centra Azure Stack ve **výpočetních**  >  **imagích**, budou mít všichni vaši klienti k imagi přístup. Uživatelé ale budou k přístupu potřebovat šablonu Azure Resource Manager. V tržišti centra Azure Stack se nezobrazí.

- **Nabídka Image prostřednictvím tržiště centra Azure Stack**  
    Po přidání Image prostřednictvím portálu pro správu centra Azure Stack můžete vytvořit nabídku Marketplace. Pokyny najdete v tématu [Vytvoření a publikování vlastní položky na webu Marketplace centra Azure Stack](azure-stack-create-and-publish-marketplace-item.md).

## <a name="add-a-platform-image"></a>Přidat image platformy

Pokud chcete přidat image platformy do centra Azure Stack, použijte portál Azure Stack centra pro správu nebo koncový bod pomocí PowerShellu. Nejdřív musíte vytvořit zobecněný virtuální pevný disk. Další informace najdete v tématu [přesun virtuálního počítače do centra Azure Stack – přehled](../user/vm-move-overview.md).

### <a name="portal"></a>[Azure Portal](#tab/image-add-portal)

Přidejte image virtuálního počítače jako operátor Azure Stackového centra pomocí portálu.

1. Přihlaste se k Azure Stack centrum jako operátor. V nabídce vyberte **všechny služby**  >  **COMPUTE**  >  **Image**  >  **Přidat**.

   ![Přidání image virtuálního počítače](./media/azure-stack-add-vm-image/tca4.png)

2. V části **vytvořit image**zadejte identifikátor URI objektu BLOB pro **vydavatele**, **nabídku**, **SKU**, **verzi**a disk s operačním systémem. Pak vyberte **vytvořit** a začněte vytvářet image virtuálního počítače.

   ![Vlastní uživatelské rozhraní pro zkušební načtení image](./media/azure-stack-add-vm-image/tca5.png)

   Po úspěšném vytvoření image se stav image virtuálního počítače změní na **úspěch**.

3. Když přidáte obrázek, je k dispozici pouze pro šablony založené na Azure Resource Manager a nasazení prostředí PowerShell. Pokud chcete uživatelům zpřístupnit Image jako položku Marketplace, publikujte položku Marketplace pomocí kroků v článku [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md). Nezapomeňte si poznamenat hodnoty **vydavatele**, **nabídky**, **SKU**a **verze** . Budete je potřebovat, když upravíte šablonu Správce prostředků a Manifest.jsna vlastní. azpkg.

### <a name="powershell"></a>[PowerShell](#tab/image-add-ps)

 Přidejte image virtuálního počítače jako operátor Azure Stackového centra pomocí PowerShellu.

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
     Verze image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače Tato verze je ve formátu ** \# . \# . \# **. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  
   - **osType**  
     Příklad: `Linux`  
     **OsType** obrázku musí být buď **Windows** , nebo **Linux**.  
   - **OSUri**  
     Příklad: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Pro můžete zadat identifikátor URI úložiště objektů BLOB `osDisk` .  

     Další informace najdete v referenčních informacích k prostředí PowerShell pro rutinu [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) .

4. Když přidáte obrázek, je k dispozici pouze pro šablony založené na Azure Resource Manager a nasazení prostředí PowerShell. Pokud chcete uživatelům zpřístupnit Image jako položku Marketplace, publikujte položku Marketplace pomocí kroků v článku [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md). Nezapomeňte si poznamenat hodnoty **vydavatele**, **nabídky**, **SKU**a **verze** . Budete je potřebovat, když upravíte šablonu Správce prostředků a Manifest.jsna vlastní. azpkg.

---

## <a name="remove-a-platform-image"></a>Odebrání image platformy

Image platformy můžete odebrat pomocí portálu nebo PowerShellu.

### <a name="portal"></a>[Azure Portal](#tab/image-rem-portal)

Pokud chcete odebrat image virtuálního počítače jako operátor centra Azure Stack pomocí portálu Azure Stack hub, postupujte takto:

1. Otevřete portál Azure Stack centrum pro [správu](https://portal.azure.com/signin/index).

2. Pokud má image virtuálního počítače přidruženou položku Marketplace, vyberte **Správa Marketplace**a pak vyberte položku virtuálního počítače, kterou chcete odstranit.

3. Pokud image virtuálního počítače nemá přidruženou položku Marketplace, přejděte do části **všechny služby > compute > image virtuálních počítačů**a potom vyberte tři tečky (**...**) vedle image virtuálního počítače.

4. Vyberte **Odstranit**.

### <a name="powershell"></a>[PowerShell](#tab/image-rem-ps)

Pokud chcete odebrat image virtuálního počítače jako operátor centra Azure Stack pomocí prostředí PowerShell, postupujte takto:

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
     Verze image virtuálního počítače, kterou uživatelé používají při nasazování image virtuálního počítače Tato verze je ve formátu ** \# . \# . \# **. Do tohoto pole nezahrnujte mezeru ani jiné speciální znaky.  

     Další informace o rutině **Remove-AzsPlatformImage** najdete v [dokumentaci k modulu operátora centra Azure Stack](/powershell/azure/azure-stack/overview)Microsoft PowerShellu.

---

## <a name="next-steps"></a>Další kroky

- [Vytvoření a publikování položky Marketplace pro vlastní Azure Stack centra](azure-stack-create-and-publish-marketplace-item.md)
- [Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)