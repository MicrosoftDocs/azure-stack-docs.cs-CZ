---
title: Referenční informace k privilegovanému koncovému bodu centra Azure Stack
description: Reference pro Azure Stack s privilegovaným koncovým bodem prostředí PowerShell
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 218aff04912abcce97cf0f3568b6bff1321b4cba
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563265"
---
# <a name="azure-stack-hub-privileged-endpoint-reference"></a>Referenční informace k privilegovanému koncovému bodu centra Azure Stack

Prostředí PowerShell Azure Stack privilegovaným koncovým bodem (PEP) je předem nakonfigurovaná konzola vzdáleného prostředí PowerShell, která poskytuje dostatek možností, které vám pomůžou provést požadovanou úlohu. Koncový bod používá prostředí PowerShell JEA (jen dostatečná Správa) k zobrazení pouze omezené sady rutin.

## <a name="privilege-endpoint-cmdlets"></a>Rutiny koncového bodu oprávnění

| Rutina | Popis |
| --- | --- |
| [Zavřít – PrivilegedEndpoint](Close-PrivilegedEndpoint.md) | Žádný popis. |
| [Get-ActionStatus](Get-ActionStatus.md) | Získá stav poslední akce pro operaci se zadaným názvem funkce. |
| [Get-AzureStackLog](Get-AzureStackLog.md) | Získejte protokoly z různých rolí AzureStack s časovým limitem. |
| [Get-AzureStackStampInformation](Get-AzureStackStampInformation.md) | Získá informace o razítku. |
| [Get-AzureStackSupportConfiguration](Get-AzureStackSupportConfiguration.md) | Načte nastavení konfigurace služby podpory. |
| [Get-CloudAdminPasswordRecoveryToken](Get-CloudAdminPasswordRecoveryToken.md) | Žádný popis. |
| [Get-CloudAdminUserList](Get-CloudAdminUserList.md) | Žádný popis. |
| [Get-ClusterLog](Get-ClusterLog.md) | Žádný popis. |
| [Get-GraphApplication](Get-GraphApplication.md) | Get-GraphApplication je Obálková funkce pro získání informací o aplikaci grafu pro zadaný název aplikace nebo identifikátor. |
| [Get-StorageJob](Get-StorageJob.md) | Žádný popis. |
| [Get-SupportSessionInfo](Get-SupportSessionInfo.md) | Žádný popis. |
| [Get-SupportSessionToken](Get-SupportSessionToken.md) | Žádný popis. |
| [Get-SyslogClient](Get-SyslogClient.md) | Načte nastavení klienta syslog. |
| [Get-SyslogServer](Get-SyslogServer.md) | Získá koncový bod serveru syslog. |
| [Get-ThirdPartyNotices](Get-ThirdPartyNotices.md) | Žádný popis. |
| [Get-TLSPolicy](Get-TLSPolicy.md) | Žádný popis. |
| [Get-VirtualDisk](Get-VirtualDisk.md) | Žádný popis. |
| [Invoke – AzureStackOnDemandLog](Invoke-AzureStackOnDemandLog.md) | Vygeneruje protokoly na vyžádání z rolí AzureStack, kde je to možné. |
| [New-AzureBridgeServicePrincipal](New-AzureBridgeServicePrincipal.md) | Vytvoří nový objekt zabezpečení službu v Azure Active Directory. |
| [New-AzureStackActivation](New-AzureStackActivation.md) | Aktivujte Azure Stack. |
| [New-CloudAdminUser](New-CloudAdminUser.md) | Žádný popis. |
| [New-GraphApplication](New-GraphApplication.md) | New-GraphApplication je funkce obálky pro volání rutin grafu služby ADFS na AD FS. |
| [New-RegistrationToken](New-RegistrationToken.md) | Vytvoří nový registrační token. |
| [Registrovat – CustomAdfs](Register-CustomAdfs.md) | Skript pro registraci vlastního služba FS (Federation Service) Active Directory (AD FS) jako zprostředkovatele deklarací identity pomocí Azure Stack AD FS. |
| [Registrovat – CustomDnsServer](Register-CustomDnsServer.md) | Skript pro registraci vlastních serverů DNS s Azure Stack DNS |
| [Registrovat – DirectoryService](Register-DirectoryService.md) | Skript, který zaregistruje zákazníka službu Active Directory se službou Graph. |
| [Remove-AzureStackActivation](Remove-AzureStackActivation.md) | Žádný popis. |
| [Remove-CloudAdminUser](Remove-CloudAdminUser.md) | Žádný popis. |
| [Remove-GraphApplication](Remove-GraphApplication.md) | Remove-GraphApplication je funkce obálky pro volání rutin grafu služby ADFS na AD FS. |
| [Opravit – VirtualDisk](Repair-VirtualDisk.md) | Žádný popis. |
| [Reset – DatacenterIntegrationConfiguration](Reset-DatacenterIntegrationConfiguration.md) | Skript pro resetování změn integrace Datacenter |
| [Odeslat – AzureStackDiagnosticLog](Send-AzureStackDiagnosticLog.md) | Odesílá protokoly diagnostiky Azure Stack společnosti Microsoft. |
| [Set-CloudAdminUserPassword](Set-CloudAdminUserPassword.md) | Žádný popis. |
| [Set-GraphApplication](Set-GraphApplication.md) | Set-GraphApplication je funkce obálky pro volání rutin grafu služby ADFS na AD FS. |
| [Set-ServiceAdminOwner](Set-ServiceAdminOwner.md) | Skript, který aktualizuje Správce služby. |
| [Set-SyslogClient](Set-SyslogClient.md) | Importuje a použije certifikát koncového bodu klienta syslog. |
| [Set-SyslogServer](Set-SyslogServer.md) | Nastaví koncový bod serveru syslog. |
| [Sada-telemetrie](Set-Telemetry.md) | Povolí nebo zakáže přenos dat telemetrie do Microsoftu. |
| [Set-TLSPolicy](Set-TLSPolicy.md) | Žádný popis. |
| [Spustit – AzureStack](Start-AzureStack.md) | Spustí všechny Azure Stack služby. |
| [Spustit – SecretRotation](Start-SecretRotation.md) | Spustí rotaci tajného kódu razítka. |
| [Stop – AzureStack](Stop-AzureStack.md) | Zastaví všechny Azure Stack služby. |
| [Test-AzureStack](Test-AzureStack.md) | Ověří stav Azure Stack. |
| [Odemknout – SupportSession](Unlock-SupportSession.md) | Žádný popis. |

## <a name="next-steps"></a>Další kroky

Další informace o privilegovaném koncovém bodu v centru Azure Stack najdete v tématu [použití privilegovaného koncového bodu v Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).
