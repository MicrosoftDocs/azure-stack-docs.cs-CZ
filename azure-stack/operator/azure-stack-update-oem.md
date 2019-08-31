---
title: Použití aktualizace výrobce OEM (Original Equipment Manufacturer) na Azure Stack | Microsoft Docs
description: Naučte se, jak použít aktualizaci OEM (Original Equipment Manufacturer) na Azure Stack.
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
ms.date: 08/28/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: bea83d743e7f43ff25d446a09b0e12d0c0af4232
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188365"
---
# <a name="apply-azure-stack-original-equipment-manufacturer-oem-updates"></a>Použít Azure Stack aktualizace OEM (Original Equipment Manufacturer)

*Platí pro: Azure Stack integrovaných systémů*

Můžete použít aktualizace OEM (Original Equipment Manufacturer) na vaše Azure Stack hardwarové komponenty pro přijímání vylepšení ovladačů a firmwaru a také opravy zabezpečení při minimalizaci dopadu na uživatele. V tomto článku se dozvíte o aktualizacích OEM, kontaktní údaje výrobce OEM a o tom, jak použít aktualizaci OEM.

## <a name="overview-of-oem-updates"></a>Přehled aktualizací OEM

Kromě Microsoft Azure Stack aktualizací mnoho výrobců OEM také vydává pravidelné aktualizace pro Azure Stack hardware, jako jsou aktualizace ovladačů a firmwaru. Ty se označují jako **aktualizace balíčků OEM**. Informace o tom, jestli výrobci OEM uvolňují aktualizace balíčků OEM, najdete v [dokumentaci k Azure Stack výrobce OEM](#oem-contact-information).

Tyto aktualizace balíčků OEM se nahrají do účtu úložiště **updateadminaccount** a používají se prostřednictvím portálu pro správu Azure Stack. Další informace najdete v tématu [použití aktualizací OEM](#apply-oem-updates).

Zeptejte se výrobce OEM na svůj konkrétní proces oznámení, aby bylo zajištěno, že oznámení o aktualizacích balíčků OEM dosáhnou vaší organizace.

Někteří dodavatelé hardwaru můžou vyžadovat *virtuální počítač dodavatele hardwaru* , který zpracovává interní proces aktualizace firmwaru. Další informace najdete v tématu [Konfigurace virtuálního počítače dodavatele hardwaru](#configure-hardware-vendor-vm) .

## <a name="oem-contact-information"></a>Kontaktní informace OEM 

Tato část obsahuje kontaktní informace OEM a odkazy na referenční materiál OEM Azure Stack.

| Hardwarový partner | Oblast | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Vše | [Podpora a aktualizace firmwaru Cisco Azure Stack – Automatické oznámení (vyžaduje se účet/přihlášení)](https://software.cisco.com/download/redirect?i=!y&mdfid=283862063&softwareid=286320368&release=1.0(0)&os=)<br><br>[Poznámky k verzi integrovaného systému Cisco pro Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Vše | [Cloud pro Microsoft Azure Stack 14G (vyžaduje se účet a přihlášení)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pro Microsoft Azure Stack 13G (vyžaduje se účet a přihlášení)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPONSKO | [Oddělení podpory spravované služby Fujitsu (vyžaduje se účet a přihlášení)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | & V OBLASTI EMEA | [Společnosti Fujitsu podporují IT produkty a systémy](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | Vše | [HPE pro Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Vše | [Nejlepší recepty ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Balíček OEM/firmware](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[dokumentace k Terra Azure Stack (včetně jednotky FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>Použít aktualizace OEM

Použijte balíčky OEM pomocí následujících kroků:

1. Budete se muset obrátit na výrobce OEM, aby:
      - Zjistěte aktuální verzi balíčku OEM.  
      - Najděte nejlepší metodu pro stažení balíčku OEM.  
2. Připravte si balíček OEM s postupem popsaným v části [stažení balíčků aktualizací pro integrované systémy](azure-stack-servicing-policy.md).
3. Aktualizace použijte s postupem popsaným v části [použití aktualizací v Azure Stack](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Konfigurace virtuálního počítače dodavatele hardwaru

Někteří dodavatelé hardwaru můžou vyžadovat, aby virtuální počítač mohl pomáhat s procesem aktualizace OEM. Dodavatel hardwaru bude zodpovědný za vytváření těchto virtuálních počítačů a dokumentaci, `ProxyVM` Pokud při spuštění rutiny `HardwareManager` **set-OEMExternalVM** vyžadujete nebo **VMType** , a také to, které přihlašovací údaje se mají použít **– Přihlašovací údaje**. Po vytvoření virtuálních počítačů je nakonfigurujte pomocí **set-OEMExternalVM** z privilegovaného koncového bodu.

Další informace o privilegovaném koncovém bodu v Azure Stack najdete v tématu [použití privilegovaného koncového bodu v Azure Stack](azure-stack-privileged-endpoint.md).

1.  Přístup k privilegovanému koncovému bodu

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Nakonfigurujte virtuální počítač dodavatele hardwaru pomocí rutiny **set-OEMExternalVM** . Rutina ověří IP adresu a přihlašovací údaje pro **– VMType** `ProxyVM`. Rutina **-VMType** `HardwareManager` neprovede ověření vstupu. Parametr **-Credential** zadaný pro **set-OEMExternalVM** je ten, který bude jasně zdokumentován v dokumentaci výrobce hardwaru.  Nejedná se o CloudAdmin přihlašovací údaje, které se používají s privilegovaným koncovým bodem, nebo jakékoli jiné existující Azure Stack přihlašovací údaje.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Další kroky

[Aktualizace Azure Stack](azure-stack-updates.md)
