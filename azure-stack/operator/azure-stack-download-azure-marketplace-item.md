---
title: Stažení položek z webu Marketplace z Azure a publikování do centra Azure Stack | Microsoft Docs
description: Přečtěte si, jak stáhnout položky z webu Marketplace z Azure a publikovat do centra Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: cff400b3faace08dfcdae86f0fe0d10effb42cae
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817410"
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
- **Část 2**: nahrání a publikování na tržišti Azure Stack hub. Soubory, které jste stáhli do prostředí Azure Stack hub, přesouváte, naimportujete do centra Azure Stack a pak je publikujete do služby Azure Stack hub Marketplace.

### <a name="prerequisites"></a>Požadavky

- Připojené prostředí (nemusí být Azure Stack hub). Potřebujete připojení, abyste získali seznam produktů z Azure s jejich podrobnostmi a stáhli všechno místně. Až to uděláte, zbývající část postupu nevyžaduje připojení k Internetu. Vytvoří katalog položek, které jste předtím stáhli, abyste je mohli použít v odpojeném prostředí.

- Vyměnitelná média pro připojení k vašemu odpojenému prostředí a přenos všech potřebných artefaktů.

- Prostředí odpojeného centra Azure Stack s následujícími požadavky:

  - Vaše nasazení centra Azure Stack musí být zaregistrované v Azure.

  - Počítač, který má připojení k Internetu, musí mít **Azure Stack modulu PowerShell verze 1.2.11** nebo novější. Pokud ještě neexistuje, [nainstalujte Azure Stack moduly PowerShellu specifické pro centrum](azure-stack-powershell-install.md).

  - Aby bylo možné povolit import stažené položky Marketplace, je nutné nakonfigurovat [prostředí PowerShell pro operátor centra Azure Stack](azure-stack-powershell-configure-admin.md) .

  - Naklonujte [nástroje Azure Stack Hub](https://github.com/Azure/AzureStack-Tools) úložiště GitHub.

- Musíte mít [účet úložiště](azure-stack-manage-storage-accounts.md) v centru Azure Stack s veřejně přístupným kontejnerem (což je objekt BLOB úložiště). Kontejner se používá jako dočasné úložiště pro soubory galerie položek Marketplace. Pokud nejste obeznámeni s účty úložiště a kontejnery, přečtěte si téma [práce s objekty blob – Azure Portal](/azure/storage/blobs/storage-quickstart-blobs-portal) v dokumentaci k Azure.

- Nástroj pro syndikaci na webu Marketplace se stáhne během prvního postupu.

-  [AzCopy](/azure/storage/common/storage-use-azcopy) můžete nainstalovat pro optimální výkon stahování, ale není to nutné.

Po registraci můžete ignorovat následující zprávu, která se zobrazí v okně správy Marketplace, protože to není relevantní pro případ odpojeného použití:

![Správa Marketplace](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí nástroje pro syndikaci na webu Marketplace

> [!IMPORTANT]
> Nezapomeňte si stáhnout nástroj syndikace webu Marketplace při každém stažení položek Marketplace v odpojeném scénáři. V tomto skriptu jsou k disopakující se změny a nejaktuálnější verze by se měly používat pro každé stažení.

1. V počítači s připojením k Internetu otevřete konzolu PowerShellu jako správce.

2. Přidejte účet Azure, který jste použili k registraci centra Azure Stack. Pokud chcete účet přidat, v PowerShellu spusťte rutinu **Add-AzureRmAccount** bez parametrů. Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování v závislosti na konfiguraci vašeho účtu.

   > [!NOTE]
   > Pokud vaše relace vyprší, vaše heslo se změnilo nebo chcete jednoduše přepnout účty, spusťte následující rutinu ještě před přihlášením pomocí rutiny **Add-AzureRmAccount**: **Remove-AzureRmAccount-Scope Process**.

3. Pokud máte více předplatných, spusťte následující příkaz a vyberte ten, který jste použili pro registraci:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Stáhněte si nejnovější verzi nástroje syndikace webu Marketplace pomocí následujícího skriptu:

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master
   ```

5. Importujte modul syndikace a potom spusťte nástroj spuštěním následujícího příkazu:

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1
   ```

6. Pokud chcete exportovat položky Marketplace, jako jsou image virtuálních počítačů, rozšíření nebo šablony řešení, spusťte následující příkaz. Nahraďte cestu k cílové složce umístěním pro uložení souborů, které stáhnete z Azure Marketplace:

   ```powershell
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" -azCopyDownloadThreads "[optional - AzCopy threads number]" -azureContext $AzureContext
   ```

   Pokud chcete exportovat poskytovatele prostředků nebo služby PaaS, spusťte následující příkaz:

   ```powershell
   Export-AzSOfflineResourceProvider -destination "Destination folder path" -azCopyDownloadThreads "AzCopy threads number" -azureContext $AzureContext
   ```

   Parametr `-azCopyDownloadThreads` je nepovinný. Měla by se používat jenom v případě, že máte síť s malou šířkou pásma a používáte ke stažení prémii. Tato možnost určuje počet souběžných operací v AzCopy. Pokud používáte síť s nízkou šířkou pásma, můžete zadat nižší číslo, aby nedocházelo k selhání způsobenému konkurencí prostředků. Další podrobnosti najdete v [tomto článku Azure](/previous-versions/azure/storage/storage-use-azcopy#specify-the-number-of-concurrent-operations-to-start).

   Parametr `-azureContext` je také volitelný. Pokud nezadáte kontext Azure, rutina použije výchozí kontext Azure.

7. Po spuštění nástroje by se měla zobrazit obrazovka podobná následujícímu obrázku se seznamem dostupných položek Azure Marketplace:

   ![Položky Marketplace](media/azure-stack-download-azure-marketplace-item/tool1.png)

8. Pokud je k dispozici více než jedna verze položky Marketplace, zobrazí se ve sloupci **verze**  **více verzí**. Pokud je verze položky zobrazená jako **více verzí**, můžete tuto položku vybrat a pak z okna pro výběr výsledné verze zvolit konkrétní verzi.

9. Vyberte položku, kterou chcete stáhnout, a poznamenejte si **verzi**. Pokud chcete vybrat více imagí, můžete podržet klávesu **Ctrl** . Odkazujete na  *verze* při importu položky v dalším postupu.

    Seznam imagí můžete filtrovat také pomocí možnosti **Přidat kritéria** .

10. Pokud jste nenainstalovali nástroje Azure Storage, zobrazí se následující zpráva. Aby bylo možné nainstalovat tyto nástroje, je třeba stáhnout [AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy):

    ![Nástroje úložiště](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

11. Vyberte **OK**a pak si přečtěte a přijměte právní podmínky.

12. Doba, kterou stahování trvá, závisí na velikosti položky. Po dokončení stahování bude položka k dispozici ve složce, kterou jste zadali ve skriptu. Ke stažení patří soubor VHD (pro virtuální počítače) nebo soubor. zip (pro rozšíření virtuálních počítačů a poskytovatele prostředků). Může taky zahrnovat balíček Galerie ve formátu *. azpkg* , který je jednoduše souborem. zip.

13. Pokud se stahování nepovede, můžete to zkusit znovu spuštěním následující rutiny PowerShellu:

   ```powershell
   # for Marketplace items
   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"

   # for Resource providers
   Export-AzSOfflineResourceProvider -Destination "Destination folder path in quotes"
   ```

   Před opakováním pokusu odeberte složku produktu, ve které se stahování nepovedlo. Například pokud se při stahování do **D:\downloadFolder\microsoft.CustomScriptExtension-ARM-1.9.1**nepovede skript ke stažení, odeberte složku **D:\downloadFolder\microsoft.CustomScriptExtension-ARM-1.9.1** a pak znovu spusťte rutinu.

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>Import stažení a publikování do webu Azure Stack hub Marketplace pomocí prostředí PowerShell

1. Soubory, které jste stáhli [již dříve](#use-the-marketplace-syndication-tool-to-download-marketplace-items) , je nutné přesunout místně, aby byly dostupné pro vaše prostředí Azure Stack hub. Nástroj pro syndikaci na webu Marketplace musí být také dostupný pro prostředí Azure Stack hub, protože k provedení operace importu je nutné použít nástroj.

   Následující obrázek ukazuje příklad struktury složek.  **D:\downloadfolder** obsahuje všechny stažené položky Marketplace. Každá podsložka je položkou Marketplace (například **Microsoft. Custom-Script-Linux-ARM-2.0.3**) s názvem ID produktu. V každé podsložce je stažený obsah položky webu Marketplace.

   ![Adresářová struktura pro stažení Marketplace](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. Postupujte podle pokynů v [tomto článku](azure-stack-powershell-configure-admin.md) a nakonfigurujte relaci PowerShellu operátora centra Azure Stack.

3. Importujte modul syndikace a potom spusťte nástroj syndikace webu Marketplace spuštěním následujícího skriptu:

   ```powershell
   $credential = Get-Credential -Message "Enter the Azure Stack Hub operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Parametr `-origin` Určuje složku na nejvyšší úrovni, která obsahuje všechny stažené produkty. například `"D:\downloadfolder"`.

   Parametr `-AzsCredential` je nepovinný. Používá se k obnovení přístupového tokenu, pokud vypršela jeho platnost. Pokud není zadán parametr `-AzsCredential` a vyprší platnost tokenu, zobrazí se výzva k zadání přihlašovacích údajů operátora.

   > [!NOTE]
   > AD FS podporuje pouze interaktivní ověřování s identitami uživatele. Pokud je vyžadován objekt přihlašovacích údajů, je nutné použít instanční objekt (SPN). Další informace o nastavení instančního objektu pomocí centra Azure Stack a AD FS jako služby správy identit najdete v tématu [Správa služby AD FS instančního objektu](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. Po úspěšném dokončení skriptu by měla být položka k dispozici v tržišti Azure Stack hub.
