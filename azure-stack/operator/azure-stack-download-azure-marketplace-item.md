---
title: Stažení položek z Marketplace z Azure | Microsoft Docs
description: Operátor cloudu může stáhnout položky Marketplace z Azure do nasazení Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 4c4bac9a083ca35f851acb2f2d8201742be0ed1e
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991834"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Stažení položek z webu Marketplace z Azure do Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Jako operátor cloudu můžete stahovat položky z Azure Marketplace a zpřístupnit je v Azure Stack. Položky, které můžete vybrat, jsou z uspořádaného seznamu Azure Marketplacech položek, které jsou předem testovány a podporovány pro práci s Azure Stack. Do tohoto seznamu se často přidají další položky, takže se budete moct vrátit k novému obsahu.

Pro připojení k Azure Marketplace existují dva scénáře:

- **Připojený scénář** – vyžaduje, aby vaše Azure Stack prostředí bylo připojené k Internetu. K vyhledání a stažení položek použijete portál Azure Stack.
- **Odpojený nebo částečně připojený scénář** – vyžaduje přístup k Internetu pomocí syndikačního nástroje na webu Marketplace ke stažení položek Marketplace. Pak přenesete soubory ke stažení na odpojenou Azure Stack instalaci. Tento scénář používá PowerShell.

Úplný seznam položek na webu Marketplace, které si můžete stáhnout, najdete v tématu [Azure Marketplace položky Azure Stack](azure-stack-marketplace-azure-items.md) . Seznam nedávných přidání, odstranění a aktualizací na webu Azure Stack Marketplace najdete v článku o [Azure Stack na webu Marketplace](azure-stack-marketplace-changes.md) .

## <a name="connected-scenario"></a>Připojený scénář

Pokud se Azure Stack připojí k Internetu, můžete k stažení položek z webu Marketplace použít portál pro správu.

### <a name="prerequisites"></a>Požadavky

