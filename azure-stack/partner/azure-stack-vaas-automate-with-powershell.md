---
title: Automatizace Azure Stack ověřování pomocí PowerShellu | Microsoft Docs
description: Můžete automatizovat Azure Stack ověřování pomocí prostředí PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7c6da84700c73e0976214bd999dfb3ca4ba0ee47
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167339"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatizace Azure Stack ověřování pomocí PowerShellu

Ověřování jako služba (VaaS) poskytuje možnost automatizovat spouštění testů pomocí skriptu **LaunchVaaSTests. ps1** .

> [!NOTE]  
> Automatizace je k dispozici pouze pro pracovní postup testovacího průchodu. Pracovní postupy ověření balíčku a ověření řešení se podporují jenom prostřednictvím portálu VaaS.

Tento skript se dá použít k těmto akcím:

> [!div class="checklist"]
> * Požadavky na instalaci
> * Nainstalovat a spustit místního agenta
> * Spustit kategorii testů, jako je *integrace*, *funkční*, *spolehlivost*
> * Sestava výsledků testu

## <a name="launch-the-test-pass-workflow"></a>Spuštění pracovního postupu test Pass

1. Otevřete příkazový řádek PowerShell se zvýšenými oprávněními.

2. Spusťte následující skript pro stažení skriptu automatizace:

    ```powershell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Spusťte následující skript s příslušnými hodnotami parametrů:

    ```powershell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Parametry**

    | Parametr | Popis |
    | --- | --- |
    | VaaSUserId | Vaše ID uživatele VaaS. |
    | VaaSUserPassword | Vaše heslo VaaS |
    | VaaSAccountTenantId | Identifikátor GUID vašeho tenanta VaaS |
    | VaaSSolutionName | Název řešení VaaS, pod kterým se testovací průchod spustí. |
    | VaaSTestPassName | Název pracovního postupu VaaS test Pass, který se má vytvořit. |
    | VaaSTestCategories | `Integration`, `Functional`nebo. `Reliability`. Pokud použijete více hodnot, oddělte každou hodnotu čárkou.  |
    | ServiceAdminUserName | Váš účet správce služby Azure Stack.  |
    | ServiceAdminPassword | Vaše heslo služby Azure Stack.  |
    | TenantAdminUserName | Správce primárního klienta.  |
    | TenantAdminPassword | Heslo pro primárního klienta.  |
    | CloudAdminUserName | Uživatelské jméno správce cloudu.  |
    | CloudAdminPassword | Heslo pro správce cloudu.  |

4. Zkontrolujte výsledky testu. Další možnosti najdete v tématu [monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Další kroky

Další informace o PowerShellu v Azure Stack najdete v nejnovějších modulech.

> [!div class="nextstepaction"]
> [Modul Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
