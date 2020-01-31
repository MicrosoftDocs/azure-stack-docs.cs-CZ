---
title: Přidání uzlů jednotek škálování do centra Azure Stack
description: Naučte se přidávat uzly jednotek škálování do jednotek škálování v Azure Stackovém centru.
author: mattbriggs
ms.topic: article
ms.date: 1/16/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: d138fe5c4203cc28cedfb44964d7b99d9fc72931
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876588"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack-hub"></a>Přidání dalších uzlů jednotek škálování do centra Azure Stack

Operátoři centra Azure Stack můžou zvýšit celkovou kapacitu existující jednotky škálování přidáním dalšího fyzického počítače. Fyzický počítač se také označuje jako uzel jednotky škálování. Každý nový uzel jednotky škálování, který přidáte, musí být homogenní v typu procesoru, paměti a čísla a velikosti disku pro uzly, které jsou již přítomny v jednotce škálování.

Chcete-li přidat uzel jednotky škálování, budete pracovat v Azure Stack hub a spouštět nástroje od výrobce hardwaru (OEM). Nástroje pro výrobce OEM běží na hostiteli životního cyklu hardwaru (HLH), aby se zajistilo, že nový fyzický počítač odpovídá stejné úrovni firmwaru jako stávající uzly.

Následující vývojový diagram znázorňuje obecný proces přidání uzlu jednotky škálování:

![přidat tok jednotek škálování](media/azure-stack-add-scale-node/add-node-flow.png)
<br> *Bez ohledu na to, jestli dodavatel hardwaru OEM dokládá umístění racku fyzického serveru a aktualizuje firmware, se liší podle vaší smlouvy o podpoře.*

Dokončení operace přidání nového uzlu může trvat několik hodin nebo dní. V systému není žádný vliv na spuštěné úlohy, zatímco je přidaný další uzel jednotky škálování.

> [!Note]  
> Neprovádějte žádnou z následujících operací, pokud již probíhá operace přidání uzlu jednotky škálování:
>
>  - Aktualizace centra Azure Stack
>  - Rotace certifikátů
>  - Zastavit Azure Stack centra
>  - Opravit uzel jednotky škálování


## <a name="add-scale-unit-nodes"></a>Přidání uzlů jednotky škálování

Následující kroky jsou nejdůležitějším přehledem o tom, jak přidat uzel. Tyto kroky neprovádějte, aniž byste nejdřív odkazovali na dokumentaci k rozšíření kapacity poskytované výrobcem OEM.

