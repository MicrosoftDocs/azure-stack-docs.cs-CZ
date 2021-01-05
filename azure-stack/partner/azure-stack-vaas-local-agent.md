---
title: Nasazení místního agenta
titleSuffix: Azure Stack Hub
description: Naučte se nasadit místního agenta pro Azure Stack ověřování centra jako služby.
author: mattbriggs
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ef131c1b7b54c05b6feee9336a0875f3507f5174
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868365"
---
# <a name="deploy-the-local-agent"></a>Nasazení místního agenta

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Naučte se používat místního agenta ověřování jako služby (VaaS) ke spouštění ověřovacích testů. Aby bylo možné spustit ověřovací testy, musí být nasazen místní agent.

> [!Note]  
> Ujistěte se, že počítač, na kterém je spuštěný místní agent, neztratí odchozí přístup k Internetu. Tento počítač by měl být přístupný jenom uživatelům, kteří mají oprávnění používat VaaS jménem vašeho tenanta.

Nasazení místního agenta:

1. Stáhněte a nainstalujte místního agenta.
2. Před zahájením testů provést správnosti kontroly.
3. Spusťte místního agenta.

## <a name="download-and-start-the-local-agent"></a>Stažení a spuštění místního agenta

Stáhněte si agenta do počítače, který splňuje požadavky ve vašem datovém centru, a má přístup ke všem koncovým bodům centra Azure Stack. Tento počítač by neměl být součástí systému Azure Stack hub nebo hostovaný v cloudu centra Azure Stack.

### <a name="machine-prerequisites"></a>Požadavky na počítač

Ověřte, že váš počítač splňuje následující kritéria:

- Přístup ke všem koncovým bodům centra Azure Stack.
- Rozhraní .NET 4,6 a PowerShell 5,0 jsou nainstalovány.
- Aspoň 8 GB paměti RAM.
- Minimálně 8 základních procesorů.
- Minimálně 200 GB místa na disku.
- Síťové připojení k Internetu je stabilní.

### <a name="download-and-install-the-local-agent"></a>Stažení a instalace místního agenta

1. Otevřete prostředí Windows PowerShell v příkazovém řádku se zvýšenými oprávněními na počítači, který použijete ke spuštění testů.
2. Spusťte následující příkaz ke stažení a instalaci závislostí místního agenta a zkopírování imagí úložiště veřejných imagí (PIR) (VHD) do prostředí centra Azure Stack.

    ```powershell
    # Review and update the following five parameters
    $RootFolder = "c:\VaaS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $AadServiceAdminUserName = "<AAD service admin user name>"
    $AadServiceAdminPassword = "<AAD service admin password>"

    if (-not(Test-Path($RootFolder))) {
        mkdir $RootFolder
    }
    Set-Location $RootFolder
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "$rootFolder\OnPremAgent.zip"
    Expand-Archive -Path "$rootFolder\OnPremAgent.zip" -DestinationPath "$rootFolder\VaaSOnPremAgent" -Force
    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"

    $cloudAdminCredential = New-Object System.Management.Automation.PSCredential($cloudAdmindUserName, (ConvertTo-SecureString $cloudAdminPassword -AsPlainText -Force))
    $getStampInfoUri = "https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation" 
    $stampInfo = Invoke-RestMethod -Method Get -Uri $getStampInfoUri -Credential $cloudAdminCredential -ErrorAction Stop
    $serviceAdminCreds = New-Object System.Management.Automation.PSCredential $aadServiceAdminUserName, (ConvertTo-SecureString $aadServiceAdminPassword -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $stampInfo.AADTenantID `
                            -ServiceAdminCreds $serviceAdminCreds `
                            -ArmEndpoint $stampInfo.AdminExternalEndpoints.AdminResourceManager `
                            -Region $stampInfo.RegionName
    ```

> [!Note]  
> `Install-VaaSPrerequisites` rutina stahuje velké soubory imagí virtuálních počítačů. Pokud máte pomalou rychlost sítě, můžete si stáhnout soubory na místní souborový server a ručně přidat image virtuálních počítačů do testovacího environemntu. Další informace najdete v tématu [zpracování pomalých síťových připojení](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).

**Parametry**

| Parametr | Popis |
| --- | --- |
| `AadServiceAdminUser` | Uživatel globálního správce pro vašeho tenanta Azure AD. Příklad: vaasadmin@contoso.onmicrosoft.com. |
| `AadServiceAdminPassword` | Heslo pro uživatele globálního správce |
| `CloudAdminUserName` | Uživatel s oprávněním správce cloudu, který má přístup k povoleným příkazům v rámci privilegovaného koncového bodu Příklad: AzusreStack\CloudAdmin. Další informace najdete v tématu [společné parametry pracovních postupů pro VaaS](azure-stack-vaas-parameters.md). |
| `CloudAdminPassword` | Heslo pro účet správce cloudu.|

![Stáhnout požadavky pro místního agenta](media/installing-prereqs.png)

## <a name="perform-sanity-checks-before-starting-the-tests"></a>Před zahájením testů provést kontrolu správnosti

Testy spouštějí vzdálené operace. Počítač, který spouští testy, musí mít přístup k koncovým bodům centra Azure Stack, jinak testy nebudou fungovat. Pokud používáte místního agenta VaaS, použijte počítač, na kterém se Agent spustí. Spuštěním následujících kontrol můžete zkontrolovat, jestli má počítač přístup k koncovým bodům centra Azure Stack:

1. Ověřte, zda je možné dosáhnout základního identifikátoru URI. Otevřete příkazový řádek nebo prostředí bash a spusťte následující příkaz a nahraďte `<EXTERNALFQDN>` ho externí plně kvalifikovaným názvem domény (FQDN) vašeho prostředí:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Otevřete prohlížeč a pokračujte na to `https://adminportal.<EXTERNALFQDN>` , abyste zkontrolovali, že je možné získat přístup k portálu MAS.

