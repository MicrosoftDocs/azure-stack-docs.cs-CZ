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
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2ee5a873a6281ed5d71db05b05abd15986043676
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167370"
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

    ![obsahu souboru ZIP](media/vaas-create-oem-package-1.png) ![komprimace obsahu položky](media/vaas-create-oem-package-2.png)

3. Přejmenujte výsledný soubor tak, aby byl dostatečně popisný, abyste ho mohli identifikovat.

## <a name="verifying-the-contents"></a>Ověření obsahu

Chcete-li ověřit strukturu souboru zip, zkontrolujte jej a zkontrolujte, zda nejsou k dispozici žádné podsložky. TLD má obsah pro zip. Níže je uvedena platná struktura balíčku.
> [!IMPORTANT]
> Zipování nadřazené složky namísto obsahu způsobí selhání podepisování balíčku.

![Správný obsah balíčku zip](media/vaas-create-oem-package-3.png)

Soubor zip se teď dá nahrát na VaaS a podepsaný Microsoftem v pracovním postupu ověření balíčku.

## <a name="next-steps"></a>Další kroky

- [Ověření balíčku OEM](azure-stack-vaas-validate-oem-package.md)
