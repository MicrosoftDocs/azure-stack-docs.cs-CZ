---
title: Škálování akcí uzlu jednotky v Azure Stack | Microsoft Docs
description: Naučte se zobrazovat stav uzlu a akce zapnutí, vypnutí, zakázání a obnovení uzlů v Azure Stack integrovaném systému.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 7ac25e86be91cf6a2e8384c88c79fe3022b3f00d
ms.sourcegitcommit: 159da88a52701679571bbedde1c36b72bbfe32dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380470"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Škálování akcí uzlu jednotky v Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje, jak zobrazit stav jednotky škálování. Můžete zobrazit uzly jednotky. Můžete spouštět akce uzlu, například zapnout, vypnout, vypnout, vyprázdnit, obnovit a opravit. Obvykle tyto akce uzlu použijete během nahrazování částí nebo k obnovení uzlu.

> [!Important]  
> Akce všech uzlů popsaných v tomto článku by měly cílit na jeden uzel v jednom okamžiku.

## <a name="view-the-node-status"></a>Zobrazit stav uzlu

Na portálu pro správu můžete zobrazit stav jednotky škálování a přidružených uzlů.

Zobrazení stavu jednotky škálování:

1. Na dlaždici **Správa oblasti** vyberte oblast.
2. Na levé straně v části **prostředky infrastruktury**vyberte **jednotky škálování**.
3. Ve výsledcích vyberte jednotku škálování.
4. Vlevo v části **Obecné**vyberte **uzly**.

   Podívejte se na následující informace:

   - Seznam jednotlivých uzlů
   - Provozní stav (viz seznam níže)
   - Stav napájení (spuštěno nebo zastaveno)
   - Model serveru
   - IP adresa řadiče pro správu základní desky (BMC)
   - Celkový počet jader
   - Celková velikost paměti

