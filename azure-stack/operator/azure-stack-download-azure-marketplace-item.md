---
title: Stažení položek z webu Marketplace z Azure a publikování do Azure Stack | Microsoft Docs
description: Naučte se stahovat položky Marketplace z Azure a publikovat na Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 91314fcd33d3b4171dc7e9a3e2d78cdf07e2f50e
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283542"
---
# <a name="download-existing-marketplace-items-from-azure-and-publish-to-azure-stack"></a>Stažení stávajících položek Marketplace z Azure a publikování do Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Jako operátor cloudu můžete stahovat položky z Azure Marketplace a zpřístupnit je v Azure Stack. Položky, které můžete vybrat, jsou z uspořádaného seznamu Azure Marketplacech položek, které jsou předem testovány a podporovány pro práci s Azure Stack. Do tohoto seznamu se často přidají další položky, takže se budete moct vrátit k novému obsahu.

Existují dva scénáře, jak se připojit k Azure Marketplace:

- **Připojený scénář** – vyžaduje, aby vaše Azure Stack prostředí bylo připojené k Internetu. K vyhledání a stažení položek použijete portál Azure Stack.
- **Odpojený nebo částečně připojený scénář** – vyžaduje přístup k Internetu pomocí syndikačního nástroje na webu Marketplace ke stažení položek Marketplace. Pak přenesete soubory ke stažení na odpojenou Azure Stack instalaci. Tento scénář používá PowerShell.

Úplný seznam položek na webu Marketplace, které si můžete stáhnout, najdete v tématu [Azure Marketplace položky Azure Stack](azure-stack-marketplace-azure-items.md) . Seznam nedávných přidání, odstranění a aktualizací pro Azure Stack Marketplace najdete v článku o [Azure Stack na webu Marketplace](azure-stack-marketplace-changes.md) .

## <a name="connected-scenario"></a>Scénář připojení

Pokud se Azure Stack připojí k Internetu, můžete k stažení položek z webu Marketplace použít portál pro správu.

### <a name="prerequisites"></a>Požadavky

