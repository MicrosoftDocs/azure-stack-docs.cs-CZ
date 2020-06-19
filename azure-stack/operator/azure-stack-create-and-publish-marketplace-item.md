---
title: Vytvoření a publikování položky Marketplace v centru Azure Stack
description: Naučte se vytvářet a publikovat položku na webu Marketplace centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 06/11/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 16ea5f5873e7904931fb05d6113c0b6cb74f9612
ms.sourcegitcommit: bc246d59f4ad42cc2cc997884f9d52c5097f0964
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "85069124"
---
# <a name="create-and-publish-a-custom-azure-stack-hub-marketplace-item"></a>Vytvoření a publikování položky Marketplace pro vlastní Azure Stack centra

Každá položka publikovaná na tržišti centra Azure Stack používá formát balíčku Galerie Azure (. azpkg). Nástroj *Azure Gallery Packager* umožňuje vytvořit vlastní balíček Azure Gallery, který můžete nahrát na web centra Azure Stack hub, který pak můžou uživatelé stáhnout. Proces nasazení používá šablonu Azure Resource Manager.

## <a name="marketplace-items"></a>Položky Marketplace

Příklady v tomto článku ukazují, jak vytvořit jednu nabídku tržiště virtuálního počítače typu Windows nebo Linux.

## <a name="create-a-marketplace-item"></a>Vytvořit položku Marketplace

> [!IMPORTANT]
> Než vytvoříte položku virtuálního počítače Marketplace, nahrajte vlastní image virtuálního počítače na portál centra Azure Stack podle pokynů v části [Přidání image virtuálního počítače do centra Azure Stack](azure-stack-add-vm-image.md). Pak postupujte podle pokynů v tomto článku a zabalením Image (vytvořte soubor. azpkg) a nahrajte ho do tržiště centra Azure Stack.

Pokud chcete vytvořit vlastní položku Marketplace, udělejte toto:

