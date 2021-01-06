---
title: Škálování akcí uzlu jednotky v Azure Stackovém centru – robustní
description: Seznamte se s akcemi uzlu jednotky škálování, včetně zapnutí, vypnutí, zakázání, obnovení a zobrazení stavu uzlu v integrovaných systémech Azure Stack hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/20/2020
ms.openlocfilehash: 126f96069b2440b6a38887306a92409b75f5b682
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910597"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub---ruggedized"></a>Škálování akcí uzlu jednotky v Azure Stackovém centru – robustní

Tento článek popisuje, jak zobrazit stav jednotky škálování. Můžete zobrazit uzly jednotky. Můžete spouštět akce uzlu, jako je zapnutí, vypnutí, vypnutí, vyprázdnění, obnovení a oprava. Obvykle tyto akce uzlu použijete během nahrazování částí nebo k obnovení uzlu.

> [!Important]  
> Akce všech uzlů popsaných v tomto článku by měly cílit na jeden uzel v jednom okamžiku.

## <a name="view-the-node-status"></a>Zobrazit stav uzlu

Na portálu pro správu můžete zobrazit stav jednotky škálování a přidružených uzlů.

Zobrazení stavu jednotky škálování:

1. Na dlaždici **Správa oblasti** vyberte oblast.
2. Na levé straně v části **prostředky infrastruktury** vyberte **jednotky škálování**.
3. Ve výsledcích vyberte jednotku škálování.
4. Vlevo v části **Obecné** vyberte **uzly**.

   Podívejte se na následující informace:

   - Seznam jednotlivých uzlů
   - Provozní stav (viz seznam níže).
   - Stav napájení (spuštěno nebo zastaveno).
   - Model serveru.//
   - IP adresa řadiče pro správu základní desky (BMC).
   - Celkový počet jader
   - Celková velikost paměti.

![stav jednotky škálování](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Provozní stavy uzlu

| Status | Popis |
|----------------------|-------------------------------------------------------------------|
| Spuštěno | Uzel je aktivně zapojen do jednotky škálování. |
| Zastaveno | Uzel není k dispozici. |
| Přidávání | Uzel se aktivně přidávají do jednotky škálování. |
| Opravíte | Uzel je aktivně opravován. |
| Údržba | Uzel je pozastaven a není spuštěna žádná úloha aktivního uživatele. |
| Vyžaduje nápravu | Zjistila se chyba, která vyžaduje, aby byl uzel opravený. |

## <a name="scale-unit-node-actions"></a>Akce uzlu škálování jednotky

Když zobrazíte informace o uzlu jednotky škálování, můžete také provádět akce uzlu jako:

 - Zahajte a zastavte (v závislosti na aktuálním stavu napájení).
 - Zakáže a obnoví (v závislosti na stavu operace).
 - Opravdu.
 - Vypnutí.

Provozní stav uzlu určuje, které možnosti jsou k dispozici.

Je potřeba nainstalovat moduly PowerShellu Azure Stack hub. Tyto rutiny jsou v modulu **AZS. Fabric. admin** . Pokud chcete nainstalovat nebo ověřit instalaci PowerShellu pro centrum Azure Stack, přečtěte si téma [instalace PowerShellu pro centrum Azure Stack](../../operator/azure-stack-powershell-install.md).

## <a name="stop"></a>Zastavit

Akce **zastavit** vypne uzel. Je stejný jako při stisknutí tlačítka napájení. Neposílá signál vypnutí operačnímu systému. V případě plánovaných operací zastavení vždy zkuste operaci vypnutí provést jako první.

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

V nepravděpodobném případě, že akce spuštění nefunguje, zkuste operaci zopakovat. Pokud se to nepovede podruhé, místo toho použijte webové rozhraní řadiče pro správu základní desky (BMC).

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

Akce **pokračovat** obnoví zakázaný uzel a označí ho jako aktivní pro umístění úloh. Dřívější úlohy, které byly spuštěny na uzlu, nevrátí navrácení služeb po obnovení. (Pokud používáte operaci vyprázdnění na uzlu, ujistěte se, že je zapnutý. Když svůj uzel zapnete zpátky, není označený jako aktivní pro umístění úloh. Až budete připraveni, musíte použít akci obnovit a označit uzel jako aktivní.)

Pokud chcete spustit akci obnovení, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Další informace najdete v tématu [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Repair

Akce **opravy** opraví uzel. Použijte ji pouze v jednom z následujících scénářů:

- Úplné nahrazení uzlů (s novými datovými disky nebo bez nich).
- Po selhání a nahrazení hardwarových součástí (Pokud je to doporučeno v dokumentaci k umístění jednotky [FRU]).

> [!Important]  
> Přesný postup, pokud potřebujete nahradit uzel nebo jednotlivé hardwarové součásti, najdete v dokumentaci k prostředí FRU dodavatele hardwaru OEM. V dokumentaci k rozhraní FRU se určí, zda je po nahrazení hardwarové součásti nutné spustit akci opravy.

Když spustíte akci opravit, musíte zadat IP adresu řadiče pro správu základní desky.

Pokud chcete spustit akci opravy, otevřete příkazový řádek PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Vypnutí

Akce **vypnutí** nejprve přesune všechny aktivní úlohy do zbývajících uzlů ve stejné jednotce škálování. Pak akce řádně vypne uzel jednotka škálování.

Po spuštění uzlu, který byl vypnut, je nutné spustit akci [obnovit](#resume) . Dřívější úlohy, které byly spuštěny na uzlu, nevrátí navrácení služeb po obnovení.

Pokud operace vypnutí neproběhne úspěšně, zkuste operaci [vyprázdnění](#drain) , po které následuje operace vypnutí.

Pokud chcete spustit akci vypnutí, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```

## <a name="next-steps"></a>Další kroky

[Seznamte se s modulem operátoru prostředků infrastruktury Azure Stack hub](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
