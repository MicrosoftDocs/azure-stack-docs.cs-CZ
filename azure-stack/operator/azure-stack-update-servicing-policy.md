---
title: Použití aktualizace výrobce OEM (Original Equipment Manufacturer) pro Azure Stack hub
description: Naučte se, jak použít aktualizaci výrobce OEM (Original Equipment Manufacturer) na Azure Stack hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppace
ms.openlocfilehash: 3a8f63772c5aa6b34e20da2dbea13ea994c7de16
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696647"
---
# <a name="azure-stack-hub-servicing-policy"></a>Zásady obsluhy centra Azure Stack

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
