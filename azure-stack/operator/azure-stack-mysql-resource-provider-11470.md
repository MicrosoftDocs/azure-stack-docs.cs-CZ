---
title: 1\.1.47.0 zpráva k vydání verze poskytovatele prostředků MySQL centra pro Azure Stack | Microsoft Docs
description: Podívejte se na poznámky k verzi a zjistěte, co je nového ve 1.1.47.0 aktualizace poskytovatele prostředků MySQL centra pro Azure Stack.
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
ms.openlocfilehash: 32ac5cb56df9fc860bd2873c818cabb93723a170
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804892"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Poznámky k verzi pro poskytovatele prostředků MySQL 1.1.47.0

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

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

Tato verze poskytovatele prostředků Azure Stack hub MySQL je verzí oprav hotfix, která umožňuje poskytovateli prostředků kompatibilní s některými nejnovějšími změnami portálu v aktualizaci 1910 bez jakékoli nové funkce.

Podporuje také aktuální nejnovější 2019-03-01 profil verze rozhraní API centra Azure Stack – HYBRID a Azure Stack hub 1.8.0 modul PowerShellu. Takže během nasazování a aktualizace není nutné instalovat žádné konkrétní verze historie modulů.

Doporučuje se použít opravu hotfix poskytovatele prostředků MySQL 1.1.47.0 po upgradu centra Azure Stack na verzi 1910.

## <a name="known-issues"></a>Známé problémy

Žádné.

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md). 
