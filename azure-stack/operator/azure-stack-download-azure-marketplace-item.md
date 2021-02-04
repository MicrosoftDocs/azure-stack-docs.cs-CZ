---
title: Stažení položek z webu Marketplace z Azure a publikování do centra Azure Stack
description: Přečtěte si, jak stáhnout položky z webu Marketplace z Azure a publikovat do centra Azure Stack.
author: sethmanheim
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/16/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 3634862089f3811da314d3f85865fc1a29324f5f
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534041"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Stažení položek z Marketplace do centra Azure Stack

Jako operátor cloudu můžete z Marketplace stahovat položky do centra Azure Stack a zpřístupnit je všem uživatelům pomocí prostředí Azure Stack hub. Položky, které můžete vybrat, jsou z uspořádaného seznamu Azure Marketplacech položek, které jsou předem testovány a podporovány pro práci s centrem Azure Stack. Do tohoto seznamu se často přidají další položky, takže se budete moct vrátit k novému obsahu.

Existují dva scénáře stažení produktů z webu Marketplace:

- **Odpojený nebo částečně připojený scénář**: vyžaduje, abyste měli přístup k Internetu pomocí syndikačního nástroje Marketplace ke stažení položek Marketplace. Pak přenesete soubory ke stažení na odpojenou instalaci centra Azure Stack. Tento scénář používá PowerShell.
- **Připojený scénář**: vyžaduje, aby bylo prostředí centra Azure Stack připojené k Internetu. K vyhledání a stažení položek slouží portál pro správu centra Azure Stack.

Úplný seznam položek z webu Marketplace, které si můžete stáhnout, najdete v tématu [Azure Marketplace položky pro centrum Azure Stack](azure-stack-marketplace-azure-items.md) . Seznam nedávných přidání, odstranění a aktualizací služby Azure Stack hub Marketplace najdete v článku o [změnách na webu Marketplace centra Azure Stack](azure-stack-marketplace-changes.md) .

> [!NOTE]
> Katalog se bude lišit v závislosti na cloudu, ke kterému je připojen systém Azure Stack hub. Cloudové prostředí se určuje pomocí předplatného Azure, které používáte k registraci centra Azure Stack.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

::: zone pivot="state-connected"
Připojené nasazení umožňuje používat portál pro správu ke stažení položek Marketplace.

## <a name="prerequisites"></a>Požadavky

Vaše nasazení centra Azure Stack musí mít připojení k Internetu a musí být zaregistrované v Azure.

## <a name="use-the-portal-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí portálu

1. Přihlaste se k portálu pro správu centra Azure Stack.

