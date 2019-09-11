---
title: Použití aktualizace výrobce OEM (Original Equipment Manufacturer) na Azure Stack | Microsoft Docs
description: Naučte se, jak použít aktualizaci OEM (Original Equipment Manufacturer) na Azure Stack.
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
ms.openlocfilehash: 70fe7c8cdb43f55c8e013dcca27dcde870b1cc9a
ms.sourcegitcommit: dc633e862d49412a963daee481226c1543287e5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862965"
---
# <a name="azure-stack-servicing-policy"></a>Zásady obsluhy Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje zásady pro obsluhu Azure Stack integrovaných systémů, co je potřeba udělat, abyste zachovali systém v podporovaném stavu, a jak získat podporu.

## <a name="keep-your-system-under-support"></a>Zachování systému v rámci podpory

Chcete-li nadále získávat podporu, je nutné aktualizace udržovat Azure Stack aktuální.

Aby instance Azure Stack zůstala v podporovaném stavu, musí instance běžet z poslední vydané verze aktualizace nebo spustit jednu z těchto dvou předchozích verzí aktualizace.

Opravy hotfix nejsou považovány za hlavní verze aktualizací. Pokud je vaše instance Azure Stack za *více než dvěma aktualizacemi*, je považována za nedodržující předpisy. Aby bylo možné získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi.

Pokud je například poslední dostupná verze aktualizace 1904 a předchozí dva aktualizační balíčky byly verze 1903 a 1902, zůstane podpora 1902 i 1903. 1901 ale nepodporují. Zásada má hodnotu true, pokud není k dispozici žádná verze na měsíc nebo dvě. Pokud je například aktuální verze 1807 a nebyla vydána žádná verze 1806, v rámci podpory zůstanou předchozí dva balíčky aktualizací 1805 a 1804.

Balíčky aktualizací softwaru společnosti Microsoft jsou nekumulativní a vyžadují jako součást předchozí balíček aktualizace nebo opravu hotfix. Pokud se rozhodnete jednu nebo více aktualizací odložit, zvažte celkový modul runtime, pokud chcete získat nejnovější verzi.

## <a name="get-support"></a>Získat podporu

Azure Stack se řídí stejným procesem podpory jako Azure. Podnikoví zákazníci mohou postupovat podle postupu popsaného v tématu [Postup vytvoření žádosti o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Pokud jste zákazníkem poskytovatele cloudové služby (CSP), požádejte o podporu svého CSP. Další informace najdete v nejčastějších dotazech k [podpoře Azure](https://azure.microsoft.com/support/faq/).

# <a name="next-steps"></a>Další postup

[Správa aktualizací ve službě Azure Stack](azure-stack-updates.md)
