---
title: 1.1.93.0 zpráva k vydání verze poskytovatele prostředků SQL centra pro Azure Stack
description: Podívejte se na poznámky k verzi a zjistěte, co je nového Azure Stack v 1.1.93.0 aktualizace poskytovatele prostředků SQL hub.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 035e1cc2e78aaebcc0ea653354d00f617d7e95c5
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585900"
---
# <a name="sql-resource-provider-11930-release-notes"></a>1.1.93.0 poznámky k verzi poskytovatele prostředků SQL

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi 1.1.93.0 poskytovatele prostředků SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků SQL, je uvedena níže:

> |Podporovaná verze centra Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 2005|[SQL RP verze 1.1.93.0](https://aka.ms/azshsqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků MySQL použijte pro integrovaný systém služby Azure Stack hub minimální podporovanou aktualizaci centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků SQL centra Azure Stack zahrnuje následující vylepšení a opravy:

- **Aktualizujte základní virtuální počítač na specializovaný Windows Server.** Tato verze Windows serveru je specializovaná pro infrastrukturu Azure Stackho přidaných rozbočovačů a není viditelná pro tržiště tenanta. Před nasazením nebo upgradem na tuto verzi poskytovatele prostředků SQL se ujistěte, že jste si stáhli **jenom interní image Windows serveru pro doplněk Microsoft AZURESTACK RP** .
- **Podpora odebrání osamocených metadat databáze a metadat hostitelských serverů.** Pokud již nelze hostitelský server připojit, bude mít klient možnost odebrat Osamocená metadata databáze z portálu. Pokud nejsou žádná Osamocená metadata databáze propojená s hostitelským serverem, operátor bude moci odebrat Osamocená metadata hostitelského serveru z portálu pro správu.
- **Při provádění rotace tajných kódů nastavte KeyVaultPfxPassword nepovinný argument.** Podrobnosti najdete v [tomto dokumentu](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) .
- **Další opravy chyb.**

Doporučuje se použít 1.1.93.0 poskytovatele prostředků SQL po upgradu centra Azure Stack na verzi 2005.

## <a name="known-issues"></a>Známé problémy
Žádné

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).
- [Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md).

