---
title: Ověřit integraci AD FS
titleSuffix: Azure Stack Hub
description: Naučte se používat kontrolu připravenosti centra Azure Stack k ověření AD FS Integration pro Azure Stack hub.
services: azure-stack
documentationcenter: ''
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 6c4ed64f3d7327004d3f508c54f98913c97af5fa
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366651"
---
# <a name="validate-ad-fs-integration-for-azure-stack-hub"></a>Ověřit integraci AD FS pro centrum Azure Stack

Pomocí nástroje pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker) ověřte, že je vaše prostředí připravené pro integraci Active Directory Federation Services (AD FS) (AD FS) s Azure Stackm rozbočovačem. Než začnete s integrací Datacenter nebo nasazením centra Azure Stack, ověřte integraci AD FS.

Kontrola připravenosti ověřuje:

* *Federační metadata* obsahují platné elementy XML pro federaci.
* Je možné načíst *certifikát AD FS SSL* a vytvořit řetěz důvěryhodnosti. V případě razítka musí AD FS důvěřovat řetězu certifikátů SSL. Certifikát musí být podepsaný stejnou *certifikační autoritou* , která se používá pro certifikáty nasazení Azure Stack hub nebo partnera důvěryhodné kořenové autority. Úplný seznam partnerů důvěryhodných kořenových autorit najdete na [webu TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
* *Podpisový certifikát AD FS* je důvěryhodný a nemá blízko vypršení platnosti.

Další informace o integraci centrálního centra Azure Stack najdete v tématu věnovaném [integraci služby Azure Stack hub Datacenter-identity](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Získat nástroj pro kontrolu připravenosti

Z [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker)si stáhněte nejnovější verzi nástroje pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker).  

## <a name="prerequisites"></a>Předpoklady

Je nutné, aby byly splněny následující požadavky.

**Počítač, ve kterém se nástroj spouští:**

* Windows 10 nebo Windows Server 2016 s připojením k doméně
* PowerShell 5,1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující příkaz PowerShellu a pak zkontrolujte *Hlavní* verzi a *dílčí* verze:  
    ```powershell
    $PSVersionTable.PSVersion
    ```
* Nejnovější verzi nástroje pro [kontrolu připravenosti centra Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) .

**Active Directory Federation Services (AD FS) prostředí:**

Potřebujete alespoň jednu z následujících forem metadat:

- Adresa URL AD FS federačních metadat. Například: `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`.
* Soubor XML s federačními metadaty. Příklad: FederationMetadata. XML.

## <a name="validate-ad-fs-integration"></a>Ověřit integraci AD FS

1. V počítači, který splňuje požadavky, otevřete příkazový řádek PowerShell pro správu a spusťte následující příkaz pro instalaci AzsReadinessChecker:

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

1. Z příkazového řádku PowerShellu spusťte následující příkaz, který spustí ověřování. Jako identifikátor URI pro federační metadata zadejte hodnotu **CustomADFSFederationMetadataEndpointUri** .

     ```powershell
     Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml
     ```

1. Po spuštění nástroje si Projděte výstup. Potvrďte, že stav je OK pro AD FS požadavky na integraci. Úspěšné ověření je podobné jako v následujícím příkladu:

    ```powershell
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

V produkčních prostředích není testování řetězů certifikátů důvěry z pracovní stanice operátoru plně postavo vztahu důvěryhodnosti PKI v infrastruktuře centra Azure Stack. Veřejná VIP adresa razítka centra Azure Stack potřebuje připojení k seznamu odvolaných certifikátů infrastruktury veřejných klíčů (PKI).

## <a name="report-and-log-file"></a>Soubor sestavy a protokolu

Pokaždé, když se ověřování spustí, protokoluje výsledky do **AzsReadinessChecker. log** a **AzsReadinessCheckerReport. JSON**. Umístění těchto souborů se zobrazí s výsledky ověřování v prostředí PowerShell.

Soubory ověření vám můžou přispět ke sdílení stavu před nasazením centra Azure Stack nebo prozkoumání problémů s ověřováním. Oba soubory uchovávají výsledky každé následné kontroly ověření. Tato sestava poskytne vašemu týmu nasazení potvrzení konfigurace identity. Soubor protokolu může pomoci týmu nasazení nebo podpory prozkoumat problémy s ověřením.

Ve výchozím nastavení jsou oba soubory zapisovány do `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Použije

* `-OutputPath`: parametr *path* na konci příkazu Run určuje jiné umístění sestavy.
* `-CleanReport`: parametr na konci příkazu Run vymaže AzsReadinessCheckerReport. JSON předchozí informace sestavy. Další informace najdete v tématu [Sestava ověřování centra Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Selhání ověřování

Pokud se ověření nezdaří, zobrazí se v okně PowerShellu podrobnosti o selhání. Nástroj také zapisuje informace do *protokolu AzsReadinessChecker. log*.

V následujících příkladech jsou uvedeny pokyny k běžným chybám ověření.

### <a name="command-not-found"></a>Příkaz nenalezen

```powershell
Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
```

**Příčina**: prostředí PowerShell AUTOLOAD se nepodařilo správně načíst modul pro kontrolu připravenosti.

**Řešení**: explicitně importujte modul pro kontrolu připravenosti. Zkopírujte a vložte následující kód do PowerShellu a aktualizujte `<version>` číslem aktuálně nainstalované verze.

```powershell
Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force
```

## <a name="next-steps"></a>Další kroky

[Zobrazit sestavu připravenosti](azure-stack-validation-report.md)  
[Obecné pokyny k integraci centra Azure Stack](azure-stack-datacenter-integration.md)  
