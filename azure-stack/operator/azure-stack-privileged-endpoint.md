---
title: Použití privilegovaného koncového bodu v centru Azure Stack | Microsoft Docs
description: Naučte se používat privilegovaný koncový bod (PEP) v centru Azure Stack jako operátor.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 1/8/2020
ms.openlocfilehash: 7754a1e88a8000ede2b4058e09a7e91c3c6171e7
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75810048"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>Použití privilegovaného koncového bodu v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Jako operátor centra Azure Stack byste pro většinu každodenních úloh správy měli používat rozhraní API pro správu portálu, PowerShell nebo Azure Resource Manager. U některých méně běžných operací ale potřebujete použít *privilegovaný koncový bod* (PEP). PEP je předem nakonfigurovaná konzola vzdáleného prostředí PowerShell, která poskytuje dostatek možností, které vám pomůžou provést požadovanou úlohu. Koncový bod používá [prostředí POWERSHELL JEA (jen dostatečná Správa)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) k zobrazení pouze omezené sady rutin. Pokud chcete získat přístup k PEP a vyvolat omezenou sadu rutin, použije se účet s nízkými oprávněními. Nejsou vyžadovány žádné účty správců. Pro zvýšení zabezpečení není skriptování povoleno.

K provedení těchto úloh můžete použít PEP:

- Úlohy nízké úrovně, jako je například [shromažďování diagnostických protokolů](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).
- Mnoho úloh integrace Datacenter po nasazení pro integrované systémy, jako je přidání služby pro odesílání DNS (Domain Name System) po nasazení, nastavení integrace Microsoft Graph integrace, Active Directory Federation Services (AD FS) (AD FS), certifikát rotace atd.
- Pro práci s podporou pro získání dočasného přístupu s vysokou úrovní pro důkladné řešení potíží s integrovaným systémem.

PEP zaznamená všechny akce (a odpovídající výstupy), které v relaci PowerShellu provedete. To zajišťuje úplnou transparentnost a kompletní auditování operací. Tyto soubory protokolu můžete zachovat pro budoucí audity.

> [!NOTE]
> V Azure Stack Development Kit (ASDK) můžete spouštět některé příkazy, které jsou k dispozici v PEP, přímo z relace PowerShellu na hostiteli vývojové sady. Můžete ale chtít otestovat některé operace pomocí PEP, jako je například shromažďování protokolů, protože se jedná o jedinou metodu, kterou lze použít k provádění určitých operací v prostředí integrovaných systémů.

## <a name="access-the-privileged-endpoint"></a>Přístup k privilegovanému koncovému bodu

K PEP přistupujete prostřednictvím vzdálené relace PowerShellu na virtuálním počítači, který hostuje PEP. V ASDK se tento virtuální počítač jmenuje **AzS-ERCS01**. Pokud používáte integrovaný systém, existují tři instance PEP, z nichž každý běží v rámci virtuálního počítače (*předpona*-ERCS01, *prefix*-ERCS02 nebo *prefix*-ERCS03) na různých hostitelích pro zajištění odolnosti.

Než zahájíte tento postup pro integrovaný systém, ujistěte se, že máte přístup k PEP buď podle IP adresy, nebo prostřednictvím DNS. Po počátečním nasazení centra Azure Stack můžete k PEP přistupovat jenom pomocí IP adresy, protože integrace DNS zatím není nastavená. Dodavatel hardwaru OEM vám poskytne soubor JSON s názvem **AzureStackStampDeploymentInfo** , který obsahuje IP adresy PEP.

IP adresu můžete najít také na portálu pro správu centra Azure Stack. Otevřete portál, například `https://adminportal.local.azurestack.external`. Vyberte > **vlastnosti** **oblasti Správa oblastí** .

Při spuštění privilegovaného koncového bodu budete potřebovat nastavit aktuální nastavení jazykové verze na `en-US`, jinak rutiny jako test-AzureStack nebo Get-AzureStackLog nebudou fungovat podle očekávání.

> [!NOTE]
> Z bezpečnostních důvodů vyžadujeme, abyste se připojili ke službě PEP jenom z posíleného virtuálního počítače, který běží na hostiteli životního cyklu hardwaru, nebo z vyhrazeného a zabezpečeného počítače, jako je například [pracovní stanice privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). Původní konfigurace hostitele životního cyklu hardwaru nesmí být upravena z původní konfigurace (včetně instalace nového softwaru) nebo používaná pro připojení k PEP.

1. Vytvořte vztah důvěryhodnosti.

      - V integrovaném systému spusťte následující příkaz z relace se zvýšenými oprávněními Windows PowerShellu a přidejte PEP jako důvěryhodného hostitele na zesílený virtuální počítač, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu.

      ```powershell  
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

      - Pokud používáte ASDK, přihlaste se k hostiteli vývojové sady.

2. V zesíleném virtuálním počítači, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu, otevřete relaci prostředí Windows PowerShell. Spusťte následující příkazy, abyste navázali vzdálenou relaci na virtuálním počítači, který je hostitelem PEP:
 
  - V integrovaném systému:

    ```powershell  
    $cred = Get-Credential

    $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
    Enter-PSSession $pep
    ```
    
    Parametr `ComputerName` může být buď IP adresa, nebo název DNS jednoho z virtuálních počítačů, které hostují PEP.

    > [!NOTE]  
    >Azure Stack centrum neprovádí vzdálené volání při ověřování přihlašovacích údajů PEP. K tomu se spoléhá na místně uložený veřejný klíč RSA.

   - Pokud používáte ASDK:

     ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
     ```
    
   - Po zobrazení výzvy použijte následující přihlašovací údaje:
   
       - **Uživatelské jméno**: zadejte účet CloudAdmin ve formátu **&lt;*Azure Stack doméně centra*&gt;\cloudadmin**. (Pro ASDK se uživatelské jméno **azurestack\cloudadmin**.)
  
        - **Heslo**: zadejte stejné heslo, které bylo zadáno během instalace pro účet správce domény AzureStackAdmin.

      > [!NOTE]
      > Pokud se nemůžete připojit ke koncovému bodu ERCS, opakujte kroky jedna a dvě s jinou IP adresou virtuálního počítače ERCS.

