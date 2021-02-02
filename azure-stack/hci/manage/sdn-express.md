---
title: Nasazení infrastruktury SDN pomocí SDN Express
description: Naučte se nasazovat infrastrukturu SDN pomocí nástroje SDN Express.
author: v-dasis
ms.topic: how-to
ms.date: 02/01/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b8431b7e2cf2cad3d238386619839a760b722042
ms.sourcegitcommit: d74f6d8630783de49667956f39cac67e1968a89d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99473187"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Nasazení infrastruktury SDN pomocí SDN Express

> Platí pro Azure Stack HCI, verze 20H2

V tomto tématu nasadíte koncovou infrastrukturu SDN (Software Defined Network) pomocí skriptů SDN Express PowerShell. Infrastruktura může zahrnovat vysoce dostupný síťový adaptér (HA), vysoce dostupný software Load Balancer (SLB) a bránu s vysokou dostupností.  Skripty podporují postupné nasazení, kde můžete nasadit jenom síťový adaptér a dosáhnout tak základní sady funkcí s minimálními požadavky na síť. 

Infrastrukturu SDN můžete nasadit taky pomocí System Center Virtual Machine Manager (VMM). Další informace najdete v tématu [Správa prostředků SDN v prostředcích infrastruktury nástroje VMM](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Než začnete

Než začnete s nasazením SDN, naplánujte a nakonfigurujte svoji fyzickou a hostitelskou infrastrukturu sítě. Odkázat na následující články:

- [Požadavky na fyzickou síť](../concepts/physical-network-requirements.md)
- [Požadavky na síť hostitele](../concepts/host-network-requirements.md)
- [Vytvoření clusteru pomocí centra pro správu Windows](../deploy/create-cluster.md)
- [Vytvoření clusteru pomocí Windows PowerShellu](../deploy/create-cluster-powershell.md)
- [Plánování infrastruktury softwarově definované sítě](../concepts/plan-software-defined-networking-infrastructure.md)

Nemusíte nasazovat všechny součásti SDN. V části [dvoufázové nasazení](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) v tématu [plánování infrastruktury softwarově definované sítě](../concepts/plan-software-defined-networking-infrastructure.md) určete, které součásti infrastruktury budete potřebovat, a pak skripty spusťte odpovídajícím způsobem.

Zajistěte, aby na všech hostitelských serverech byl nainstalován operační systém Azure Stack HCI. Postup najdete v tématu [nasazení operačního systému Azure Stack HCI](../deploy/operating-system.md) .

## <a name="requirements"></a>Požadavky

Úspěšné nasazení SDN musí splňovat následující požadavky:

- Na všech hostitelských serverech musí být povolená technologie Hyper-V.
- Všechny hostitelské servery musí být připojené ke službě Active Directory.
- Je nutné vytvořit virtuální přepínač.
- Fyzická síť musí být nakonfigurovaná pro podsítě a sítě VLAN, které jsou definované v konfiguračním souboru.
- Soubor VHDX zadaný v konfiguračním souboru musí být dosažitelný z počítače nasazení, ve kterém je spuštěný skript SDN Express.

## <a name="create-the-vdx-file"></a>Vytvoření souboru VDX

SDN používá soubor VHDX, který obsahuje Azure Stack systému HCI jako zdroj pro vytváření virtuálních počítačů SDN (VM). Verze operačního systému v souboru VHDX se musí shodovat s verzí, kterou používají hostitelé Hyper-V.

Pokud jste stáhli a nainstalovali operační systém HCI Azure Stack z ISO, můžete tento disk VHDX vytvořit pomocí tohoto `Convert-WindowsImage` nástroje, jak je popsáno v [centru skriptů](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f).

Následující příklad ukazuje příklad použití `Convert-WindowsImage` :

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>Stáhnout úložiště GitHub

Soubory skriptu migrace SDN na webu GitHub. Prvním krokem je získat potřebné soubory a složky do počítače pro nasazení.

1. Přejít na úložiště GitHubu [SDN Express](https://github.com/microsoft/SDN) .

1. Stáhněte si soubory z úložiště do určeného počítače nasazení. Vyberte **klonovat** nebo **Stáhnout ZIP**.

    > [!NOTE]
    > Na určeném počítači pro nasazení musí být spuštěný systém Windows Server 2016 nebo novější.

1. Extrahujte soubor ZIP a zkopírujte `SDNExpress` složku do složky počítače nasazení `C:\` .

## <a name="edit-the-configuration-file"></a>Úprava konfiguračního souboru

`MultiNodeSampleConfig.psd1`Datový soubor konfigurace PowerShellu obsahuje všechny parametry a nastavení, které jsou potřebné pro skript SDN Express jako vstup pro různé parametry a nastavení konfigurace. Tento soubor obsahuje konkrétní informace o tom, co je potřeba vyplnit, na základě toho, jestli nasazujete jenom součást síťového adaptéru, nebo taky Nástroj pro vyrovnávání zatížení softwaru a součásti brány.

Podrobné informace najdete v tématu [plánování infrastruktury softwarově definované sítě](../concepts/plan-software-defined-networking-infrastructure.md) .

Pojďme začít:

1. Přejděte do složky `C:\SDNExpress\scripts`.

1. Otevřete `MultiNodeSampleConfig.psd1` soubor ve svém oblíbeném textovém editoru.

1. Změňte konkrétní hodnoty parametrů tak, aby vyhovovaly vaší infrastruktuře.

## <a name="run-the-deployment-script"></a>Spuštění zaváděcího skriptu

Skript SDN Express nasadí infrastrukturu SDN. Po dokončení skriptu je vaše infrastruktura připravená k použití pro nasazení úloh.

1. V tomto `README.md` souboru najdete nejnovější informace o tom, jak spustit skript nasazení.  

1. Z uživatelského účtu s přihlašovacími údaji správce pro hostitelské servery clusteru spusťte následující příkaz:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Po vytvoření virtuálních počítačů síťového adaptéru nakonfigurujte dynamické aktualizace DNS pro název clusteru síťového adaptéru na serveru DNS. Další informace najdete v kroku 3 [požadavky pro nasazení síťového adaptéru](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Další kroky

Spravujte své virtuální počítače. Další informace najdete v tématu [Správa virtuálních počítačů](../manage/vm.md) .