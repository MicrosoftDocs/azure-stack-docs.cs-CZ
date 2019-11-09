---
title: Azure Stack ověření jako poznámky k verzi služby | Microsoft Docs
description: Azure Stack ověření jako poznámky k verzi služby.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: aa85310314a09db47f10424e84fe40e355bacb25
ms.sourcegitcommit: ed44d477b9fd11573d1e0d1ed3a3c0ef4512df53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846227"
---
# <a name="release-notes-for-validation-as-a-service"></a>Poznámky k verzi pro ověřování jako službu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

V tomto článku najdete poznámky k verzi pro ověřování Azure Stack jako služby.

## <a name="version-4353"></a>4\.3.5.3 verze

2019. listopadu 7

- Testování aktualizací obsahu
  - Ověřování aktualizací Azure Stack měsíčně (verze 5.1.46.0-> 5.1.49.0)
  - Ověření balíčku rozšíření OEM (verze 5.1.46.0-> 5.1.49.0)
  - Výsledky pro 5.1.46.0 se zachovají. Pokud jste v 5.1.46.0 úspěšně běželi, upozorněte vaashelp@microsoft.com při odesílání výsledků.

- Opravy chyb
  - Opravili jsme problém, kdy se nepovedlo spustit měsíční Azure Stack ověření aktualizace, pokud soubor Update. zip obsahuje speciální znaky.

- Známé problémy
  - Testy VaaS selžou, pokud se nenalezne MSTest. exe. Odstraníte
    1. CTRL + C agent v okně PowerShellu
    1. Zadejte MSTest. exe, abyste ověřili, že MSTest. exe je rozpoznaný program.
    1. Pokud MSTest. exe nerozpoznáte, zavřete aktuální okno prostředí PowerShell.
    1. Klikněte na Start (ne na hlavním panelu prostředí PowerShell), najděte PowerShell a otevřete ho jako správce.
    1. Zadejte MSTest. exe a ověřte, zda je k dispozici jako příkaz.
    1. Restartujte agenta a spusťte test znovu.
  - V některých případech bude modul simulace cloudu hlásit selhání pomocí \*ch testů virtuálních počítačů. Než se pokusíte znovu spustit, kontaktujte vaashelp@microsoft.com. 


2019. října 29

- Online dokumentace pro měsíční pracovní postup aktualizace Azure Stack a pracovní postup pro ověření balíčku OEM byly aktualizovány.

    Projděte si aktualizovanou dokumentaci, která ověří balíčky OEM a ověří aktualizace softwaru od Microsoftu.
- Aktualizace pracovního postupu VaaS: měsíční aktualizace Azure Stack (verze 5.1.30.0-> 5.1.46.0) – aktualizoval se pracovní postup testu měsíčního Azure Stack aktualizace.

    Pracovní postup už nevyžaduje ruční zásah a dá se naplánovat na bezproblémové spuštění.
- Aktualizace pracovního postupu VaaS: ověření balíčku OEM (verze 5.1.30.0-> 5.1.46.0) – pracovní postup ověření balíčku OEM byl aktualizován.

    Pracovní postup už nevyžaduje ruční zásah a dá se naplánovat na bezproblémové spuštění.
- Modul pro simulaci cloudu v pracovním postupu ověření balíčku OEM (verze 5.1.30.0-> 5.1.46.0) byl aktualizován tak, aby urychlil čas ověření: doba běhu zkrácená na 1 hodinu.
- Modul pro simulaci cloudu v pracovním postupu ověření balíčku OEM a pracovní postup aktualizace Azure Stack (verze 5.1.30.0-> 5.1.46.0) vyžaduje, aby se aktualizace ověřily ve 2 různých nadřazených složkách bez dalších aktualizací v podřízených složkách.
- Modul pro simulaci cloudu v pracovním postupu ověření balíčku OEM a pracovní postup aktualizace služby Azure Stack (verze 5.1.30.0-> 5.1.46.0) vyžaduje, aby byly testy naplánovány v následujícím pořadí – měsíčně Azure Stack test ověření aktualizace, balíček rozšíření OEM Ověřovací test a nakonec modul simulace cloudu.
- Aktualizace agenta VaaS: aktualizovaný agent VaaS teď Azure Stack používá přihlašovací údaje správce cloudu k dotazování razítka k získání informací o razítku k automatickému naplnění pracovních postupů. 

    Tato aktualizace vyžaduje, aby všichni agenti aktualizovali a restartovali. Podívejte se prosím na tyto pokyny, jak aktualizovat agenta VaaS: https://docs.microsoft.com/en-us/azure-stack/partner/azure-stack-vaas-local-agent
