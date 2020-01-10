---
title: 1\.1.47.0 zpráva k vydání verze poskytovatele prostředků SQL centra pro Azure Stack | Microsoft Docs
description: Přečtěte si, co je v nejnovější aktualizaci poskytovatele prostředků SQL centra Azure Stack, včetně všech známých problémů a místa, kde ho stáhnout.
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
ms.openlocfilehash: 949a0eee848c5ad6a123e3d4bd8ae52c9819d23e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814622"
---
# <a name="sql-resource-provider-11470-release-notes"></a>1\.1.47.0 poznámky k verzi poskytovatele prostředků SQL

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi 1.1.47.0 poskytovatele prostředků SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků SQL, je uvedena níže:

> |Minimální verze centra Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 1910 (1.1910.0.58)|[SQL RP verze 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků SQL použijte minimální podporovanou aktualizaci centra Azure Stack do integrovaného systému služby Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato verze poskytovatele prostředků SQL centra Azure Stack je vydáním opravy hotfix, které umožňuje poskytovateli prostředků kompatibilní s některými nejnovějšími změnami portálu v aktualizaci 1910 bez jakékoli nové funkce.

Podporuje také aktuální nejnovější 2019-03-01 profil verze rozhraní API centra Azure Stack – HYBRID a Azure Stack hub 1.8.0 modul PowerShellu. Takže během nasazování a aktualizace není nutné instalovat žádné konkrétní verze historie modulů.

Postupujte prosím podle procesu aktualizace poskytovatele prostředků, abyste použili opravu hotfix poskytovatele prostředků SQL 1.1.47.0 po upgradu centra Azure Stack na aktualizaci 1910. Pomůže vyřešit známý problém na portálu pro správu, kde se načítají monitorování kapacity v poskytovateli prostředků SQL.

## <a name="known-issues"></a>Známé problémy

Žádné.

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md). 
