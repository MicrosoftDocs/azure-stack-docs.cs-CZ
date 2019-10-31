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
ms.openlocfilehash: 0ac21cc388b55be6548f9fdba6c8985dd2316c4e
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167133"
---
# <a name="release-notes-for-validation-as-a-service"></a>Poznámky k verzi pro ověřování jako službu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

V tomto článku najdete poznámky k verzi pro ověřování Azure Stack jako služby.

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
