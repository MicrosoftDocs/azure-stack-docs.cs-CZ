---
title: Monitorování aktualizací služby Azure Stack Hub pomocí privilegovaného koncového bodu
description: Přečtěte si, jak pomocí privilegovaného koncového bodu monitorovat stav aktualizací pro integrované systémy centra Azure Stack.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 769de4eb2c86c1fa17dd4a575611816b3ef0e9d9
ms.sourcegitcommit: dc11aabd3b97c505c5b3cecd3bdb2d5c8e8496aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98981236"
---
# <a name="monitor-updates-in-azure-stack-hub-using-the-privileged-endpoint"></a>Monitorování aktualizací služby Azure Stack Hub pomocí privilegovaného koncového bodu

Pomocí [privilegovaného koncového bodu](azure-stack-privileged-endpoint.md) můžete monitorovat průběh spuštění aktualizace centra Azure Stack. Pomocí privilegovaného koncového bodu můžete také pokračovat v neúspěšném spuštění aktualizace z posledního úspěšného kroku, protože portál Azure Stack hub nebude k dispozici. Použití portálu centra Azure Stack je doporučená metoda pro správu aktualizací v centru Azure Stack.

Následující nové rutiny PowerShellu pro správu aktualizací jsou součástí aktualizace 1710 pro integrované systémy Azure Stack hub.

| Rutina  | Popis  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Vrátí stav aktuálně spuštěných, dokončených nebo neúspěšných aktualizací. Poskytuje stav operace aktualizace na nejvyšší úrovni a dokumentu XML, který popisuje aktuální a odpovídající stav. |
| `Resume-AzureStackUpdate` | Obnoví neúspěšnou aktualizaci v místě, kde se nezdařila. V některých scénářích může být nutné před pokračováním aktualizace dokončit kroky pro zmírnění rizik.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Ověřte, jestli jsou dostupné rutiny.
Vzhledem k tomu, že rutiny jsou nové v balíčku aktualizace 1710 pro centrum Azure Stack, musí se proces aktualizace 1710 dostat do určitého bodu, než bude k dispozici možnost monitorování. Rutiny jsou obvykle dostupné, pokud stav na portálu pro správu označuje, že aktualizace 1710 je v kroku **restartovat úložiště hostitelé** . Konkrétně proběhne aktualizace rutiny během **kroku: krok 2,6 – aktualizace seznamu povolených PrivilegedEndpoint**.

Můžete také zjistit, jestli jsou rutiny k dispozici programově pomocí dotazu na seznam příkazů z privilegovaného koncového bodu. K provedení tohoto dotazu spusťte následující příkazy z hostitele životního cyklu hardwaru nebo z pracovní stanice privilegovaného přístupu. Také se ujistěte, že je privilegovaným koncovým bodem důvěryhodný hostitel. Další informace najdete v části Krok 1 [přístupu k privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

1. Vytvořte relaci PowerShellu na libovolném virtuálním počítači s ERCS ve vašem prostředí centra Azure Stack (*předpona*-ERCS01, *prefix*-ERCS02 nebo *prefix*-ERCS03). Nahraďte *předponu* řetězcem PŘEDPONY virtuálního počítače, který je specifický pro vaše prostředí.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Po zobrazení výzvy k zadání přihlašovacích údajů použijte účet služby &lt; *Azure Stack hub Domain* &gt; \cloudadmin nebo účet, který je členem skupiny CloudAdmins. Pro účet CloudAdmin zadejte stejné heslo, které bylo zadáno během instalace pro účet správce domény AzureStackAdmin.

2. Získejte úplný seznam příkazů, které jsou k dispozici v privilegovaném koncovém bodu.

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Zjistěte, jestli se privilegovaný koncový bod aktualizoval.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Zobrazí seznam příkazů, které jsou specifické pro modul Microsoft. AzureStack. UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Příklad:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Použití rutin pro správu aktualizací

> [!NOTE]
> Spusťte následující příkazy z hostitele životního cyklu hardwaru nebo z pracovní stanice privilegovaného přístupu. Také se ujistěte, že je privilegovaným koncovým bodem důvěryhodný hostitel. Další informace najdete v části Krok 1 [přístupu k privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Připojte se k privilegovanému koncovému bodu a přiřaďte proměnnou relace.

Spuštěním následujících příkazů vytvořte relaci PowerShellu na libovolném virtuálním počítači s ERCS v prostředí centra Azure Stack (*předpona*-ERCS01, *prefix*-ERCS02 nebo *prefix*-ERCS03) a přiřaďte proměnnou relace.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Po zobrazení výzvy k zadání přihlašovacích údajů použijte účet služby &lt; *Azure Stack hub Domain* &gt; \cloudadmin nebo účet, který je členem skupiny CloudAdmins. Pro účet CloudAdmin zadejte stejné heslo, které bylo zadáno během instalace pro účet správce domény AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Získá stav vysoké úrovně pro aktuální běh aktualizace.

Chcete-li získat stav vysoké úrovně aktuálního spuštění aktualizace, spusťte následující příkazy:

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Mezi možné hodnoty patří:

- Spuštěno
- Dokončeno
- Neúspěšný 
- Zrušeno

Chcete-li zobrazit nejaktuálnější stav, můžete spustit tyto příkazy opakovaně. Nemusíte znovu navázat připojení pro kontrolu.

### <a name="get-the-full-update-run-status-with-details"></a>Získat úplný stav spuštění aktualizace s podrobnostmi

Můžete získat souhrn úplného spuštění aktualizace jako řetězec XML. Můžete napsat řetězec do souboru k vyhodnocení nebo ho převést na dokument XML a pomocí PowerShellu ho analyzovat. Následující příkaz analyzuje soubor XML a získá hierarchický seznam aktuálně spuštěných kroků:

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

V následujícím příkladu má krok nejvyšší úrovně (cloudová aktualizace) podřízený plán pro aktualizaci a restartování hostitelů úložiště. Ukazuje, že je v plánu hostitelé restartování úložiště aktualizace služby Blob Storage na jednom z hostitelů.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="resume-a-failed-update-operation"></a>Obnoví neúspěšnou operaci aktualizace.

Pokud se aktualizace nepovede, můžete pokračovat ve spuštění aktualizace, kde skončila.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Řešení potíží

Privilegovaný koncový bod je k dispozici na všech virtuálních počítačích s ERCS v prostředí centra Azure Stack. Vzhledem k tomu, že připojení není navázáno na koncový bod s vysokou dostupností, může docházet k příležitostnému přerušení, varování nebo chybovým zprávám. Tyto zprávy mohou znamenat, že relace byla odpojena nebo došlo k chybě při komunikaci se službou EHK. Jde o očekávané chování. Operaci můžete opakovat za několik minut nebo vytvořit novou relaci privilegovaného koncového bodu na jednom z dalších virtuálních počítačů ERCS.

Další informace o řešení potíží s aktualizacemi najdete v tématu [řešení potíží s Azure Stack](azure-stack-troubleshooting.md) .

## <a name="next-steps"></a>Další kroky

- [Správa aktualizací v centru Azure Stack](azure-stack-updates.md)


