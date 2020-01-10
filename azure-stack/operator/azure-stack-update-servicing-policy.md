---
title: Použití aktualizace výrobce OEM (Original Equipment Manufacturer) na centra Azure Stack | Microsoft Docs
description: Naučte se, jak použít aktualizaci výrobce OEM (Original Equipment Manufacturer) na Azure Stack hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: f3beee51b6971a62ac24a2787426de235c879792
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75813398"
---
# <a name="azure-stack-hub-servicing-policy"></a>Zásady obsluhy centra Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra*

Tento článek popisuje zásady obsluhy pro integrované systémy Azure Stack hub, co je potřeba udělat, abyste zachovali systém v podporovaném stavu, a jak získat podporu.

## <a name="keep-your-system-under-support"></a>Zachování systému v rámci podpory

Chcete-li nadále získávat podporu, je třeba zachovat aktualizace v centru Azure Stack aktuální.

Aby systém Azure Stack hub zůstal v podporovaném stavu, musí instance běžet z poslední vydané verze aktualizace nebo spustit jednu z těchto dvou předchozích verzí aktualizace.

Opravy hotfix nejsou považovány za hlavní verze aktualizací. Pokud je váš systém Azure Stack hub za *více než dvěma aktualizacemi*, považuje se za nedodržování předpisů. Aby bylo možné získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi.

Pokud je například poslední dostupná verze aktualizace 1904 a předchozí dva aktualizační balíčky byly verze 1903 a 1902, zůstane podpora 1902 i 1903. 1901 ale nepodporují. Zásada má hodnotu true, pokud není k dispozici žádná verze na měsíc nebo dvě. Pokud je například aktuální verze 1807 a nebyla vydána žádná verze 1806, v rámci podpory zůstanou předchozí dva balíčky aktualizací 1805 a 1804.

Balíčky aktualizací softwaru společnosti Microsoft jsou nekumulativní a vyžadují jako součást předchozí balíček aktualizace nebo opravu hotfix. Pokud se rozhodnete jednu nebo více aktualizací odložit, zvažte celkový modul runtime, pokud chcete získat nejnovější verzi.

## <a name="get-support"></a>Získat podporu

Centrum Azure Stack se řídí stejným procesem podpory jako Azure. Podnikoví zákazníci mohou postupovat podle postupu popsaného v tématu [Postup vytvoření žádosti o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Pokud jste zákazníkem poskytovatele Cloud Solution Provider (CSP), požádejte o podporu svého CSP. Další informace najdete v nejčastějších dotazech k [podpoře Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

[Správa aktualizací v centru Azure Stack](azure-stack-updates.md)
