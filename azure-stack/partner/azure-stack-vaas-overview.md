---
title: Přehled ověřování jako služba pro službu Azure Stack | Dokumentace Microsoftu
description: Přehled Azure Stack ověření jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ca22f29597cb452d6d33338b8ba0367c9377f6fb
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297917"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Co je ověření jako služba pro službu Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ověření jako služba (VaaS) je navržená pro řešení partnerů, kteří jsou společně technické nabídek Azure Stack s Microsoftem nativní služby Azure. Partneři řešení můžete použít službu Pokud chcete zkontrolovat, že svá řešení požadavkům Microsoftu na a fungovat podle očekávání pomocí služby Azure Stack.

Primární použití pro VaaS jsou:

- Ověřuje se nová řešení Azure Stack
- Ověřování změn v softwaru Azure Stack
- Digitální podepisování balíčků partnerských řešení používá při nasazení
- Zajištění testu VaaS náhledu

## <a name="validate-a-new-azure-stack-solution"></a>Ověření nové řešení Azure Stack

Partneři použití **ověření řešení** pracovní postup pro ověření nového řešení služby Azure Stack. Řešení musí projít testy požadovaných součástí Hardware Lab Kit sady HLK () Azure Stack. Chcete-li certifikovat celou řadu konfigurací hardwaru, je potřeba použít pracovní postup dvakrát pro každé nové řešení: jednou každý konfigurace minimální a maximální.

Další informace najdete v tématu [ověřit nové řešení Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Ověření změn softwaru Azure Stack

Partneři použití **ověřování balíčku** pracovního postupu ke kontrole, jestli jejich řešení funguje s nejnovější aktualizací softwaru Azure Stack. Pracovní postup ověření balíčku je třeba spustit na Microsoft doporučuje hardwarové prostředí použití opravy a aktualizace (P & U) k použití aktualizace. Doporučujeme také spustit pracovní postup při sestavování směrného plánu.

Další informace najdete v tématu [ověření aktualizace softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Získání řešení digitálně podepsané balíčky partnera

Kromě ověřování aktualizace služby Azure Stack, partnery použijte **ověřování balíčku** pracovní postup pro ověření aktualizace balíčků přizpůsobení výrobce OEM, mezi které patří Azure Stack partnera konkrétní ovladače, firmware a další software používá se při nasazení softwaru Azure Stack. Nasazení balíčku, který se ověřuje na aktuální verze softwaru Azure Stack pomocí alespoň na minimální velikost řešení, které budou podporovat. Balíček se odešle do VaaS před spuštěním testů. Pokud testy úspěšné, upozorní [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) , že balíček dokončil testování a by měl být digitálně podepsané pomocí služby Azure Stack digitální podpis. Microsoft podepíše balíček a upozorní partnera Azure Stack, že balíček je k dispozici ke stažení na portálu VaaS.

Další informace najdete v tématu [ověřit OEM balíčky](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Zajištění testu VaaS ve verzi Preview

Microsoft pravidelně zpřístupňuje nové funkce ve službě Azure Stack. Jako součást procesu vývoje pro doručování tyto funkce na trh, je k dispozici v nové testovací zajištění **průchodu testů** pracovního postupu. Pracovní postup průchodu testu obsahuje testovací zajištění z jiných pracovních postupů umožňující provádění neoficiální testů. Nepoužívejte pracovního postupu průchodu testů odeslat výsledky ke schválení. Pomocí pracovních postupů řešení ověřování a ověřování balíčku můžete získat oficiální schválení pro vaše řešení.

Další informace najdete v tématu [rychlý start: Použít ověření jako portál Service k naplánování vaší první test](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Souhrn testů ověření pracovního postupu

| Pracovní postup ověření | Požadované testy |
|----|------------|
| [Nové řešení ověřování](azure-stack-vaas-validate-solution-new.md) | Simulace modulu cloudu<br>COMPUTE provozní sady SDK<br>Test identifikace disku<br>Provozní sady SDK rozšíření služby KeyVault<br>Provozní sady SDK služby KeyVault<br>Provozní sadě SDK sítě<br>Provozní sady SDK účtu úložiště<br> |
| [Ověření balíčku výrobce OEM](azure-stack-vaas-validate-oem-package.md) | Ověřování balíčků rozšíření výrobce OEM<br>Simulace modulu cloudu |
| [Ověření měsíční aktualizace](azure-stack-vaas-validate-microsoft-updates.md) | Měsíční aktualizace ověřovací služby Azure Stack<br>Simulace modulu cloudu<br> |

## <a name="next-steps"></a>Další postup

- [Nastavení ověření jako prostředky služby](azure-stack-vaas-set-up-resources.md)
- Další informace o [ověření jako klíčové koncepty služby](azure-stack-vaas-key-concepts.md)
