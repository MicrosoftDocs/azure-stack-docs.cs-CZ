---
title: Použití privilegovaného koncového bodu v centru Azure Stack
description: Naučte se používat privilegovaný koncový bod (PEP) v centru Azure Stack jako operátor.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/28/2020
ms.custom: conteperfq4
ms.openlocfilehash: 18c1935c1e683fab1a2cda8886a45648ee69fbf4
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974281"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>Použití privilegovaného koncového bodu v centru Azure Stack

Jako operátor centra Azure Stack byste pro většinu každodenních úloh správy měli používat rozhraní API pro správu portálu, PowerShell nebo Azure Resource Manager. U některých méně běžných operací ale potřebujete použít *privilegovaný koncový bod* (PEP). PEP je předem nakonfigurovaná konzola vzdáleného prostředí PowerShell, která poskytuje dostatek možností, které vám pomůžou provést požadovanou úlohu. Koncový bod používá [prostředí POWERSHELL JEA (jen dostatečná Správa)](/powershell/scripting/learn/remoting/jea/overview) k zobrazení pouze omezené sady rutin. Pokud chcete získat přístup k PEP a vyvolat omezenou sadu rutin, použije se účet s nízkými oprávněními. Nejsou vyžadovány žádné účty správců. Pro zvýšení zabezpečení není skriptování povoleno.

K provedení těchto úloh můžete použít PEP:

- Úlohy nízké úrovně, jako je například [shromažďování diagnostických protokolů](azure-stack-get-azurestacklog.md).
- Mnoho úloh integrace Datacenter po nasazení u integrovaných systémů, jako je například přidání serverů pro přeposílání DNS (Domain Name System) po nasazení, nastavení integrace Microsoft Graph, integrace Active Directory Federation Services (AD FS) (AD FS), rozšíření certifikátu atd.
- Pro práci s podporou pro získání dočasného přístupu s vysokou úrovní pro důkladné řešení potíží s integrovaným systémem.

PEP zaznamená všechny akce (a odpovídající výstupy), které v relaci PowerShellu provedete. To zajišťuje úplnou transparentnost a kompletní auditování operací. Tyto soubory protokolu můžete zachovat pro budoucí audity.

> [!NOTE]
> V Azure Stack Development Kit (ASDK) můžete spouštět některé příkazy, které jsou k dispozici v PEP, přímo z relace PowerShellu na hostiteli vývojové sady. Můžete ale chtít otestovat některé operace pomocí PEP, jako je například shromažďování protokolů, protože se jedná o jedinou metodu, kterou lze použít k provádění určitých operací v prostředí integrovaných systémů.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

## <a name="access-the-privileged-endpoint"></a>Přístup k privilegovanému koncovému bodu

K PEP přistupujete prostřednictvím vzdálené relace PowerShellu na virtuálním počítači, který hostuje PEP. V ASDK se tento virtuální počítač jmenuje **AzS-ERCS01**. Pokud používáte integrovaný systém, existují tři instance PEP, z nichž každý běží v rámci virtuálního počítače (*předpona*-ERCS01, *prefix*-ERCS02 nebo *prefix*-ERCS03) na různých hostitelích pro zajištění odolnosti.

Než zahájíte tento postup pro integrovaný systém, ujistěte se, že máte přístup k PEP buď podle IP adresy, nebo prostřednictvím DNS. Po počátečním nasazení centra Azure Stack můžete k PEP přistupovat jenom pomocí IP adresy, protože integrace DNS zatím není nastavená. Dodavatel hardwaru OEM vám poskytne soubor JSON s názvem **AzureStackStampDeploymentInfo** , který obsahuje IP adresy PEP.

IP adresu můžete najít také na portálu pro správu centra Azure Stack. Otevřete portál, například `https://adminportal.local.azurestack.external` . Vyberte **Vlastnosti správy oblastí**  >  .

`en-US`Při spuštění privilegovaného koncového bodu budete muset nastavit aktuální nastavení jazykové verze, jinak rutiny, jako je Test-AzureStack nebo Get-AzureStackLog, nebudou fungovat podle očekávání.

