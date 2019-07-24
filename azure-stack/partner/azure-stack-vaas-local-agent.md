---
title: Nasadit místního agenta | Microsoft Docs
description: Nasadit místního agenta pro ověřování Azure Stack jako služby.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b1a658b428d13cdd12c16b767430f87a80e89fdc
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418366"
---
# <a name="deploy-the-local-agent"></a>Nasazení místního agenta

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Naučte se používat místního agenta ověřování jako služby (VaaS) ke spouštění ověřovacích testů. Aby bylo možné spustit ověřovací testy, musí být nasazen místní agent.

> [!Note]  
> Ujistěte se, že počítač, na kterém je spuštěný místní agent, neztratí odchozí přístup k Internetu. Tento počítač by měl být přístupný jenom uživatelům, kteří mají oprávnění používat VaaS jménem vašeho tenanta.

Nasazení místního agenta:

1. Nainstalujte místního agenta.
2. Provede správnosti kontroly.
3. Spusťte místního agenta.

## <a name="download-and-start-the-local-agent"></a>Stažení a spuštění místního agenta

Stáhněte si agenta do počítače, který splňuje požadavky ve vašem datovém centru a má přístup ke všem koncovým bodům Azure Stack. Tento počítač by neměl být součástí Azure Stackho systému nebo hostovat v cloudu Azure Stack.

### <a name="machine-prerequisites"></a>Požadavky na počítač

Ověřte, že váš počítač splňuje následující kritéria:

- Přístup ke všem koncovým bodům Azure Stack
- Nainstalovaná rozhraní .NET 4,6 a PowerShell 5,0
- Aspoň 8 GB paměti RAM
- Minimálně 8 základních procesorů
- Minimálně 200 GB místa na disku
- Stálé síťové připojení k Internetu

### <a name="download-and-install-the-agent"></a>Stažení a instalace agenta

1. Otevřete prostředí Windows PowerShell v příkazovém řádku se zvýšenými oprávněními na počítači, který použijete ke spuštění testů.
2. Spusťte následující příkaz pro stažení místního agenta:

    ```powershell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Spuštěním následujícího příkazu nainstalujte závislosti místních agentů:

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
    | aadServiceAdminUser | Uživatel globálního správce pro vašeho tenanta Azure AD. Například může být vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Heslo pro uživatele globálního správce |
    | AadTenantId | ID tenanta Azure AD pro účet Azure zaregistrovaný k ověřování jako služba |
    | ExternalFqdn | Plně kvalifikovaný název domény můžete získat z konfiguračního souboru. Pokyny najdete v tématu [společné parametry pracovních postupů v Azure Stack ověřování jako služba](azure-stack-vaas-parameters.md). |
    | Oblast | Oblast vašeho tenanta Azure AD. |

Příkaz stáhne image úložiště veřejných imagí (PIR) (VHD s operačním systémem) a zkopíruje je do úložiště služby Azure Blob Storage do úložiště Azure Stack.

![Stáhnout požadavky](media/installingprereqs.png)

> [!Note]
> Pokud při stahování těchto imagí dochází k pomalé rychlosti sítě, Stáhněte si je samostatně do místní sdílené složky a zadejte parametr **-LocalPackagePath** *FileShareOrLocalPath*. Další doprovodné materiály k PIR stažení najdete v části [zpracování pomalých síťových připojení](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) k [řešení potíží s ověřováním jako služby](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Kontroluje před zahájením testů.

Testy spouštějí vzdálené operace. Počítač, který spouští testy, musí mít přístup k koncovým bodům Azure Stack, jinak testy nebudou fungovat. Pokud používáte místního agenta VaaS, použijte počítač, na kterém se Agent spustí. Spuštěním následujících kontrol můžete zkontrolovat, jestli má počítač přístup k koncovým bodům Azure Stack:

1. Ověřte, zda je možné dosáhnout základního identifikátoru URI. Otevřete příkazový řádek nebo prostředí bash a spusťte následující příkaz a nahraďte `<EXTERNALFQDN>` ho externím plně kvalifikovaným názvem domény vašeho prostředí:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Otevřete webový prohlížeč a pokračujte na `https://adminportal.<EXTERNALFQDN>` to, abyste zkontrolovali, že je možné získat přístup k portálu MAS.

3. Přihlaste se pomocí hodnoty jméno a heslo správce služby Azure AD, které jste zadali při vytváření testovacího průchodu.

4. Zkontrolujte stav systému spuštěním rutiny PowerShellu **test-AzureStack** , jak je popsáno v tématu [spuštění ověřovacího testu pro Azure Stack](../operator/azure-stack-diagnostic-test.md). Před spuštěním testů opravte všechna upozornění a chyby.

## <a name="run-the-agent"></a>Spustit agenta

1. Otevřete prostředí Windows PowerShell v příkazovém řádku se zvýšenými oprávněními.

2. Spusťte následující příkaz:

    ```powershell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parametry**  

    | Parametr | Popis |
    | --- | --- |
    | VaaSUserId | ID uživatele, které se používá k přihlášení na portál VaaS (například UserName\@contoso.com) |
    | VaaSTenantId | ID tenanta Azure AD pro účet Azure zaregistrovaný k ověřování jako služba |

    > [!Note]  
    > Když spustíte agenta, aktuální pracovní adresář musí být umístěním spustitelného souboru hostitele modulu úloh, **Microsoft. VaaSOnPrem. TaskEngineHost. exe.**

Pokud se nezobrazí žádné chyby, místní Agent byl úspěšný. Následující vzorový text se zobrazí v okně konzoly.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Spuštěný Agent](media/startedagent.png)

Agent je jednoznačně identifikovaný podle názvu. Ve výchozím nastavení používá plně kvalifikovaný název domény (FQDN) počítače, ze kterého byl spuštěn. Okno je nutné minimalizovat, aby nedocházelo k náhodnému výběru v okně, protože změna fokusu pozastaví všechny ostatní akce.

## <a name="next-steps"></a>Další postup

- [Řešení potíží s ověřováním jako službou](azure-stack-vaas-troubleshoot.md)
- [Koncepce ověřování jako klíč služby](azure-stack-vaas-key-concepts.md)
- [Rychlé zprovoznění: Použití ověřování jako portálu služby k naplánování prvního testu](azure-stack-vaas-schedule-test-pass.md)