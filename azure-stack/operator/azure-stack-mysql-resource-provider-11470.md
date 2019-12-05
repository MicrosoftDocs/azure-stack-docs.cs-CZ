---
title: 1\.1.47.0 poznámky k verzi poskytovatele prostředků MySQL | Azure Stack Microsoft Docs
description: Podívejte se na poznámky k verzi a zjistěte, co je nového Azure Stack ve 1.1.47.0 aktualizace poskytovatele prostředků MySQL.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 979aa822c981b4d34cb430e69dc15f80661818fe
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823478"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Poznámky k verzi pro poskytovatele prostředků MySQL 1.1.47.0

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků MySQL verze 1.1.47.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků MySQL a potom spusťte samočinného extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení Azure Stack. Minimální verze Azure Stack vydaná pro instalaci této verze poskytovatele prostředků MySQL je uvedena níže:

> |Minimální verze Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Verze 1910 (1.1910.0.58)|[MySQL RP verze 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků MySQL použijte minimální podporovanou aktualizaci Azure Stack v integrovaném systému Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků Azure Stack MySQL je verzí oprav hotfix, která umožňuje poskytovateli prostředků kompatibilní s některými nejnovějšími změnami portálu v aktualizaci 1910 bez nové funkce.

Podporuje také aktuální nejnovější Azure Stack profil verze rozhraní API 2019-03-01 – Hybrid a Azure Stack 1.8.0 modulu PowerShellu. Takže během nasazování a aktualizace není nutné instalovat žádné konkrétní verze historie modulů.

Doporučuje se použít opravu hotfix poskytovatele prostředků MySQL 1.1.47.0 po upgradu Azure Stack na verzi 1910.

## <a name="known-issues"></a>Známé problémy

Žádné.

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md). 