> [!NOTE]
> Z bezpečnostních důvodů vyžadujeme, abyste se připojili ke službě PEP jenom z posíleného virtuálního počítače, který běží na hostiteli životního cyklu hardwaru, nebo z vyhrazeného a zabezpečeného počítače, jako je například [pracovní stanice privilegovaného přístupu](/windows-server/identity/securing-privileged-access/privileged-access-workstations). Původní konfigurace hostitele životního cyklu hardwaru nesmí být upravena z původní konfigurace (včetně instalace nového softwaru) nebo používaná pro připojení k PEP.

1. Vytvořte vztah důvěryhodnosti.

   - V integrovaném systému spusťte následující příkaz z relace se zvýšenými oprávněními Windows PowerShellu a přidejte PEP jako důvěryhodného hostitele na zesílený virtuální počítač, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu.

      ```powershell  
      Set-Item WSMan:\localhost\Client\TrustedHosts -Value '<IP Address of Privileged Endpoint>' -Concatenate
      ```
      
   - Pokud používáte ASDK, přihlaste se k hostiteli vývojové sady.

1. V zesíleném virtuálním počítači, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu, otevřete relaci prostředí Windows PowerShell. Spusťte následující příkazy, abyste navázali vzdálenou relaci na virtuálním počítači, který je hostitelem PEP:
 
   - V integrovaném systému:

      ```powershell  
      $cred = Get-Credential

      $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
      ```
    
      `ComputerName`Parametr může být buď IP adresa, nebo název DNS jednoho z virtuálních počítačů, které hostují PEP.

      > [!NOTE]  
      > Azure Stack centrum neprovádí vzdálené volání při ověřování přihlašovacích údajů PEP. K tomu se spoléhá na místně uložený veřejný klíč RSA.

   - Pokud používáte ASDK:

      ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
      ```
    
   Po zobrazení výzvy použijte následující přihlašovací údaje:
   
   -  **Uživatelské jméno**: zadejte účet CloudAdmin ve formátu **&lt; *Azure Stack centrum doména* &gt; \cloudadmin**. (Pro ASDK se uživatelské jméno **azurestack\cloudadmin**.)
   - **Heslo**: zadejte stejné heslo, které bylo zadáno během instalace pro účet správce domény AzureStackAdmin.

   > [!NOTE]
   > Pokud se nemůžete připojit ke koncovému bodu ERCS, opakujte kroky jedna a dvě s jinou IP adresou virtuálního počítače ERCS.

1. Po připojení se výzva změní na **[*IP adresa nebo název virtuálního počítače ERCS*]: PS>** nebo na **[AZS-ercs01]: PS>**, v závislosti na prostředí. Z tohoto místa spusťte příkaz `Get-Command` pro zobrazení seznamu dostupných rutin.

   Odkaz na rutiny najdete v [referenčních informacích o privilegovaném koncovém bodu Azure Stack hub](../reference/pep-2002/index.md) .

   Mnohé z těchto rutin jsou určené jenom pro integrovaná systémová prostředí (například rutiny související s integrací Datacenter). V ASDK byly ověřeny následující rutiny:

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="how-to-use-the-privileged-endpoint"></a>Použití privilegovaného koncového bodu 

Jak je uvedeno výše, PEP je koncový bod [POWERSHELL JEA](/powershell/scripting/learn/remoting/jea/overview) . Při poskytování silné bezpečnostní vrstvy JEA koncový bod omezuje některé základní funkce PowerShellu, jako je například skriptování nebo dokončování karet. Pokud se pokusíte použít nějaký typ operace skriptu, operace se nezdařila s chybou **ScriptsNotAllowed**. Toto selhání je očekávané chování.

Chcete-li například získat seznam parametrů pro danou rutinu, spusťte následující příkaz:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Alternativně můžete pomocí rutiny [**Import-PSSession**](/powershell/module/microsoft.powershell.utility/import-pssession?view=powershell-5.1) importovat všechny rutiny Pep do aktuální relace na místním počítači. Rutiny a funkce PEP jsou teď k dispozici na vašem místním počítači společně s doplňováním karet a obecně se jedná o skriptování. Můžete také spustit modul **[Get-Help](/powershell/module/microsoft.powershell.core/get-help)** a zkontrolovat pokyny k rutinám.

Pokud chcete importovat relaci PEP na místním počítači, proveďte následující kroky:

1. Vytvořte vztah důvěryhodnosti.

   - V integrovaném systému spusťte následující příkaz z relace se zvýšenými oprávněními Windows PowerShellu a přidejte PEP jako důvěryhodného hostitele na zesílený virtuální počítač, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu.

      ```powershell
      winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

   - Pokud používáte ASDK, přihlaste se k hostiteli vývojové sady.