Vaše nasazení Azure Stack musí mít připojení k Internetu a musí být [zaregistrované v Azure](azure-stack-registration.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí portálu
  
1. Přihlaste se k portálu správce Azure Stack.

2. Před stažením položek z webu Marketplace zkontrolujte dostupný prostor úložiště. Později můžete po výběru položek ke stažení porovnat velikost stahovaných zařízení s dostupnou kapacitou úložiště. Pokud je kapacita omezená, zvažte možnosti [správy dostupného místa](azure-stack-manage-storage-shares.md#manage-available-space).

    Chcete-li zkontrolovat dostupné místo: v **oblasti Správa oblastí**vyberte oblast, kterou chcete prozkoumat, a pak přejít k **poskytovatelům prostředků** > **úložiště**:

    ![Kontrola místa v úložišti Azure Stack portálu pro správu](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Otevřete web Azure Stack Marketplace a připojte se k Azure. Provedete to tak, že vyberete službu **pro správu Marketplace** , vyberete **položky Marketplace**a pak vyberete **Přidat z Azure**:

    ![Přidání položek z Marketplace z Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. Portál zobrazí seznam položek, které jsou k dispozici ke stažení z Azure Marketplace. Produkty můžete filtrovat podle názvu, vydavatele nebo typu produktu. Můžete také kliknout na každou položku a zobrazit její popis a další informace, včetně její velikosti ke stažení:

    ![Seznam Azure Marketplacech položek ](media/azure-stack-download-azure-marketplace-item/image03.PNG)

5. Vyberte položku, kterou chcete, a pak vyberte **Stáhnout**. Doba stahování se liší.

    ![Stahování položky Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image04.png)

    Po dokončení stahování můžete novou položku Marketplace nasadit buď jako operátor Azure Stack, nebo na uživatele.

6. Pokud chcete staženou položku nasadit, vyberte **+ vytvořit prostředek**a potom vyhledejte kategorie nové položky Marketplace. Dále vyberte položku, která zahájí proces nasazení. Tento proces se liší pro různé položky Marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Odpojený nebo částečně připojený scénář

Pokud je Azure Stack v odpojeném režimu, můžete k stažení položek Marketplace na počítač s připojením k Internetu použít PowerShell a *Nástroj pro syndikaci na webu Marketplace* . Pak přenesete položky do prostředí Azure Stack. V odpojeném prostředí nemůžete stáhnout položky Marketplace pomocí portálu Azure Stack.

Nástroj syndikace na webu Marketplace se dá použít taky v připojeném scénáři.

Tento scénář obsahuje dvě části:

- **Část 1:** Stažení z Azure Marketplace. Na počítači s přístupem k Internetu konfigurujete PowerShell, stáhnete nástroj syndikace a pak stáhnete položky z Azure Marketplace.  
- **Část 2:** Nahrajte a publikujte na webu Azure Stack Marketplace. Soubory, které jste stáhli do prostředí Azure Stack, přesouváte, naimportujete do Azure Stack a pak je publikujete na Azure Stack Marketplace.  

### <a name="prerequisites"></a>Požadavky

- Připojené prostředí (nemusí být Azure Stack). Potřebujete připojení, abyste získali seznam produktů z Azure s jejich podrobnostmi a stáhli všechno místně. Až to uděláte, zbývající část postupu nevyžaduje žádné připojení k Internetu. Vytvoří katalog položek, které jste předtím stáhli, abyste je mohli použít v odpojeném prostředí.

- Klíč USB nebo externí disk pro připojení k odpojenému prostředí a přenos všech potřebných artefaktů.

- Odpojené Azure Stack prostředí s následujícími požadavky:
  - Vaše nasazení Azure Stack musí být [zaregistrované v Azure](azure-stack-registration.md).
  - Počítač, který má připojení k Internetu, musí mít **Azure Stack modul PowerShell verze 1.2.11** nebo novější. Pokud ještě neexistují, [nainstalujte moduly prostředí PowerShell specifické pro Azure Stack](azure-stack-powershell-install.md).
  - Pokud chcete povolit import stažené položky Marketplace, musí se nakonfigurovat [prostředí PowerShell pro operátor Azure Stack](azure-stack-powershell-configure-admin.md) .
  - Naklonujte úložiště GitHub [nástroje Azure Stack Tools](https://github.com/Azure/AzureStack-Tools) .

- V Azure Stack musíte mít [účet úložiště](azure-stack-manage-storage-accounts.md) , který má veřejně přístupný kontejner (což je objekt BLOB úložiště). Kontejner se používá jako dočasné úložiště pro soubory galerie položek Marketplace. Pokud nejste obeznámeni s účty úložiště a kontejnery, přečtěte si téma [práce s objekty blob – Azure Portal](/azure/storage/blobs/storage-quickstart-blobs-portal) v dokumentaci k Azure.

- Nástroj pro syndikaci na webu Marketplace se stáhne během prvního postupu.

- [AzCopy](/azure/storage/common/storage-use-azcopy) je možné nainstalovat pro optimální výkon stahování, ale není to nutné.

Po registraci můžete ignorovat následující zprávu, která se zobrazí v okně správy Marketplace, protože to není relevantní pro případ odpojeného použití:

[![Neregistrovaná]zpráva zpráva(media/azure-stack-download-azure-marketplace-item/toolsmsgsm.png "neregistrována")](media/azure-stack-download-azure-marketplace-item/toolsmsg.png#lightbox)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí nástroje pro syndikaci na webu Marketplace

> [!IMPORTANT]
> Nezapomeňte si stáhnout nástroj syndikace webu Marketplace při každém stažení položek Marketplace v odpojeném scénáři. V tomto skriptu jsou k disopakující se změny a nejaktuálnější verze by se měly používat pro každé stažení.

1. V počítači s připojením k Internetu otevřete konzolu PowerShellu jako správce.

2. Přidejte účet Azure, který jste použili k registraci Azure Stack. Pokud chcete účet přidat, v PowerShellu spusťte `Add-AzureRmAccount` bez parametrů. Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování v závislosti na konfiguraci vašeho účtu.

   [!include[Remove Account](../../includes/remove-account.md)]

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

5. Importujte modul syndikace a potom spusťte nástroj spuštěním následujících příkazů. Nahraďte `Destination folder path` umístěním pro uložení souborů, které stáhnete z Azure Marketplace.

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
   ```

   Všimněte si, že `Export-AzSOfflineMarketplaceItem` má další příznak `-cloud`, který určuje cloudové prostředí. Ve výchozím nastavení je to **azurecloud**.

6. Po spuštění nástroje by se měla zobrazit obrazovka podobná následujícímu obrázku se seznamem dostupných položek Azure Marketplace:

   [![Položky Azure Marketplace automaticky otevírané okno](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace položky")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Pokud jste nenainstalovali nástroje Azure Storage, zobrazí se následující zpráva. Aby bylo možné nainstalovat tyto nástroje, je třeba stáhnout [AzCopy](/azure/storage/common/storage-use-azcopy#download-and-install-azcopy-on-windows):

   ![Nástroje úložiště](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

8. Vyberte položku, kterou chcete stáhnout, a poznamenejte si **verzi**. Můžete podržet klávesu **CTRL** a vybrat více obrázků. Při importu položky v dalším postupu odkazujete na *verzi* .

   Seznam imagí můžete filtrovat také pomocí možnosti **Přidat kritéria** .

9. Vyberte **OK**a pak si přečtěte a přijměte právní podmínky.

10. Doba, kterou stahování trvá, závisí na velikosti položky. Po dokončení stahování bude položka k dispozici ve složce, kterou jste zadali ve skriptu. Ke stažení patří soubor VHD (pro virtuální počítače) nebo soubor. zip (pro rozšíření virtuálních počítačů). Může taky zahrnovat balíček Galerie ve formátu *. azpkg* , který je jednoduše souborem. zip.

11. Pokud se stahování nepovede, můžete to zkusit znovu spuštěním následující rutiny PowerShellu:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Před opakováním pokusu odeberte složku produktu, ve které se stahování nepovedlo. Například pokud se skript pro stažení nezdařil při stahování do `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, odeberte složku `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` a pak znovu spusťte rutinu.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-using-powershell"></a>Import stažení a publikování na Azure Stack Marketplace pomocí prostředí PowerShell

1. Soubory, které jste [předtím stáhli](#use-the-marketplace-syndication-tool-to-download-marketplace-items) , je nutné přesunout do místního počítače, aby byly k dispozici pro vaše Azure Stack prostředí. Nástroj pro syndikaci na webu Marketplace musí být také dostupný pro vaše Azure Stack prostředí, protože k provedení operace importu je nutné použít nástroj.

   Následující obrázek ukazuje příklad struktury složek. `D:\downloadfolder` obsahuje všechny stažené položky Marketplace. Každá podsložka je položkou Marketplace (například `microsoft.custom-script-linux-arm-2.0.3`) s názvem ID produktu. V každé podsložce je stažený obsah položky webu Marketplace.

   [Adresář pro ![Stažení služby Marketplace]adresář pro stažení(media/azure-stack-download-azure-marketplace-item/mp1sm.png "Marketplace struktura adresářů")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Podle pokynů v [tomto článku](azure-stack-powershell-configure-admin.md) nakonfigurujte relaci powershellu Azure Stack operator.

3. Importujte modul syndikace a potom spusťte nástroj syndikace webu Marketplace spuštěním následujícího skriptu:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Parametr `-origin` Určuje složku na nejvyšší úrovni, která obsahuje všechny stažené produkty. například `"D:\downloadfolder"`.

   Parametr `-AzsCredential` je nepovinný. Používá se k obnovení přístupového tokenu, pokud vypršela jeho platnost. Pokud není zadán parametr `-AzsCredential` a vyprší platnost tokenu, zobrazí se výzva k zadání přihlašovacích údajů operátora.

    > [!NOTE]  
    > AD FS podporuje pouze interaktivní ověřování s identitami uživatele. Pokud je vyžadován objekt přihlašovacích údajů, je nutné použít instanční objekt (SPN). Další informace o nastavení instančního objektu pomocí Azure Stack a AD FS jako služby správy identit najdete v tématu [Správa služby AD FS instančního objektu](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. Po úspěšném dokončení skriptu by měla být položka k dispozici v Azure Stack Marketplace.

## <a name="next-steps"></a>Další kroky

- [Přidat vlastní image virtuálního počítače](azure-stack-add-vm-image.md)
- [Vytvoření a publikování vlastní položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