![stav jednotky škálování](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Provozní stavy uzlu

| Stav | Popis |
|----------------------|-------------------------------------------------------------------|
| Spuštěno | Uzel je aktivně zapojen do jednotky škálování. |
| Zastaveno | Uzel není k dispozici. |
| Přidávání | Uzel se aktivně přidávají do jednotky škálování. |
| Probíhají opravy | Uzel je aktivně opravován. |
| Údržba | Uzel je pozastaven a není spuštěna žádná úloha aktivního uživatele. |
| Vyžaduje nápravu | Zjistila se chyba, která vyžaduje, aby byl uzel opravený. |

## <a name="scale-unit-node-actions"></a>Akce uzlu škálování jednotky

Když zobrazíte informace o uzlu jednotky škálování, můžete také provádět akce uzlu, jako například:
 - Spustit a zastavit (v závislosti na aktuálním stavu napájení)
 - Zakázat a obnovit (v závislosti na stavu operací)
 - Opravit
 - Shutdown

Provozní stav uzlu určuje, které možnosti jsou k dispozici.

Je potřeba nainstalovat Azure Stack moduly PowerShellu. Tyto rutiny jsou v modulu **AZS. Fabric. admin** . Pokud chcete nainstalovat nebo ověřit instalaci PowerShellu pro Azure Stack, přečtěte si téma [instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Zastavit

Akce **zastavit** vypne uzel. Je to stejné jako při stisknutí tlačítka napájení. Neodesílá signál vypnutí operačnímu systému. V případě plánovaných operací zastavení vždy zkuste operaci vypnutí provést jako první. 

Tato akce se obvykle používá v případě, že je uzel ve stavu neodpovídá a již nereaguje na požadavky.

Pokud chcete spustit akci zastavení, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

V nepravděpodobném případě, že akce zastavení nefunguje, zkuste operaci zopakovat, a pokud ji podruhé použijete, použijte místo toho webové rozhraní řadiče pro správu základní desky (BMC).

Další informace najdete v tématu [stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Spustit

Akce **Spustit** zapne uzel. Je to stejné jako při stisknutí tlačítka napájení. 
 
Pokud chcete spustit akci spustit, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

V nepravděpodobném případě, že akce spuštění nefunguje, zkuste operaci zopakovat, a pokud se to nepovede, podruhé místo použijte webové rozhraní řadiče pro správu základní desky (BMC).

Další informace najdete v tématu [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Vyprazdňuje

Akce **vyprázdnění** přesune všechny aktivní úlohy do zbývajících uzlů v příslušné jednotce škálování.

Tato akce se obvykle používá během nahrazování částí, jako je například nahrazení celého uzlu.

> [!Important]
> Nezapomeňte použít operaci vyprázdnění na uzlu během plánovaného časového období údržby, kde byli uživatelé upozorněni. Za určitých podmínek můžou aktivní úlohy zacházet s přerušením.

Pokud chcete spustit akci vyprázdnění, otevřete příkazový řádek PowerShellu se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Další informace najdete v tématu [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Obnovit

Akce **pokračovat** obnoví zakázaný uzel a označí ho jako aktivní pro umístění úloh. Dřívější úlohy, které byly spuštěny na uzlu, se nevrátí navrácení služeb po obnovení. (Pokud používáte operaci vyprázdnění na uzlu, ujistěte se, že je zapnutý. Když zapnete uzel znovu, není označen jako aktivní pro umístění úloh. Až budete připraveni, musíte použít akci obnovit a označit uzel jako aktivní.)

Pokud chcete spustit akci obnovení, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Další informace najdete v tématu [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Opravit

> [!CAUTION]  
> Úroveň firmwaru je zásadní pro úspěch operace popsané v tomto článku. Chybějící tento krok může vést k nestabilitě systému, poklesu výkonu, vláknům zabezpečení nebo zabránit automatizaci Azure Stack k nasazení operačního systému. Při nahrazování hardwaru vždy projděte dokumentaci k vašemu hardwarovému partnerovi, aby se zajistilo, že aplikovaný firmware odpovídá verzi OEM zobrazené na [portálu Azure Stack pro správu](azure-stack-updates.md).<br>
Další informace a odkazy na dokumentaci k partnerům najdete v tématu [Výměna hardwarové komponenty](azure-stack-replace-component.md).

| Hardwarový partner | Oblast | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Vše | [Příručka k operačnímu systému Cisco Integrated System for Microsoft Azure Stack](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Poznámky k verzi integrovaného systému Cisco pro Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Vše | [Cloud pro Microsoft Azure Stack 14G (vyžaduje se účet a přihlášení)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pro Microsoft Azure Stack 13G (vyžaduje se účet a přihlášení)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPONSKO | [Oddělení podpory spravované služby Fujitsu (vyžaduje se účet a přihlášení)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EVROPA, STŘEDNÍ VÝCHOD A AFRIKA | [Společnosti Fujitsu podporují IT produkty a systémy](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [Fujitsu MySupport (vyžaduje se účet a přihlášení)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Vše | [HPE pro Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Vše | [Nejlepší recepty ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

Akce **opravy** opraví uzel. Použijte ji pouze v jednom z následujících scénářů:
 - Úplné nahrazení uzlů (s novými datovými disky nebo bez nich)
 - Po selhání a nahrazení hardwarových součástí (Pokud je to doporučeno v dokumentaci k poli replacená jednotka (FRU)).

> [!Important]  
> Přesný postup, pokud potřebujete nahradit uzel nebo jednotlivé hardwarové součásti, najdete v dokumentaci k prostředí FRU dodavatele hardwaru OEM. V dokumentaci k rozhraní FRU se určí, zda je po nahrazení hardwarové součásti nutné spustit akci opravy. 

Když spustíte akci opravit, musíte zadat IP adresu řadiče pro správu základní desky. 

Pokud chcete spustit akci opravy, otevřete příkazový řádek PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

Akce **vypnutí** Fist přesune všechny aktivní úlohy do zbývajících uzlů ve stejné jednotce škálování. Pak akce řádně vypne uzel jednotka škálování.

Po spuštění uzlu, který byl vypnut, je nutné spustit akci [obnovit](#resume) . Dřívější úlohy, které byly spuštěny na uzlu, se nevrátí navrácení služeb po obnovení.

Pokud operace vypnutí neproběhne úspěšně, zkuste [](#drain) operaci vyprázdnění, po které následuje operace vypnutí.

Pokud chcete spustit akci vypnutí, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>Další postup

Další informace o modulu Správce Azure Stack Fabric najdete v tématu [AZS. Fabric. admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
