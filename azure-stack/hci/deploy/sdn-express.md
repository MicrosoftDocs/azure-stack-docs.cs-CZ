---
title: Nasazení infrastruktury SDN pomocí SDN Express
description: Naučte se nasazovat infrastrukturu SDN pomocí nástroje SDN Express.
author: v-dasis
ms.topic: how-to
ms.date: 12/16/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: ba28d5904e58d84a913777fe80c5aa05a8ecdffe
ms.sourcegitcommit: 6a99a188bbad491e7d2817de0b9500a27797107e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97658275"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Nasazení infrastruktury SDN pomocí SDN Express

> Platí pro Azure Stack HCI, verze 20H2

V tomto tématu nasadíte koncovou infrastrukturu SDN (Software Defined Network) pomocí skriptů SDN Express PowerShell. Infrastruktura zahrnuje síťový adaptér s vysokou dostupností (HA), vysoce dostupný software Load Balancer (SLB) a bránu s vysokou dostupností.  

Skripty podporují postupné nasazení, kde můžete nasadit jenom síťový adaptér a dosáhnout tak základní sady funkcí s minimálními požadavky na síť. Síťový adaptér můžete nasadit také pomocí Průvodce vytvořením clusteru v centru pro správu systému Windows. K nasazení jiných komponent SDN, jako je SLB a Gateway, je ale nutné použít skripty SDN Express.

Infrastrukturu SDN můžete nasadit taky pomocí System Center Virtual Machine Manager (VMM). Další informace najdete v tématu [Správa prostředků SDN v prostředcích infrastruktury nástroje VMM](https://docs.microsoft.com/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Před zahájením

Než začnete s nasazením SDN, naplánujte a nakonfigurujte svoji fyzickou a hostitelskou infrastrukturu sítě. Odkázat na následující články:

- [Požadavky na fyzickou síť](../concepts/physical-network-requirements.md)
- [Požadavky na síť hostitele](../concepts/host-network-requirements.md)
- [Vytvoření clusteru pomocí centra pro správu Windows](create-cluster.md)
- [Vytvoření clusteru pomocí Windows PowerShellu](create-cluster-powershell.md)
- [Plánování infrastruktury softwarově definované sítě](../concepts/plan-software-defined-networking-infrastructure.md)

Nemusíte nasazovat všechny součásti SDN. V části [dvoufázové nasazení](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) v tématu [plánování infrastruktury softwarově definované sítě](../concepts/plan-software-defined-networking-infrastructure.md) určete, které součásti infrastruktury budete potřebovat, a pak skripty spusťte odpovídajícím způsobem.

Zajistěte, aby na všech hostitelských serverech byl nainstalován operační systém Azure Stack HCI. Postup najdete v tématu [nasazení operačního systému Azure Stack HCI](operating-system.md) .

## <a name="run-the-sdn-express-scripts"></a>Spuštění skriptů SDN Express

1. Přejít na úložiště GitHubu [SDN Express](https://github.com/microsoft/SDN) .

1. Stáhněte si soubory z úložiště do určeného počítače nasazení. Vyberte **klonovat** nebo **Stáhnout ZIP**.

    > [!NOTE]
    > Na určeném počítači pro nasazení musí být spuštěný systém Windows Server 2016 nebo novější.

1. Extrahujte soubor ZIP a zkopírujte `SDNExpress` složku do složky počítače nasazení `C:\` .

1. Přejděte do složky `C:\SDNExpress\scripts`.

1. Spusťte `SDNExpress.ps1` soubor skriptu. Tento skript používá jako vstup soubor nasazení prostředí PowerShell (PSD) pro různá nastavení konfigurace. `README.md`Pokyny ke spuštění skriptu najdete v souboru.  

1. Jako zdroj pro vytváření virtuálních počítačů SDN použijte VHDX, který obsahuje Azure Stack operační systém HCI. Pokud jste stáhli a nainstalovali operační systém HCI Azure Stack z ISO, můžete tento disk VHDX vytvořit pomocí tohoto `convert-windowsimage` nástroje, jak je popsáno v dokumentaci.

1. Upravte soubor tak, že `SDNExpress\scripts\MultiNodeSampleConfig.psd1` změníte konkrétní hodnoty tak, aby odpovídaly vaší infrastruktuře, včetně názvů hostitelů, názvů domén, uživatelských jmen a hesel a informací o síti pro sítě, které jsou uvedené v tématu [plánování infrastruktury softwarově definované sítě](../concepts/plan-software-defined-networking-infrastructure.md) . Tento soubor obsahuje konkrétní informace o tom, co je potřeba vyplnit, na základě toho, jestli nasazujete jenom součást síťového adaptéru, nebo taky Nástroj pro vyrovnávání zatížení softwaru a součásti brány.

1. Spusťte následující skript z uživatelského účtu s přihlašovacími údaji správce na hostitelích Hyper-V:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Po vytvoření virtuálních počítačů síťového adaptéru nakonfigurujte dynamické aktualizace DNS pro název clusteru síťového adaptéru na serveru DNS. Další informace najdete v kroku 3 [požadavky pro nasazení síťového adaptéru](https://docs.microsoft.com/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Další kroky

Spravujte své virtuální počítače. Další informace najdete v tématu [Správa virtuálních počítačů](../manage/vm.md) .