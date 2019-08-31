---
title: Zásady pro obsluhu Azure Stack | Microsoft Docs
description: Přečtěte si o zásadách pro obsluhu Azure Stack a o tom, jak zachovat integrovaný systém v podporovaném stavu.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1022ab056157ea1a9bc925d3992a99bd0b395a35
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188078"
---
- **Hardware výrobce OEM – aktualizace poskytované dodavatelem** Azure Stack hardwarovým partnerům zodpovídáme za kompletní životní cyklus údržby (včetně pokynů) pro balíčky pro firmware a aktualizace ovladačů souvisejících s hardwarem. Kromě toho Azure Stack hardwaroví partneři vlastní a udržovat pokyny pro veškerý software a hardware na hostiteli životního cyklu hardwaru. Dodavatel hardwaru OEM tyto balíčky aktualizací hostuje na svém vlastním webu pro stažení.

## <a name="update-package-release-cadence"></a>Aktualizace tempo verze balíčku

Microsoft očekává, že se balíčky aktualizací softwaru vydávají měsíčně tempo. Je ale možné, že v měsíci budete mít několik nebo žádné aktualizační verze. Výrobci hardwaru OEM vydávají své aktualizace podle potřeby.

Najděte si dokumentaci, jak naplánovat a spravovat aktualizace a jak zjistit aktuální verzi v tématu [Správa aktualizací – přehled](azure-stack-updates.md).

Informace o konkrétní aktualizaci, včetně toho, jak ji stáhnout, najdete v poznámkách k verzi této aktualizace:

- [Aktualizace Azure Stack 1908](azure-stack-release-notes-1908.md)
- [Aktualizace Azure Stack 1907](azure-stack-release-notes-1907.md)
- [Aktualizace Azure Stack 1906](azure-stack-release-notes-1906.md)
- [Aktualizace Azure Stack 1905](azure-stack-release-notes-1905.md)

## <a name="hotfixes"></a>Opravy hotfix

V některých případech společnost Microsoft poskytuje opravy hotfix pro Azure Stack, které řeší konkrétní problém, který je často preventivní nebo časově citlivý.  Každá oprava hotfix je vydána spolu s odpovídajícím článkem znalostní báze Microsoft Knowledge Base, který podrobně popisuje problém, příčinu a řešení.

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje zásady pro obsluhu Azure Stack integrovaných systémů, co je potřeba udělat, abyste zachovali systém v podporovaném stavu, a jak získat podporu.

## <a name="keep-your-system-under-support"></a>Zachování systému v rámci podpory

Aby instance Azure Stack zůstala v podporovaném stavu, musí instance běžet z poslední vydané verze aktualizace nebo spustit jednu z těchto dvou předchozích verzí aktualizace.

Opravy hotfix nejsou považovány za hlavní verze aktualizací. Pokud je vaše instance Azure Stack za *více než dvěma aktualizacemi*, je považována za nedodržující předpisy. Aby bylo možné získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi.

Pokud je například poslední dostupná verze aktualizace 1904 a předchozí dva aktualizační balíčky byly verze 1903 a 1902, zůstane podpora 1902 i 1903. 1901 ale nepodporují. Zásada má hodnotu true, pokud není k dispozici žádná verze na měsíc nebo dvě. Pokud je například aktuální verze 1807 a nebyla vydána žádná verze 1806, v rámci podpory zůstanou předchozí dva balíčky aktualizací 1805 a 1804.

Balíčky aktualizací softwaru společnosti Microsoft jsou nekumulativní a vyžadují jako součást předchozí balíček aktualizace nebo opravu hotfix. Pokud se rozhodnete jednu nebo více aktualizací odložit, zvažte celkový modul runtime, pokud chcete získat nejnovější verzi.

## <a name="get-support"></a>Získat podporu

Azure Stack se řídí stejným procesem podpory jako Azure. Podnikoví zákazníci mohou postupovat podle postupu popsaného v tématu [Postup vytvoření žádosti o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Pokud jste zákazníkem poskytovatele cloudové služby (CSP), požádejte o podporu svého CSP. Další informace najdete v nejčastějších dotazech k [podpoře Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další postup

- [Správa aktualizací ve službě Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
