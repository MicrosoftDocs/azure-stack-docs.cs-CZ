---
title: Nasazení infrastruktury SDN pomocí SDN Express
description: Naučte se nasazovat infrastrukturu SDN pomocí nástroje SDN Express.
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e367602252207a673316caf3482d7805bff02ba8
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647806"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Nasazení infrastruktury SDN pomocí SDN Express

> Platí pro Azure Stack HCI, verze 20H2

V tomto tématu nasadíte koncovou infrastrukturu SDN (Software Defined Network) pomocí skriptů SDN Express PowerShell. Infrastruktura může zahrnovat vysoce dostupný síťový adaptér (HA), vysoce dostupný software Load Balancer (SLB) a bránu s vysokou dostupností (GS).  Skripty podporují postupné nasazení, kde můžete nasadit jenom součást síťového adaptéru, abyste dosáhli základní sady funkcí s minimálními požadavky na síť.

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

SDN používá jako zdroj pro vytváření virtuálních počítačů SDN soubor VHDX, který obsahuje Azure Stack operační systém HCI (OS). Verze operačního systému v souboru VHDX se musí shodovat s verzí, kterou používá hostitelé Azure Stack HCL technologie Hyper-V. Tento soubor VHDX se používá u všech komponent infrastruktury SDN.

Pokud jste stáhli a nainstalovali operační systém HCI Azure Stack z ISO, můžete vytvořit soubor VHDX pomocí nástroje [Convert-WindowsImage ](https://www.powershellgallery.com/packages/Convert-WindowsImage/10.0) .

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

1. Přejít do úložiště [Microsoft SDN GitHub](https://github.com/microsoft/SDN) .

1. V úložišti rozbalte rozevírací seznam **kód** a zvolte možnost **klonovat** nebo **Stáhnout soubor zip** ke stažení souborů SDN do určeného počítače nasazení.

    > [!NOTE]
    > Na určeném počítači pro nasazení musí být spuštěný systém Windows Server 2016 nebo novější.

1. Extrahujte soubor ZIP a zkopírujte `SDNExpress` složku do složky počítače nasazení `C:\` .

## <a name="edit-the-configuration-file"></a>Úprava konfiguračního souboru

`MultiNodeSampleConfig.psd1`Datový soubor konfigurace PowerShellu obsahuje všechny parametry a nastavení, které jsou potřebné pro skript SDN Express jako vstup pro různé parametry a nastavení konfigurace. Tento soubor obsahuje konkrétní informace o tom, co je potřeba vyplnit, na základě toho, jestli nasazujete jenom součást síťového adaptéru, nebo taky Nástroj pro vyrovnávání zatížení softwaru a součásti brány. Podrobné informace najdete v tématu [plánování infrastruktury softwarově definované sítě](../concepts/plan-software-defined-networking-infrastructure.md) .

Přejděte do `C:\SDNExpress\scripts` složky a otevřete `MultiNodeSampleConfig.psd1` soubor ve svém oblíbeném textovém editoru. Změňte konkrétní hodnoty parametrů tak, aby vyhovovaly vaší infrastruktuře a nasazení:

### <a name="general-settings-and-parameters"></a>Obecné nastavení a parametry

Nastavení a parametry používá SDN obecně pro všechna nasazení:

- **VHDPath** – cesta k souboru VHD používaná všemi virtuálními počítači infrastruktury SDN (NC, SLB, GS)
- **VHDFile** – název souboru VHD používaný všemi virtuálními počítači infrastruktury Sdn
- **VMLocation** – cesta k souboru pro virtuální počítače infrastruktury Sdn
- **JoinDomain** -doména, ke které jsou připojené virtuální počítače infrastruktury Sdn
- **SDNMacPoolStart** – počáteční adresa MAC fondu pro virtuální počítače úlohy klienta
- **SDNMacPoolEnd** -end adresa fondu adres MAC pro virtuální počítače úlohy klienta
- **ManagementSubnet** síť pro správu, kterou používá NC ke správě hostitelů Hyper-V, součástí SLB a GS
- **ManagementGateway** – adresa brány pro síť pro správu
- **ManagementDNS** -server DNS pro síť pro správu
- **ManagementVLANID** – ID sítě VLAN pro síť pro správu
- **DomainJoinUsername** – uživatelské jméno správce
- **LocalAdminDomainUser** – heslo správce
-  Název názvu DNS používaný klienty pro správu (například centrum pro správu systému Windows) ke komunikaci s NC
- **HyperVHosts** – hostitelské servery, které se mají spravovat pomocí síťového adaptéru
- **NCUsername** – uživatelské jméno účtu síťového adaptéru
- **ProductKey** – kód Product Key pro virtuální počítače infrastruktury Sdn
- **Přepínač** – požadováno pouze v případě, že v hostitelích Hyper-V existuje více než jeden virtuální přepínač
- **VMMemory** – paměť (v GB) přiřazená k virtuálním počítačům infrastruktury. Výchozí hodnota je 4 GB.
- **VMProcessorCount** – počet procesorů přiřazených k virtuálním počítačům infrastruktury. Výchozí hodnota je 8.
- **Národní prostředí** – Pokud není zadané, použije se národní prostředí počítače nasazení.
- **Časové pásmo** – Pokud není zadáno, použije se místní časové pásmo počítače nasazení.

Hesla lze volitelně zahrnout, pokud jsou uloženy šifrované jako zabezpečené řetězce kódované jako text.  Hesla se použijí jenom v případě, že se na stejném počítači, kde se hesla šifrují, spouštějí skripty SDN Express, v opačném případě se zobrazí výzva k zadání těchto hesel:

- **DomainJoinSecurePassword** – pro doménový účet
- **LocalAdminSecurePassword** – pro účet místního správce
- **NCSecurePassword** – účet síťového adaptéru

> [!NOTE]
> Skript SDN Express nepodporuje adresování DHCP. Zajistěte, aby byly adresy MAC všech virtuálních počítačů infrastruktury SDN mimo `SDNMACPool` rozsah parametrů.

### <a name="network-controller-vm-section"></a>Oddíl virtuálního počítače síťového adaptéru

`NCs = @()`Oddíl se používá pro virtuální počítače síťového adaptéru. Ujistěte se, že adresa MAC každého virtuálního počítače síťového adaptéru je mimo `SDNMACPool` rozsah uvedený v obecných nastaveních:

- **Názevpočítače** – název virtuálního počítače síťového adaptéru
- **Hostname** – název hostitele serveru, kde se nachází virtuální počítač NC
- **ManagementIP** -síťová IP adresa pro virtuální počítač NC
- **MACAddress** -MAC adresa pro virtuální počítač NC

### <a name="software-load-balancer-vm-section"></a>Část software Load Balancer VM

`Muxes = @()`Oddíl se používá pro virtuální počítače SLB. Ujistěte se, že adresa MAC každého virtuálního počítače SLB je mimo rozsah, který je `SDNMACPool` uvedený v obecných nastaveních. `Muxes = @()`Pokud nepoužíváte nasazení komponenty SLB, nechte tuto část prázdnou ():

- **Názevpočítače** – název virtuálního počítače SLB
- **Hostname** – název hostitele serveru, na kterém se nachází virtuální počítač SLB
- **ManagementIP** -síťová IP adresa pro virtuální počítač SLB
- **MACAddress** -MAC adresa pro virtuální počítač SLB
- IP adresa sítě poskytovatele **PAIPAddress** (PA) pro virtuální počítač SLB
- IP adresa sítě poskytovatele **PAMACAddress** (PA) pro virtuální počítač SLB

### <a name="gateway-vm-section"></a>Oddíl brány VM

`Gateways = @()`Oddíl se používá pro virtuální počítače brány. Ujistěte se, že adresa MAC každého virtuálního počítače brány je mimo rozsah, který je `SDNMACPool` uvedený v obecných nastaveních. `Gateways = @()`Pokud nepoužíváte nasazení součásti brány, nechte tuto část prázdnou ():

- **Názevpočítače** – název virtuálního počítače brány
- **Hostname** – název hostitele serveru, kde se nachází virtuální počítač brány
- **ManagementIP** – síťová IP adresa pro virtuální počítač brány
- **MACAddress** -MAC adresa pro virtuální počítač brány
- IP adresa front-end sítě **FrontEndIp** -Provider pro virtuální počítač brány
- Adresa MAC front-end sítě poskytovatele **FrontEndMac** pro virtuální počítač brány
- Adresa MAC back-end sítě poskytovatele **BackEndMac** pro virtuální počítač brány

### <a name="additional-settings-for-slb-and-gateway"></a>Další nastavení pro SLB a bránu

Virtuální počítače s SLB a branou používají následující další parametry. Pokud nejsou nasazené virtuální počítače služby SLB nebo brány, ponechte tyto hodnoty prázdné:

- **SDNASN** -autonomní systém číslo (ASN), který používá SDN pro partnery se síťovými přepínači
- **RouterASN** – ASN směrovače brány
- **RouterIPAddress** – IP adresa směrovače brány
- **PrivateVIPSubnet** – virtuální IP adresa (VIP) pro privátní podsíť
- **PublicVIPSubnet** – virtuální IP adresa pro veřejnou podsíť

Následující další parametry používají jenom virtuální počítače brány. Pokud nepoužíváte nasazení virtuálních počítačů brány, ponechte tyto hodnoty prázdné:

- Název **fondu** – název fondu používaný všemi virtuálními počítači brány
- **GRESubnet** -VIP podsíť pro GRE (Pokud používáte připojení GRE)
- Kapacita **kapacity** v KB/s pro každý virtuální počítač brány ve fondu

### <a name="settings-for-tenant-overlay-networks"></a>Nastavení pro překryvné sítě tenantů

Následující parametry se používají, pokud nasazujete a spravujete překryté virtualizované sítě pro klienty. Pokud místo toho chcete spravovat tradiční sítě VLAN pomocí síťového adaptéru, můžou být tyto hodnoty ponechány prázdné.

- **PASubnet** – podsíť pro síť poskytovatele adresy (PA)
- **PAVLANID** – ID sítě VLAN pro síť PA
- **PAGateway** -IP adresa pro síťovou BRÁNu PA
- **PAPoolStart** – počáteční IP adresa pro fond sítě PA
- **PAPoolEnd** -end IP adresa pro fond sítě PA

## <a name="run-the-deployment-script"></a>Spuštění zaváděcího skriptu

Skript SDN Express nasadí zadanou infrastrukturu SDN. Po dokončení skriptu je vaše infrastruktura SDN připravená k použití pro nasazení úloh virtuálních počítačů.

1. V tomto `README.md` souboru najdete nejnovější informace o tom, jak spustit skript nasazení.  

1. Z uživatelského účtu s přihlašovacími údaji správce pro hostitelské servery clusteru spusťte následující příkaz:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Po vytvoření virtuálních počítačů NC nakonfigurujte dynamické aktualizace DNS pro název clusteru síťového adaptéru na serveru DNS. Další informace najdete v tématu [Postup konfigurace dynamických aktualizací DNS](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-dns-dynamic-updates-windows-server-2003).

## <a name="next-steps"></a>Další kroky

Spravujte své virtuální počítače. Další informace najdete v tématu [Správa virtuálních počítačů](../manage/vm.md).
