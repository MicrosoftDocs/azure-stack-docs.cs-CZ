---
title: Nasazení místního agenta | Dokumentace Microsoftu
description: Nasazení místního agenta pro ověřování Azure Stack jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1e194583b583bfc442a3c7b99a842ee788fc423c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298940"
---
# <a name="deploy-the-local-agent"></a>Nasazení místního agenta

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Další informace o použití ověřování jako místní agent Service (VaaS) ke spuštění ověřovacích testů. Místní agent musí být nasazený před spuštěním testů pro ověření.

> [!Note]  
> Ujistěte se, že počítači, na kterém je spuštěná místní agent nedojde ke ztrátě odchozí přístup k Internetu. Tento počítač musí být přístupný jenom uživatelům, kteří jsou oprávněni používat VaaS jménem svého tenanta.

Nasazení místního agenta:

1. Nainstalujte místního agenta.
2. Kontrolu správnosti.
3. Spusťte místní agent.

## <a name="download-and-start-the-local-agent"></a>Stáhněte a spusťte místní agent

Stáhněte agenta do počítače, který splňuje požadavky ve vašem datovém centru a má přístup do všech koncových bodů služby Azure Stack. Tento počítač by neměl být součástí systému Azure Stack nebo hostované v cloudu Azure Stack.

### <a name="machine-prerequisites"></a>Požadavky na počítač

Zkontrolujte, že váš počítač splňuje následující kritéria:

- Přístup k všechny koncové body služby Azure Stack
- Rozhraní .NET 4.6 a nainstalovaný PowerShell 5.0
- Minimálně 8 GB paměti RAM
- Nejméně 8 procesory
- Minimální 200 GB místa
- Stabilní síťové připojení k Internetu

### <a name="download-and-install-the-agent"></a>Stažení a instalace agenta

1. V řádku se zvýšenými oprávněními na počítači, který použijete ke spuštění testů otevřete prostředí Windows PowerShell.
2. Spuštěním následujícího příkazu stáhněte místní agent:

    ```powershell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Spuštěním následujícího příkazu nainstalujte místního agenta závislosti:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parametry**

    | Parametr | Popis |
    | --- | --- |
    | aadServiceAdminUser | Uživatele s rolí globální správce pro vašeho tenanta Azure AD. Například může být, vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Heslo pro uživatele s rolí globálního správce. |
    | AadTenantId | ID tenanta Azure AD pro účet Azure zaregistrován ověření jako služba. |
    | ExternalFqdn | Plně kvalifikovaný název domény můžete získat z konfiguračního souboru. Pokyny naleznete v tématu [společných parametrů pracovních postupů v Azure stacku ověření jako služba](azure-stack-vaas-parameters.md). |
    | Oblast | Oblast vašeho tenanta Azure AD. |

Tento příkaz stáhne veřejnou image z úložiště (PIR) obrázku (operačního systému virtuálního pevného disku) a zkopírujte ze služby Azure blob storage do úložiště Azure Stack.

![Stáhnout nezbytné součásti](media/installingprereqs.png)

> [!Note]
> Pokud se zobrazuje rychlost pomalé sítě při stahování těchto bitových kopií, samostatně stáhnout do místní sdílené složky a zadat parametr **- LocalPackagePath** *FileShareOrLocalPath*. Ještě s něčím poradit na PIR stahování můžete najít v části [popisovač pomalé síťové připojení](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) z [ověření řešení jako služba](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Kontroly před zahájením testů

Testy spouštět vzdálené operace. Počítač, který spouští testy musí mít přístup ke koncovým bodům služby Azure Stack, jinak nebude fungovat testy. Pokud používáte místní agent VaaS, použijte na počítači, kde bude agent spuštěn. Můžete zkontrolovat, že váš počítač má přístup ke koncovým bodům služby Azure Stack spuštěním následující kontroly:

1. Zkontrolujte, jestli se dá kontaktovat základní identifikátor URI. Otevřete příkazový řádek nebo prostředí bash a spusťte následující příkaz a nahraďte `<EXTERNALFQDN>` s externí plně kvalifikovaný název domény vašeho prostředí:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Otevřete webový prohlížeč a přejděte na `https://adminportal.<EXTERNALFQDN>` za účelem ověření, jestli se dá kontaktovat portál MAS.

3. Přihlášení pomocí Azure AD služby hodnoty název a heslo správce určili při vytváření průchodu testu.

4. Kontrola stavu systému spuštěním **testovací AzureStack** rutiny Powershellu, jak je popsáno v [spustit test pro ověření pro službu Azure Stack](../operator/azure-stack-diagnostic-test.md). Opravte jakékoli upozornění a chyby před spuštěním žádné testy.

## <a name="run-the-agent"></a>Spustit agenta

1. Otevřete prostředí Windows PowerShell v řádku se zvýšenými oprávněními.

2. Spusťte následující příkaz:

    ```powershell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parametry**  

    | Parametr | Popis |
    | --- | --- |
    | VaaSUserId | ID uživatele použít k přihlášení k portálu VaaS (například uživatelské jméno\@Contoso.com) |
    | VaaSTenantId | ID tenanta Azure AD pro účet Azure zaregistrován ověření jako služba. |

    > [!Note]  
    > Při spuštění agenta aktuální pracovní adresář musí být umístění hostitele modulu úlohy, spustitelný soubor, **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Pokud nevidíte všechny chyby ohlásil, místního agenta proběhla úspěšně. Následující příklad text se zobrazí v okně konzoly.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Začínáme agenta](media/startedagent.png)

Agenta je jednoznačně identifikují pomocí jeho názvu. Ve výchozím nastavení použije název domény plně kvalifikovaný název (FQDN) počítače, ve kterém byl spuštěn. Musíte minimalizovat okno, aby všechny náhodného vybere v okně jako změna výběru pozastaví dalších akcí.

## <a name="next-steps"></a>Další postup

- [Řešení potíží s ověření jako služba](azure-stack-vaas-troubleshoot.md)
- [Ověření jako klíčové koncepty služby](azure-stack-vaas-key-concepts.md)
- [Rychlé zprovoznění: Použít ověření jako portál služby k naplánování prvního testu](azure-stack-vaas-schedule-test-pass.md)