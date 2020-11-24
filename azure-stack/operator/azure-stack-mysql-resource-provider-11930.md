---
title: 1.1.93.0 zpráva k vydání verze poskytovatele prostředků MySQL centra pro Azure Stack
description: Podívejte se na poznámky k verzi a zjistěte, co je nového ve 1.1.93.0 aktualizace poskytovatele prostředků MySQL centra pro Azure Stack.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: f3e9e177e4d335c667754a308698ed362c6535b2
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812749"
---
# <a name="mysql-resource-provider-11930-release-notes"></a>Poznámky k verzi pro poskytovatele prostředků MySQL 1.1.93.0

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků MySQL verze 1.1.93.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků MySQL a potom spusťte samočinného extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků MySQL, je uvedena níže:

> |Podporovaná verze centra Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Verze 2008, 2005|[MySQL RP verze 1.1.93.0](https://aka.ms/azshmysqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků MySQL použijte pro integrovaný systém služby Azure Stack hub minimální podporovanou aktualizaci centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků centra Azure Stacku MySQL zahrnuje následující vylepšení a opravy:

- **Aktualizujte základní virtuální počítač na specializovaný Windows Server.** Tato verze Windows serveru je specializovaná pro infrastrukturu centra Azure Stack Add-On RP a není viditelná pro tržiště tenanta. Před nasazením nebo upgradem na tuto verzi poskytovatele prostředků MySQL Nezapomeňte stáhnout **pouze interní image Microsoft AzureStack Add-On RP Windows serveru** .
- **Podpora odebrání osamocených metadat databáze a metadat hostitelských serverů.** Pokud již nelze hostitelský server připojit, bude mít klient možnost odebrat Osamocená metadata databáze z portálu. Pokud nejsou žádná Osamocená metadata databáze propojená s hostitelským serverem, operátor bude moci odebrat Osamocená metadata hostitelského serveru z portálu pro správu.
- **Při provádění rotace tajných kódů nastavte KeyVaultPfxPassword nepovinný argument.** Podrobnosti najdete v [tomto dokumentu](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) .
- **Další opravy chyb.**

Doporučuje se použít poskytovatele prostředků MySQL 1.1.93.0 po upgradu centra Azure Stack na verzi 2005.

## <a name="known-issues"></a>Známé problémy
Žádné

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).
- [Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md).
