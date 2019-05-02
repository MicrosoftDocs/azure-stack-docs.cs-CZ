---
title: Osvědčené postupy pro Azure Stack ověření | Dokumentace Microsoftu
description: Tento článek popisuje osvědčené postupy při používání ověřování jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 568817c6e25952f15a396e5748d314187345945b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298665"
---
# <a name="create-an-oem-package"></a>Vytvoření balíčku výrobce OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Balíček rozšíření Azure Stack OEM je mechanismus, podle které OEM konkrétní obsah se přidá do infrastruktury Azure stacku pro použití v nasazení, jakož i provozní procesy, jako je aktualizace, rozšíření a nahrazení pole.

## <a name="creating-the-package"></a>Vytvoření balíčku

Po vytvoření a ověření, OEM balíček rozšíření je možné v VaaS.  Než budete pokračovat, ujistěte se, že jste dokončili postup [vytvoření balíčku pro výrobce OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Balíček se pak odešle společnosti Microsoft spolu s výsledky testu VaaS pro podepisování se v procesu ověřování balíčku. Následující kroky podrobně popisují, jak začlenit do souboru zip jeden, které využívají VaaS generované soubory.

1. Identifikujte následující obsah pro balíček:
    - Soubor zip, který obsahuje obsah balíčku
    - Soubor manifestu s názvem `oemMetadata.xml`, který by měl být stejný obsah jako soubor metadata.xml v kořenovém adresáři balíčku obsahu.

2. Vyberte soubory obsahu a vytvořit soubor zip z obsahu:

    ![Obsah souboru ZIP](media/vaas-create-oem-package-1.png) ![Komprimovat obsah položky](media/vaas-create-oem-package-2.png)

3. Výsledný soubor přejmenujte, tak, aby byl dostatečně výstižný, můžete pro jeho rozpoznání.

## <a name="verifying-the-contents"></a>Ověření obsahu

K ověření struktury souboru zip, zkontrolujte ho a zkontrolujte, že neexistují žádné podsložky. Nejvyšší úrovně má ZIP obsah. Struktura platný balíček je uveden níže.
> [!IMPORTANT]
> Zipování nadřazené složky namísto obsah způsobí selhání podepisování sady.

![Správně komprimovaný balíček obsahu](media/vaas-create-oem-package-3.png)

Soubor zip teď můžete být odeslán do VaaS a podepsán společností Microsoft v pracovním postupu ověřování balíčku.

## <a name="next-steps"></a>Další postup

- [Ověření balíčku výrobce OEM](azure-stack-vaas-validate-oem-package.md)