3. Po připojení se výzva změní na **[*IP adresa nebo název virtuálního počítače ERCS*]: PS >** nebo na **[AZS-ercs01]: PS >** , v závislosti na prostředí. Z tohoto místa spusťte `Get-Command` pro zobrazení seznamu dostupných rutin.

   Mnohé z těchto rutin jsou určené jenom pro integrovaná systémová prostředí (například rutiny související s integrací Datacenter). V ASDK byly ověřeny následující rutiny:

   - Vymazat – hostitel
   - Zavřít – PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure – objekt
   - New-CloudAdminUser
   - Výstupní – výchozí
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tipy pro použití privilegovaného koncového bodu 

Jak je uvedeno výše, PEP je koncový bod [POWERSHELL JEA](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) . Při poskytování silné bezpečnostní vrstvy JEA koncový bod omezuje některé základní funkce PowerShellu, jako je například skriptování nebo dokončování karet. Pokud se pokusíte použít nějaký typ operace skriptu, operace se nezdařila s chybou **ScriptsNotAllowed**. Toto selhání je očekávané chování.

Chcete-li například získat seznam parametrů pro danou rutinu, spusťte následující příkaz:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Alternativně můžete pomocí rutiny [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) importovat všechny rutiny Pep do aktuální relace na místním počítači. Díky tomu jsou teď všechny rutiny a funkce PEP dostupné na místním počítači, a to spolu s doplňováním tabulátoru a dalšími obecnými skriptováními.

Pokud chcete importovat relaci PEP na místním počítači, proveďte následující kroky:

1. Vytvořte vztah důvěryhodnosti.

    - V integrovaném systému spusťte následující příkaz z relace se zvýšenými oprávněními Windows PowerShellu a přidejte PEP jako důvěryhodného hostitele na zesílený virtuální počítač, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu.

    ```powershell
    winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
    ```

    - Pokud používáte ASDK, přihlaste se k hostiteli vývojové sady.

2. V zesíleném virtuálním počítači, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu, otevřete relaci prostředí Windows PowerShell. Spusťte následující příkazy, abyste navázali vzdálenou relaci na virtuálním počítači, který je hostitelem PEP:

    - V integrovaném systému:
    
      ```powershell  
        $cred = Get-Credential
      
        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```
    
      Parametr `ComputerName` může být buď IP adresa, nebo název DNS jednoho z virtuálních počítačů, které hostují PEP.

    - Pokud používáte ASDK:
     
        ```powershell  
          $cred = Get-Credential
    
          $session = New-PSSession -ComputerName azs-ercs01 `
             -ConfigurationName PrivilegedEndpoint -Credential $cred
        ```

     Po zobrazení výzvy použijte následující přihlašovací údaje:

     - **Uživatelské jméno**: zadejte účet CloudAdmin ve formátu **&lt;*Azure Stack doméně centra*&gt;\cloudadmin**. (Pro ASDK se uživatelské jméno **azurestack\cloudadmin**.)
     - **Heslo**: zadejte stejné heslo, které bylo zadáno během instalace pro účet správce domény AzureStackAdmin.

3. Importujte relaci PEP do místního počítače:

    ```powershell 
      Import-PSSession $session
    ```

4. Nyní můžete použít dokončování karet a provádět skriptování jako obvykle na místní relaci PowerShellu se všemi funkcemi a rutinami PEP, aniž by došlo ke snížení stav zabezpečení centra Azure Stack. Užijte si ji!

## <a name="close-the-privileged-endpoint-session"></a>Zavřít privilegovanou relaci koncového bodu

 Jak bylo zmíněno dříve, PEP protokoluje každou akci (a odpovídající výstup), kterou provedete v relaci PowerShellu. Relaci je nutné zavřít pomocí rutiny `Close-PrivilegedEndpoint`. Tato rutina správně ukončí koncový bod a přenáší soubory protokolu do externí sdílené složky pro uchování.

Ukončení relace koncového bodu:

1. Vytvořte externí sdílení souborů, které je přístupné pro PEP. V prostředí vývojové sady můžete pouze vytvořit sdílenou složku na hostiteli vývojové sady.
2. Spusťte následující rutinu:

  ```powershell  
     Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
  ```

   Rutina používá parametry v následující tabulce:

   | Parametr | Popis | Typ | Požaduje se |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | Cesta k externímu sdílení souborů definovaná jako "fileshareIP\sharefoldername" | Řetězec | Ano|
   | *Přihlašovací údaje* | Přihlašovací údaje pro přístup ke sdílené složce souborů | SecureString |   Ano |


Po úspěšném přenosu souborů protokolu přepisu do sdílené složky se automaticky odstraní z PEP. 

> [!NOTE]
> Pokud zavřete relaci PEP pomocí rutin `Exit-PSSession` nebo `Exit`, nebo jenom zavřete konzolu PowerShellu, protokoly přepisu se nepřenášejí do sdílené složky. Zůstávají v PEP. Při příštím spuštění `Close-PrivilegedEndpoint` a zahrnutí sdílené složky se také přenesou protokoly přepisu z předchozích relací. Nepoužívejte `Exit-PSSession` ani `Exit` k uzavření relace PEP; místo toho použijte `Close-PrivilegedEndpoint`.


## <a name="next-steps"></a>Další kroky

[Diagnostické nástroje centra Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
