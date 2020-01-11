---
title: Použití aktualizace výrobce OEM (Original Equipment Manufacturer) na centra Azure Stack | Microsoft Docs
description: Naučte se, jak použít aktualizaci výrobce OEM (Original Equipment Manufacturer) na Azure Stack hub.
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
ms.date: 10/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 21d91b9cb14fa46fbd92e033c59f7b78fbc64354
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882789"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Použití aktualizací pro výrobce OEM (Original Equipment Manufacturer) od centra Azure Stack

Můžete použít aktualizace OEM (Original Equipment Manufacturer) k hardwarovým komponentám centra Azure Stack pro příjem vylepšení ovladačů a firmwaru a také opravy zabezpečení při minimalizaci dopadu na uživatele. V tomto článku se dozvíte o aktualizacích OEM, kontaktní údaje výrobce OEM a o tom, jak použít aktualizaci OEM.

## <a name="overview-of-oem-updates"></a>Přehled aktualizací OEM

Kromě Microsoft Azure Stack aktualizací centra řada výrobců OEM také vydává pravidelné aktualizace pro váš hardware Azure Stack hub, jako jsou aktualizace ovladačů a firmwaru. Ty se označují jako **aktualizace balíčků OEM**. Informace o tom, jestli výrobci OEM uvolňují aktualizace balíčků OEM, najdete v [dokumentaci k centru pro Azure Stack výrobce OEM](#oem-contact-information).

Tyto aktualizace balíčků OEM se nahrají do účtu úložiště **updateadminaccount** a používají se na portálu správce centra Azure Stack. Další informace najdete v tématu [použití aktualizací OEM](#apply-oem-updates).

Zeptejte se výrobce OEM na svůj konkrétní proces oznámení, aby bylo zajištěno, že oznámení o aktualizacích balíčků OEM dosáhnou vaší organizace.

Někteří dodavatelé hardwaru můžou vyžadovat *virtuální počítač dodavatele hardwaru* , který zpracovává interní proces aktualizace firmwaru. Další informace najdete v tématu [Konfigurace virtuálního počítače dodavatele hardwaru](#configure-hardware-vendor-vm) .

## <a name="oem-contact-information"></a>Kontaktní informace OEM 

Tato část obsahuje kontaktní informace OEM a odkazy na referenční materiály pro výrobce OEM Azure Stack.

| Hardwarový partner | Region (Oblast) | Adresa URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Všechno | [Příručka k provoznímu systému Cisco Integrated System for Microsoft Azure Stack hub](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_01000.html)<br><br>[UCS řady UCS – připojit serverový software spravovaný přes UCS](https://software.cisco.com/download/home/283862063/type/286320368/release/2.0(0)) |
| Dell EMC | Všechno | [Cloud pro Microsoft Azure Stack hub 14G (účet a přihlášení je povinné)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pro Microsoft Azure Stack hub 13G (účet a přihlášení je povinné)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Japonsko | [Oddělení podpory spravované služby Fujitsu (vyžaduje se účet a přihlášení)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | & v oblasti EMEA | [Společnosti Fujitsu podporují IT produkty a systémy](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | Všechno | [HPE pro Centrum Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Všechno | [Nejlepší recepty ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Balíček OEM/firmware](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[dokumentace centra Terra Azure Stack (včetně jednotky FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>Použít aktualizace OEM

Použijte balíčky OEM pomocí následujících kroků:

1. Budete se muset obrátit na výrobce OEM, aby:
      - Zjistěte aktuální verzi balíčku OEM.  
      - Najděte nejlepší metodu pro stažení balíčku OEM.  
2. Předtím, než použijete aktualizaci balíčku OEM, byste měli vždy použít nejnovější opravu hotfix centra Azure Stack dostupnou v aktuální verzi centra Azure Stack systému. Další informace o opravách hotfix najdete v tématu [Azure Stack oprav hotfix centra](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy).
3. Připravte si balíček OEM s postupem popsaným v části [stažení balíčků aktualizací pro integrované systémy](azure-stack-servicing-policy.md).
4. Použijte aktualizace podle kroků uvedených v části [použití aktualizací v centru Azure Stack](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Konfigurace virtuálního počítače dodavatele hardwaru

Někteří dodavatelé hardwaru můžou vyžadovat, aby virtuální počítač mohl pomáhat s procesem aktualizace OEM. Váš dodavatel hardwaru bude zodpovědný za vytváření těchto virtuálních počítačů a dokumentování, pokud při spuštění rutiny **set-OEMExternalVM** vyžadujete `ProxyVM` nebo `HardwareManager` **VMType** , a také to, které přihlašovací údaje by se měly používat pro **přihlašovací údaje**. Po vytvoření virtuálních počítačů je nakonfigurujte pomocí **set-OEMExternalVM** z privilegovaného koncového bodu.

Další informace o privilegovaném koncovém bodu v centru Azure Stack najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](azure-stack-privileged-endpoint.md).

1.  Přístup k privilegovanému koncovému bodu

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Nakonfigurujte virtuální počítač dodavatele hardwaru pomocí rutiny **set-OEMExternalVM** . Rutina ověří IP adresu a přihlašovací údaje pro **VMType** `ProxyVM`. **VMType** `HardwareManager` rutinu neprovede ověření vstupu. Parametr **-Credential** zadaný pro **set-OEMExternalVM** je ten, který bude jasně zdokumentován v dokumentaci výrobce hardwaru.  Nejedná se o CloudAdmin přihlašovací údaje, které se používají s privilegovaným koncovým bodem, nebo s jakýmkoli jiným existujícím přihlašovacím údaji centra Azure Stack.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Další kroky

[Azure Stack aktualizace centra](azure-stack-updates.md)