- Aktualizace uživatelského rozhraní portálu VaaS: tabulka výběru agenta se přesunula nad podokno testovací plánování, aby se usnadnilo testování.

    Při plánování úlohy už není potřeba zadávat informace o razítkě, pokud byly agenti VaaS správně aktualizované.


## <a name="version-405"></a>4\.0.5 verze

2019. června 7

- Modul pro simulaci cloudu v pracovním postupu ověření balíčku byl aktualizován, aby urychlil čas ověření:  
    Doba běhu: zmenšeno na 6 hodin  
    Verze: 5.1.13.0-> 5.1.22.0  


2019. ledna 17

- Test identifikace disku se aktualizoval pro řešení nekonzistencí fondu úložiště. Verze: 5.1.14.0-> 5.1.15.0
- Azure Stack aktualizace měsíčního ověření aktualizací pro řešení schváleného softwaru a nekonzistencí ověření obsahu. Verze: 5.1.14.0-> 5.1.17.0
- Ověření balíčku rozšíření OEM bylo aktualizováno pro provedení nezbytných kontrol před krokem aktualizace Azure Stack. Verze: 5.1.14.0-> 5.1.16.0
- Opravy interních chyb

## <a name="version-402"></a>4\.0.2 verze

2019. ledna 7

Pokud používáte pracovní postup aktualizace Azure Stack měsíčně a verze balíčku OEM není 1810 nebo vyšší, zobrazí se chyba, až se dostanete k kroku aktualizace OEM. Jedná se o chybu. Vyvíjí se oprava. Postup zmírnění je následující:

1. Spusťte aktualizaci OEM jako normální.
2. Po úspěšném použití balíčku spusťte test-AzureStack a uložte výstup.
3. Zrušte test.
4. Odešlete uložený výstup do VaaSHelp@microsoft.com pro příjem výsledků pro spuštění.

## <a name="version-402"></a>4\.0.2 verze

30. listopadu 2018

- Opravy interních chyb

## <a name="version-401"></a>4\.0.1 verze

2018. října 8

- Požadavky na VaaS

    `Install-VaaSPrerequisites` už nevyžadují přihlašovací údaje správce cloudu. Pokud používáte nejnovější verzi této rutiny, přečtěte si téma [Stažení a instalace místního agenta](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) pro revidované příkazy pro instalaci požadovaných součástí. Tady jsou příkazy:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Verze 4.0.0

2018. srpna

- VaaS předpoklady a aktualizace VHD

    `Install-VaaSPrerequisites` teď vyžaduje přihlašovací údaje správce cloudu k vyřešení problému při ověřování balíčku. Dokumentace ke [Stažení a instalaci místního agenta](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) byla aktualizována následujícím způsobem:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > `$CloudAdminCreds` vyžadované skriptem jsou pro ověřování instance Azure Stack. Nejedná se o Azure Active Directory přihlašovací údaje, které používá tenant VaaS.

- Aktualizace místního agenta

    Předchozí verze místního agenta není kompatibilní s aktuálním vydáním služby 4.0.0. Všichni uživatelé musí aktualizovat své místní agenty. Pokyny k instalaci nejnovějšího agenta najdete v tématu [Stažení a instalace místního agenta](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) .

- Aktualizace automatizace PowerShellu

    Byly provedeny změny `LaunchVaaSTests` skriptů PowerShellu, které vyžadují nejnovější verzi skriptovacích balíčků. Pokyny k instalaci nejnovější verze balíčku Scripting najdete v tématu [spuštění pracovního postupu test Pass](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) .

- Ověřování jako portál služby

  - Oznámení podepisování balíčků

    Při odeslání balíčku pro přizpůsobení OEM jako součást pracovního postupu ověření balíčku se ověří formát balíčku, aby se zajistilo, že bude postupovat podle publikované specifikace. Pokud balíček nevyhovuje, spuštění se nezdaří. E-mailová oznámení se odesílají na e-mailovou adresu zaregistrovaného Azure Active Directory kontaktu pro tenanta.

  - Kategorie interaktivního testu

    Byla přidána kategorie **interaktivního** testu. Tyto testy cvičení využívají interaktivní, neautomatizované Azure Stack scénáře.

  - Interaktivní ověřování funkcí

    Možnost poskytnout fokus na určité funkce je teď dostupná v pracovním postupu test Pass. Test `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` kontroluje, zda byly konkrétní aktualizace správně aplikovány, a poté shromažďuje zpětnou vazbu.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s ověřováním jako službou](azure-stack-vaas-troubleshoot.md)
