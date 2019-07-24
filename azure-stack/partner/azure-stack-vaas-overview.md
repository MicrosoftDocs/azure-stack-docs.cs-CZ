---
title: Přehled ověřování jako služby pro Azure Stack | Microsoft Docs
description: Přehled ověřování Azure Stack jako služby.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d1cfc5c9c378ccfc48811c5896337ef91fdca2e8
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418552"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Co je ověřování jako služba pro Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ověřování jako služba (VaaS) je nativní služba Azure určená pro partnery řešení, kteří jsou spolustrojírenství Azure Stack nabídky s Microsoftem. Partneři řešení můžou službu využít ke kontrole, že jejich řešení splňují požadavky Microsoftu a fungují podle očekávání Azure Stack.

Primární použití pro VaaS jsou:

- Ověřování nových řešení Azure Stack
- Ověřování změn Azure Stack softwaru
- Digitální podepisování balíčků pro partnerských řešení používaných při nasazení
- Náhled VaaS testu

## <a name="validate-a-new-azure-stack-solution"></a>Ověřit nové řešení Azure Stack

Partneři používají pracovní postup **ověření řešení** k ověření nových řešení Azure Stack. Řešení musí předat požadovanou HLK sadu hardwarových testovacích sad () Azure Stack testy komponent. Pro certifikaci rozsahu hardwarových konfigurací je potřeba pracovní postup spustit dvakrát pro každé nové řešení: jednou pro minimální a maximální konfiguraci.

Další informace najdete v tématu [ověření nového řešení Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Ověřit změny softwaru Azure Stack

Partneři používají pracovní postup **ověření balíčku** ke kontrole, zda jejich řešení funguje s nejnovějšími aktualizacemi Azure Stack softwaru. Pracovní postup ověření balíčku se musí spustit v hardwarovém prostředí, které se doporučuje od Microsoftu, kde se aktualizace použila pro opravu a aktualizaci (P & U). Doporučuje se také spustit pracovní postup na směrném buildu.

Další informace najdete v tématu [ověření aktualizací softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Získat digitálně podepsané balíčky partnerů řešení

Kromě ověřování aktualizací Azure Stack partneři pomocí pracovního postupu **ověření balíčku** ověří aktualizace balíčků pro přizpůsobení OEM, které zahrnují Azure Stack ovladače, firmware a další software, který se používá během nasazení Azure Stack software. Nasaďte balíček, který ověřujete v aktuální verzi Azure Stack softwaru, a to s využitím aspoň minimálního řešení, které se bude podporovat. Balíček se před provedením testů odešle do VaaS. Pokud jsou testy úspěšné, upozorněte [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) na to, že balíček dokončil testování a měl by být digitálně podepsaný digitálním podpisem Azure Stack. Microsoft podepíše balíček a oznámí Azure Stack partnerovi, že balíček je k dispozici ke stažení na portálu VaaS.

Další informace najdete v tématu [ověření balíčků OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Náhled VaaS testovacích materiálů

Microsoft pravidelně poskytuje nové funkce, které jsou k dispozici v Azure Stack. V rámci procesu vývoje pro poskytování těchto funkcí na trh je k dispozici nová zkušební dokumentace v pracovním postupu **test Pass** . Pracovní postup testovacího průchodu zahrnuje testovací materiály z jiných pracovních postupů, aby bylo možné provést neoficiální provádění testů. Nepoužívejte pracovní postup test Pass k odeslání výsledků ke schválení. Použijte ověřovací pracovní postupy řešení a ověření balíčku, abyste získali oficiální schválení pro vaše řešení.

Další informace najdete v tématu [rychlý Start: Použijte ověřování jako portál služby k naplánování prvního testu](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Souhrn testů pracovního postupu ověřování

| Pracovní postup ověření | Požadované testy |
|----|------------|
| [Nové ověřování řešení](azure-stack-vaas-validate-solution-new.md) | Modul pro simulaci cloudu<br>Sada COMPUTE SDK Operational Suite<br>Test identifikace disku<br>Operační sada sady SDK rozšíření trezoru klíčů<br>Sada SDK pro Trezor klíčů – operační sada<br>Operační sada sady SDK sítě<br>Sada SDK pro účet úložiště – operační sada<br> |
| [Ověření balíčku OEM](azure-stack-vaas-validate-oem-package.md) | Ověření balíčku rozšíření OEM<br>Modul pro simulaci cloudu |
| [Ověřování měsíčních aktualizací](azure-stack-vaas-validate-microsoft-updates.md) | Ověřování aktualizací Azure Stack měsíčně<br>Modul pro simulaci cloudu<br> |

## <a name="next-steps"></a>Další postup

- [Nastavení ověřování jako prostředků služby](azure-stack-vaas-set-up-resources.md)
- Přečtěte si o [principech ověření jako Key služby](azure-stack-vaas-key-concepts.md)
