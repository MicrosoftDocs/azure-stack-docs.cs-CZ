---
title: Sestava ověření centra Azure Stack
titleSuffix: Azure Stack Hub
description: K vygenerování sestavy ověření použijte nástroj pro kontrolu připravenosti centra Azure Stack.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: ec94d4112bc1739aad974220aa4438c99874795d
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868926"
---
# <a name="azure-stack-hub-validation-report"></a>Sestava ověření centra Azure Stack

Pomocí [Nástroje pro kontrolu připravenosti centra Azure Stack](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.2002.1111.69) spusťte ověřování, které podporuje nasazení a údržbu prostředí Azure Stack hub. Nástroj zapisuje výsledky do souboru sestavy. JSON. V sestavě se zobrazí podrobná a souhrnná data o stavu požadavků pro nasazení centra Azure Stack. V sestavě se zobrazí také informace o rotaci tajných klíčů pro existující nasazení centra Azure Stack.  

## <a name="where-to-find-the-report"></a>Kde najít sestavu

Když se nástroj spustí, protokoluje výsledky, které se **AzsReadinessCheckerReport.js**. Nástroj také vytvoří protokol s názvem **AzsReadinessChecker. log**. Umístění těchto souborů se zobrazí spolu s výsledky ověřování v PowerShellu:

![výsledky spuštění – ověření pro kontrolu připravenosti centra Azure Stack](./media/azure-stack-validation-report/validation.png)

Oba soubory uchovávají Výsledky následných ověřovacích kontrol při spuštění ve stejném počítači. Například můžete spustit nástroj pro ověření certifikátů, znovu spustit pro ověření identity Azure a pak třetí čas ověřit registraci. Výsledky všech tří ověření jsou k dispozici ve výsledné sestavě. JSON.  

Ve výchozím nastavení jsou oba soubory zapisovány do `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json` .  

- Použijte `-OutputPath <path>` parametr na konci příkazového řádku a zadejte jiné umístění sestavy.
- Použijte `-CleanReport` parametr na konci příkazového řádku, chcete-li vymazat informace o předchozích spuštěních nástroje z **AzsReadinessCheckerReport.js**.

## <a name="view-the-report"></a>Zobrazení sestavy

Chcete-li zobrazit sestavu v prostředí PowerShell, zadejte cestu k sestavě jako hodnotu pro `-ReportPath` . Tento příkaz zobrazí obsah sestavy a určí ověření, která ještě neobsahují výsledky.

Chcete-li například zobrazit sestavu z příkazového řádku prostředí PowerShell, který je otevřen v umístění, kde je umístěna sestava, spusťte následující příkaz:

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

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>Zobrazit souhrn sestavy

Chcete-li zobrazit souhrn sestavy, můžete přidat `-summary` parametr na konec příkazu prostředí PowerShell. Například:

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

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>Zobrazit filtrovanou sestavu

Chcete-li zobrazit sestavu, která je filtrována podle jednoho typu ověřování, použijte `-ReportSections` parametr s jednou z následujících hodnot:

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
