---
title: Vyrovnávání zatížení virtuálních počítačů
description: V tomto tématu se dozvíte, jak nakonfigurovat funkci Vyrovnávání zatížení virtuálního počítače v Azure Stack HCI a Windows Server.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 1/14/2021
ms.openlocfilehash: 9caf8efdd8db46479cce3c5c4299fb5588c7d37a
ms.sourcegitcommit: 51ce5ba6cf0a377378d25dac63f6f2925339c23d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224610"
---
# <a name="virtual-machine-load-balancing"></a>Vyrovnávání zatížení virtuálních počítačů

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019; Windows Server 2016

Klíčovým aspektem nasazení rozhraní HCI jsou kapitálové výdaje (CapEx) potřebné k tomu, aby přešly do produkčního prostředí. Je běžné přidat redundanci, abyste se vyhnuli kapacitě provozu během špičky v produkčním prostředí, ale tím se zvýší CapEx. Tato redundance je často nutná, protože některé servery v clusteru hostují více virtuálních počítačů, zatímco jiné servery jsou nevyužité.

Ve výchozím nastavení povoleno v Azure Stack HCL, Windows Server 2019 a Windows Server 2016 je vyrovnávání zatížení virtuálního počítače funkce, která umožňuje optimalizovat využití serveru v clusterech. Identifikuje přetížené servery a za provozu migruje virtuální počítače z těchto serverů na potvrzené servery. Jsou dodrženy zásady selhání, jako je například anti-spřažení, domény selhání (lokality) a možní vlastníci.

Vyrovnávání zatížení virtuálního počítače vyhodnocuje zatížení serveru na základě následujících heuristik:

- **Aktuální tlak paměti:** Paměť je nejběžnější omezení prostředků na hostiteli Hyper-V.
- **Průměrné využití procesoru v rámci pětiletého okna:** Snižuje riziko, že se server v clusteru stane nepřesný.

## <a name="how-does-vm-load-balancing-work"></a>Jak funguje vyrovnávání zatížení virtuálního počítače?

Vyrovnávání zatížení virtuálního počítače probíhá automaticky při přidání nového serveru do clusteru a je možné ho také nakonfigurovat tak, aby prováděl periodické a opakované vyrovnávání zatížení.

### <a name="when-a-new-server-is-added-to-a-cluster"></a>Když se do clusteru přidá nový server

Když připojíte nový server ke clusteru, funkce Vyrovnávání zatížení virtuálního počítače automaticky vyrovnává kapacitu z existujících serverů do nově přidaného serveru v tomto pořadí:

1. Zatížení paměti a využití CPU se vyhodnotí na stávajících serverech v clusteru.
2. Identifikují se všechny servery, které překračují prahovou hodnotu.
3. Pro určení priority vyrovnávání se identifikují servery s největším zatížením paměti a využitím procesoru.
4. Virtuální počítače se migrují za provozu (bez výpadků) ze serveru, který překračuje prahovou hodnotu pro nově přidaný server v clusteru.

:::image type="content" source="media/vm-load-balancing/server-added.png" alt-text="Obrázek znázorňující nově přidávaného serveru do clusteru" border="false"::: 

### <a name="recurring-load-balancing"></a>Opakované vyrovnávání zatížení

Ve výchozím nastavení je vyrovnávání zatížení virtuálního počítače nakonfigurované pro periodické vyrovnávání: zatížení paměti a využití procesoru na každém serveru v clusteru se vyhodnotí každých 30 minut. Tady je postup:

1. Zatížení paměti a využití CPU se vyhodnocuje na všech serverech v clusteru.
2. Identifikují se všechny servery překračující prahovou hodnotu a ta pod prahovou hodnotou.
3. Pro určení priority vyrovnávání se identifikují servery s největším zatížením paměti a využitím procesoru.
4. Virtuální počítače se migrují za provozu (bez výpadků) ze serveru, který překračuje prahovou hodnotu na jiný server, který je pod minimální prahovou hodnotou.

:::image type="content" source="media/vm-load-balancing/periodic-balancing.png" alt-text="Obrázek znázorňující automatické převážení aktivního clusteru" border="false"::: 

## <a name="configure-vm-load-balancing-using-windows-admin-center"></a>Konfigurace vyrovnávání zatížení virtuálních počítačů pomocí centra pro správu Windows

Nejjednodušší způsob, jak nakonfigurovat vyrovnávání zatížení virtuálních počítačů, je použití centra pro správu systému Windows. 

:::image type="content" source="media/vm-load-balancing/vm-load-balancing.png" alt-text="Konfigurace vyrovnávání zatížení virtuálních počítačů pomocí centra pro správu Windows" lightbox="media/vm-load-balancing/vm-load-balancing.png":::

1. Připojte se ke svému clusteru a v **nabídce nástroje > nastavení**.

2. V části **Nastavení** vyberte **Vyrovnávání zatížení virtuálního počítače**.

3. V části **Vyrovnávání virtuálních počítačů** vyberte **vždy** , pokud se má vyrovnávat zatížení, a to každých 30 minut, připojení **serveru** k vyrovnávání zatížení, jenom když se připojíte k serveru, nebo **nikdy** nezakažte funkci Vyrovnávání zatížení virtuálního počítače Výchozí nastavení je **vždy**.

4. V části **agresivní** vyberte virtuální počítače s **nízkou** migrací za provozu, pokud je server více než 80 **%, který se bude** migrovat, pokud je server více než 70%, nebo **Pokud** je server v clusteru větší než 5% nad průměrem. Výchozí nastavení je **nízké**.

## <a name="configure-vm-load-balancing-using-windows-powershell"></a>Konfigurace vyrovnávání zatížení virtuálního počítače pomocí Windows PowerShellu

Můžete nakonfigurovat, jestli a kdy dochází k vyrovnávání zatížení pomocí společné vlastnosti clusteru `AutoBalancerMode` . Pokud chcete určit, kdy se má cluster vyrovnávat, spusťte v PowerShellu následující příkaz a nahraďte hodnotu z tabulky níže:

```PowerShell
(Get-Cluster).AutoBalancerMode = <value>
```

|AutoBalancerMode |Chování|
|-----------------|-----------|
| 0 | Zakázáno |
| 1 | Vyrovnávání zatížení po připojení serveru |
| 2 (výchozí) | Vyrovnávání zatížení po připojení serveru a každých 30 minut |

Můžete taky nakonfigurovat agresivní vyrovnávání pomocí společné vlastnosti clusteru `AutoBalancerLevel` . Pokud chcete řídit prahovou hodnotu agresivní, spusťte v PowerShellu následující příkaz a nahraďte hodnotu z tabulky níže:

```PowerShell
(Get-Cluster).AutoBalancerLevel = <value>
```

| AutoBalancerLevel | Agresivity | Chování |
|-------------------|----------------|----------|
| 1 (výchozí) | Nízká | Přesunout, pokud je hostitel více než 80% načten |
| 2 | Střední | Přesunout, pokud je hostitel více než 70% načten |
| 3 | Vysoká | Průměrný počet serverů v clusteru a přesun v případě, že je hostitel větší než 5% nad průměrem |

Chcete-li zjistit `AutoBalancerLevel` `AutoBalancerMode` , jak jsou vlastnosti a nastaveny, spusťte následující příkaz v PowerShellu:

```PowerShell
Get-Cluster | fl AutoBalancer*
```

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Správa virtuálních počítačů](vm.md)
- [Správa virtuálních počítačů pomocí PowerShellu](vm-powershell.md)
- [Vytvořit pravidla spřažení virtuálních počítačů](vm-affinity.md)
