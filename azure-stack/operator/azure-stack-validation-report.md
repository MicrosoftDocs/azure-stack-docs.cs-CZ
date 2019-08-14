---
title: Sestava ověření pro Azure Stack | Microsoft Docs
description: Pomocí sestavy nástroje pro kontrolu připravenosti na Azure Stack můžete zkontrolovat výsledky ověření.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: c00ce005ac72fcde34b58a1afe7e134c27274247
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991729"
---
# <a name="azure-stack-validation-report"></a>Sestava ověření Azure Stack

Pomocí nástroje pro *kontrolu připravenosti na Azure Stack* můžete spouštět ověřování, která podporují nasazení a obsluhu Azure Stackho prostředí. Nástroj zapisuje výsledky do souboru sestavy. JSON. V sestavě se zobrazí podrobná a souhrnná data o stavu požadavků pro nasazení Azure Stack. Sestava obsahuje také informace o rotaci tajných klíčů pro existující nasazení Azure Stack.  

## <a name="where-to-find-the-report"></a>Kde najít sestavu

Když se nástroj spustí, protokoluje výsledky do **AzsReadinessCheckerReport. JSON**. Nástroj také vytvoří protokol s názvem **AzsReadinessChecker. log**. Umístění těchto souborů se zobrazí spolu s výsledky ověřování v PowerShellu:

![spustit – ověření](./media/azure-stack-validation-report/validation.png)

Oba soubory uchovávají Výsledky následných ověřovacích kontrol při spuštění ve stejném počítači. Například můžete spustit nástroj pro ověření certifikátů, znovu spustit pro ověření identity Azure a pak třetí čas ověřit registraci. Výsledky všech tří ověření jsou k dispozici ve výsledné sestavě. JSON.  

Ve výchozím nastavení jsou oba soubory zapisovány do **C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.JSON**.  

- `-OutputPath <path>` Použijte parametr na konci příkazového řádku a zadejte jiné umístění sestavy.
- Použijte parametr na konci příkazového řádku, chcete-li vymazat informace o předchozích spuštěních nástroje z **AzsReadinessCheckerReport. JSON.** `-CleanReport`

## <a name="view-the-report"></a>Zobrazení sestavy

Chcete-li zobrazit sestavu v prostředí PowerShell, zadejte cestu k sestavě jako hodnotu pro `-ReportPath`. Tento příkaz zobrazí obsah sestavy a identifikuje ověření, která ještě nemají výsledky.

Chcete-li například zobrazit sestavu z příkazového řádku prostředí PowerShell, který je otevřen v umístění, kde je sestava umístěna, spusťte následující příkaz:

```powershell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

Výstup se podobá následujícímu příkladu:

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>Zobrazit souhrn sestavy

Chcete-li zobrazit souhrn sestavy, můžete přidat `-summary` parametr na konec příkazu prostředí PowerShell. Příklad:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

Souhrn zobrazuje ověření, která nemají výsledky, a označuje úspěch nebo neúspěch pro ověření, která jsou dokončena. Výstup se podobá následujícímu příkladu:

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Zobrazit filtrovanou sestavu

Pokud chcete zobrazit sestavu, která je filtrovaná podle jednoho typu ověřování, použijte parametr **-ReportSections** s jednou z následujících hodnot:

- Certifikát
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Úlohy
- Vše  

Chcete-li například zobrazit souhrn sestavy pouze pro certifikáty, použijte následující příkazový řádek prostředí PowerShell:

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
