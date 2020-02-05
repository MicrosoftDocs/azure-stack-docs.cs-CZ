---
title: Stažení položek z webu Marketplace z Azure a publikování do centra Azure Stack
description: Přečtěte si, jak stáhnout položky z webu Marketplace z Azure a publikovat do centra Azure Stack.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: 5fee671c0d31f78d92e84733cc1ebf1f7626a50f
ms.sourcegitcommit: b5541815abfab3f8750fa419fdd1f93a8844731a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "77012908"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Stažení položek z Marketplace do centra Azure Stack 

Jako operátor cloudu můžete z Marketplace stahovat položky do centra Azure Stack a zpřístupnit je všem uživatelům pomocí prostředí Azure Stack hub. Položky, které můžete vybrat, jsou z uspořádaného seznamu Azure Marketplacech položek, které jsou předem testovány a podporovány pro práci s centrem Azure Stack. Do tohoto seznamu se často přidají další položky, takže se budete moct vrátit k novému obsahu.

Existují dva scénáře stažení produktů z webu Marketplace:

- **Připojený scénář**: vyžaduje, aby bylo prostředí centra Azure Stack připojené k Internetu. K vyhledání a stažení položek slouží portál pro správu centra Azure Stack.
- **Odpojený nebo částečně připojený scénář**: vyžaduje, abyste měli přístup k Internetu pomocí syndikačního nástroje Marketplace ke stažení položek Marketplace. Pak přenesete soubory ke stažení na odpojenou instalaci centra Azure Stack. Tento scénář používá PowerShell.

Úplný seznam položek z webu Marketplace, které si můžete stáhnout, najdete v tématu [Azure Marketplace položky pro centrum Azure Stack](azure-stack-marketplace-azure-items.md) . Seznam nedávných přidání, odstranění a aktualizací služby Azure Stack hub Marketplace najdete v článku o [změnách na webu Marketplace centra Azure Stack](azure-stack-marketplace-changes.md) .

> [!NOTE]
> Katalog se bude lišit v závislosti na cloudu, ke kterému je připojen systém Azure Stack hub. Cloudové prostředí se určuje pomocí předplatného Azure, které používáte k registraci centra Azure Stack.

## <a name="connected-scenario"></a>Připojený scénář

Pokud se centrum Azure Stack připojuje k Internetu, můžete k stažení položek z Marketplace použít portál pro správu.

### <a name="prerequisites"></a>Požadavky

Vaše nasazení centra Azure Stack musí mít připojení k Internetu a musí být zaregistrované v Azure.

### <a name="use-the-portal-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí portálu

1. Přihlaste se k portálu pro správu centra Azure Stack.

