---
title: Akce uzlů jednotek škálování ve službě Azure Stack Hub
description: Seznamte se s akcemi uzlu jednotky škálování, včetně zapnutí, vypnutí, zakázání, obnovení a zobrazení stavu uzlu v integrovaných systémech Azure Stack hub.
author: PatAltimore
ms.topic: how-to
ms.date: 11/19/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: dc98263b16a72e6b9809e7ef3bc0c286b627749d
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869759"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub"></a>Akce uzlů jednotek škálování ve službě Azure Stack Hub

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
   - Serverový model.
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

### <a name="azure-stack-hub-shows-adding-status-after-an-operation"></a>Azure Stack centrum zobrazuje přidání stavu po operaci.

Centrum Azure Stack může zobrazit stav provozního uzlu, který se **přidává** po provedení operace jako vyprázdnění, obnovení, opravy, vypnutí nebo spuštění.
K tomu může dojít, když se mezipaměť role poskytovatele prostředků infrastruktury po operaci neaktualizovala. 

Před použitím následujících kroků zajistěte, aby aktuálně neprobíhala žádná operace. Aktualizujte koncový bod tak, aby odpovídal vašemu prostředí.



### <a name="az-modules"></a>[AZ modules](#tab/az1)

1. Otevřete PowerShell a přidejte prostředí Azure Stack hub. To vyžaduje, [aby byl do počítače nainstalován Azure Stack hub PowerShell](./powershell-install-az-module.md) .

    ```powershell
    Add-AzEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Add-AzAccount -Environment AzureStack
    ```

2. Spuštěním následujícího příkazu restartujte roli poskytovatele prostředků infrastruktury.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Ověří provozní stav uzlu jednotky škálování, který má vliv na **běhu**. Můžete použít portál pro správu nebo následující příkaz prostředí PowerShell:

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. Pokud je provozní stav uzlu stále zobrazený, protože je **Přidání** pokračovat, otevřete incident podpory.

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm1)

1. Otevřete PowerShell a přidejte prostředí Azure Stack hub. To vyžaduje, [aby byl do počítače nainstalován Azure Stack hub PowerShell](./powershell-install-az-module.md) .

    ```powershell
    Add-AzureRMEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Add-AzureRMAccount -Environment AzureStack
    ```

2. Spuštěním následujícího příkazu restartujte roli poskytovatele prostředků infrastruktury.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Ověří provozní stav uzlu jednotky škálování, který má vliv na **běhu**. Můžete použít portál pro správu nebo následující příkaz prostředí PowerShell:

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. Pokud je provozní stav uzlu stále zobrazený, protože je **Přidání** pokračovat, otevřete incident podpory.

---



## <a name="scale-unit-node-actions"></a>Akce uzlu škálování jednotky

Když zobrazíte informace o uzlu jednotky škálování, můžete také provádět akce uzlu jako:

 - Zahajte a zastavte (v závislosti na aktuálním stavu napájení).
 - Zakáže a obnoví (v závislosti na stavu operace).
 - Opravdu.
 - Vypnutí.

Provozní stav uzlu určuje, které možnosti jsou k dispozici.

Je potřeba nainstalovat moduly PowerShellu Azure Stack hub. Tyto rutiny jsou v modulu **AZS. Fabric. admin** . Pokud chcete nainstalovat nebo ověřit instalaci PowerShellu pro centrum Azure Stack, přečtěte si téma [instalace PowerShellu pro centrum Azure Stack](powershell-install-az-module.md).

## <a name="stop"></a>Zastavit

Akce **zastavit** vypne uzel. Je stejný jako při stisknutí tlačítka napájení. Neposílá signál vypnutí operačnímu systému. V případě plánovaných operací zastavení vždy zkuste operaci vypnutí provést jako první.

Tato akce se obvykle používá v případě, že uzel již nereaguje na požadavky.

Pokud chcete spustit akci zastavení, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

V nepravděpodobném případě, že akce zastavení nefunguje, zkuste operaci zopakovat, a pokud ji podruhé použijete, použijte místo toho webové rozhraní řadiče pro správu základní desky (BMC).

Další informace najdete v tématu [stop-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Start

Akce **Spustit** zapne uzel. Je to stejné jako při stisknutí tlačítka napájení.

Pokud chcete spustit akci spustit, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

V nepravděpodobném případě, že akce spuštění nefunguje, zkuste operaci zopakovat. Pokud se to nepovede podruhé, místo toho použijte webové rozhraní řadiče pro správu základní desky (BMC).

Další informace najdete v tématu [Start-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Vyprazdňuje

Akce **vyprázdnění** přesune všechny aktivní úlohy do zbývajících uzlů v příslušné jednotce škálování.

Tato akce se obvykle používá během nahrazování částí, jako je například nahrazení celého uzlu.

> [!Important]
> Nezapomeňte použít operaci vyprázdnění na uzlu během plánovaného časového období údržby, kde byli uživatelé upozorněni. Za určitých podmínek můžou aktivní úlohy zacházet s přerušením.

Pokud chcete spustit akci vyprázdnění, otevřete příkazový řádek PowerShellu se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Další informace najdete v tématu [Disable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Obnovit

Akce **pokračovat** obnoví zakázaný uzel a označí ho jako aktivní pro umístění úloh. Dřívější úlohy, které byly spuštěny na uzlu, nevrátí navrácení služeb po obnovení. (Pokud používáte operaci vyprázdnění na uzlu, ujistěte se, že je zapnutý. Když svůj uzel zapnete zpátky, není označený jako aktivní pro umístění úloh. Až budete připraveni, musíte použít akci obnovit a označit uzel jako aktivní.)

Pokud chcete spustit akci obnovení, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Další informace najdete v tématu [Enable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Repair

> [!CAUTION]  
> Úroveň firmwaru je zásadní pro úspěch operace popsané v tomto článku. Chybějící tento krok může vést k nestabilitě systému, snížení výkonu, bezpečnostním hrozbám nebo selhání při nasazení služby Azure Stack hub Automation. Při nahrazování hardwaru vždy projděte dokumentaci k vašemu hardwarovému partnerovi, aby se zajistilo, že aplikovaný firmware odpovídá verzi OEM zobrazené na [portálu pro správu centra Azure Stack](azure-stack-updates.md).<br><br>
Další informace a odkazy na dokumentaci k partnerům najdete v tématu [Výměna hardwarové komponenty](azure-stack-replace-component.md).

| Hardwarový partner | Oblast | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Vše | [Příručka k provoznímu systému Cisco Integrated System for Microsoft Azure Stack hub](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Poznámky k verzi integrovaného systému Cisco pro Centrum Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Vše | [Cloud pro Microsoft Azure Stack hub 14G (účet a přihlášení je povinné)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pro Microsoft Azure Stack hub 13G (účet a přihlášení je povinné)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Japonsko | [Oddělení podpory spravované služby Fujitsu (vyžaduje se účet a přihlášení)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Společnosti Fujitsu podporují IT produkty a systémy](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [Fujitsu MySupport (vyžaduje se účet a přihlášení)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Vše | [HPE pro Centrum Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Vše | [Nejlepší recepty ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

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

- [Instalace Azure Stack PowerShellu](./powershell-install-az-module.md)
- [Seznamte se s modulem operátoru prostředků infrastruktury Azure Stack hub.](/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0&preserve-view=true)
- [Monitorování operací přidání uzlu](./azure-stack-add-scale-node.md#monitor-add-node-operations)
