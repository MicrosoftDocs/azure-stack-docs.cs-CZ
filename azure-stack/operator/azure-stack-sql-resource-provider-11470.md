---
title: Azure Stack 1.1.47.0 zpráva k vydání verze poskytovatele prostředků SQL | Microsoft Docs
description: Přečtěte si o tom, co je v nejnovější aktualizaci poskytovatele prostředků SQL Azure Stack, včetně všech známých problémů a místa, kde si je můžete stáhnout.
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
ms.openlocfilehash: 703edc0cc2c5229c3301db0b68a400507fe4c872
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823489"
---
# <a name="sql-resource-provider-11470-release-notes"></a>1\.1.47.0 poznámky k verzi poskytovatele prostředků SQL

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi 1.1.47.0 poskytovatele prostředků SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení Azure Stack. Minimální verze Azure Stack vydaná pro instalaci této verze poskytovatele prostředků SQL je uvedená níže:

> |Minimální verze Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 1910 (1.1910.0.58)|[SQL RP verze 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků SQL použijte minimální podporovanou aktualizaci Azure Stack v integrovaném systému Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků Azure Stack SQL je vydáním oprav hotfix, aby byl poskytovatel prostředků kompatibilní s některými nejnovějšími změnami portálu v aktualizaci 1910 bez jakékoli nové funkce.

Podporuje také aktuální nejnovější Azure Stack profil verze rozhraní API 2019-03-01 – Hybrid a Azure Stack 1.8.0 modulu PowerShellu. Takže během nasazování a aktualizace není nutné instalovat žádné konkrétní verze historie modulů.

Postupujte prosím podle procesu aktualizace poskytovatele prostředků, abyste použili opravu hotfix poskytovatele prostředků SQL 1.1.47.0 po Azure Stack upgradu na aktualizaci 1910. Pomůže vyřešit známý problém na portálu pro správu, kde se načítají monitorování kapacity v poskytovateli prostředků SQL.

## <a name="known-issues"></a>Známé problémy

Žádné.

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md). 
