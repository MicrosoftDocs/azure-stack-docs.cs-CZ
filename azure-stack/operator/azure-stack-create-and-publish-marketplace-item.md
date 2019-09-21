---
title: Vytvoření a publikování položky Marketplace v Azure Stack | Microsoft Docs
description: Naučte se, jak vytvořit a publikovat položku Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 668882b1f5e0702ce51798468c8f102efe92edcd
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159682"
---
# <a name="create-and-publish-a-marketplace-item-in-azure-stack"></a>Vytvoření a publikování položky Marketplace v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Vytvoření položky Marketplace

1. Stáhněte si [nástroj Azure Gallery Packager](https://www.aka.ms/azurestackmarketplaceitem) a ukázkovou položku Azure Stack Marketplace.
2. Otevřete ukázkovou položku Marketplace a přejmenujte složku **SimpleVMTemplate** . Použijte stejný název jako položka Marketplace; například **contoso. TodoList**. Tato složka obsahuje:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Vytvořte šablonu Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) nebo vyberte šablonu z GitHubu. Položka Marketplace používá tuto šablonu k vytvoření prostředku.

    > [!NOTE]  
    > Nikdy nepoužívejte v šabloně Azure Resource Manager žádné tajné kódy, jako jsou kódy Product Key, heslo nebo žádné informace o zákazníkovi. Soubory JSON šablon jsou přístupné bez nutnosti ověřování po publikování v galerii. Ukládejte všechna tajná klíče v [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) a volejte je v rámci šablony.