1. Umístěte nový fyzický server do stojanu a zapojte ho správně. 
2. Povolte porty fyzických přepínačů a v případě potřeby upravte seznamy řízení přístupu (ACL).
3. Nakonfigurujte správnou IP adresu v řadiči pro správu základní desky (BMC) a použijte všechna nastavení systému BIOS podle vaší dokumentace poskytované výrobcem OEM.
4. Použijte aktuální standardní hodnotu firmwaru pro všechny komponenty pomocí nástrojů, které jsou k dispozici výrobcem hardwaru, který běží na HLH.
5. Spusťte operaci přidat uzel na portálu správce centra Azure Stack.
6. Ověřte, zda je operace přidat uzel úspěšná. Provedete to tak, že zkontrolujete [ **stav** jednotky škálování](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Přidat uzel

K přidání nových uzlů můžete použít portál pro správu nebo PowerShell. Operace přidat uzel nejprve přidá nový uzel jednotky škálování jako dostupnou výpočetní kapacitu a pak automaticky rozšíří kapacitu úložiště. Kapacita se automaticky rozbalí, protože Azure Stack hub je prosblížený systém, ve kterém se navzájem provádí *Výpočet* a škálování *úložiště* .

### <a name="use-the-administrator-portal"></a>Použití portálu pro správu

1. Přihlaste se k portálu Azure Stackového centra pro správu jako operátor služby Azure Stack.
2. Přejděte na **+ vytvořit prostředek** > **kapacity** > **uzel jednotky škálování**.
   uzel jednotky škálování ![](media/azure-stack-add-scale-node/select-node1.png)
3. V podokně **přidat uzel** vyberte *oblast*a potom vyberte *jednotku škálování* , do které chcete uzel přidat. Zadejte také *IP adresu řadiče pro správu základní desky* pro uzel jednotky škálování, který přidáváte. Najednou můžete přidat jenom jeden uzel.
   ![přidat podrobnosti uzlu](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>Použití PowerShellu

K přidání uzlu použijte rutinu **New-AzsScaleUnitNodeObject** .  

Před použitím některého z následujících ukázkových skriptů PowerShellu nahraďte *názvy uzlů* a *IP adresy* hodnotami z vašeho prostředí Azure Stack hub.

  > [!Note]  
  > Při pojmenovávání uzlu musíte zachovat název kratší než 15 znaků. Nemůžete také použít název, který obsahuje mezeru, nebo obsahuje některý z následujících znaků: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{``}``_`.

**Přidat uzel:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Monitorování operací přidání uzlu 
K získání stavu operace přidání uzlu použijte portál pro správu nebo PowerShell. Dokončení operací přidání uzlu může trvat několik hodin.

### <a name="use-the-administrator-portal"></a>Použití portálu pro správu 
Chcete-li monitorovat přidání nového uzlu, Projděte si část jednotka škálování nebo objekty uzlu jednotky škálování na portálu pro správu. Provedete to tak, že přejdete do **oblasti správa** > **jednotky škálování**. V dalším kroku vyberte jednotku škálování nebo uzel jednotky škálování, který chcete zkontrolovat. 

### <a name="use-powershell"></a>Použití PowerShellu
Stav jednotky škálování a uzly jednotek škálování se dají načíst pomocí PowerShellu následujícím způsobem:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Stav operace přidat uzel 
**Pro jednotku škálování:**

|Stav               |Popis  |
|---------------------|---------|
|Spuštěno              |Všechny uzly se aktivně podílejí na jednotce škálování.|
|Zastaveno              |Uzel jednotka škálování je buď nedostupný, nebo nedosažitelný.|
|Zvyšování            |Jeden nebo více uzlů jednotek škálování se momentálně přidávají jako výpočetní kapacita.|
|Konfigurace úložiště  |Výpočetní kapacita se rozšířila a konfigurace úložiště je spuštěná.|
|Vyžaduje nápravu |Zjistila se chyba, která vyžaduje opravu jednoho nebo více uzlů jednotek škálování.|


**Pro uzel jednotky škálování:**

|Stav                |Popis  |
|----------------------|---------|
|Spuštěno               |Uzel je aktivně zapojen do jednotky škálování.|
|Zastaveno               |Uzel není k dispozici.|
|Přidávání                |Uzel se aktivně přidávají do jednotky škálování.|
|Opravíte             |Uzel je aktivně opravován.|
|Údržba           |Uzel je pozastaven a není spuštěna žádná úloha aktivního uživatele. |
|Vyžaduje nápravu  |Zjistila se chyba, která vyžaduje, aby byl uzel opravený.|


## <a name="troubleshooting"></a>Řešení potíží
Níže jsou uvedeny běžné problémy, které se zobrazují při přidávání uzlu. 

**Scénář 1:**  Operace přidání uzlu jednotky škálování se nezdařila, ale jeden nebo více uzlů je uveden stavem zastaveno.  
- Náprava: Opravte jeden nebo více uzlů pomocí operace opravy. V jednom okamžiku může běžet jenom jedna operace opravy.

**Scénář 2:** Byl přidán jeden nebo více uzlů jednotek škálování, ale rozšíření úložiště se nezdařilo. V tomto scénáři objekt uzlu škálování jednotky oznamuje stav spuštěno, ale úloha konfigurace úložiště není spuštěná.  
- Náprava: pomocí privilegovaného koncového bodu zkontrolujte stav úložiště spuštěním následující rutiny prostředí PowerShell:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Scénář 3:** Obdrželi jste výstrahu, která indikuje, že úloha škálování úložiště se nezdařila.  
- Náprava: v tomto případě se nezdařila úloha konfigurace úložiště. Tento problém vyžaduje, abyste kontaktovali podporu.


## <a name="next-steps"></a>Další kroky 
[Přidat veřejné IP adresy](azure-stack-add-ips.md) 
