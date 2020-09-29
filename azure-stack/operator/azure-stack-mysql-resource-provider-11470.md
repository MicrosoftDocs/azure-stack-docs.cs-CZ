---
title: 1.1.47.0 zpráva k vydání verze poskytovatele prostředků MySQL centra pro Azure Stack
description: Podívejte se na poznámky k verzi a zjistěte, co je nového ve 1.1.47.0 aktualizace poskytovatele prostředků MySQL centra pro Azure Stack.
author: justinha
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 5bc03134a859182909e62c60159049d639eb8ffe
ms.sourcegitcommit: 08a421ab5792ab19cc06b849763be22f051e6d78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89364673"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Poznámky k verzi pro poskytovatele prostředků MySQL 1.1.47.0

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků MySQL verze 1.1.47.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků MySQL a potom spusťte samočinného extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků MySQL, je uvedena níže:

> |Minimální verze centra Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Verze 1910 (1.1910.0.58)|[MySQL RP verze 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Než nasadíte nejnovější verzi poskytovatele prostředků MySQL, použijte pro integrovaný systém centra Azure Stack minimálně podporu Azure Stack centra nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků MySQL centra Azure Stack je vydáním oprav hotfix, aby poskytovatel prostředků, který je kompatibilní s některými nejnovějšími změnami portálu v aktualizaci 1910. Neexistují žádné nové funkce.

Podporuje také nejnovější 2019-03-01 profil verze rozhraní API centra Azure Stack – HYBRID a Azure Stack hub 1.8.0 modul PowerShellu. Takže během nasazování a aktualizace není nutné instalovat žádné konkrétní verze historie modulů.

Doporučuje se použít opravu hotfix poskytovatele prostředků MySQL 1.1.47.0 po upgradu centra Azure Stack na verzi 1910.

## <a name="known-issues"></a>Známé problémy

Při [střídání certifikátu](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation) pro integrované systémy Azure Stack hub je argument KeyVaultPfxPassword mendatory, a to i v případě, že neexistují žádné záměry aktualizovat heslo certifikátu Key Vault.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).
- [Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md).
