---
title: Použití aktualizace OEM na centrum Azure Stack
description: Naučte se, jak použít aktualizaci výrobce OEM (Original Equipment Manufacturer) na Azure Stack hub.
author: PatAltimore
ms.topic: how-to
ms.date: 10/15/2019
ms.author: patricka
ms.lastreviewed: 03/04/2020
ms.reviewer: ppacent
ms.openlocfilehash: 8b8c3a39f61693cad9904b99bd1f82b175d53a7b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868859"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Použití aktualizací pro výrobce OEM (Original Equipment Manufacturer) od centra Azure Stack

Pokud chcete získat aktualizace ovladačů, aktualizace firmwaru a opravy zabezpečení, můžete použít aktualizace OEM (Original Equipment Manufacturer) pro hardwarové komponenty centra Azure Stack. Tyto aktualizace se probíhají při minimalizaci dopadu na uživatele. V tomto článku se dozvíte o aktualizacích OEM, kontaktní údaje výrobce OEM a o tom, jak použít aktualizaci OEM.

## <a name="overview-of-oem-updates"></a>Přehled aktualizací OEM

Kromě Microsoft Azure Stack aktualizací centra řada výrobců OEM také vydává pravidelné aktualizace pro váš hardware Azure Stack hub, jako jsou aktualizace ovladačů a firmwaru. Tyto aktualizace se označují jako **aktualizace balíčků OEM**. Informace o tom, jestli výrobci OEM uvolňují aktualizace balíčků OEM, najdete v [dokumentaci k centru pro Azure Stack výrobce OEM](#oem-contact-information).

Tyto aktualizace balíčků OEM se nahrají do účtu úložiště **updateadminaccount** a používají se na portálu správce centra Azure Stack. Další informace najdete v tématu [použití aktualizací OEM](#apply-oem-updates).

Požádejte výrobce OEM o konkrétní proces oznámení, aby se zajistilo, že oznámení o aktualizacích balíčků OEM dosáhnou vaší organizace.

Někteří dodavatelé hardwaru můžou vyžadovat *virtuální počítač dodavatele hardwaru* , který zpracovává interní proces aktualizace firmwaru. Další informace najdete v tématu [Konfigurace virtuálního počítače dodavatele hardwaru](#configure-hardware-vendor-vm).

## <a name="oem-contact-information"></a>Kontaktní informace OEM

Tato část obsahuje kontaktní informace OEM a odkazy na referenční materiály pro výrobce OEM Azure Stack.

| Hardwarový partner | Oblast | URL |
|-----|----|-----|
| Cisco | Vše | [Příručka k provoznímu systému Cisco Integrated System for Microsoft Azure Stack hub](https://aka.ms/aa708e2)<br><br>[UCS Rack-Mount UCS-Managed serverový software řady UCS C-Series](https://aka.ms/aa700rq) |
| Dell EMC | Vše | [Cloud pro Microsoft Azure Stack hub 14G (účet a přihlášení je povinné)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pro Microsoft Azure Stack hub 13G (účet a přihlášení je povinné)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Japonsko | [Oddělení podpory spravované služby Fujitsu (vyžaduje se účet a přihlášení)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | & V OBLASTI EMEA | [Společnosti Fujitsu podporují IT produkty a systémy](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | Vše | [HPE pro Centrum Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Vše | [Nejlepší recepty ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Balíček OEM/firmware](https://aka.ms/AA6z600)<br>[dokumentace centra Terra Azure Stack (včetně jednotky FRU)](https://aka.ms/aa6zktc)

## <a name="apply-oem-updates"></a>Použít aktualizace OEM

Použijte balíčky OEM pomocí následujících kroků:

> [!IMPORTANT]
> Před použitím aktualizací v centru Azure Stack se ujistěte, že jste dokončili **všechny** kroky v [kontrolním seznamu před aktualizací](release-notes-checklist.md) a naplánovali jste příslušné okno údržby pro typ aktualizace, který používáte.

1. Kontaktujte výrobce OEM, aby:
      - Zjistěte aktuální verzi balíčku OEM.  
      - Najděte nejlepší metodu pro stažení balíčku OEM.  
2. Před použitím aktualizace balíčku OEM vždy použijte nejnovější opravu hotfix centra Azure Stack dostupnou na aktuální verzi centra Azure Stack v systému. Další informace o opravách hotfix najdete v tématu věnovaném [opravám hotfix centra Azure Stack](azure-stack-servicing-policy.md).
3. Připravte si balíček OEM s postupem popsaným v části [stažení balíčků aktualizací pro integrované systémy](azure-stack-servicing-policy.md).
4. Použijte aktualizace podle kroků uvedených v části [použití aktualizací v centru Azure Stack](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Konfigurace virtuálního počítače dodavatele hardwaru

Někteří dodavatelé hardwaru můžou potřebovat virtuální počítač (VM), který bude pomáhat s procesem aktualizace OEM. Váš dodavatel hardwaru zodpovídá za vytváření těchto virtuálních počítačů a dokumentaci, pokud při `ProxyVM` `HardwareManager` spuštění rutiny **set-OEMExternalVM** potřebujete nebo **VMType** , a taky to, které přihlašovací údaje se mají použít pro **přihlašovací údaje**. Po vytvoření virtuálních počítačů je nakonfigurujte pomocí **set-OEMExternalVM** z privilegovaného koncového bodu.

Další informace o privilegovaném koncovém bodu v centru Azure Stack najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](azure-stack-privileged-endpoint.md).

1. Přístup k privilegovanému koncovému bodu

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Nakonfigurujte virtuální počítač dodavatele hardwaru pomocí rutiny **set-OEMExternalVM** . Rutina ověří IP adresu a přihlašovací údaje pro **– VMType** `ProxyVM` . Rutina **-VMType** `HardwareManager` neověřuje vstup. Parametr **-Credential** zadaný pro **set-OEMExternalVM** je ten, který bude jasně zdokumentován v dokumentaci výrobce hardwaru.  Nejedná se o CloudAdmin přihlašovací údaje, které se používají s privilegovaným koncovým bodem, *nebo s jakýmkoli* jiným existujícím přihlašovacím údaji centra Azure Stack.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        {
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Další kroky

- [Azure Stack aktualizace centra](azure-stack-updates.md)