4. Chcete-li zajistit, aby bylo možné prostředek nasadit úspěšně, otestujte šablonu pomocí rozhraní API Microsoft Azure Stack.
5. Pokud vaše šablona spoléhá na image virtuálního počítače, postupujte podle pokynů pro [Přidání image virtuálního počítače do Azure Stack](azure-stack-add-vm-image.md).
6. Uložte šablonu Azure Resource Manager do složky **/contoso.TodoList/DeploymentTemplates/** .
7. Vyberte ikony a text pro položku Marketplace. Přidejte ikony do složky **ikony** a přidejte text do souboru Resources ve složce **řetězce** . Použijte pro ikony **malé**, **střední**, **velké**a **široké** konvence pojmenování. Podrobný popis těchto velikostí najdete v referenčních informacích k [uživatelskému rozhraní položky Marketplace](#reference-marketplace-item-ui) .

   > [!NOTE]
   > Pro správné vytvoření položky Marketplace se vyžadují všechny čtyři velikosti ikon (malá, střední, Velká, rozsáhlá).
   >
   >

8. V souboru **manifest. JSON** změňte **název** na název vaší položky Marketplace. Změňte také **vydavatele** na jméno nebo společnost. Před publikováním image aktualizujte soubor manifest. JSON a aktualizujte tento řádek: "název": "XXX". Nezapomeňte pro každou verzi image použít jiný název.
9. Včásti artefakty změňte **název** a **cestu** na správné informace pro šablonu Azure Resource Manager, kterou jste zahrnuli:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Nahradit **Moje položky Marketplace** seznamem kategorií, ve kterých by se měla zobrazit vaše položka Marketplace:

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. Další úpravy manifestu manifest. JSON najdete v [referenčních informacích: Položka webu Marketplace manifest.](#reference-marketplace-item-manifestjson)JSON.

12. Chcete-li zabalit složky do souboru. azpkg, otevřete příkazový řádek a spusťte následující příkaz:

    ```shell
    AzureGalleryPackager.exe package -m <absolute path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > Musí existovat úplná cesta k souboru manifest. JSON i výstupní balíček. Pokud je například výstupní cesta C:\MarketPlaceItem\yourpackage.azpkg, musí existovat složka **C:\MarketPlaceItem** .
    >
    >

## <a name="publish-a-marketplace-item"></a>Zveřejnění položky Marketplace

1. Pomocí PowerShellu nebo Průzkumník služby Azure Storage nahrajte položku Marketplace (. azpkg) do úložiště objektů BLOB v Azure. Můžete nahrávat do místního úložiště Azure Stack nebo nahrávat do Azure Storage, což je dočasné umístění balíčku. Ujistěte se, že je objekt BLOB veřejně přístupný.
2. Na virtuálním počítači klienta v prostředí Microsoft Azure Stack se ujistěte, že je vaše relace PowerShellu nastavená s přihlašovacími údaji správce služby. Pokyny k ověřování PowerShellu v Azure Stack najdete v tématu [nasazení šablony pomocí PowerShellu](../user/azure-stack-deploy-template-powershell.md).
3. Pokud používáte [prostředí PowerShell 1.3.0](azure-stack-powershell-install.md) nebo novější, můžete k publikování položky Marketplace do Azure Stack použít rutinu prostředí PowerShell **Add-AzsGalleryItem** . V případě starších verzí použijte rutinu **Add-AzureRMGalleryitem** místo rutiny **Add-AzsGalleryItem**. Například při použití prostředí PowerShell 1.3.0 nebo novější:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg -Verbose
   ```

   | Parametr | Popis |
   | --- | --- |
   | SubscriptionID |ID předplatného správce. Můžete ho načíst pomocí PowerShellu. Pokud byste ho chtěli zobrazit na portálu, přejdete do předplatného poskytovatele a zkopírujete ID předplatného. |
   | GalleryItemUri |Identifikátor URI objektu BLOB pro balíček galerie, který je už nahraný do úložiště |
   | Apiversion |Nastavte jako **2015-04-01**. |

4. Přejít na portál. Položku Marketplace teď můžete zobrazit na portálu, jako operátor nebo jako uživatel. Zobrazení balíčku může trvat několik minut.

5. Vaše položka Marketplace je teď uložená na webu Azure Stack Marketplace. Můžete ji odstranit z umístění úložiště objektů BLOB.

    > [!CAUTION]  
    > Všechny výchozí artefakty galerie a vaše vlastní artefakty galerie jsou teď dostupné bez ověřování v těchto adresách URL:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Položku Marketplace můžete odebrat pomocí rutiny **Remove-AzureRMGalleryItem** . Příklad:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  -Verbose
   ```

   > [!NOTE]
   > Po odebrání položky může uživatelské rozhraní Marketplace zobrazit chybu. Chybu opravíte tak, že na portálu kliknete na **Nastavení** . Pak v části **vlastní nastavení portálu**vyberte **zrušit změny** .
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Další informace: Položka Marketplace manifest. JSON

### <a name="identity-information"></a>Informace o identitě

| Name | Požadováno | type | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Name |X |Řetězec |[A-Za-z0-9]+ | |
| Vydavatel |X |Řetězec |[A-Za-z0-9]+ | |
| Version |X |Řetězec |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadata

| Name | Požadováno | type | Omezení | Popis |
| --- | --- | --- | --- | --- |
| DisplayName |X |Řetězec |Doporučení 80 znaků |Portál nemusí správně zobrazit název položky, pokud je delší než 80 znaků. |
| PublisherDisplayName |X |Řetězec |Doporučení 30 znaků |Portál nemusí správně zobrazit název vašeho vydavatele, pokud je delší než 30 znaků. |
| PublisherLegalName |X |Řetězec |Maximálně 256 znaků | |
| Souhrn |X |Řetězec |60 až 100 znaků | |
| LongSummary |X |Řetězec |140 až 256 znaků |V Azure Stack ještě neplatí. |
| Popis |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 až 5 000 znaků | |

### <a name="images"></a>Obrázky

Tržiště používá následující ikony:

| Name | Šířka | Výška | Poznámky |
| --- | --- | --- | --- |
| Široký |255 px |115 px |Vždy vyžadováno |
| Velké |115 px |115 px |Vždy vyžadováno |
| Střední |90 px |90 px |Vždy vyžadováno |
| Malé |40 px |40 px |Vždy vyžadováno |
| Snímek obrazovky |533 px |324 px |Vždy vyžadováno |

### <a name="categories"></a>Categories

Každá položka Marketplace by měla být označena kategorií, která identifikuje, kde se položka zobrazí v uživatelském rozhraní portálu. Můžete zvolit jednu z existujících kategorií v Azure Stack (**COMPUTE**, **data + úložiště**a tak dále) nebo zvolit nové.

### <a name="links"></a>Odkazy

Každá položka na webu Marketplace může obsahovat různé odkazy na další obsah. Odkazy jsou zadány jako seznam názvů a identifikátorů URI:

| Name | Požadováno | type | Omezení | Popis |
| --- | --- | --- | --- | --- |
| DisplayName |X |Řetězec |Maximálně 64 znaků. | |
| Uri |X |Identifikátor URI | | |

### <a name="additional-properties"></a>Další vlastnosti

Kromě předchozích metadat mohou autoři na webu Marketplace poskytovat vlastní data párů klíč/hodnota v následujícím tvaru:

| Name | Požadováno | type | Omezení | Popis |
| --- | --- | --- | --- | --- |
| DisplayName |X |Řetězec |Maximálně 25 znaků. | |
| Value |X |Řetězec |Maximálně 30 znaků. | |

### <a name="html-sanitization"></a>Upravení HTML

Pro každé pole, které povoluje jazyk HTML, [jsou povoleny následující prvky a atributy](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Další informace: Uživatelské rozhraní položky Marketplace

V následujícím příkladu jsou ikony a text pro položky Marketplace, jak je vidět na portálu Azure Stack.

### <a name="create-blade"></a>Okno Vytvořit

![Vytvořit okno – Azure Stack položky Marketplace](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Okno podrobností položky Marketplace

![Okno podrobností položky Azure Stack Marketplace](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Stack Marketplace](azure-stack-marketplace.md)
* [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)
