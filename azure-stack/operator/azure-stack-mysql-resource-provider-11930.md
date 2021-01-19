---
title: 1.1.93.0 zpráva k vydání verze poskytovatele prostředků MySQL centra pro Azure Stack
description: Podívejte se na poznámky k verzi a zjistěte, co je nového ve 1.1.93.0 aktualizace poskytovatele prostředků MySQL centra pro Azure Stack.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 225c765642dc48b299c899d71eb0d0ce13d4852d
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571361"
---
# <a name="mysql-resource-provider-1193x-release-notes"></a>Poznámky k verzi 1.1.93. x poskytovatele prostředků MySQL

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků MySQL verze 1.1.93. x.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků MySQL a potom spusťte samočinného extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků MySQL, je uvedena níže:

> |Podporovaná verze centra Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Verze 2008, 2005|[MySQL RP verze 1.1.93.1](https://aka.ms/azshmysqlrp11931)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků MySQL použijte pro integrovaný systém služby Azure Stack hub minimální podporovanou aktualizaci centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků centra Azure Stacku MySQL zahrnuje následující vylepšení a opravy:

- **Aktualizujte základní virtuální počítač na specializovaný Windows Server.** Tato verze Windows serveru je specializovaná pro infrastrukturu centra Azure Stack Add-On RP a není viditelná pro tržiště tenanta. Před nasazením nebo upgradem na tuto verzi poskytovatele prostředků MySQL Nezapomeňte stáhnout image **Microsoft AzureStack Add-On RP Windows serveru** .
- **Podpora odebrání osamocených metadat databáze a metadat hostitelských serverů.** Pokud již nelze hostitelský server připojit, bude mít klient možnost odebrat Osamocená metadata databáze z portálu. Pokud nejsou žádná Osamocená metadata databáze propojená s hostitelským serverem, operátor bude moci odebrat Osamocená metadata hostitelského serveru z portálu pro správu.
- **Při provádění rotace tajných kódů nastavte KeyVaultPfxPassword nepovinný argument.** Podrobnosti najdete v [tomto dokumentu](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) .
- **Další opravy chyb.**

Doporučuje se použít poskytovatele prostředků MySQL 1.1.93.1 po upgradu centra Azure Stack na verzi 2005.

## <a name="known-issues"></a>Známé problémy
Pokud se použije nesprávná AzureRmContext, nasazení verze 1.1.93.0 se nemusí zdařit. Doporučuje se upgradovat na verzi 1.1.93.1 přímo. Pokud jste už úspěšně provedli upgrade na 1.1.93.0, můžete 1.1.93.1 verzi bezpečně přeskočit.

Při opětovném nasazení poskytovatele prostředků MySQL v době, kdy byla stejná verze již nasazena (například když je již nasazen poskytovatel prostředků MySQL 1.1.93.1 a je znovu nasazena stejná verze), bude zastaven virtuální počítač, který je hostitelem poskytovatele prostředků MySQL. Pokud chcete tento problém vyřešit, přejděte na portál pro správu a vyhledejte a restartujte virtuální počítač s názvem mysqlvm \<version\> ve skupině prostředků s názvem System. \<region\> . mysqladapter.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).
- [Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md).