1. Stáhněte si [nástroj Azure Gallery Packager](https://aka.ms/azsmarketplaceitem) a ukázkový balíček Azure Stack centra pro galerii. Tento soubor ke stažení obsahuje vlastní šablony virtuálních počítačů. Rozbalte soubor. zip a v části **vlastní virtuální počítače**složky můžete použít buď systémy Linux, nebo šablony systému Windows, které jsou k dispozici. Můžete se rozhodnout znovu použít předem připravené šablony a upravit příslušné parametry s údaji o produktu, které se zobrazí na portálu centra Azure Stack. Nebo můžete jednoduše znovu použít soubor. azpkg k dispozici a přeskočit následující postup pro přizpůsobení vlastního balíčku galerie.

2. Vytvořte šablonu Azure Resource Manager nebo použijte naše ukázkové šablony pro Windows/Linux. Tyto ukázkové šablony jsou k dispozici v souboru. zip nástroje balíčku, který jste stáhli v kroku 1. Můžete buď použít šablonu a změnit textová pole, nebo si můžete stáhnout předem nakonfigurovanou šablonu z GitHubu. Další informace o šablonách Azure Resource Manager naleznete v tématu [Azure Resource Manager Templates](/azure/azure-resource-manager/resource-group-authoring-templates).

3. Balíček Galerie by měl obsahovat následující strukturu:

   ![Snímek obrazovky se strukturou balíčku galerie](media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png)

   Struktura souborů šablon nasazení se zobrazí takto:

   ![Snímek obrazovky struktury šablon nasazení](media/azure-stack-create-and-publish-marketplace-item/gallerypkg2.png)

4. Nahraďte následující zvýrazněné hodnoty (s čísly) v Manifest.jsu šablony hodnotou, kterou jste zadali při [nahrávání vlastní image](azure-stack-add-vm-image.md).

   > [!NOTE]  
   > Nikdy nepoužívejte v šabloně Azure Resource Manager žádné tajné kódy, jako jsou kódy Product Key, heslo nebo žádné informace o zákazníkovi. Soubory JSON šablon jsou přístupné bez nutnosti ověřování po publikování v galerii. Ukládejte všechna tajná klíče v [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) a volejte je v rámci šablony.

   Následující šablona je ukázka Manifest.jsv souboru:

    ```json
    {
       "$schema": "https://gallery.azure.com/schemas/2015-10-01/manifest.json#",
       "name": "Test", (1)
       "publisher": "<Publisher name>", (2)
       "version": "<Version number>", (3)
       "displayName": "ms-resource:displayName", (4)
       "publisherDisplayName": "ms-resource:publisherDisplayName", (5)
       "publisherLegalName": "ms-resource:publisherDisplayName", (6)
       "summary": "ms-resource:summary",
       "longSummary": "ms-resource:longSummary",
       "description": "ms-resource:description",
       "longDescription": "ms-resource:description",
       "uiDefinition": {
          "path": "UIDefinition.json" (7)
          },
       "links": [
        { "displayName": "ms-resource:documentationLink", "uri": "http://go.microsoft.com/fwlink/?LinkId=532898" }
        ],
       "artifacts": [
          {
             "name": "<Template name>",
             "type": "Template",
             "path": "DeploymentTemplates\\<Template name>.json", (8)
             "isDefault": true
          }
       ],
       "categories":[ (9)
          "Custom",
          "<Template name>"
          ],
       "images": [{
          "context": "ibiza",
          "items": [{
             "id": "small",
             "path": "icons\\Small.png", (10)
             "type": "icon"
             },
             {
                "id": "medium",
                "path": "icons\\Medium.png",
                "type": "icon"
             },
             {
                "id": "large",
                "path": "icons\\Large.png",
                "type": "icon"
             },
             {
                "id": "wide",
                "path": "icons\\Wide.png",
                "type": "icon"
             }]
        }]
    }
    ```

    Následující seznam vysvětluje předchozí číslované hodnoty v příkladu šablony:

    - (1) – název nabídky
    - (2) – název vydavatele bez mezer.
    - (3) – verze šablony bez mezer.
    - (4) – název, který se zákazníkům zobrazí.
    - (5) – název vydavatele, který se zobrazí zákazníkům.
    - (6) – právní jméno vydavatele.
    - (7) – cesta k umístění, kam se má **UIDefinition.jsv** souboru ukládat.  
    - (8) – cesta a název souboru hlavní šablony JSON.
    - (9) – názvy kategorií, ve kterých se tato šablona zobrazuje
    - (10) – cesta a název pro každou ikonu.

5. U všech polí odkazujících na **MS-Resource**je nutné změnit příslušné hodnoty uvnitř **řetězců nebo resources.jsv** souboru:

    ```json
    {
    "displayName": "<OfferName.PublisherName.Version>",
    "publisherDisplayName": "<Publisher name>",
    "summary": "Create a simple VM",
    "longSummary": "Create a simple VM and use it",
    "description": "<p>This is just a sample of the type of description you could create for your gallery item!</p><p>This is a second paragraph.</p>",
    "documentationLink": "Documentation"
    }
    ```

    ![Zobrazení balíčku zobrazení balíčku ](media/azure-stack-create-and-publish-marketplace-item/pkg1.png) ![](media/azure-stack-create-and-publish-marketplace-item/pkg2.png)

6. Chcete-li zajistit, aby bylo možné prostředek nasadit úspěšně, otestujte šablonu pomocí [rozhraní API centra Azure Stack](../user/azure-stack-profiles-azure-resource-manager-versions.md).

7. Pokud vaše šablona spoléhá na image virtuálního počítače, postupujte podle pokynů pro [Přidání image virtuálního počítače do centra Azure Stack](azure-stack-add-vm-image.md).

8. Uložte šablonu Azure Resource Manager do složky **/contoso.TodoList/DeploymentTemplates/** .

9. Vyberte ikony a text pro položku Marketplace. Přidejte ikony do složky **ikony** a přidejte text do souboru **Resources** ve složce **řetězce** . Použijte pro ikony **malé**, **střední**, **velké**a **široké** konvence pojmenování. Podrobný popis těchto velikostí najdete v [referenčních informacích k uživatelskému rozhraní položky Marketplace](#reference-marketplace-item-ui) .

    > [!NOTE]
    > Pro správné vytvoření položky Marketplace se vyžadují všechny čtyři velikosti ikon (malá, střední, Velká, rozsáhlá).

10. Další úpravy Manifest.jsv naleznete v tématu [Reference: položka Marketplace manifest.jsna](#reference-marketplace-item-manifestjson).

11. Až dokončíte úpravu souborů, převeďte ji na soubor. azpkg. Převod provedete pomocí nástroje **AzureGallery.exe** a balíčku Sample Gallery, který jste předtím stáhli. Spusťte následující příkaz:

    ```shell
    .\AzureGallery.exe package -m c:\<path>\<gallery package name>\manifest.json -o c:\Temp
    ```

    > [!NOTE]
    > Výstupní cesta může být libovolná cesta, kterou si zvolíte, a nemusí být v jednotce C:. Nicméně musí existovat úplná cesta k manifest.jsv souboru i ve výstupním balíčku. Například pokud výstupní cesta je `C:\<path>\galleryPackageName.azpkg` , složka `C:\<path>` musí existovat.
    >
    >

## <a name="publish-a-marketplace-item"></a>Publikování položky Marketplace

1. Pomocí PowerShellu nebo Průzkumník služby Azure Storage nahrajte položku Marketplace (. azpkg) do úložiště objektů BLOB v Azure. Můžete nahrávat do místního úložiště centra Azure Stack nebo nahrávat do Azure Storage, což je dočasné umístění balíčku. Ujistěte se, že je objekt BLOB veřejně přístupný.

2. Pokud chcete importovat balíček galerie do centra Azure Stack, prvním krokem je vzdálené připojení (RDP) k virtuálnímu počítači klienta, aby bylo možné zkopírovat soubor, který jste právě vytvořili, do centra Azure Stack.

3. Přidejte kontext:

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Spusťte následující skript pro import prostředku do Galerie:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg -Verbose
    ```

5. Ověřte, že máte platný účet úložiště, který je k dispozici pro uložení vaší položky. Tuto hodnotu můžete získat `GalleryItemURI` z portálu pro správu centra Azure Stack. Vyberte **účet úložiště-> vlastnosti objektu BLOB – > URL**s příponou. azpkg. Účet úložiště se používá jenom pro dočasné použití, aby se daly publikovat na webu Marketplace.

   Po dokončení balíčku galerie a jeho nahrání pomocí **Add-AzsGalleryItem**by se teď měl váš vlastní virtuální počítač zobrazit na webu Marketplace i v zobrazení **vytvořit prostředek** . Všimněte si, že ve **správě Marketplace**není balíček vlastní Galerie viditelný.

   [![Odeslala se vlastní položka Marketplace](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Odeslala se vlastní položka Marketplace")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Po úspěšném publikování položky na webu Marketplace můžete obsah odstranit z účtu úložiště.

   > [!CAUTION]  
   > Všechny výchozí artefakty galerie a vaše vlastní artefakty galerie jsou teď dostupné bez ověřování v těchto adresách URL:  
   `https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
   `https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`

6. Položku Marketplace můžete odebrat pomocí rutiny **Remove-AzureRMGalleryItem** . Příklad:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

   > [!NOTE]
   > Po odebrání položky může uživatelské rozhraní Marketplace zobrazit chybu. Chybu opravíte tak, že na portálu kliknete na **Nastavení** . Pak v části **vlastní nastavení portálu**vyberte **zrušit změny** .
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Referenční dokumentace: manifest.jspoložky Marketplace na

### <a name="identity-information"></a>Informace o identitě

| Name | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Name |× |Řetězec |[A-za-Z0-9] + | |
| Publisher |× |Řetězec |[A-za-Z0-9] + | |
| Verze |× |Řetězec |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadata

| Name | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| DisplayName |× |Řetězec |Doporučení 80 znaků |Portál nemusí správně zobrazit název položky, pokud je delší než 80 znaků. |
| PublisherDisplayName |× |Řetězec |Doporučení 30 znaků |Portál nemusí správně zobrazit název vašeho vydavatele, pokud je delší než 30 znaků. |
| PublisherLegalName |× |Řetězec |Maximálně 256 znaků | |
| Souhrn |× |Řetězec |60 až 100 znaků | |
| LongSummary |× |Řetězec |140 až 256 znaků |Ještě neplatí v Azure Stack hub. |
| Popis |× |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 až 5 000 znaků | |

### <a name="images"></a>Image

Tržiště používá následující ikony:

| Name | impulzu | Vlastnost Height | Poznámky |
| --- | --- | --- | --- |
| Rozlehlý |255 px |115 px |Vždy vyžadováno |
| Velká |115 px |115 px |Vždy vyžadováno |
| Střední |90 px |90 px |Vždy vyžadováno |
| Malá |40 px |40 px |Vždy vyžadováno |
| Snímek obrazovky |533 px |324 px |Volitelné |

### <a name="categories"></a>Kategorie

Každá položka Marketplace by měla být označena kategorií, která identifikuje, kde se položka zobrazí v uživatelském rozhraní portálu. Můžete zvolit jednu z existujících kategorií v Azure Stackovém centru (**COMPUTE**, **data + úložiště**atd.) nebo zvolit novou.

### <a name="links"></a>Odkazy

Každá položka na webu Marketplace může obsahovat různé odkazy na další obsah. Odkazy jsou zadány jako seznam názvů a identifikátorů URI:

| Name | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| DisplayName |× |Řetězec |Maximálně 64 znaků. | |
| Identifikátor URI |× |Identifikátor URI | | |

### <a name="additional-properties"></a>Další vlastnosti

Kromě předchozích metadat mohou autoři na webu Marketplace poskytovat vlastní data párů klíč/hodnota v následujícím tvaru:

| Name | Požaduje se | Typ | Omezení | Popis |
| --- | --- | --- | --- | --- |
| DisplayName |× |Řetězec |Maximálně 25 znaků. | |
| Hodnota |× |Řetězec |Maximálně 30 znaků. | |

### <a name="html-sanitization"></a>Upravení HTML

Pro každé pole, které povoluje jazyk HTML, [jsou povoleny následující prvky a atributy](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Referenční dokumentace: uživatelské rozhraní položky Marketplace

V následujícím příkladu jsou ikony a text pro položky Marketplace, jak je vidět na portálu centra Azure Stack.

### <a name="create-blade"></a>Okno Vytvořit

![Vytvořit okno – Azure Stack položky centra Marketplace](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Okno podrobností položky Marketplace

![Okno s podrobnostmi o položce Azure Stack centra Marketplace](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Další kroky

- [Přehled centra Azure Stack Marketplace](azure-stack-marketplace.md)
- [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)
- [Formátování a struktura Azure Resource Manager šablon](/azure/azure-resource-manager/resource-group-authoring-templates)
