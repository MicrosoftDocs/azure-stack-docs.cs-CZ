---
title: Poznámky k ověřování jako k verzi služby
titleSuffix: Azure Stack Hub
description: Poznámky k verzi pro ověřování centra Azure Stack jako službu.
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6f51b4720655159cc1b191c28b640daa74f71a6e
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764626"
---
# <a name="release-notes-for-validation-as-a-service"></a>Poznámky k verzi pro ověřování jako službu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

V tomto článku najdete poznámky k verzi pro ověřování centra Azure Stack jako službu.

## <a name="version-4432"></a>4.4.3.2 verze

2. února 2020

- Aktualizace údržby služby.

- Opravy chyb
  - Pevné testy Test101LinuxEmptyAttachedDiskManagedDisk, Test101WindowsEmptyAttachedDiskManagedDisk.

## <a name="version-4421"></a>4.4.2.1 verze

2020. ledna 9

- Test aktualizací obsahu:
  - Pracovní postup ověření výrobce OEM (verze 5.1.52.0-> 5.1.53.0): snížen počet požadovaných parametrů z podokna testovacího plánu.
  - Opravu chyb pro výpočetní výkon – TestVMOperations

- Známé problémy:
  - Kontaktujte vaashelp@microsoft.com , pokud se následující testovací případy nepodaří spustit během ověřovacího pracovního postupu výrobce OEM:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

2019 prosince 3

- Test aktualizací obsahu:
  - Online dokumentace pro měsíční pracovní postup aktualizace centra Azure Stack a pracovní postup ověření balíčku OEM byl aktualizován. Projděte si aktualizovanou dokumentaci [ověřit balíčky OEM](azure-stack-vaas-validate-oem-package.md) a [Ověřte aktualizace softwaru](azure-stack-vaas-validate-microsoft-updates.md) od Microsoftu.
  - Aktualizace pracovního postupu ověření balíčku VaaS: ověřovací pracovní postup OEM je jediným testem vyžadovaným pro měsíční Azure Stack ověřování aktualizací centra a ověření balíčku OEM. Test aktualizuje razítko pomocí poskytnutých balíčků AzureStack/OEM a spustí ověřovací testy modulu simulace cloudu.
  - Aktualizace rozšíření PowerShell VaaS: automatizace pracovních postupů ověření balíčku je teď podporovaná. Podrobné informace o umístění a podrobné pokyny k použití tohoto rozšíření najdete v tématu Azure Stack centra VaaS automatizovat pomocí prostředí PowerShell.

- Známé problémy:
  - Kontaktujte vaashelp@microsoft.com , pokud se následující testovací případy nepodaří spustit během ověřovacího pracovního postupu výrobce OEM:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk


## <a name="version-4353"></a>4.3.5.3 verze

2019. listopadu 7

- Test aktualizací obsahu:
  - Měsíční ověření aktualizace centra Azure Stack (verze 5.1.46.0-> 5.1.49.0).
  - Ověření balíčku rozšíření OEM (verze 5.1.46.0-> 5.1.49.0).
  - Výsledky pro 5.1.46.0 se zachovají. Pokud jste v 5.1.46.0 úspěšně spustili, upozorněte vaashelp@microsoft.com při odesílání výsledků.

- Opravy chyb
  - Opravili jsme problém, kdy se nepovedlo spustit měsíční Azure Stack ověření aktualizace centra, pokud soubor Update. zip obsahuje speciální znaky.

- Známé problémy
  - Pokud se mstest.exe nenajde, testy VaaS selžou. Alternativní řešení:
    1. CTRL + C agent v okně PowerShellu
    1. Zadejte mstest.exe a ověřte, že mstest.exe je rozpoznaný program.
    1. Pokud mstest.exe nerozpoznáte, zavřete aktuální okno prostředí PowerShell.
    1. Klikněte na Start (ne na hlavním panelu prostředí PowerShell), najděte PowerShell a otevřete ho jako správce.
    1. Zadejte mstest.exe a ověřte, že je k dispozici jako příkaz.
    1. Restartujte agenta a spusťte test znovu.
  - Občas modul pro simulaci cloudu ohlásí selhání s \* testy virtuálních počítačů. vaashelp@microsoft.comPřed pokusem o spuštění znovu kontaktujte.


2019. října 29

- Online dokumentace pro měsíční pracovní postup aktualizace centra Azure Stack a pracovní postup ověření balíčku OEM byl aktualizován.

    Projděte si aktualizovanou dokumentaci [ověřit balíčky OEM](azure-stack-vaas-validate-oem-package.md) a [Ověřte aktualizace softwaru](azure-stack-vaas-validate-microsoft-updates.md) od Microsoftu.
- Aktualizace pracovního postupu VaaS: měsíční aktualizace centra Azure Stack (verze 5.1.30.0-> 5.1.46.0) – aktualizoval se měsíční pracovní postup testování aktualizace centra Azure Stack.

    Pracovní postup už nevyžaduje ruční zásah a dá se naplánovat na bezproblémové spuštění.
- Aktualizace pracovního postupu VaaS: ověření balíčku OEM (verze 5.1.30.0-> 5.1.46.0) – pracovní postup ověření balíčku OEM byl aktualizován.

    Pracovní postup už nevyžaduje ruční zásah a dá se naplánovat na bezproblémové spuštění.