3. Přihlaste se pomocí hodnoty jméno a heslo správce služby Azure AD, které jste zadali při vytváření testovacího průchodu.

4. Zkontrolujte stav systému spuštěním rutiny **test-AzureStack** prostředí PowerShell, jak je popsáno v tématu [spuštění ověřovacího testu pro centrum Azure Stack](../operator/azure-stack-diagnostic-test.md). Před spuštěním testů opravte všechna upozornění a chyby.

## <a name="run-the-local-agent"></a>Spustit místního agenta

1. Otevřete prostředí Windows PowerShell v příkazovém řádku se zvýšenými oprávněními.

2. Spusťte následující příkaz:

    ```powershell
   # Review and update the following five parameters
    $RootFolder = "c:\VAAS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $VaaSUserId = "<VaaS user ID>"
    $VaaSTenantId = "<VaaS tenant ID>"

    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u $VaaSUserId -t $VaaSTenantId -x $CloudAdmindUserName -y $CloudAdminPassword
    ```

      **Parametry**  

    | Parametr | Popis |
    | --- | --- |
    | `CloudAdminUserName` | Uživatel s oprávněním správce cloudu, který má přístup k povoleným příkazům v rámci privilegovaného koncového bodu Příklad: AzusreStack\CloudAdmin. Další informace najdete v tématu [společné parametry pracovních postupů pro VaaS](azure-stack-vaas-parameters.md) . |
    | `CloudAdminPassword` | Heslo pro účet správce cloudu.|
    | `VaaSUserId` | ID uživatele, které se používá pro přihlášení na portál pro ověřování centra Azure Stack. Příklad: UserName \@ contoso.com). |
    | `VaaSTenantId` | ID tenanta Azure AD pro účet Azure zaregistrovaný k ověřování jako služba |

    > [!Note]  
    > Když spustíte agenta, aktuální pracovní adresář musí být umístěním spustitelného souboru hostitele modulu úloh **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Pokud se nezobrazí žádné chyby, místní Agent byl úspěšný. Následující vzorový text se zobrazí v okně konzoly.

`Heartbeat was sent successfully.`

![Spuštěný Agent](media/started-agent.png)

Agent je jednoznačně identifikovaný podle názvu. Ve výchozím nastavení používá název FQDN počítače, ze kterého byl spuštěn. Okno je nutné minimalizovat, aby nedocházelo k náhodnému výběru v okně, protože změna fokusu pozastaví všechny ostatní akce.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s ověřováním jako službou](azure-stack-vaas-troubleshoot.md)
- [Koncepce ověřování jako klíč služby](azure-stack-vaas-key-concepts.md)
- [Rychlý Start: použití portálu pro ověřování centra Azure Stack k naplánování prvního testu](azure-stack-vaas-schedule-test-pass.md)