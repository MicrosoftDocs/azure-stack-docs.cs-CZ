---
title: Vytvoření a publikování položky Marketplace ve službě Azure Stack | Dokumentace Microsoftu
description: Vytvoření a publikování položky Marketplace ve službě Azure Stack.
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
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 59e86e15289833d63b85314a84d0bb9e60dc5da8
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172573"
---
# <a name="create-and-publish-a-marketplace-item"></a>Vytvoření a publikování položky Marketplace

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Vytvoření položky Marketplace

1. Stáhněte si [nástroj Azure Galerie Packageru](https://www.aka.ms/azurestackmarketplaceitem) a ukázkové položky Azure Stack Marketplace.
2. Otevření ukázkové položky Marketplace a přejmenovat **SimpleVMTemplate** složky. Použijte stejný název jako vaše položky Marketplace; například **Contoso.TodoList**. Tato složka obsahuje:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Vytvoření šablony Azure Resource Manageru](/azure/azure-resource-manager/resource-group-authoring-templates) nebo výběr šablony z Githubu. Položka Marketplace používá tuto šablonu k vytvoření prostředku.

    > [!NOTE]  
    > Nikdy intenzivně kódu všechny tajné kódy, jako jsou kódy product key, heslo nebo žádné identifikovatelné informace o zákaznících v šablony Azure Resource Manageru. Soubory JSON šablony jsou přístupné bez nutnosti ověřování po publikování v galerii. Store všech tajných kódů v [služby Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) a zavoláme z v rámci šablony.

