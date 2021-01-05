---
title: 1.1.47.0 zpráva k vydání verze poskytovatele prostředků SQL centra pro Azure Stack
description: Podívejte se, co je nového v nejnovější aktualizaci poskytovatele prostředků SQL centra Azure Stack, včetně nových funkcí, oprav a známých problémů.
author: PatAltimore
ms.topic: article
ms.date: 11/26/2019
ms.author: patricka
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: d95be92f4cab37c41e1802fdcb18969e3276f459
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869215"
---
# <a name="sql-resource-provider-11470-release-notes"></a>1.1.47.0 poznámky k verzi poskytovatele prostředků SQL

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi 1.1.47.0 poskytovatele prostředků SQL.

## <a name="build-reference"></a>Referenční informace o buildu

Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků SQL, je uvedena níže:

> |Minimální verze centra Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 1910 (1.1910.0.58)|[SQL RP verze 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků SQL použijte pro integrovaný systém služby Azure Stack hub minimální podporovanou aktualizaci centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků SQL centra Azure Stack je vydáním oprav hotfix, aby poskytovatel prostředků kompatibilní s nejnovějšími změnami portálu byl v aktualizaci 1910. Neexistují žádné nové funkce.

Podporuje také nejnovější 2019-03-01 profil verze rozhraní API centra Azure Stack – HYBRID a Azure Stack hub 1.8.0 modul PowerShellu. Takže během nasazování a aktualizace není nutné instalovat žádné konkrétní verze historie modulů.

Postupujte podle postupu aktualizace poskytovatele prostředků, abyste použili opravu hotfix poskytovatele prostředků SQL 1.1.47.0 po upgradu centra Azure Stack na aktualizaci 1910. Pomůže vyřešit známý problém na portálu pro správu, kde se načítají monitorování kapacity v poskytovateli prostředků SQL.

## <a name="known-issues"></a>Známé problémy

Při [střídání certifikátu](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation) pro integrované systémy Azure Stack hub je argument KeyVaultPfxPassword mendatory, a to i v případě, že neexistují žádné záměry aktualizovat heslo certifikátu Key Vault.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).
- [Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md).