- Modul pro simulaci cloudu v pracovním postupu ověření balíčku OEM (verze 5.1.30.0-> 5.1.46.0) byl aktualizován tak, aby urychlil čas ověření: doba běhu zkrácená na 1 hodinu.
- Modul pro simulaci cloudu v pracovním postupu ověření balíčku OEM a pracovní postup aktualizace centra Azure Stack (verze 5.1.30.0-> 5.1.46.0) vyžaduje, aby se aktualizace ověřily ve dvou různých nadřazených složkách, které neobsahují žádné další aktualizace v podřízených složkách.
- Modul pro simulaci cloudu v pracovním postupu ověření balíčku OEM a pracovní postup aktualizace centra Azure Stack (verze 5.1.30.0-> 5.1.46.0) vyžaduje, aby testy byly naplánovány v následujícím pořadí – měsíčně Azure Stack test ověření aktualizace centra rozšíření OEM, test ověření balíčku rozšíření OEM a nakonec modul simulace cloudu.
- Aktualizace agenta VaaS: aktualizovaný agent VaaS teď používá přihlašovací údaje správce cloudu Azure Stack centra pro dotazování razítka k získání informací o razítku, aby bylo možné automaticky naplnit pracovní postupy.

    Tato aktualizace vyžaduje, aby všichni agenti aktualizovali a restartovali. Informace o tom, jak aktualizovat agenta VaaS, najdete v těchto pokynech: https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent
- Aktualizace uživatelského rozhraní portálu VaaS: tabulka výběru agenta se přesunula nad podokno testovací plánování, aby se usnadnilo testování.

    Při plánování úlohy už nemusíte zadávat informace o razítkě, pokud byly agenti VaaS správně aktualizované.

## <a name="version-405"></a>4.0.5 verze

2019. června 7

- Modul pro simulaci cloudu v pracovním postupu ověření balíčku byl aktualizován, aby urychlil čas ověření:  
    Doba běhu: zmenšeno na 6 hodin  
    Verze: 5.1.13.0-> 5.1.22.0  


2019. ledna 17

- Test identifikace disku se aktualizoval pro řešení nekonzistencí fondu úložiště. Verze: 5.1.14.0-> 5.1.15.0
- Aktualizace měsíčního ověřování centra Azure Stack se aktualizovala, aby se vyřešilo schválené softwarové a nekonzistence ověření obsahu. Verze: 5.1.14.0-> 5.1.17.0
- Ověření balíčku rozšíření OEM bylo aktualizováno pro provedení nezbytných kontrol před krokem aktualizace centra Azure Stack. Verze: 5.1.14.0-> 5.1.16.0
- Opravy interních chyb.

## <a name="version-403"></a>4.0.3 verze

2019. ledna 7

Pokud používáte pracovní postup měsíčního ověření aktualizace centra Azure Stack a verze balíčku OEM pro aktualizace není 1810 nebo vyšší, zobrazí se při obdržení kroku aktualizace OEM chyba. Tato chyba je chybou. Vyvíjí se oprava. Postup zmírnění je následující:

1. Spusťte aktualizaci OEM jako normální.
2. Po úspěšném použití balíčku spusťte test-AzureStack a uložte výstup.
3. Zrušte test.
4. Odešlete uložený výstup do VaaSHelp@microsoft.com pro příjem výsledků pro spuštění.

## <a name="version-402"></a>4.0.2 verze

30. listopadu 2018

- Opravy interních chyb.

## <a name="version-401"></a>4.0.1 verze

2018. října 8

- Předpoklady pro VaaS:

    `Install-VaaSPrerequisites` už nevyžaduje přihlašovací údaje správce cloudu. Pokud používáte nejnovější verzi této rutiny, přečtěte si téma [Stažení a instalace místního agenta](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) pro revidované příkazy pro instalaci požadovaných součástí. Tady jsou příkazy:

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

- VaaS předpoklady a aktualizace VHD:

    `Install-VaaSPrerequisites` nyní vyžaduje přihlašovací údaje správce cloudu k vyřešení problému při ověřování balíčku. Dokumentace ke [Stažení a instalaci místního agenta](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) byla aktualizována pomocí následujícího kódu:

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
    > `$CloudAdminCreds`Vyžadovaná skriptem jsou pro ověřování instance centra Azure Stack. Nejedná se o Azure Active Directory přihlašovací údaje, které používá tenant VaaS.

- Aktualizace místního agenta:

    Předchozí verze místního agenta není kompatibilní s aktuálním vydáním služby 4.0.0. Všichni uživatelé musí aktualizovat své místní agenty. Pokyny k instalaci nejnovějšího agenta najdete v tématu [Stažení a instalace místního agenta](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) .

- Aktualizace automatizace PowerShellu:

    `LaunchVaaSTests`Ve skriptech PowerShellu, které vyžadují nejnovější verzi skriptovacích balíčků, byly provedeny změny. Pokyny k instalaci nejnovější verze balíčku Scripting najdete v tématu [spuštění pracovního postupu test Pass](azure-stack-vaas-automate-with-powershell.md) .

- Ověřování jako portál služby:

  - Oznámení podepisování balíčků

    Při odeslání balíčku pro přizpůsobení OEM v rámci pracovního postupu ověření balíčku se ověří formát balíčku, aby se zajistilo, že bude postupovat podle publikované specifikace. Pokud balíček nevyhovuje, spuštění se nezdaří. E-mailová oznámení se odesílají na e-mailovou adresu zaregistrovaného Azure Active Directory kontaktu pro tenanta.

  - Kategorie interaktivního testu:

    Byla přidána kategorie **interaktivního** testu. Tyto testy cvičení využívají interaktivní scénáře neautomatizovaného centra Azure Stack.

  - Ověřování interaktivních funkcí:

    Možnost poskytnout fokus na určité funkce je teď dostupná v pracovním postupu test Pass. `OEM Update on Azure Stack Hub 1806 RC Validation 5.1.4.0`Test zkontroluje, zda byly konkrétní aktualizace správně aplikovány, a poté shromáždí zpětnou vazbu.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s ověřováním jako službou](azure-stack-vaas-troubleshoot.md)