2. Před stažením položek z webu Marketplace zkontrolujte dostupný prostor úložiště. Později můžete po výběru položek ke stažení porovnat velikost stahovaných zařízení s dostupnou kapacitou úložiště. Pokud je kapacita omezená, zvažte možnosti [správy dostupného místa](azure-stack-manage-storage-shares.md#manage-available-space).

   Chcete-li zkontrolovat dostupné místo: v **oblasti Správa oblastí**vyberte oblast, kterou chcete prozkoumat, a poté vyhledejte **poskytovatele prostředků** > **úložiště**:

   ![Kontrola prostoru úložiště na portálu pro správu centra Azure Stack](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Otevřete web Centrum pro Azure Stack a připojte se k Azure. Provedete to tak, že vyberete službu **tržiště Management** , vyberete **položky Marketplace**a pak vyberete **Přidat z Azure**:

   ![Přidání položek z Marketplace z Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. V každé položce řádku se zobrazí také aktuálně dostupná verze. Pokud je k dispozici více než jedna verze položky Marketplace, zobrazí se ve sloupci **verze** **více**. Kliknutím na každou položku můžete zobrazit její popis a další informace, včetně její velikosti ke stažení:

   ![Přidat z Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. Pokud je verze položky zobrazená jako **více**, můžete tuto položku vybrat a pak vybrat konkrétní verzi z rozevíracího seznamu výsledná verze – výběr:

   ![Přidat z Azure](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. Vyberte položku, kterou chcete, a pak vyberte **Stáhnout**. Doba stahování se liší a závisí na připojení k síti. Po dokončení stahování můžete novou položku Marketplace nasadit jako operátor centra Azure Stack nebo uživatele.

7. Pokud chcete staženou položku nasadit, vyberte **+ vytvořit prostředek**a potom vyhledejte kategorie nové položky Marketplace. Pak vyberte položku, která zahájí proces nasazení. Tento proces se liší pro různé položky Marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Odpojený nebo částečně připojený scénář

Pokud centrum Azure Stack má omezené nebo žádné připojení k Internetu, můžete k stažení položek Marketplace na počítač s připojením k Internetu použít PowerShell a *Nástroj syndikace webu marketplace* . Pak přenesete položky do prostředí Azure Stack hub. V odpojeném prostředí nemůžete stáhnout položky Marketplace pomocí portálu Azure Stack hub.

Nástroj syndikace na webu Marketplace se dá použít taky v připojeném scénáři.

Tento scénář obsahuje dvě části:

- **Část 1**: stažení z položek Marketplace. V počítači s přístupem k Internetu konfigurujete PowerShell, stáhnete nástroj syndikace a pak stáhnete položky z Azure Marketplace.
- **Část 2**: nahrání a publikování na tržišti Azure Stack hub. Soubory, které jste stáhli do prostředí centra Azure Stack, přesunete a pak je publikujete na webu služby Azure Stack hub Marketplace.

### <a name="prerequisites"></a>Požadavky

- Připojené prostředí (nemusí být Azure Stack hub). Potřebujete připojení, abyste získali seznam produktů z Azure s jejich podrobnostmi a stáhli všechno místně. Až to uděláte, zbývající část postupu nevyžaduje připojení k Internetu. Vytvoří katalog položek, které jste předtím stáhli, abyste je mohli použít v odpojeném prostředí.

- Vyměnitelná média pro připojení k vašemu odpojenému prostředí a přenos všech potřebných artefaktů.

- Prostředí odpojeného centra Azure Stack s následujícími požadavky:

  - Vaše nasazení centra Azure Stack musí být zaregistrované v Azure.

  - Počítač, který má připojení k Internetu, musí mít **Azure Stack modulu PowerShell verze 1.2.11** nebo novější. Pokud ještě neexistuje, [nainstalujte Azure Stack moduly PowerShellu specifické pro centrum](azure-stack-powershell-install.md).

  - Aby bylo možné povolit import stažené položky Marketplace, je nutné nakonfigurovat [prostředí PowerShell pro operátor centra Azure Stack](azure-stack-powershell-configure-admin.md) .

- Stáhněte modul AZS. Syndication. admin z Galerie prostředí PowerShell pomocí příkazu níže.
  ```
  Install-Module -Name Azs.Syndication.Admin
  ```

Po registraci Azure Stack můžete ignorovat následující zprávu, která se zobrazí v okně správy Marketplace, protože není relevantní pro případ odpojeného použití:

![Správa Marketplace](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí nástroje pro syndikaci na webu Marketplace

> [!IMPORTANT]
> Nezapomeňte si stáhnout nástroj syndikace webu Marketplace při každém stažení položek Marketplace v odpojeném scénáři. V tomto nástroji jsou k disopakující se změny a nejaktuálnější verze by se měla použít pro každé stažení.

1. V počítači s připojením k Internetu otevřete konzolu PowerShellu jako správce.

2. Přihlaste se ke příslušnému klientovi Azure Cloud a AzureAD Directory pomocí účtu Azure, který jste použili k registraci centra Azure Stack. Pokud chcete účet přidat, v PowerShellu spusťte **Add-AzureRmAccount**. 

   ```powershell  
   Login-AzureRmAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```
   Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování v závislosti na konfiguraci vašeho účtu.

   > [!NOTE]
   > Pokud vaše relace vyprší, vaše heslo se změnilo nebo chcete jednoduše přepnout účty, spusťte následující rutinu ještě před přihlášením pomocí rutiny **Add-AzureRmAccount**: **Remove-AzureRmAccount-Scope Process**.

3. Pokud máte více předplatných, spusťte následující příkaz a vyberte ten, který jste použili pro registraci:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   ```

4. Pokud jste to ještě neudělali v kroku požadavky, Stáhněte si nejnovější verzi nástroje syndikace webu Marketplace:

   ```powershell
   Install-Module -Name Azs.Syndication.Admin
   ```

5. Pokud chcete vybrat položky Marketplace, jako jsou image virtuálních počítačů, rozšíření nebo šablony řešení, které se mají stáhnout, spusťte následující příkaz. 

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   Tím se zobrazí tabulka se seznamem všech registrací Azure Stack dostupných ve vybraném předplatném. Zvolte registraci, která odpovídá Azure Stack prostředí, pro které stahujete položky Marketplace, a vyberte **OK**.

     ![Vybrat registrace Azure Stack](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   Nyní by se měla zobrazit druhá tabulka se seznamem všech položek Marketplace, které jsou k dispozici ke stažení. Vyberte položku, kterou chcete stáhnout, a poznamenejte si **verzi**. Pokud chcete vybrat více imagí, můžete podržet klávesu **Ctrl** .
     ![vybrat registrace Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   Seznam imagí můžete filtrovat také pomocí možnosti **Přidat kritéria** .
   ![vybrat registrace Azure Stack](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   Po provedení výběru vyberte OK.

6. ID položek Marketplace, které jste vybrali ke stažení, se uloží do proměnné `$products`. Pomocí následujícího příkazu začněte stahovat vybrané položky. Nahraďte cestu k cílové složce umístěním pro uložení souborů, které stáhnete z Azure Marketplace:

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. Doba, kterou stahování trvá, závisí na velikosti položky. Po dokončení stahování bude položka k dispozici ve složce, kterou jste zadali ve skriptu. Ke stažení patří soubor VHD (pro virtuální počítače) nebo soubor. zip (pro rozšíření virtuálních počítačů a poskytovatele prostředků). Může také zahrnovat balíček Galerie ve formátu *. azpkg* , což je soubor. zip.

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

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Import stažení a publikování do webu Azure Stack hub Marketplace pomocí prostředí PowerShell

1. Soubory, které jste [už dříve stáhli](#use-the-marketplace-syndication-tool-to-download-marketplace-items) , je nutné přesunout do počítače, který má připojení k vašemu prostředí služby Azure Stack hub. Nástroj pro syndikaci na webu Marketplace musí být také dostupný pro prostředí Azure Stack hub, protože k provedení operace importu je nutné použít nástroj.

   Následující obrázek ukazuje příklad struktury složek.  **D:\downloadfolder** obsahuje všechny stažené položky Marketplace. Každá podsložka je položkou Marketplace (například **Microsoft. Custom-Script-Linux-ARM-2.0.3**) s názvem ID produktu. V každé podsložce je stažený obsah položky webu Marketplace.

   ![Adresářová struktura pro stažení Marketplace](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Postupujte podle pokynů v [tomto článku](azure-stack-powershell-configure-admin.md) a nakonfigurujte relaci PowerShellu operátora centra Azure Stack.

3. Přihlaste se k centru Azure Stack pomocí identity, která má přístup vlastníka k předplatnému default Provider.

4. Importujte modul syndikace a potom spusťte nástroj syndikace webu Marketplace spuštěním následujícího skriptu:

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. Po úspěšném dokončení skriptu by měly být položky Marketplace dostupné v tržišti Azure Stack hub.