4. Pokud chcete mít jistotu, že je úspěšně nasazena na prostředek, otestujte šablonu s rozhraními API sady Microsoft Azure Stack.
5. Pokud vaše šablony závisí na image virtuálního počítače, postupujte podle pokynů a [přidat image virtuálního počítače do služby Azure Stack](azure-stack-add-vm-image.md).
6. Uložit šablonu Azure Resource Manageru v **/Contoso.TodoList/DeploymentTemplates/** složky.
7. Zvolte ikon a textu pro položku Marketplace. Přidání ikon **ikony** složky a přidejte text, který má **prostředky** ve **řetězce** složky. Použití **malé**, **střední**, **velké**, a **široké** zásady vytváření názvů pro ikony. Zobrazit [položky Marketplace Reference k uživatelskému rozhraní](#reference-marketplace-item-ui) podrobný popis těchto velikostí.

   > [!NOTE]
   > Všechny čtyři ikony velikosti (malá, střední, velká široký) jsou požadovány pro vytváření položky Marketplace správně.
   >
   >

8. V **Manifest.json** změňte **název** k názvu položky Marketplace. Také změnit **vydavatele** na název vaší společnosti.
9. V části **artefakty**, změňte **název** a **cesta** do správné informace pro šablony Azure Resource Manageru, který jste zahrnuli:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Nahraďte **Moje položky Marketplace** seznam kategorie, ve kterém by se měla zobrazit vaše položky Marketplace:

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. Veškeré další úpravy do souboru Manifest.json najdete [odkaz: Manifest.json položky Marketplace](#reference-marketplace-item-manifestjson).

12. Do složky balíčku do souboru .azpkg, otevřete příkazový řádek a spusťte následující příkaz:

    ```shell
    AzureGalleryPackager.exe package -m <path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > Úplná cesta k výstupní balíčku musí existovat. Například, pokud je výstupní cesta je C:\MarketPlaceItem\yourpackage.azpkg složce **C:\MarketPlaceItem** musí existovat.
    >
    >

## <a name="publish-a-marketplace-item"></a>Zveřejnění položky Marketplace

1. Nahrát položky Marketplace (.azpkg) do úložiště objektů Blob v Azure pomocí Powershellu nebo Průzkumníka služby Azure Storage. Můžete nahrát do místního úložiště služby Azure Stack nebo můžete nahrát do služby Azure Storage; což je dočasné umístění balíčku. Ujistěte se, že objekt blob je veřejně dostupná.
2. Na virtuálním klientovi v prostředí Microsoft Azure Stack Ujistěte se, že relace prostředí PowerShell je nastavené pomocí svých přihlašovacích údajů správce služby. Můžete najít pokyny o tom, jak ověřit prostředí PowerShell ve službě Azure Stack v [nasazení šablony pomocí prostředí PowerShell](../user/azure-stack-deploy-template-powershell.md).
3. Při použití [PowerShell 1.3.0](azure-stack-powershell-install.md) nebo později, můžete použít **přidat AzsGalleryItem** rutiny Powershellu k publikování položky Marketplace do služby Azure Stack. Před použitím prostředí PowerShell 1.3.0, použijte rutinu **přidat AzureRMGalleryitem** místo **přidat AzsGalleryItem**. Například při použití prostředí PowerShell 1.3.0 nebo novější:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg -Verbose
   ```

   | Parametr | Popis |
   | --- | --- |
   | ID předplatného |ID správce předplatného. To můžete načíst pomocí prostředí PowerShell. Pokud chcete získat na portálu, přejděte na předplatné poskytovatele a zkopírujte ID předplatného. |
   | GalleryItemUri |Identifikátor URI objektu BLOB pro balíček galerie, která je již nahraná do služby storage. |
   | Verze rozhraní API |Nastavit jako **2015-04-01**. |

4. Přejděte na portál. Nyní je vidět položku Marketplace. na portálu, jako operátor nebo jako uživatel. Balíček může trvat několik minut.

5. Položky Marketplace se nyní uložil do Tržiště Azure Stack. Můžete ho odstranit z umístění úložiště objektů Blob.

    > [!CAUTION]  
    > Všechny artefakty výchozí galerie a Galerie vlastních artefaktů jsou teď k dispozici bez ověřování v rámci následující adresy URL:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Můžete odebrat položky Marketplace pomocí **odebrat AzureRMGalleryItem** rutiny. Příklad:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  -Verbose
   ```

   > [!NOTE]
   > Po odebrání položky se můžou zobrazovat uživatelského rozhraní Marketplace k chybě. Oprava chyby, klikněte na tlačítko **nastavení** na portálu. Vyberte **zahodit úpravy** pod **přizpůsobení portálu**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Další informace: Manifest.json položky Marketplace

### <a name="identity-information"></a>Informace o identitě

| Název | Požaduje se | Type | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Název |X |String |[A-Za-z0-9]+ | |
| Vydavatel |X |String |[A-Za-z0-9]+ | |
| Version |X |String |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadata

| Název | Požaduje se | Type | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |String |Doporučení 80 znaků. |Název položky na portálu nezobrazí správně, pokud je delší než 80 znaků. |
| PublisherDisplayName |X |String |Doporučení 30 znaků |Název vydavatele na portálu nezobrazí správně, pokud je delší než 30 znaků. |
| PublisherLegalName |X |String |Maximálně 256 znaků. | |
| Souhrn |X |String |60 až 100 znaků | |
| LongSummary |X |String |140 až 256 znaků |Není zatím k dispozici ve službě Azure Stack. |
| Popis |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |500 až 5 000 znaků | |

### <a name="images"></a>Obrázky

Na webu Marketplace používá následující ikony:

| Název | Šířka | Výška | Poznámky |
| --- | --- | --- | --- |
| Široký |255 px |115 px |Vždycky se vyžaduje |
| Velká |115 px |115 px |Vždycky se vyžaduje |
| Středně velká |90 px |90 px |Vždycky se vyžaduje |
| Malá |40 px |40 px |Vždycky se vyžaduje |
| Snímek obrazovky |533 px |32 px |Volitelná |

### <a name="categories"></a>Categories

Každá položka Marketplace by měl být s klíčovým slovem kategorii, která určuje, kde se položka zobrazí v Uživatelském rozhraní portálu. Můžete zvolit jednu existující kategorie ve službě Azure Stack (**Compute**, **Data + úložiště**atd), nebo zvolit nové.

### <a name="links"></a>Odkazy

Každá položka Marketplace může obsahovat různé odkazy na další obsah. Odkazy jsou zadané jako seznam názvy a identifikátory URI:

| Název | Požaduje se | Type | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |String |Maximálně 64 znaků. | |
| URI |X |Identifikátor URI | | |

### <a name="additional-properties"></a>Další vlastnosti

Kromě předchozích metadat můžete Marketplace autoři uvádějí data dvojice vlastní klíč/hodnota v následujícím tvaru:

| Název | Požaduje se | Type | Omezení | Popis |
| --- | --- | --- | --- | --- |
| Zobrazovaný název |X |String |Maximálně 25 znaků. | |
| Hodnota |X |String |Maximálně 30 znaků. | |

### <a name="html-sanitization"></a>Sanitizace HTML

Pro všechna pole, která umožňuje HTML, následující [elementy a atributy jsou povoleny](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Další informace: Položka Marketplace uživatelského rozhraní

Ikon a textu pro položky Marketplace, jak je vidět na portálu Azure Stack se.

### <a name="create-blade"></a>Okno Vytvořit

![Okno Vytvořit](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Okno s podrobnostmi o položky Marketplace

![Okno s podrobnostmi o položky Marketplace](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Další postup

* [Přehled služby Azure Stack Marketplace](azure-stack-marketplace.md)
* [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)
