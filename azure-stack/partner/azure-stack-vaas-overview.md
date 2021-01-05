---
title: Přehled ověřování jako služby
titleSuffix: Azure Stack Hub
description: Přehled Azure Stack ověřování centra jako služby.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 89e05e776538ab2032995634e69d704b1ae4981b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868178"
---
# <a name="what-is-validation-as-a-service-for-azure-stack-hub"></a>Co je ověřování jako služba pro centrum Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ověřování jako služba (VaaS) je nativní služba Azure určená pro partnery řešení, kteří jsou spolustrojírenství v Azure Stack centra s Microsoftem. Partneři řešení můžou službu využít ke kontrole, že jejich řešení splňují požadavky Microsoftu a fungují podle očekávání pomocí centra Azure Stack.

Primární použití pro VaaS jsou:

- Ověřují se nová řešení centra Azure Stack.
- Ověřování změn v softwaru centra Azure Stack.
- Umožňuje digitálně podepisovat balíčky partnerů pro řešení používané při nasazení.
- Náhled VaaS testovacích materiálů.

## <a name="validate-a-new-azure-stack-hub-solution"></a>Ověřit nové řešení centra Azure Stack

Partneři používají pracovní postup **ověření řešení** k ověření nových řešení centra Azure Stack. Řešení musí předat požadovanou HLK sadu hardwarových testovacích sad () Azure Stackch testů komponent centra. Pro certifikaci rozsahu hardwarových konfigurací je potřeba pracovní postup spustit dvakrát pro každé nové řešení: jednou pro minimální a maximální konfiguraci.

Další informace najdete v tématu [ověření nového řešení centra Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-hub-software"></a>Ověřit změny softwaru centra Azure Stack

Partneři používají pracovní postup **ověření balíčku** ke kontrole, že jejich řešení funguje s nejnovějšími aktualizacemi softwaru centra Azure Stack. Pracovní postup ověření balíčku se musí spustit v hardwarovém prostředí, které se doporučuje od Microsoftu, kde se aktualizace použila pro opravu a aktualizaci (P&U). Doporučuje se také spustit pracovní postup na směrném buildu.

Další informace najdete v tématu [ověření aktualizací softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Získat digitálně podepsané balíčky partnerů řešení

Kromě ověřování Azure Stackch aktualizací centra partneři pomocí pracovního postupu **ověření balíčku** ověří aktualizace balíčků pro přizpůsobení OEM. Tyto aktualizace zahrnují ovladače, firmware a další software, který se používá při nasazování softwaru Azure Stack hub, do centra Azure Stack. Nasaďte balíček, který ověřujete v aktuální verzi softwaru Azure Stack hub, pomocí aspoň řešení s minimální velikostí, které se bude podporovat. Balíček se před provedením testů odešle do VaaS. Pokud jsou testy úspěšné, upozorněte [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) na to, že balíček dokončil testování a měl by být digitálně podepsaný pomocí digitálního podpisu centra Azure Stack. Microsoft podepíše balíček a oznámí partnerovi centra Azure Stack, že balíček je dostupný ke stažení na portálu pro ověřování centra Azure Stack.

Další informace najdete v tématu [ověření balíčků OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Náhled VaaS testovacích materiálů

Microsoft pravidelně poskytuje nové funkce, které jsou dostupné v Azure Stack hub. V rámci procesu vývoje pro poskytování těchto funkcí na trh je k dispozici nová zkušební dokumentace v pracovním postupu **test Pass** . Pracovní postup testovacího průchodu zahrnuje testovací materiály z jiných pracovních postupů, aby bylo možné provést neoficiální provádění testů. Nepoužívejte pracovní postup test Pass k odeslání výsledků ke schválení. Použijte ověřovací pracovní postupy řešení a ověření balíčku, abyste získali oficiální schválení pro vaše řešení.

Další informace najdete v tématu [rychlý Start: použití portálu pro ověřování centra Azure Stack k naplánování prvního testu](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Souhrn testů pracovního postupu ověřování

| Pracovní postup ověření | Požadované testy |
|----|------------|
| [Nové ověřování řešení](azure-stack-vaas-validate-solution-new.md) | Modul pro simulaci cloudu<br>Sada COMPUTE SDK Operational Suite<br>Test identifikace disku<br>Operační sada sady SDK rozšíření trezoru klíčů<br>Sada SDK pro Trezor klíčů – operační sada<br>Operační sada sady SDK sítě<br>Sada SDK pro účet úložiště – operační sada<br> |
| [Ověření balíčku OEM](azure-stack-vaas-validate-oem-package.md) | Ověření balíčku rozšíření OEM<br>Modul pro simulaci cloudu |
| [Ověřování měsíčních aktualizací](azure-stack-vaas-validate-microsoft-updates.md) | Ověřování aktualizací centra Azure Stack měsíčně<br>Modul pro simulaci cloudu<br> |

## <a name="next-steps"></a>Další kroky

- [Nastavení ověřování jako prostředků služby](azure-stack-vaas-set-up-resources.md)
- Přečtěte si o [principech ověření jako Key služby](azure-stack-vaas-key-concepts.md)