Vaše nasazení Azure Stack musí mít připojení k Internetu a musí být zaregistrované [v Azure](azure-stack-registration.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí portálu
  
1. Přihlaste se k portálu správce Azure Stack.

2. Před stažením položek z webu Marketplace zkontrolujte dostupný prostor úložiště. Později můžete po výběru položek ke stažení porovnat velikost stahovaných zařízení s dostupnou kapacitou úložiště. Pokud je kapacita omezená, zvažte možnosti [správy dostupného místa](azure-stack-manage-storage-shares.md#manage-available-space).

    Chcete-li zkontrolovat dostupné místo, v části **Správa oblastí** vyberte oblast, kterou chcete prozkoumat, a pak > v části poskytovatelé prostředků**úložiště**:

    ![Kontrola prostoru úložiště](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Otevřete web Azure Stack Marketplace a připojte se k Azure. Provedete to tak, že vyberete službu **pro správu Marketplace** , vyberete **položky Marketplace**a pak vyberete **Přidat z Azure**:

    ![Přidat z Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    Portál zobrazí seznam položek, které jsou k dispozici ke stažení z Azure Marketplace. Produkty můžete filtrovat podle názvu, vydavatele nebo typu produktu. Můžete také kliknout na každou položku a zobrazit její popis a další informace, včetně její velikosti ke stažení:

    ![Seznam Marketplace](media/azure-stack-download-azure-marketplace-item/image03.PNG)

4. Vyberte položku, kterou chcete, a pak vyberte **Stáhnout**. Doba stahování se liší.

    ![Stáhnout zprávu](media/azure-stack-download-azure-marketplace-item/image04.png)

    Po dokončení stahování můžete novou položku Marketplace nasadit buď jako operátor Azure Stack, nebo jako uživatele.

5. Pokud chcete staženou položku nasadit, vyberte **+ vytvořit prostředek**a potom vyhledejte kategorie nové položky Marketplace. Dále vyberte položku, která zahájí proces nasazení. Tento proces se liší pro různé položky Marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Odpojený nebo částečně připojený scénář

Pokud je Azure Stack v odpojeném režimu a bez připojení k Internetu, můžete ke stažení položek Marketplace na počítač s připojením k Internetu použít PowerShell a nástroj pro syndikaci na *Marketplace* . Pak přenesete položky do prostředí Azure Stack. V odpojeném prostředí nemůžete stáhnout položky Marketplace pomocí portálu Azure Stack.

Nástroj syndikace na webu Marketplace se dá použít taky v připojeném scénáři.

Tento scénář obsahuje dvě části:

- **Část 1:** Stažení z Azure Marketplace. Na počítači s přístupem k Internetu konfigurujete PowerShell, stáhnete nástroj syndikace a pak stáhnete položky z Azure Marketplace.  
- **Část 2:** Nahrajte a publikujte na webu Azure Stack Marketplace. Soubory, které jste stáhli do prostředí Azure Stack, přesouváte, naimportujete do Azure Stack a pak je publikujete na Azure Stack Marketplace.  

### <a name="prerequisites"></a>Požadavky

- Vaše nasazení Azure Stack musí být [zaregistrované v Azure](azure-stack-registration.md).

- Počítač, který má připojení k Internetu, musí mít **Azure Stack modul PowerShell verze 1.2.11** nebo novější. Pokud ještě není k dispozici, [nainstalujte Azure Stack specifické moduly prostředí PowerShell](azure-stack-powershell-install.md).  

- Pokud chcete povolit import stažené položky Marketplace, musí se nakonfigurovat [prostředí PowerShell pro operátor Azure Stack](azure-stack-powershell-configure-admin.md) .  

- V Azure Stack musíte mít [účet úložiště](azure-stack-manage-storage-accounts.md) , který má veřejně přístupný kontejner (což je objekt BLOB úložiště). Kontejner se používá jako dočasné úložiště pro soubory galerie položek Marketplace. Pokud nejste obeznámeni s účty úložiště a kontejnery, přečtěte si téma [práce s objekty blob – Azure Portal](/azure/storage/blobs/storage-quickstart-blobs-portal) v dokumentaci k Azure.

- Nástroj pro syndikaci na webu Marketplace se stáhne během prvního postupu.

- [AzCopy](/azure/storage/common/storage-use-azcopy) můžete nainstalovat pro optimální výkon stahování, ale to není nutné.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí nástroje pro syndikaci na webu Marketplace

1. V počítači s připojením k Internetu otevřete konzolu PowerShellu jako správce.

2. Přidejte účet Azure, který jste použili k registraci Azure Stack. Pokud chcete účet přidat, v PowerShellu `Add-AzureRmAccount` spusťte bez parametrů. Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a v závislosti na konfiguraci vašeho účtu možná budete muset použít 2-Factor Authentication.

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
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importujte modul syndikace a potom spusťte nástroj spuštěním následujících příkazů. Nahraďte `Destination folder path` umístěním pro uložení souborů, které stáhnete z Azure Marketplace.

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
   ```

   Všimněte si `Export-AzSOfflineMarketplaceItem` , že má `-cloud` další příznak, který určuje cloudové prostředí. Ve výchozím nastavení se jedná o **azurecloud**.

6. Po spuštění nástroje by se měla zobrazit obrazovka podobná následujícímu obrázku se seznamem dostupných položek Marketplace:

   [![Místní nabídka položek Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace položky")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Vyberte položku, kterou chcete stáhnout, a poznamenejte si **verzi**. Můžete podržet klávesu **CTRL** a vybrat více obrázků. Při importu položky v dalším postupu odkazujete na *verzi* .

   Seznam imagí můžete filtrovat také pomocí možnosti **Přidat kritéria** .

8. Vyberte **OK**a pak si přečtěte a přijměte právní podmínky.

9. Doba, kterou stahování trvá, závisí na velikosti položky. Po dokončení stahování bude položka k dispozici ve složce, kterou jste zadali ve skriptu. Ke stažení patří soubor VHD (pro virtuální počítače) nebo soubor. zip (pro rozšíření virtuálních počítačů). Může taky zahrnovat balíček Galerie ve formátu *. azpkg* , který je jednoduše souborem. zip.

10. Pokud se stahování nepovede, můžete to zkusit znovu spuštěním následující rutiny PowerShellu:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Před opakováním pokusu odeberte složku produktu, ve které se stahování nepovedlo. Například pokud se skript pro stažení nezdařil při stahování do `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` odeberte složku a pak znovu spusťte rutinu.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>Import stažení a publikování na Azure Stack Marketplace (1811 a novější)

1. Soubory, které jste [předtím stáhli](#use-the-marketplace-syndication-tool-to-download-marketplace-items) , je nutné přesunout do místního počítače, aby byly k dispozici pro vaše Azure Stack prostředí. Nástroj pro syndikaci na webu Marketplace musí být také dostupný pro vaše Azure Stack prostředí, protože k provedení operace importu je nutné použít nástroj.

   Následující obrázek ukazuje příklad struktury složek. `D:\downloadfolder`obsahuje všechny stažené položky Marketplace. Každá podsložka je položka Marketplace (například `microsoft.custom-script-linux-arm-2.0.3`), která je pojmenována podle ID produktu. Uvnitř každé podsložky je stažený obsah položky webu Marketplace.

   [![Adresářová struktura pro stažení Marketplace](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Adresářová struktura pro stažení Marketplace")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Podle pokynů v [tomto článku](azure-stack-powershell-configure-admin.md) nakonfigurujte relaci powershellu Azure Stack operator.

3. Importujte modul syndikace a potom spusťte nástroj syndikace webu Marketplace spuštěním následujícího skriptu:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   Parametr určuje složku nejvyšší úrovně, která obsahuje všechny stažené produkty, `"D:\downloadfolder"`například. `-origin`

   `-AzsCredential` Parametr je nepovinný. Používá se k obnovení přístupového tokenu, pokud vypršela jeho platnost. `-AzsCredential` Pokud parametr není zadaný a vyprší platnost tokenu, zobrazí se výzva k zadání přihlašovacích údajů operátora.

    > [!NOTE]  
    > AD FS podporuje pouze interaktivní ověřování s identitami uživatele. Pokud je vyžadován objekt přihlašovacích údajů, je nutné použít instanční objekt (SPN). Další informace o nastavení instančního objektu pomocí Azure Stack a AD FS jako služby správy identit najdete v tématu [Správa služby AD FS instančního objektu](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

4. Po úspěšném dokončení skriptu by měla být položka k dispozici na webu Azure Stack Marketplace.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>Import stažení a publikování na Azure Stack Marketplace (1809 a nižší)

1. Soubory imagí virtuálních počítačů nebo šablon řešení, které jste [předtím stáhli](#use-the-marketplace-syndication-tool-to-download-marketplace-items) , je třeba zpřístupnit místně pro vaše Azure Stack prostředí.  

2. Pomocí portálu pro správu nahrajte balíček položky Marketplace (soubor. azpkg) a image virtuálního pevného disku (soubor. VHD) do Azure Stack BLOB Storage. Nahrání balíčku a souborů na disku zpřístupňuje Azure Stack, takže později můžete položku Publikovat na webu Azure Stack Marketplace.

   Nahrávání vyžaduje, abyste měli účet úložiště s veřejně přístupným kontejnerem (viz předpoklady pro tento scénář).  
   1. Na portálu pro správu Azure Stack klikněte na **všechny služby** a potom v kategorii **data + úložiště** vyberte **účty úložiště**.  

   2. Vyberte účet úložiště z vašeho předplatného a potom v části **BLOB Service**vyberte **kontejnery**.  
      [![BLOB Service](media/azure-stack-download-azure-marketplace-item/blob-service.png "BLOB Service")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  

   3. Vyberte kontejner, který chcete použít, a potom výběrem **Odeslat** otevřete podokno **nahrát objekt BLOB** .  
      [![Kontejner](media/azure-stack-download-azure-marketplace-item/container.png "Kontejner")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  

   4. V podokně nahrát objekt BLOB vyhledejte soubory balíčku a disku, které chcete načíst do úložiště, a pak vyberte **nahrát**: [![Nahrát](media/azure-stack-download-azure-marketplace-item/uploadsm.png "Nahrát")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. Soubory, které nahrajete, se zobrazí v podokně kontejneru. Vyberte soubor a pak zkopírujte adresu URL z podokna **vlastnosti objektu BLOB** . Tuto adresu URL použijete v dalším kroku, když naimportujete položku Marketplace do Azure Stack.  Na následujícím obrázku je kontejnerem **objekt BLOB-test-Storage** a soubor je **Microsoft. WindowsServer2016DatacenterServerCore-ARM. 1.0.801. azpkg**. Adresa URL souboru je **https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg** .  
      [![Vlastnosti objektu BLOB](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "Vlastnosti objektu BLOB")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Importujte bitovou kopii VHD do Azure Stack pomocí rutiny **Add-AzsPlatformimage** . Pokud použijete tuto rutinu, `publisher`nahraďte `offer`hodnoty, a dalšími hodnotami parametru hodnotou importovaného obrázku.

   Hodnoty `publisher`, `offer`a obrázkumůžetezískatztextovéhosouboru,kterýsestahujesouborem.azpkg.`sku` Textový soubor je uložený v cílovém umístění. `version` Hodnota je verze zjištěná při stahování položky z Azure v předchozím postupu.

   V následujícím ukázkovém skriptu se používají hodnoty pro virtuální počítač jádro Windows serveru 2016 Datacenter-Server. Hodnota pro `-Osuri` je ukázková cesta k umístění úložiště objektů BLOB pro položku.

   Jako alternativu k tomuto skriptu můžete použít [postup popsaný v tomto článku](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) k importu image VHD pomocí Azure Portal.

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **O šablonách řešení:** Některé šablony můžou zahrnovat malý soubor VHD o velikosti 3 MB s názvem **fixed3. VHD**. Tento soubor nemusíte naimportovat do Azure Stack. Soubor Fixed3. VHD je součástí některých šablon řešení, aby splňoval požadavky na publikování Azure Marketplace.

   Přečtěte si popis šablon a stáhněte a pak importujte další požadavky, například virtuální pevné disky, které jsou potřeba pro práci se šablonou řešení.  

   **O rozšířeních:** Při práci s rozšířeními imagí virtuálních počítačů použijte následující parametry:
   - *Publisher*
   - *Typ*
   - *Verze*  

   Pro rozšíření nepoužíváte *nabídku* .

4. Pomocí PowerShellu můžete publikovat položku Marketplace do Azure Stack pomocí rutiny **Add-AzsGalleryItem** . Příklad:

    ```powershell
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     -Verbose
    ```

5. Po publikování položky Galerie je teď možné ji použít. Pokud chcete potvrdit publikování položky galerie, klikněte na **všechny služby**a potom v kategorii **Obecné** vyberte **Marketplace**.  Pokud je vaše stažení šablonou řešení, nezapomeňte pro šablonu řešení přidat jakoukoli závislou image VHD.  
  [![Zobrazit Marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "Zobrazit Marketplace")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

S vydáním Azure Stack PowerShellu 1.3.0 teď můžete přidat rozšíření virtuálních počítačů. Příklad:

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>Další postup

[Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
