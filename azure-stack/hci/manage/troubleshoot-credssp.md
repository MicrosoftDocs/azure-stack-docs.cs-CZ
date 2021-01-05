---
title: Řešení CredSSP
description: Naučte se řešit potíže s zprostředkovatelem CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 472f0cf6103ac1559a9f8a0f757d66bc545f9a5d
ms.sourcegitcommit: f4a1a7e9d0b64ca84105d48170a23e1f473e976c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743535"
---
# <a name="troubleshoot-credssp"></a>Řešení CredSSP

> Platí pro Azure Stack HCI, verze v20H2

Některé Azure Stack operace HCI používají Vzdálená správa systému Windows (WinRM), což ve výchozím nastavení nepovoluje delegování přihlašovacích údajů. Aby bylo možné delegování povolit, musí mít počítač dočasně povolený zprostředkovatel zabezpečení (CredSSP) (Credential Security Support Provider). CredSSP je poskytovatel podpory zabezpečení, který umožňuje klientovi delegovat přihlašovací údaje na server pro vzdálené ověřování.

Povolení CredSSP je degradované zabezpečení stav a ve většině případů by mělo být zakázáno po dokončení úkolu nebo operace.

Mezi úlohy, které vyžadují, aby bylo možné povolit CredSSP, patří:

- Pracovní postup Průvodce vytvořením clusteru
- Dotazy nebo aktualizace služby Active Directory
- SQL Server dotazy nebo aktualizace
- Vyhledání účtů nebo počítačů v jiné doméně nebo prostředí, které není připojené k doméně

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Pokud máte problémy se zprostředkovatelem CredSSP, může vám pomáhat následující tipy k odstraňování potíží:

- Chcete-li použít Průvodce vytvořením clusteru při spuštění centra pro správu systému Windows na serveru místo počítače, musíte být členem skupiny Správci brány na serveru centra pro správu systému Windows. Další informace najdete v tématu [Možnosti přístupu uživatele v centru pro správu systému Windows](/windows-server/manage/windows-admin-center/plan/user-access-options).

- Při spuštění Průvodce vytvořením clusteru může zprostředkovatel CredSSP ohlásit problém, pokud se nevytvoří vztah důvěryhodnosti služby Active Directory nebo dojde k jeho přerušení. Výsledkem je, že se pro vytvoření clusteru použijí servery založené na pracovní skupině. V takovém případě zkuste ručně restartovat každý server v clusteru.

- Při spuštění centra pro správu systému Windows na serveru se ujistěte, že uživatelský účet je členem skupiny Správci brány.

- Doporučujeme spustit centrum pro správu systému Windows v počítači, který je členem stejné domény jako spravované servery.

- Aby bylo možné povolit nebo zakázat zprostředkovatele CredSSP na serveru, ujistěte se, že jste členem skupiny správců brány na daném počítači. Další informace najdete v prvním dvou částech [Konfigurace uživatelských Access Control a oprávnění](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

- Restartování služby WinRM na serverech v clusteru vás může vyzvat k opětovnému vytvoření připojení WinRM mezi jednotlivými servery clusteru a centrem pro správu systému Windows.

    Můžete to udělat tak, že na každém serveru clusteru kliknete a v centru pro správu systému Windows v nabídce **nástroje** vyberte **služby**, vyberte **WinRM**, vyberte **restartovat** a pak na příkazovém řádku pro **restart služby** vyberte **Ano**.

## <a name="manual-troubleshooting"></a>Ruční odstraňování potíží

Pokud se zobrazí následující chybová zpráva služby WinRM, zkuste chybu vyřešit pomocí kroků ručního ověření v této části. Příklad chybové zprávy:

`Connecting to remote <sever name> failed with the following error message: The WinRM client cannot process the request. A computer policy does not allow the delegation of the user credentials to the target computer because the computer is not trusted. The identity of the target computer can be verified if you configure the WSMAN service to use a valid certificate.`

Postup ručního ověření v této části vyžaduje, abyste nakonfigurovali následující počítače:
- Počítač s centrem pro správu systému Windows
- Server, na který se zobrazila chybová zpráva

Chcete-li vyřešit tuto chybu, proveďte následující opravné kroky podle potřeby:

**Náprava 1:**
1. Restartujte počítač s centrem pro správu systému Windows a serverem.
1. Zkuste znovu spustit Průvodce vytvořením clusteru.

    Podrobnosti o spuštění Průvodce najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí centra pro správu systému Windows](../deploy/create-cluster.md).

**Náprava 2:**
1. V počítači, na kterém je spuštěný centrum pro správu Windows, otevřete Windows PowerShell jako správce a spusťte následující příkazy:

    ```powershell
    Disable-WsmanCredSSP -Role Client  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Client -DelagateComputer <Server FQDN Name>
    ```

1. Připojte se k serveru pomocí funkce RDP a pak spusťte následující příkazy PowerShellu:

    ```powershell  
    Disable-WsmanCredSSP -Role Server  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Server  
    ```
    
1. Zkuste znovu spustit Průvodce vytvořením clusteru.

    Podrobnosti o spuštění Průvodce najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí centra pro správu systému Windows](../deploy/create-cluster.md).

**Náprava 3:**
1. V počítači s centrem pro správu systému Windows spusťte následující příkaz prostředí PowerShell pro kontrolu hlavního názvu služby (SPN):

    ```powershell
    setspn -Q WSMAN/<Windows Admin Center Computer Name>  
    ```
    
    Výsledek by měl vypsat následující výstup:

    `WSMAN/<Windows Admin Center Computer Name>`

    `WSMAN/<Windows Admin Center Computer FQDN Name>`

1. Pokud nejsou uvedené výsledky, spusťte následující příkazy PowerShellu k registraci hlavního názvu služby (SPN):

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer FQDN Name>  
    ```

1. Pomocí funkce RDP se připojte k serveru a spusťte následující příkaz PowerShellu pro kontrolu hlavního názvu služby (SPN):

    ```powershell
    setspn -Q WSMAN/<Server Name>  
    ```

    Výsledek by měl vypsat následující výstup:

    `WSMAN/<Server Name>`

    `WSMAN/<Server FQDN Name>`

1. Pokud nejsou uvedené výsledky, spusťte následující příkazy PowerShellu k registraci hlavního názvu služby (SPN):

    ```powershell
    setspn -S WSMAN/<Server Name> <Server Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Server Name> <Server FQDN Name>  
    ```

1. Zkuste znovu spustit Průvodce vytvořením clusteru.

    Podrobnosti o spuštění Průvodce najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí centra pro správu systému Windows](../deploy/create-cluster.md).


**Opravení 4:**

Pokud některý z předchozích kroků nápravy selhal nebo nebyl dokončen, může to znamenat, že došlo ke konfliktu záznamů ve službě Active Directory. K resetování záznamu jako nového záznamu ve službě Active Directory můžete použít jiný název počítače.

Chcete-li obnovit záznam ve službě Active Directory, přeinstalujte operační systém Azure Stack HCI s novým názvem počítače.

## <a name="next-steps"></a>Další kroky

Další informace o CredSSP najdete v tématu [Zprostředkovatel podpory zabezpečení přihlašovacích údajů](/windows/win32/secauthn/credential-security-support-provider).