1. V zesíleném virtuálním počítači, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu, otevřete relaci prostředí Windows PowerShell. Spusťte následující příkazy, abyste navázali vzdálenou relaci na virtuálním počítači, který je hostitelem PEP:

   - V integrovaném systému:
    
      ```powershell  
      $cred = Get-Credential
      
      $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
        -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```
    
      `ComputerName`Parametr může být buď IP adresa, nebo název DNS jednoho z virtuálních počítačů, které hostují PEP.

   - Pokud používáte ASDK:
     
      ```powershell  
      $cred = Get-Credential
    
      $session = New-PSSession -ComputerName azs-ercs01 `
        -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```

   Po zobrazení výzvy použijte následující přihlašovací údaje:

   - **Uživatelské jméno**: zadejte účet CloudAdmin ve formátu **&lt; *Azure Stack centrum doména* &gt; \cloudadmin**. (Pro ASDK se uživatelské jméno **azurestack\cloudadmin**.)
      
   - **Heslo**: zadejte stejné heslo, které bylo zadáno během instalace pro účet správce domény AzureStackAdmin.

1. Importujte relaci PEP do místního počítače:

   ```powershell 
   Import-PSSession $session
   ```

1. Nyní můžete použít dokončování karet a provádět skriptování jako obvykle na místní relaci PowerShellu se všemi funkcemi a rutinami PEP, aniž by došlo ke snížení stav zabezpečení centra Azure Stack. Užijte si ji!


## <a name="close-the-privileged-endpoint-session"></a>Zavřít privilegovanou relaci koncového bodu

Jak bylo zmíněno dříve, PEP protokoluje každou akci (a odpovídající výstup), kterou provedete v relaci PowerShellu. Relaci musíte uzavřít pomocí  `Close-PrivilegedEndpoint` rutiny. Tato rutina správně ukončí koncový bod a přenáší soubory protokolu do externí sdílené složky pro uchování.

Ukončení relace koncového bodu:

1. Vytvořte externí sdílení souborů, které je přístupné pro PEP. V prostředí vývojové sady můžete pouze vytvořit sdílenou složku na hostiteli vývojové sady.
1. Spusťte následující rutinu:

   ```powershell  
   Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
   ```

   Rutina používá parametry v následující tabulce:

   | Parametr | Popis | Typ | Vyžadováno |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | Cesta k externímu sdílení souborů definovaná jako "fileshareIP\sharefoldername" | Řetězec | Ano|
   | *Přihlašovací údaj* | Přihlašovací údaje pro přístup ke sdílené složce souborů | SecureString |   Ano |


Po úspěšném přenosu souborů protokolu přepisu do sdílené složky se automaticky odstraní z PEP. 

> [!NOTE]
> Pokud zavřete relaci PEP pomocí rutin `Exit-PSSession` nebo `Exit` nebo pouze zavřete konzolu PowerShellu, protokoly přepisu se nepřenášejí do sdílené složky. Zůstávají v PEP. Při příštím spuštění `Close-PrivilegedEndpoint` a zahrnutí sdílené složky se také přenesou protokoly přepisu z předchozích relací. Nepoužívejte `Exit-PSSession` nebo `Exit` k ukončení relace PEP použijte `Close-PrivilegedEndpoint` místo toho.

## <a name="unlocking-the-privileged-endpoint-for-support-scenarios"></a>Odemknutí privilegovaného koncového bodu pro scénáře podpory

Během scénáře podpory může pracovník podpory společnosti Microsoft potřebovat zvýšit relaci prostředí PowerShell privilegovaného koncového bodu, aby mohl získat přístup k interním údajům infrastruktury centra Azure Stack. Tento proces je někdy neformálně označován jako "rozbití skla" nebo "Unlock The PEP". Proces zvýšení úrovně relace PEP je dvěma kroky, dvou, dvou a dvou procesů ověřování organizace. Postup odemknutí je iniciován operátorem centra Azure Stack, který neustále uchovává kontrolu nad prostředím. Operátor přistupuje k PEP a provede tuto rutinu:
 
 ```powershell  
      Get-SupportSessionToken
 ```

Rutina vrátí token žádosti o relaci podpory, velmi dlouhý alfanumerický řetězec. Operátor pak předá pracovníkovi technické podpory společnosti Microsoft prostřednictvím konkrétního výběru (např. chat, e-mail). Pracovník podpory společnosti Microsoft používá token žádosti pro vygenerování, pokud je platný, token pro autorizaci relace podpory a pošle ho zpět do operátoru centra Azure Stack. Ve stejné relaci PowerShellu PEP operátor pak předá do této rutiny autorizační token jako vstup:

```powershell  
      unlock-supportsession
      cmdlet Unlock-SupportSession at command pipeline position 1
      Supply values for the following parameters:
      ResponseToken:
 ```

Pokud je autorizační token platný, relace PowerShellu PEP se zvýší na vyšší úroveň tím, že poskytuje úplné možnosti správy a úplný přístup k infrastruktuře. 

> [!NOTE]
> Všechny operace a rutiny spouštěné v relaci PEP se zvýšenými oprávněními se musí provádět v rámci striktního dozoru technické podpory společnosti Microsoft. Tato chyba by mohla vést k vážným výpadkům, ztrátě dat a může vyžadovat úplné opětovné nasazení prostředí Azure Stack hub.

Po ukončení relace podpory je velmi důležité zavřít zpátky relaci PEP s vyššími oprávněními pomocí rutiny **Close-PrivilegedEndpoint** , jak je vysvětleno v části výše. Jedna relace PEP je ukončena, token odemčení již není platný a nelze jej znovu použít k odemčení relace PEP.
Relace PEP se zvýšenými oprávněními má platnost 8 hodin, po které se v případě neukončení ukončí relace PEP se zvýšenými oprávněními automaticky znovu na běžnou relaci PEP.

## <a name="content-of-the-privileged-endpoint-tokens"></a>Obsah tokenů privilegovaných koncových bodů

PEP podporuje požadavek relace a tokeny autorizace k ochraně přístupu a zajištění, aby relace PEP mohla odemknout pouze autorizované tokeny. Tokeny jsou navržené tak, aby kryptograficky zaručují, že token odpovědi může přijmout jenom relace PEP, která vygenerovala token žádosti. Tokeny PEP neobsahují žádný druh informací, které by mohly jednoznačně identifikovat Azure Stackho centra nebo zákazníka. Jsou zcela anonymní. Jsou uvedené podrobnosti o obsahu jednotlivých tokenů.
 
### <a name="support-session-request-token"></a>Token žádosti o relaci podpory

Token žádosti o relaci podpory PEP se skládá ze tří objektů:

- Náhodně generované ID relace.
- Certifikát podepsaný svým držitelem generovaný za účelem použití páru veřejného a privátního klíče s jednou dobou. Certifikát neobsahuje žádné informace o daném prostředí.
- Časové razítko, které indikuje vypršení platnosti tokenu žádosti.

Token žádosti je pak zašifrovaný pomocí veřejného klíče cloudu Azure, ke kterému je prostředí centra Azure Stack zaregistrované.
 
### <a name="support-session-authorization-response-token"></a>Token odpovědi na autorizaci relace podpory

Token odpovědi na ověření PEP support se skládá ze dvou objektů:

- Náhodně generované ID relace extrahované z tokenu žádosti
- Časové razítko, které indikuje vypršení platnosti tokenu odpovědi.
      
Token odpovědi je pak zašifrovaný pomocí certifikátu podepsaného svým držitelem, který je obsažený v tokenu žádosti. Certifikát podepsaný svým držitelem byl dešifrován pomocí privátního klíče přidruženého ke cloudu Azure, proti kterému je prostředí centra Azure Stack registrované.


## <a name="next-steps"></a>Další kroky

- [Diagnostické nástroje centra Azure Stack](./azure-stack-diagnostic-log-collection-overview.md)
- [Referenční informace k privilegovanému koncovému bodu centra Azure Stack](../reference/pep-2002/index.md)
