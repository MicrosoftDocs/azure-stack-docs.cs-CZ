---
title: Vytvoření balíčku rozšíření OEM
titleSuffix: Azure Stack Hub
description: Naučte se vytvořit balíček rozšíření OEM v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c38a2738fcdfcb61c032982ce4ae0d2ea88d6e3f
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661469"
---
# <a name="create-an-oem-package"></a>Vytvoření balíčku OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Balíček rozšíření OEM Azure Stack hub je mechanismus, podle kterého se přidá obsah specifický pro výrobce OEM do infrastruktury centra Azure Stack. Obsah se používá v procesech nasazení a provozní procesy, jako je aktualizace, rozšiřování a nahrazování polí.

## <a name="creating-the-package"></a>Vytvoření balíčku

Po vytvoření a ověření se balíček rozšíření OEM dá použít v VaaS. Než budete pokračovat, ujistěte se, že jste dokončili kroky pro [Vytvoření balíčku OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Balíček se pak odešle do Microsoftu společně s výsledky VaaS testů pro přihlášení k pracovnímu postupu ověření balíčku. Následující kroky podrobně popisují, jak seskupit generované soubory do jednoho souboru zip, který může VaaS spotřebovat.

1. Identifikujte následující obsah balíčku:
    - Soubor zip, který obsahuje obsah balíčku.
    - Soubor manifestu s názvem `oemMetadata.xml`, který by měl být identický v obsahu se `metadata.xml` souborem v kořenu obsahu balíčku.

2. Vyberte soubory obsahu a vytvořte soubor zip:

    ![Obsah souboru ZIP při vytváření balíčku rozšíření](media/vaas-create-oem-package-1.png) ![OEM komprimace obsahu položky při vytváření balíčku rozšíření OEM](media/vaas-create-oem-package-2.png)

3. Přejmenujte výsledný soubor tak, aby byl dostatečně výstižný, abyste ho mohli identifikovat.

## <a name="verifying-the-contents"></a>Ověření obsahu

Chcete-li ověřit strukturu souboru zip, zkontrolujte jej a zkontrolujte, zda nejsou k dispozici žádné podsložky. TLD má obsah pro zip. Níže je uvedena platná struktura balíčku:

> [!IMPORTANT]
> Zipování nadřazené složky namísto obsahu způsobí selhání podepisování balíčku.

![Správný obsah balíčku zip při vytváření balíčku rozšíření OEM](media/vaas-create-oem-package-3.png)

Soubor zip se teď dá nahrát na VaaS a podepsaný Microsoftem v pracovním postupu ověření balíčku.

## <a name="next-steps"></a>Další kroky

- [Ověření balíčku OEM](azure-stack-vaas-validate-oem-package.md)