2. Před stažením položek z webu Marketplace zkontrolujte dostupný prostor úložiště. Později můžete po výběru položek ke stažení porovnat velikost stahovaných zařízení s dostupnou kapacitou úložiště. Pokud je kapacita omezená, zvažte možnosti [správy dostupného místa](azure-stack-manage-storage-shares.md#manage-available-space).

   Chcete-li zkontrolovat dostupné místo: v **oblasti Správa oblastí** vyberte oblast, kterou chcete prozkoumat, a pak klikněte na úložiště **poskytovatelé prostředků**  >  :

   ![Kontrola prostoru úložiště na portálu pro správu centra Azure Stack](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Otevřete web Centrum pro Azure Stack a připojte se k Azure. Provedete to tak, že vyberete službu **pro správu Marketplace** , vyberete **položky Marketplace** a pak vyberete **Přidat z Azure**:

   ![Přidání položek z Marketplace z Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. V každé položce řádku se zobrazí také aktuálně dostupná verze. Pokud je k dispozici více než jedna verze položky Marketplace, zobrazí se ve sloupci **verze** **více**. Kliknutím na každou položku můžete zobrazit její popis a další informace, včetně její velikosti ke stažení:

   ![Snímek obrazovky zobrazující dostupné verze položky Marketplace](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Pokud je verze položky zobrazená jako **více**, můžete tuto položku vybrat a pak vybrat konkrétní verzi z rozevíracího seznamu výsledné verze výběr. Všimněte si, že Microsoft teď má možnost přidat atributy, které blokují správcům stažení produktů z Marketplace, které nejsou kompatibilní s jejich Azure Stack, z důvodu různých vlastností, jako je například Azure Stack verze nebo model fakturace. Pouze společnost Microsoft může přidat tyto atributy:

   [![Přidat z Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3sm.png "Více verzí")](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png#lightbox)

6. Vyberte položku, kterou chcete, a pak vyberte **Stáhnout**. Doba stahování se liší a závisí na připojení k síti. Po dokončení stahování můžete novou položku Marketplace nasadit jako operátor centra Azure Stack nebo uživatele.

7. Pokud chcete staženou položku nasadit, vyberte **+ vytvořit prostředek** a potom vyhledejte kategorie nové položky Marketplace. Pak vyberte položku, která zahájí proces nasazení. Tento proces se liší pro různé položky Marketplace.
::: zone-end

::: zone pivot="state-disconnected"
Pokud Azure Stack hub má omezené nebo žádné připojení k Internetu, můžete ke stažení položek Marketplace na počítač s připojením k Internetu použít PowerShell a *Nástroj pro syndikaci na webu Marketplace* . Pak přenesete položky do prostředí Azure Stack hub. V odpojeném prostředí nemůžete stáhnout položky Marketplace pomocí portálu Azure Stack hub.

Nástroj syndikace na webu Marketplace se dá použít taky v připojeném scénáři.

Tento scénář obsahuje dvě části:

- **Část 1**: stažení z položek Marketplace. V počítači s přístupem k Internetu konfigurujete PowerShell, stáhnete nástroj syndikace a pak stáhnete položky z Azure Marketplace.
- **Část 2**: nahrání a publikování na tržišti Azure Stack hub. Soubory, které jste stáhli do prostředí centra Azure Stack, přesunete a pak je publikujete na webu služby Azure Stack hub Marketplace.

## <a name="prerequisites"></a>Požadavky

- Připojené prostředí (nemusí být Azure Stack hub). Potřebujete připojení, abyste získali seznam produktů z Azure s jejich podrobnostmi a stáhli všechno místně. Až to uděláte, zbývající část postupu nevyžaduje připojení k Internetu. Vytvoří katalog položek, které jste předtím stáhli, abyste je mohli použít v odpojeném prostředí.

- Vyměnitelná média pro připojení k vašemu odpojenému prostředí a přenos všech potřebných artefaktů.

- Prostředí odpojeného centra Azure Stack s následujícími požadavky:

  - Vaše nasazení centra Azure Stack musí být zaregistrované v Azure.

  - Počítač, který má připojení k Internetu, musí mít **modul prostředí PowerShell pro Azure Stack verze 1.2.11** nebo novější. Pokud ještě neexistuje, [nainstalujte Azure Stack moduly PowerShellu specifické pro centrum](powershell-install-az-module.md).

  - Pokud chcete povolit import stažené položky Marketplace, musí se nakonfigurovat [prostředí PowerShell pro operátor centra Azure Stack](azure-stack-powershell-configure-admin.md) .
  - .NET Framework 4,7 nebo novější.

Stáhněte modul **AZS. Syndication. admin** z Galerie prostředí PowerShell pomocí následujícího příkazu:

### <a name="az-modules"></a>[AZ modules](#tab/az1)

  ```powershell
  Install-Module -Name Azs.Syndication.Admin -AllowPrerelease -PassThru
  ```

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm1)

  ```powershell
  Install-Module -Name Azs.Syndication.Admin -RequiredVersion 0.1.140
  ```

---

Po registraci Azure Stack můžete ignorovat následující zprávu, která se zobrazí v okně správy Marketplace, protože není relevantní pro případ odpojeného použití:

![Správa Marketplace](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

## <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí nástroje pro syndikaci na webu Marketplace

> [!IMPORTANT]
> Nezapomeňte si stáhnout nástroj syndikace webu Marketplace při každém stažení položek Marketplace v odpojeném scénáři. V tomto nástroji jsou k disopakující se změny a nejaktuálnější verze by se měla použít pro každé stažení.

### <a name="az-modules"></a>[AZ modules](#tab/az2)

1. V počítači s připojením k Internetu otevřete konzolu PowerShellu jako správce.

2. Přihlaste se ke příslušnému klientovi Azure Cloud a AzureAD Directory pomocí účtu Azure, který jste použili k registraci centra Azure Stack. Pokud chcete účet přidat, v PowerShellovém běhu `Connect-AzAccount` :

   ```powershell  
   Connect-AzAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```

   Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování v závislosti na konfiguraci vašeho účtu.

   > [!NOTE]
   > Pokud vaše relace vyprší, vaše heslo se změnilo nebo chcete přepnout účty, spusťte následující rutinu ještě před přihlášením pomocí `Connect-AzAccount` : `Remove-AzAccount -Scope Process` .

3. Pokud máte více předplatných, spusťte následující příkaz a vyberte ten, který jste použili pro registraci:

   ```powershell  
   Get-AzSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzSubscription
   ```

4. Pokud jste to ještě neudělali v kroku požadavky, Stáhněte si nejnovější verzi nástroje syndikace webu Marketplace:

   ```powershell
   Install-Module -Name Azs.Syndication.Admin -AllowPrerelease -PassThru
   ```

5. Pokud chcete vybrat položky Marketplace, například image virtuálních počítačů, rozšíření nebo šablony řešení ke stažení, spusťte následující příkaz:

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   Tím se zobrazí tabulka se seznamem všech registrací Azure Stack dostupných ve vybraném předplatném. Zvolte registraci, která odpovídá Azure Stack prostředí, pro které stahujete položky Marketplace, a vyberte **OK**.

     ![Snímek obrazovky, který zobrazuje seznam všech registrů Azure Stack dostupných ve vybraném předplatném.](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Nyní by se měla zobrazit druhá tabulka se seznamem všech položek Marketplace, které jsou k dispozici ke stažení. Vyberte položku, kterou chcete stáhnout, a poznamenejte si **verzi**. Můžete podržet klávesu **CTRL** a vybrat více obrázků.
     ![Snímek obrazovky, který zobrazuje další seznam všech registrů Azure Stack dostupných ve vybraném předplatném.](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   Seznam imagí můžete filtrovat také pomocí možnosti **Přidat kritéria** .
   ![Vybrat registrace Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Po provedení výběru vyberte OK.

6. ID položek Marketplace, které jste vybrali ke stažení, se uloží do  `$products` proměnné. Pomocí následujícího příkazu začněte stahovat vybrané položky. Nahraďte cestu k cílové složce umístěním pro uložení souborů, které stáhnete z Azure Marketplace:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. Doba, kterou stahování trvá, závisí na velikosti položky. Po dokončení stahování bude položka k dispozici ve složce, kterou jste zadali ve skriptu. Ke stažení patří soubor VHD (pro virtuální počítače) nebo soubor. zip (pro rozšíření virtuálních počítačů a poskytovatele prostředků). Může také zahrnovat balíček Galerie ve formátu *. azpkg* , což je soubor. zip.

8. Pokud se stahování nepovede, můžete to zkusit znovu spuštěním následující rutiny PowerShellu:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. Měli byste také exportovat modul **AZS. Syndication. admin** místně, abyste ho mohli zkopírovat do počítače, ze kterého importujete položky Marketplace do centra Azure Stack.

   > [!NOTE]
   > Cílová složka pro export tohoto modulu by se měla lišit od umístění, do kterého jste exportovali položky Marketplace.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm2)

1. V počítači s připojením k Internetu otevřete konzolu PowerShellu jako správce.

2. Přihlaste se ke příslušnému klientovi Azure Cloud a AzureAD Directory pomocí účtu Azure, který jste použili k registraci centra Azure Stack. Pokud chcete účet přidat, v PowerShellovém běhu `Add-AzureRMAccount` :

   ```powershell  
   Login-AzureRMAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```

   Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování v závislosti na konfiguraci vašeho účtu.

   > [!NOTE]
   > Pokud vaše relace vyprší, vaše heslo se změnilo nebo chcete přepnout účty, spusťte následující rutinu ještě před přihlášením pomocí `Add-AzureRMAccount` : `Remove-AzureRMAccount -Scope Process` .

3. Pokud máte více předplatných, spusťte následující příkaz a vyberte ten, který jste použili pro registraci:

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRMSubscription
   ```

4. Pokud jste to ještě neudělali v kroku požadavky, Stáhněte si nejnovější verzi nástroje syndikace webu Marketplace:

   ```powershell
   Install-Module -Name Azs.Syndication.Admin -RequiredVersion 0.1.140
   ```

5. Pokud chcete vybrat položky Marketplace, například image virtuálních počítačů, rozšíření nebo šablony řešení ke stažení, spusťte následující příkaz:

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   Tím se zobrazí tabulka se seznamem všech registrací Azure Stack dostupných ve vybraném předplatném. Zvolte registraci, která odpovídá Azure Stack prostředí, pro které stahujete položky Marketplace, a vyberte **OK**.

     ![Snímek obrazovky, který zobrazuje seznam všech registrů Azure Stack dostupných ve vybraném předplatném.](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Nyní by se měla zobrazit druhá tabulka se seznamem všech položek Marketplace, které jsou k dispozici ke stažení. Vyberte položku, kterou chcete stáhnout, a poznamenejte si **verzi**. Můžete podržet klávesu **CTRL** a vybrat více obrázků.
     ![Snímek obrazovky, který zobrazuje další seznam všech registrů Azure Stack dostupných ve vybraném předplatném.](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   Seznam imagí můžete filtrovat také pomocí možnosti **Přidat kritéria** .
   ![Vybrat registrace Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Po provedení výběru vyberte OK.

6. ID položek Marketplace, které jste vybrali ke stažení, se uloží do  `$products` proměnné. Pomocí následujícího příkazu začněte stahovat vybrané položky. Nahraďte cestu k cílové složce umístěním pro uložení souborů, které stáhnete z Azure Marketplace:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. Doba, kterou stahování trvá, závisí na velikosti položky. Po dokončení stahování bude položka k dispozici ve složce, kterou jste zadali ve skriptu. Ke stažení patří soubor VHD (pro virtuální počítače) nebo soubor. zip (pro rozšíření virtuálních počítačů a poskytovatele prostředků). Může také zahrnovat balíček Galerie ve formátu *. azpkg* , což je soubor. zip.

8. Pokud se stahování nepovede, můžete to zkusit znovu spuštěním následující rutiny PowerShellu:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. Měli byste také exportovat modul **AZS. Syndication. admin** místně, abyste ho mohli zkopírovat do počítače, ze kterého importujete položky Marketplace do centra Azure Stack.

   > [!NOTE]
   > Cílová složka pro export tohoto modulu by se měla lišit od umístění, do kterého jste exportovali položky Marketplace.

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

---



## <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Import stažení a publikování do webu Azure Stack hub Marketplace pomocí prostředí PowerShell

1. Soubory, které jste [již dříve stáhli](#use-the-marketplace-syndication-tool-to-download-marketplace-items) , je nutné přesunout do počítače, který má připojení k prostředí centra Azure Stack. Nástroj pro syndikaci na webu Marketplace musí být také dostupný pro prostředí Azure Stack hub, protože k provedení operace importu je nutné použít nástroj.

   Následující obrázek ukazuje příklad struktury složek. **D:\downloadfolder** obsahuje všechny stažené položky Marketplace. Každá podsložka je položkou Marketplace (například **Microsoft. Custom-Script-Linux-ARM-2.0.3**) s názvem ID produktu. V každé podsložce je stažený obsah položky webu Marketplace.

   ![Adresářová struktura pro stažení Marketplace](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Postupujte podle pokynů v [tomto článku](azure-stack-powershell-configure-admin.md) a nakonfigurujte relaci PowerShellu operátora centra Azure Stack.

3. Přihlaste se do centra Azure Stack s identitou, která má přístup vlastníka k předplatnému výchozího poskytovatele.

4. Importujte modul syndikace a potom spusťte nástroj syndikace webu Marketplace spuštěním následujícího skriptu:

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. Po úspěšném dokončení skriptu by měly být položky Marketplace dostupné v tržišti Azure Stack hub.
::: zone-end
