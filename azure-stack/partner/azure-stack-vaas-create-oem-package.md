---
title: Osvědčené postupy pro ověřování Azure Stack | Microsoft Docs
description: Tento článek popisuje osvědčené postupy pro používání ověřování jako služby.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7e866187c81b488cc90f7ddaa443db4868ba4d9
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417735"
---
# <a name="create-an-oem-package"></a>Vytvoření balíčku OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Balíček rozšíření Azure Stack OEM je mechanismus, podle kterého se do infrastruktury Azure Stack přidá konkrétní obsah OEM, který se použije v nasazení, a také provozní procesy, jako je aktualizace, rozšíření a náhrada polí.

## <a name="creating-the-package"></a>Vytváření balíčku

Po vytvoření a ověření se balíček rozšíření OEM dá použít v VaaS.  Než budete pokračovat, ujistěte se, že jste dokončili kroky pro [Vytvoření balíčku OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Balíček se pak odešle do Microsoftu společně s výsledky VaaS testů pro přihlášení k pracovnímu postupu ověření balíčku. Následující kroky podrobně popisují, jak seskupit generované soubory do jednoho souboru zip, který může VaaS spotřebovat.

1. Identifikujte následující obsah balíčku:
    - Soubor ZIP obsahující obsah balíčku
    - Soubor manifestu s názvem `oemMetadata.xml`, který by měl být identický v obsahu se souborem metadata. XML v kořenu obsahu balíčku.

2. Vyberte soubory obsahu a vytvořte soubor zip z tohoto obsahu:

    ![](media/vaas-create-oem-package-1.png) Obsah![souboru ZIP – komprimace obsahu položky](media/vaas-create-oem-package-2.png)

3. Přejmenujte výsledný soubor tak, aby byl dostatečně popisný, abyste ho mohli identifikovat.

## <a name="verifying-the-contents"></a>Ověření obsahu

Chcete-li ověřit strukturu souboru zip, zkontrolujte jej a zkontrolujte, zda nejsou k dispozici žádné podsložky. TLD má obsah pro zip. Níže je uvedena platná struktura balíčku.
> [!IMPORTANT]
> Zipování nadřazené složky namísto obsahu způsobí selhání podepisování balíčku.

![Správný obsah balíčku zip](media/vaas-create-oem-package-3.png)

Soubor zip se teď dá nahrát na VaaS a podepsaný Microsoftem v pracovním postupu ověření balíčku.

## <a name="next-steps"></a>Další postup

- [Ověření balíčku OEM](azure-stack-vaas-validate-oem-package.md)
