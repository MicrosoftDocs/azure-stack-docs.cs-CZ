---
title: Ověřit integraci Azure graphu
titleSuffix: Azure Stack Hub
description: Pomocí nástroje pro kontrolu připravenosti centra Azure Stack ověřte integraci grafů pro Azure Stack centrum.
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: ff9763edbd96dda39f3de8e8a764ce4f4acd7200
ms.sourcegitcommit: 5f53810d3c5917a3a7b816bffd1729a1c6b16d7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972489"
---
# <a name="validate-graph-integration-for-azure-stack-hub"></a>Ověřit integraci grafu pro Azure Stack hub

Pomocí nástroje pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker) ověřte, že je prostředí připravené pro integraci grafů s Azure Stackm centrem. Než začnete s integrací Datacenter nebo nasazením centra Azure Stack, ověřte integraci grafu.

Kontrola připravenosti ověřuje:

* Přihlašovací údaje k účtu služby vytvořenému pro integraci grafu mají příslušná práva k dotazování služby Active Directory.
* *Globální katalog* se dá vyřešit a je kontaktní.
* Služba KDC se dá vyřešit a je kontaktní.
* Nezbytné síťové připojení je zavedeno.

Další informace o integraci centrálního centra Azure Stack najdete v tématu věnovaném [integraci služby Azure Stack hub Datacenter-identity](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Získat nástroj pro kontrolu připravenosti

Z [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker)si stáhněte nejnovější verzi nástroje pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker).

## <a name="prerequisites"></a>Požadavky

Je nutné, aby byly splněny následující požadavky.

**Počítač, ve kterém se nástroj spouští:**

* Windows 10 nebo Windows Server 2016 s připojením k doméně
* PowerShell 5,1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující příkaz PowerShellu a pak zkontrolujte *Hlavní* verzi a *dílčí* verze:
    ```powershell
    $PSVersionTable.PSVersion
    ```
* Modul PowerShellu služby Active Directory.
* Nejnovější verzi nástroje pro [kontrolu připravenosti centra Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) .

**Prostředí služby Active Directory:**

* Identifikujte uživatelské jméno a heslo pro účet služby Graph v existující instanci služby Active Directory.
* Identifikujte kořenový plně kvalifikovaný název domény doménové struktury služby Active Directory.

## <a name="validate-the-graph-service"></a>Ověření služby Graph

1. V počítači, který splňuje požadavky, otevřete příkazový řádek PowerShell pro správu a spusťte následující příkaz, kterým nainstalujete AzsReadinessChecker:

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

1. Z příkazového řádku PowerShellu spusťte následující příkaz, který nastaví proměnnou *$graphCredential* na účet grafu. Nahraďte `contoso\graphservice` účtem pomocí formátu `domain\username`.

    ```powershell
    $graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"
    ```

1. Z příkazového řádku PowerShellu spusťte následující příkaz, který spustí ověřování pro službu Graph Service. Zadejte hodnotu pro `-ForestFQDN` jako plně kvalifikovaný název domény pro kořen doménové struktury.

    ```powershell
    Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential
    ```

1. Po spuštění nástroje si Projděte výstup. Ověřte, že stav je OK pro požadavky na integraci grafu. Úspěšné ověření je podobné jako v následujícím příkladu:

    ```powershell
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

V produkčních prostředích není testování připojení k síti z pracovní stanice tohoto operátoru plně informativní jako dostupné připojení Azure Stack hub. Veřejná VIP adresa razítka centra Azure Stack potřebuje připojení pro provoz protokolu LDAP, aby mohl provádět integraci identit.

## <a name="report-and-log-file"></a>Soubor sestavy a protokolu

Pokaždé, když se ověřování spustí, protokoluje výsledky do **AzsReadinessChecker. log** a **AzsReadinessCheckerReport. JSON**. Umístění těchto souborů se zobrazí s výsledky ověřování v prostředí PowerShell.

Soubory ověření vám můžou přispět ke sdílení stavu před nasazením centra Azure Stack nebo prozkoumání problémů s ověřováním. Oba soubory uchovávají výsledky každé následné kontroly ověření. Tato sestava poskytne vašemu týmu nasazení potvrzení konfigurace identity. Soubor protokolu může pomoci týmu nasazení nebo podpory prozkoumat problémy s ověřením.

Ve výchozím nastavení jsou oba soubory zapisovány do `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Použije

* `-OutputPath`: parametr *path* na konci příkazu Run určuje jiné umístění sestavy.
* `-CleanReport`: parametr na konci příkazu Run vymaže *AzsReadinessCheckerReport. JSON* předchozí informace sestavy. Další informace najdete v tématu [Sestava ověřování centra Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Selhání ověřování

Pokud se ověření nezdaří, zobrazí se v okně PowerShellu podrobnosti o selhání. Nástroj také zapisuje informace do *protokolu AzsGraphIntegration. log*.

## <a name="next-steps"></a>Další kroky

[Zobrazit sestavu připravenosti](azure-stack-validation-report.md)  
[Obecné pokyny k integraci centra Azure Stack](azure-stack-datacenter-integration.md)  
