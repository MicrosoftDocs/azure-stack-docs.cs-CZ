---
title: Zpráva k vydání verze 1.1.93. x poskytovatele prostředků SQL centra Azure Stack
description: Podívejte se na poznámky k verzi a zjistěte, co je nového v Azure Stack rámci aktualizace 1.1.93. x pro poskytovatele prostředků SQL hub.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 80caef7369a3cf5ebe79338bc907fa9b31d1a759
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737908"
---
# <a name="sql-resource-provider-1193x-release-notes"></a>Poznámky k verzi 1.1.93. x poskytovatele prostředků SQL

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků SQL verze 1.1.93. x.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků SQL, je uvedena níže:

> |Podporovaná verze centra Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 2008, 2005|[SQL RP verze 1.1.93.1](https://aka.ms/azshsqlrp11931)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků MySQL použijte pro integrovaný systém služby Azure Stack hub minimální podporovanou aktualizaci centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků SQL centra Azure Stack zahrnuje následující vylepšení a opravy:

- **Aktualizujte základní virtuální počítač na specializovaný Windows Server.** Tato verze Windows serveru je specializovaná pro infrastrukturu centra Azure Stack Add-On RP a není viditelná pro tržiště tenanta. Před nasazením nebo upgradem na tuto verzi poskytovatele prostředků SQL se ujistěte, že jste stáhli image **Microsoft AzureStack Add-On RP Windows serveru** .
- **Podpora odebrání osamocených metadat databáze a metadat hostitelských serverů.** Pokud již nelze hostitelský server připojit, bude mít klient možnost odebrat Osamocená metadata databáze z portálu. Pokud nejsou žádná Osamocená metadata databáze propojená s hostitelským serverem, operátor bude moci odebrat Osamocená metadata hostitelského serveru z portálu pro správu.
- **Při provádění rotace tajných kódů nastavte KeyVaultPfxPassword nepovinný argument.** Podrobnosti najdete v [tomto dokumentu](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) .
- **Další opravy chyb.**

Doporučuje se použít 1.1.93.1 poskytovatele prostředků SQL po upgradu centra Azure Stack na verzi 2005.

## <a name="known-issue"></a>Známý problém
Pokud se použije nesprávná AzureRmContext, nasazení verze 1.1.93.0 se nemusí zdařit. Doporučuje se upgradovat na verzi 1.1.93.1 přímo. Pokud jste už úspěšně provedli upgrade na 1.1.93.0, můžete 1.1.93.1 verzi bezpečně přeskočit.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).
- [Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md).

